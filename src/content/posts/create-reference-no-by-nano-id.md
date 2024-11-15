---
title: 在PostgresSQL利用Nano ID來建立Booking Reference No
published: 2024-11-14
description: ''
image: 'https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/image.png?alt=media&token=7eadcdf0-2680-45fb-88dc-f8f21c7e2cb1'
tags: [knex, PostgresSQL, Nano ID]
category: 'Database'
draft: false 
lang: 'zh-HK'
---
## 背景

最近在寫一個side project，是一個預約的系統。當訂單建立之後，想要一個短的比較容易看的Booking Reference No，讓用戶可以參考，並且有問題的時候，可以拿著這個no來找support解決訂單的問題。

## Postgres Sequence vs UUID vs Nano ID

### Postgres Sequence

雖然是可以在PostgreSQL建立sequence來建立有pattern的reference no，但如果這個reference no會在其他的地方會用到，用這種有pattern的可能會造成一些危險，所以我想要的Reference No是用戶不能從中找到pattern。

### UUID

本來也有想過使用UUID(e.g. `a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11`)，因為本來postgres裡面就有support，就不用再去找其他的方法怎麼麻煩。但是UUID太長了，覺得不美觀，所以最後就沒有使用。

### Nano ID

機緣巧合下，找到了UUID的替代品，Nano ID。他就可以替代UUID，弄出來的ID非常短，而且是隨機所以就想使用Nano ID來產出Booking Reference No。除了可以用來產出Booking Reference No外，也可以用來建立file 的名字

## 如何在PostgreSQL裡面使用Nano ID來產出Booking Reference No

因為在我的side project裡面我是使用knex來做migration和data seed，所以以下的過程會是使用了knex。

1. 建立一個nano id trigger的migration file
    
    ```bash
    yarn knex migrate:make create-nano-id-trigger
    ```
    
    - migration file的內文是直接複製了<a href="https://github.com/elitan/postgres-nanoid/blob/main/nanoid.sql" target="_blank"> github elitan的sql</a>
    
    ```jsx
    /**
     * @param { import("knex").Knex } knex
     * @returns { Promise<void> }
     */
    exports.up = function (knex) {
      return knex.raw(`
         //把github上的sql 貼到中間這裡
      `);
    };
    
    /**
     * @param { import("knex").Knex } knex
     * @returns { Promise<void> }
     */
    exports.down = function (knex) {
      return knex.raw(`
           DROP FUNCTION IF EXISTS nanoid(int, text, float);
    `);
    };
    ```
    
2. 建立一個 Booking table的migration file
(users和studio的table之前已經建立好了）
    
    ```bash
    yarn knex migrate:make create-booking
    ```
    
    - 在這個create-booking的migration file裡面可以用`knex.raw("nanoid('')")`來產出 nano id，如果想要產出的ID有特定的prefix可以在nanoid裡面加，例如`knex.raw("nanoid(’hk_')")`，所有產出的nano id前面都會有hk_，例如`hk_AuWin2Ff2ZuyY1kNxCAiz`
    
    ```jsx
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
    

1. 建立Trigger和Booking Table
    
    ```jsx
    yarn knex migrate:latest
    ```
    

1. 建立booking 的seed file
    
    ```jsx
    yarn knex seed:make 06-create-booking
    ```
    
    - 在create-booking的seed file裡面我們建立一些dummy的data
    (users和studio的dummy data 之前已經建立好了）
        
        ```jsx
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
              start_time: convertTo24Hour("10:00"),
              end_time: convertTo24Hour("11:00"),
              status: "confirm",
              whatsapp: "98765432",
              remarks: "NA",
              is_complained: false,
            },
            {
              user_id: 2,
              studio_id: 1,
              date: new Date("2024-12-02"),
              start_time: convertTo24Hour("11:00"),
              end_time: convertTo24Hour("12:00"),
              status: "confirm",
              whatsapp: "98765432",
              remarks: "NA",
              is_complained: false,
            },
            {
              user_id: 2,
              studio_id: 1,
              date: new Date("2024-12-02"),
              start_time: convertTo24Hour("12:00"),
              end_time: convertTo24Hour("13:00"),
              status: "pending for payment",
              whatsapp: "98765432",
              remarks: "NA",
              is_complained: false,
            },
          ]);
        };
        
        ```
        
    1. 建立Booking的seed data
        
        ```jsx
        yarn knex seed:run --specific=06-create-booking.js
        ```
        
    2. 成功使用Nano ID來建立reference no
        
        ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/image.png?alt=media&token=7eadcdf0-2680-45fb-88dc-f8f21c7e2cb1)