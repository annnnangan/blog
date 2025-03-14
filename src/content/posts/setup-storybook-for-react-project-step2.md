---
title: Step 2 Import Existing Styles to Storybook - Setup Storybook to Better Organize your Components for React Project
published: 2025-03-14
description: ''
image: 'https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/storybook%2Fstorybook-logo.jpeg?alt=media&token=19bcacd6-87c1-4e4f-9289-656261d3b61f'
tags: [Storybook, React]
category: 'Storybook'
draft: false 
lang: 'en'
---

<a href="/blog/posts/setup-storybook-for-react-project/"> < Back to see the background and other steps </a>

# Step 2 - Import Existing Styles to Storybook

## 1. Add material icons 
- Since we are using material icons for displaying icons on the react project, we need to also import it to storybook.
- To import material icons, create a new file `preview-head.html` inside the `.storybook` folder and add the material icons stylesheet link inside
    
    ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/storybook%2Fstep2%20-%20image.png?alt=media&token=b21db9a2-466a-4379-a27c-d644003ec78b)
    
    ```html
    <link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@20..48,100..700,0..1,-50..200" />
    
    ```
    

## 2. Import existing styles file
- In our react project, we used an `all.scss` to import all the scss file we had so we could just include this file in the `preview.js` file inside the `.storybook` folder
    
    ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/storybook%2Fstep2%20-%20image%201.png?alt=media&token=c2d3d2e7-5e35-4250-a94e-ea02e8734047)
    
    ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/storybook%2Fstep2%20-%20image%202.png?alt=media&token=036dd03f-5230-4e8d-9ca6-12e11502b4b6)
    
- In order for the <a href="https://storybook.js.org/recipes/sass" target="_blank">scss to work with storybook</a>, we need to install a new package, so please type the below command in terminal.
    
    ```bash
    npx storybook@latest add @storybook/addon-styling-webpack
    ```

<a href="/blog/posts/setup-storybook-for-react-project-step3/"> Go to Step 3 </a>