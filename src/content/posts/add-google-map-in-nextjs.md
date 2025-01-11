---
title: 在Next.js 裡面加入Google Map
published: 2025-01-11
description: ''
image: 'https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/Google-Map%2Fgoogle-map-4.png?alt=media&token=3f2cebb4-f127-4dc6-abf1-7d67eba47d58'
tags: [Google Map, Next.js, Side Project]
category: 'Next.js'
draft: false 
lang: 'zh-hk'
---

做這個場地租用的website的時候，希望在每一個場地的介紹頁面裡加入一個Google Map來展示場地的位置。這次的website是使用Next Js來製作，並且搭配這個 <a href="https://www.npmjs.com/package/@react-google-maps/api" target="_blank"> `@react-google-maps/api` </a> library來加入Google Map。

文章會把步驟分為3步: 

- Step 1: 申請Google Map API，並拿到API Key
- Step 2: 設定.env的檔案
- Step 3: 拿到地址的coordinates
- Step 4: 把coordinates pass到GoogleMap component

PS：這個文章是參考了以下2篇文章，非常謝謝他們，有興趣的話可以點進去看閱讀。
- <a href="https://theabhishek7.medium.com/google-map-implementation-in-react-js-with-custom-marker-cec5b0787364" target="_blank">Abhishek Mishra - Google Map Implementation in React.js with custom Marker</a>
- <a href="https://dev.to/adrianbailador/creating-an-interactive-map-with-the-google-maps-api-in-nextjs-54a4" target="_blank"> Adrián Bailador - Creating an Interactive Map with the Google Maps API in Next.js</a>

## Step 1: 申請Google Map API，並拿到API Key

1. 進入 <a href="https://console.cloud.google.com/" target="_blank">Google Cloud Console </a>
2. 建立一個新的project
3. 在side bar點擊 `APIs & Services > Library`.
4. 搜尋 "Maps JavaScript API" > 點擊 `Enable` 
5. 如果你之前還沒有設定billing的話，這個時候有機會需要你現在加入billing account。每一個月都有一定免費的額度可以用這個API，一旦超過就會從你這個billing account收錢
6. 複製 API key。
7. 你可以在 `Keys and credentials` 裡面找回你的API Key
    <img src="https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/Google-Map%2Fgoogle-map-step-6.png?alt=media&token=43ebf153-fffd-4fa1-a89f-92dbe7c60b4e" width="250"/>

## Step 2: 設定.env的檔案

1. 在你的Next Js 裡面建立一個 .env.local 的 檔案
2. 把剛剛複製的API Key放在裡面
    
    ```jsx
    NEXT_PUBLIC_MAPS_API_KEY=xxxxxxxxxxxxxxxx
    ```
    
   
    

## Step 3: 拿到地址的coordinates

要在Google Map裡面呈現到地址，需要先拿到這個地址的coordinates，所以我們先用Google Map的API來decode地址，取得coordinate。例如說，我們的地址是這個：香港銅鑼灣高士威道66號。

1. 我們先建立一個getLatLngForAddress的function，最後會拿到coordinates - `{ lat: 22.2799098, lng: 114.1896618 }`
2. 之後將這個coordinates Pass到我們GoogleMapView 的component，這個GoogleMapView 的component會放最後呈現的Google Map

```jsx
import GoogleMapView from "../GoogleMapView";
import Section from "../Section";

interface Props {
  address: string;
}

export interface Coordinates {
  lat: number;
  lng: number;
}

async function getLatLngForAddress(address: string, apiKey: string) {
  try {
    const response = await fetch(
      `https://maps.googleapis.com/maps/api/geocode/json?address=${encodeURIComponent(
        address
      )}&key=${apiKey}`
    );

    if (response.ok) {
      const data = await response.json();
      if (data.results && data.results.length > 0) {
        const location = data.results[0].geometry.location;
        return {
          lat: location.lat,
          lng: location.lng,
        };
      }
    }

    throw new Error("Unable to retrieve coordinates for the address.");
  } catch (error) {
    console.error("Error fetching coordinates:", error);
    throw error;
  }
}

const LocationSection = async () => {
  const address = "香港銅鑼灣高士威道66號"
  const coordinates: Coordinates = await getLatLngForAddress(
    address,
    process.env.NEXT_PUBLIC_MAPS_API_KEY!
  );

  return (
    <Section title={"場地位置"}>
      <GoogleMapView coordinates={coordinates} />
    </Section>
  );
};

export default LocationSection;

```

## Step 4: 把coordinates pass到GoogleMap component

1. 先安裝這個library - `@react-google-maps/api` 
    
    ```jsx
    npm install @react-google-maps/api
    ```
    
    或者
    
    ```jsx
    yarn add @react-google-maps/api
    ```
    
2. 在GoogleMapView這個component裡面，我們把這個coordinates pass到GoogleMap的center
3. 加入Marker就可以標示這個地區
4. zoom的話，你可以設定不同的數字，數字越大就會zoom得越近
    
    ```jsx
    "use client";
    import { GoogleMap, LoadScript, Marker } from "@react-google-maps/api";
    
    import { Coordinates } from "./section/LocationSection";
    
    interface Props {
      coordinates: Coordinates;
    }
    
    const containerStyle = {
      width: "100%",
      height: "400px",
    };
    
    const GoogleMapView = ({ coordinates }: Props) => {
      return (
        <LoadScript googleMapsApiKey={process.env.NEXT_PUBLIC_MAPS_API_KEY!}>
          <GoogleMap
            mapContainerStyle={containerStyle}
            center={coordinates}
            zoom={20}
          >
            <Marker position={coordinates} />
          </GoogleMap>
        </LoadScript>
      );
    };
    
    export default GoogleMapView;
    
    ```