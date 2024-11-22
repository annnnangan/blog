---
title: How to use the Floodlight Inspector chrome extension?
published: 2024-11-22
description: ''
image: 'https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/Floodlight-inspector%2Ffloodlight-inspector-1.png?alt=media&token=55e8b85c-6ae7-4f19-aef7-0a5a188c743f'
tags: [Chrome Extension]
category: 'Chrome Extension'
draft: false 
lang: 'en'
---


> [!NOTE]
> The **Floodlight Inspector** Chrome extension is a powerful tool designed to help you easily validate and troubleshoot your Floodlight setup in Google tag format . 

I have recently created a Chrome Extension to help with Floodlight audit. The tool has just uploaded for approval. You may use the <a href="https://annnnangan.github.io/demo-page-for-floodlight-inspector-chrome-extension/" target="_blank">demo page</a> to have a try on the Floodlight Inspector chrome extension later. The below guideline is based on this demo page too. 


## Key Features

- **Retain the necessary Floodlight pings to avoid confusion**
    - When checking Floodlight on the network tab, we usually see different ping being triggered with the same action. It causes confusion as we will be confused on which pings we should refer to. With this tool, we only filter out the Floodlight ping that you need to audit on.
    - For example,
        - the only ping that need to review is highlighted below but “fledge=1” and “register_conversion” are also appeared at the same time.
        
            ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/Floodlight-inspector%2Ffloodlight-inspector-2.png?alt=media&token=e9a893b1-72ad-4477-98d1-dfdef34d10aa)

        - Our tool only capture the ping that you need to review.
            ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/Floodlight-inspector%2Ffloodlight-inspector-3.png?alt=media&token=2247467e-698d-4267-adce-0398438785e4)

           
            
- **Breaks down Floodlight URLs**
    - It deconstructs the Floodlight request URLs to make it easier for you to identify and verify the data, ensuring that everything is set up correctly.
    ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/Floodlight-inspector%2Ffloodlight-inspector-4.png?alt=media&token=d21117fc-2927-4668-95fd-4888158103ad)
        
       
        
- **Custom Floodlight variable decoding**
    - This tool decodes the custom Floodlight variables so they’re understandable when your captured language isn't in English.
    ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/Floodlight-inspector%2Ffloodlight-inspector-5.png?alt=media&token=2d6ef341-acda-4916-9d4d-1201b64d1648)
   
    

## Step by Step Guidelines

1. Add this chrome extension tool to your Google Chrome 
2. On the website that you want to audit the Floodlight tag, right click > select inspector. 
    ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/Floodlight-inspector%2Ffloodlight-inspector-6.png?alt=media&token=65f48e51-1e74-43b0-9821-37e7f8e3def6)
        
        
3. Switch to the “Floodlight Inspector” tab
    ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/Floodlight-inspector%2Ffloodlight-inspector-7.png?alt=media&token=61bf2799-b3aa-456c-a537-f22dde91e8a6)
        
       
        
4. Trigger your Floodlight tag and the Floodlight tag will appear on the tool
5. Expand the accordion 
    ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/Floodlight-inspector%2Ffloodlight-inspector-8.png?alt=media&token=1f7b5b07-6a57-4009-815c-124e571db11e)
        
       
        
6. Review the Floodlight information in the Event Data accordion 
    ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/Floodlight-inspector%2Ffloodlight-inspector-11.png?alt=media&token=b7408a03-1473-41bf-ae46-3fde76f1f50b)
        
       
        
7. You could review the original request in the Full Request URL accordion
    ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/Floodlight-inspector%2Ffloodlight-inspector-9.png?alt=media&token=4028566c-446e-4ea9-9eb7-c98853e2f3c8)
        
       
        
8. You could reload the page and clear all the pings by clicking the “Reload and Clear” button
9. You could clear all the pings by clicking the “Clear Button”
10. The new triggered Floodlight tag will appear below the existing one
    ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/Floodlight-inspector%2Ffloodlight-inspector-10.png?alt=media&token=f97eab0f-597e-4750-8a3b-7ce08a24585d)
        
 