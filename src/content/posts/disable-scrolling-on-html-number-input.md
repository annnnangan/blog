---
title: HTML number input 一定要加的function - 如何防止scroll的時候修改number input的value
published: 2024-12-14
description: ''
image: ''
tags: [Next.js, React, Form]
category: 'Next.js'
draft: false 
lang: 'zh-HK'
---

## 背景

當我送出價格的數值的時候，發現數值不是我一開始輸入的，以為是自己哪裡的設定做錯了，後來發現原來數字的input field，如果你仍然onFocus在該input field，當你滑鼠scroll的時候，這個input field也會更改數字。

我覺得這個default behavior容易產生錯誤，導致最後的用戶設定錯誤，所以我希望找一個方法可以disable這個behavior。

我的project是使用Next.js 和typescript。

下面這個影片，第一個field你可以看到當我focus了這個field，然後往上往下滑的時候，數字就被改掉了。第二個的field，因為我加了下面的function，所以這個行為就被disable了。
<video src="https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/disable-scrolling-on-html-number-input%2Fdisable-scrolling-on-html-number-input-video.mp4?alt=media&token=607f3781-9c82-432a-94e7-0d9f6fee06c1" width="1080" controls></video>

## 如何防止scroll的時候修改到number input的value

在你的component裡面加入此function

```tsx
const numberInputOnWheelPreventChange = (e: WheelEvent<HTMLInputElement>) => {
    const target = e.target as HTMLInputElement; // Type assertion
    // Prevent the input value change
    target.blur();

    // Prevent the page/container scrolling
    e.stopPropagation();

    setTimeout(() => {
      target.focus();
    }, 0);
  };
```

然後在你的Input加入onWheel={numberInputOnWheelPreventChange}

```tsx
<Input
   type="number"
   id="nonPeakHourPrice"
   placeholder="請填寫非繁忙時段價格。"
   className="text-sm"
   {...register("nonPeakHourPrice")}
   onWheel={numberInputOnWheelPreventChange}
  />
```

修改後就可以disable這個behavior