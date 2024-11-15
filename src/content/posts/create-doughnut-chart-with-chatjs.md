---
title: 如何使用Chart.js來建立一個doughnut chart
published: 2024-10-26
description: '如何使用Chart.js來建立一個doughnut chart'
image: 'https://media.licdn.com/dms/image/v2/D5612AQGitElggL7p1g/article-cover_image-shrink_600_2000/article-cover_image-shrink_600_2000/0/1705583811569?e=2147483647&v=beta&t=fTRFU9o4II4zsKXEcRrK7d6odrKhIdgcdBxERf24CBU'
tags: [Chart.js]
category: 'Chart.js'
draft: false 
lang: 'zh-HK'
---

### 前言

最近和其他同學一齊完成了一個記帳程式的前端畫面，剛好設計稿裡面有使用大量的圖表，所以使用了Chart.js 這個套件。接下來的幾篇article的內容都會是關於如何使用這個套件來建立doughnut chart，並且可以customize圖表的樣式和內容。

最後的圖片樣式：
<img src="https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/tooltip.png?alt=media&token=37feaec8-9587-4cac-8acb-a7bb170b8b63">

:::tip
今天的article會先敘述如何利用Chart.js 的套件建立一個花費總覽的doughnut chart。
📌 [Codepen Demo](https://codepen.io/annnna-ngan/pen/MWNrvBg?editors=1010) 
:::

今天完成後你會得到以下的結果
<img src="https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/chartjs-1-demo.png?alt=media&token=8e3225d4-3e6c-4515-b0a8-028d86a44768">


### 步驟

1. 利用CDN把Chart.js 加入到你的HTML裡面。把下面的script 放在body之前。

```html
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
```

2. 在HTML裡面create 一個canvas，然後給它一個ID，之後你的pie chart會在這個canvas裡面出現。

```html
  <div class="container">
      <div class="chart-wrapper">
        <canvas id="total-expense-chart" ></canvas>
      </div>
 </div>
```

3. 在JS檔案裡面，我們用 getElementById 來拿到我們的canvas element。

```jsx
const totalExpenseChart = document.getElementById("total-expense-chart");
```

4. Create 一個 new Chart。
    - 第一個parameter是第三步拿的canvas element，用來表明這個chart是要在哪裡出現。
    - 第二個parameter是一個configuration object。到時候我們有什麼需要修改的樣式、pass data什麼的都是在裡面設定。現在我們可以先設定這個chart是什麼type，因為我們是希望create一個doughnut chart，所以這裡我們會寫doughnut。

```jsx
new Chart(totalExpenseChart, {
    type: "doughnut",
});
```

5. 把data 放到第四步create的chart裡面，pie chart就會出現。
    - labels: 定義pie chart的內容
    - datasets裡面的data，就是對應上面labels順序會出現的數字。例如飲食就會是$2500，購物就好似$500
    - datasets裡面的backgroundColor，可以讓我們設定每一個內容的顏色。例如飲食那一個區塊就會是#FFE4C1這個顏色。

```jsx
  new Chart(totalExpenseChart, {
    type: "doughnut",
    data: {
      labels: ["飲食", "購物", "交通", "娛樂", "學習", "生活", "醫療"],
      datasets: [
        {
          data: [2500, 500, 2000, 800, 2200, 6400, 150],
          backgroundColor: ["#FFE4C1", "#C7F1FF", "#CCDCFF", "#DAB5FF", "#B0DCC7", "#FEC9A7", "#FFCECF"],
        },
      ],
    },
  });
```



6. 我們還可以先簡單的把這個chart的樣式進行簡單的修改。我們先加一個options的object
    
    ```jsx
    new Chart(totalExpenseChart, {
        type: "doughnut",
        data: {
    			   ....
        },
        options: {
         ....
        }
      });
    ```
    
    6.1  把每一個區塊的粗度減少
    
    - 我們可以利用增加中間圓圈的大小來顯得每一個區塊的粗度減少。我們可以加一個cutout的key來調整。%越小，中間的洞就會越小。
    - 我們可以看到我們apply了70%之後，比較之前中間的洞大了，每一個區塊的粗度減少了。
    
    ```css
    new Chart(totalExpenseChart, {
        type: "doughnut",
        data: {
    			   ....
        },
        options: {
          cutout: "70%",
        }
      });
    ```
    
  
    
    6.2  設計圖裡面的border會有一點弧度，所以我們可以利用borderRadius來調整
    
    - 我會利用aspectRatio來把chart縮小，並且可以按比例，按照screen的大小縮小
    
    ```jsx
    new Chart(totalExpenseChart, {
        type: "doughnut",
        data: {
    			   ....
        },
        options: {
          borderRadius: 4,
          cutout: "70%",
        }
      });
    ```
    
7. 用CSS來把這個pie chart定位到中間

```css
@import url('https://fonts.googleapis.com/css2?family=Roboto:ital,wght@0,100;0,300;0,400;0,500;0,700;0,900;1,100;1,300;1,400;1,500;1,700;1,900&display=swap');

body{
  margin:0;
  font-family: "Roboto", sans-serif;
}

.container{
  padding:0px 20px;
  background: #E8E8E8;
  max-width: 500px;
  margin: auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}
```

下一次，我們會去講解怎麼可以修改tooltip。