---
title: 如何建立在local machine利用pg_cron和knex來建立PostgreSQL task schedule
published: 2024-11-14
description: ''
image: 'https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/postgresql-blog-cover.png?alt=media&token=e5db5ca1-f428-4c27-ba74-199164b42444'
tags: [knex, PostgresSQL, pg_cron]
category: 'Database'
draft: false 
lang: 'zh-HK'
---

## 背景

最近開始做一個next.js 的side project，是一個場地預約的系統。預約的步驟有4步，第1步是用戶需要選擇預約的時間和日期，第2步是同意條款，第3步是確認預約資料，第4步是付款，最後就是成功畫面。

因為不希望在用戶預約的流程中，有其他用戶也會同一時間選擇了一樣的時間和日期，最後導致錯誤，所以當用戶在第1步按確定之後，會把該時間和日期先on hold 15分鐘，如果15分鐘內用戶還沒有付款，那麼這個時間和日期就會release，其他人就可以再預約了。

## 流程概念

我希望的做法是

- 用戶在預約的第1步按確定之後，新的一個訂單會在PostgreSQL的booking table中建立，這時候該訂單的狀態為 - pending for payment
- 之後PostgreSQL能夠每一分鐘將所有狀態為pending for payment 的訂單，透過將現在的時間和該訂單created_at這個column的時間相減，如果相減的結果是15 分鐘的話，就自動把pending for payment 的訂單的狀態轉為 expired


經過查詢過後，發現原來可以使用<a href="https://github.com/citusdata/pg_cron" 
           target="_blank">pg_cron</a>來做到。我的side project 是利用knex來做data migration和data seeding，所以我希望在我自己的電腦裡面可以試一下上面的邏輯。

我找到了<a href="https://medium.com/@luketong/how-to-install-and-use-pg-cron-locally-with-postgresql-7b830b0b2d03" 
           target="_blank">這一篇</a>非常有用的文章，所以以下的教學會主要用這文章的內容，再搭配自己的經驗來說明如何使用在自己的電腦內運行pg_cron。

## 做法

1. 如果你本來是透過Official Installation下載了PostgreSQL的話，需要先卸載，然後使用homebrew來下載PostgreSQL。
    
    ```bash
    brew install postgresql
    ```
    
2. 之後再使用homebrew來下載pg_cron。
    
    ```bash
    brew install pg_cron
    ```
    
3. 在Terminal裡面connect到你的PostgreSQL，然後重新建立database，並且連結這個database。記得要把{{ }}裡面的variable替換到你自己的設定
    
    ```bash
    psql -U {{user name}} -d postgres
    ```
    
    ```bash
    create database {{database name}};
    ```
    
    ```bash
    \c {{database name}};
    ```
    
4. 查看postgresql.conf的位置
    
    ```bash
    SHOW config_file;
    ```
    
5. Quit psql shell，然後copy第5步的path來打開postgresql.conf
    
    ```bash
    code /opt/homebrew/var/postgresql@14/postgresql.conf
    ```
    
6. 打開postgresql.conf之後，在裡面加這以下的2行的內容，並且儲存。記得要把{{ }}裡面的variable替換到你自己的設定
    
    ```bash
    shared_preload_libraries = 'pg_cron'
    cron.database_name = {{database name}}
    ```
    
7. 然後restart PostgreSQL
    
    ```bash
    brew services restart postgresql
    ```
    
8. 重新進入你的database，在你的database裡面建立一個pg_cron extension。記得要把{{ }}裡面的variable替換到你自己的設定
    
    ```bash
    psql -U {{user name}} -d postgres
    ```
    
    ```bash
    \c {{database name}};
    ```
    
    ```bash
    CREATE EXTENSION IF NOT EXISTS pg_cron;
    ```
    
9. 在你的project裡面建立一個新的migration file ，是用來建立booking這個table的
(studio 和 user 的table都已經是之前已建立好的）
    
    ```bash
    yarn knex migrate:make create-booking
    ```
    在create-booking的migration file裡面設定table的結構
    ```js
    /**
     * @param { import("knex").Knex } knex
     * @returns { Promise<void> }
     */
    
    const tableName = "booking";
    exports.up = async function (knex) {
      await knex.schema.createTable(tableName, (table) => {
        table.increments();
        table
          .text("reference_no")
          .notNullable()
          .unique()
          .defaultTo(knex.raw("nanoid('')"));
        table.integer("user_id").unsigned();
        table.foreign("user_id").references("users.id");
        table.integer("studio_id").unsigned();
        table.foreign("studio_id").references("studio.id");
        table.date("date").notNullable();
        table.time("start_time").notNullable();
        table.time("end_time").notNullable();
        table.text("whatsapp").notNullable();
        table.text("remarks");
        table
          .enu("status", [
            "confirm",
            "cancel",
            "pending for payment",
            "expired",
            "completed",
          ])
          .notNullable();
        table.boolean("is_complained").notNullable().defaultTo(false);
        table.timestamps(false, true);
      });
      await knex.raw(`
        CREATE TRIGGER update_timestamp
        BEFORE UPDATE
        ON ${tableName}
        FOR EACH ROW
        EXECUTE PROCEDURE update_timestamp();
      `);
    };
    
    /**
     * @param { import("knex").Knex } knex
     * @returns { Promise<void> }
     */
    exports.down = async function (knex) {
      await knex.schema.dropTable(tableName);
    };
    
    ```
    

1. 在你的project裡面建立另外一個新的migration file ，這次是用來建立改變訂單狀態的schedule
    
    ```bash
    yarn knex migrate:make create-booking-status-change-schedule
    ```
    在你新建立的create-booking-status-change-schedule migration file裡面設定你的schedule
    - 這裡我把這個用來建立改變訂單狀態的schedule命名為expire_pending_booking
        
        ```js
        /**
         * @param { import("knex").Knex } knex
         * @returns { Promise<void> }
         */
        exports.up = async function (knex) {
          await knex.raw(`
            SELECT cron.schedule(
          'expire_pending_booking',
          '*/1 * * * *',
          $$
            UPDATE booking
            SET status = 'expired'
            WHERE status = 'pending for payment'
              AND created_at < NOW() - INTERVAL '15 minutes';
          $$  
        )
          `);
        };
        
        /**
         * @param { import("knex").Knex } knex
         * @returns { Promise<void> }
         */
        exports.down = async function (knex) {
          await knex.raw(`
           SELECT cron.unschedule('expire_pending_booking');
          `);
        };
        
        ```
        
2. 利用下面的command就可以建立新的booking table和schedule
    
    ```bash
    yarn knex migrate:latest
    ```
    
3. 回到第8步的terminal，檢查你的databse裡面是否已經建立了這個schedule。如果你看到有一個jobname是你剛剛建立的，那麼就是正確。
    
    ```bash
    SELECT * FROM cron.job;
    ```
    
    ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/pg_cron.png?alt=media&token=bbecb0b9-f433-4a2a-9d3b-8d7bdae86df7)
    

1. 建立Booking table 的seed file
(user 和 studio的seed data已經是之前建立好了）
    
    ```bash
    yarn knex seed:make 06-create-booking
    ```
    
    ```bash
    /**
     * @param { import("knex").Knex } knex
     * @returns { Promise<void> }
     */
    
    function convertTo24Hour(timeString) {
      let date = new Date(`01/01/2022 ${timeString}`);
      let formattedTime = date.toLocaleTimeString("en-US", { hour12: false });
      return formattedTime;
    }
    exports.seed = async function (knex) {
      // Deletes ALL existing entries
      await knex("booking").del();
      await knex("booking").insert([
        {
          user_id: 2,
          studio_id: 1,
          date: new Date("2024-12-02"),
          start_time: convertTo24Hour("12:00"),
          end_time: convertTo24Hour("13:00"),
          status: "pending for payment",
          whatsapp: "98765432",
          remarks: "N/A",
          is_complained: false,
        },
      ]);
    };
    
    ```
    
2. 運行這個新的seed file
    
    ```bash
    yarn knex seed:run --specific=06-create-booking.ts
    ```
    

1. 等待15mins，訂單的狀態會從pending for payment改變為expired