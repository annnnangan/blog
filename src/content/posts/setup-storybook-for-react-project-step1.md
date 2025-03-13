---
title: Step 1 Install Storybook - Setup Storybook to Better Organize your Components for React Project
published: 2025-03-14
description: ''
image: 'https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/storybook%2Fstorybook-logo.jpeg?alt=media&token=19bcacd6-87c1-4e4f-9289-656261d3b61f'
tags: [Storybook, React]
category: 'Storybook'
draft: false 
lang: 'en'
---

<a href="/blog/posts/setup-storybook-for-react-project/"> < Back to see the background and other steps </a>

# Step 1 - Install Storybook

1. In your **existing** react project, type in below command in your terminal 
    
    ```bash
    npm create storybook@latest
    ```
    
    - You do not need to create a new repo or new react project. If you have an existing react project, please install it in the existing project.
2. After installing the storybook, you will see two new folders, `.storybook` and `stories` , are being added to your project
    - `.storybook` is for configuration (e.g. import css file etc)
    - `stories` is for displaying your components
        
       ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/storybook%2Fstep1%20-%20image.png?alt=media&token=34d5bd4f-2ae3-4248-bd98-bd8a13ed8a3e)
        
3. After installing the storybook, a localhost:6006 page will be opened and on the screen you will see the sample of storybook. You are able to see these because it has added some sample stories inside the `stories` folder 
    
      ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/storybook%2Fstep1%20-%20image%201.png?alt=media&token=75c48d50-af80-4f14-825d-04a54613c2cf)

    Those files end with .stories.js are used to display on the Storybook screen. The remaining files are just used to create a normal react component.
    
    ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/storybook%2Fstep1%20-%20image%202.png?alt=media&token=5fdbc423-2979-4f16-9e6e-c6e4369ed7ca)
    

4. Remove all the file and folder inside the `stories` folder as we want to create stories for our own components.