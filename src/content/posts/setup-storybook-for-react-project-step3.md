---
title: Step 3 Create a story for a component - Setup Storybook to Better Organize your Components for React Project
published: 2025-03-14
description: ''
image: 'https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/storybook%2Fstorybook-logo.jpeg?alt=media&token=19bcacd6-87c1-4e4f-9289-656261d3b61f'
tags: [Storybook, React]
category: 'Storybook'
draft: false 
lang: 'en'
---

<a href="/blog/posts/setup-storybook-for-react-project/"> < Back to see the background and other steps </a>

# Step 3 - Create a story for a component

We will create a story for the below component. It is a show more button, when the text exceed the maximum character, the exceeded character will be trimmed and a ”更多” (i.e. Show More” text will appear. 

This is the script of the component. In this case, I would like to demonstrate how does it look like when the text exceed the maximum characters as well as when the text doesn’t exceed the limit.

<video src="https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/storybook%2Fdemo%20-%20show%20more%20btn.mp4?alt=media&token=f7732b20-f768-48e2-83c3-098876282660" width="1080" controls></video>

<details>
  <summary>Component JSX</summary>
  
    ```jsx

    import { useState } from "react";

    import PropTypes from "prop-types";

    export default function ShowMoreBtn({ text, initialShow = false, maxCharacter = 250 }) {
    const [isShow, setIsShow] = useState(initialShow);

    const handleClick = () => {
        setIsShow((prevIsShow) => !prevIsShow);
    };

    const displayedText = isShow ? text : text.length > maxCharacter ? text.slice(0, maxCharacter) + "..." : text;

    return (
        <>
        {displayedText
            .replace(/\\n/g, "\n")
            .split("\n")
            .map((line, index) => (
            <p key={index} className="tab-details">
                {line}
                <br></br>
            </p>
            ))}
        {text.length > maxCharacter && (
            <div className="d-flex align-items-center py-3" role="button" onClick={handleClick}>
            <p className="text-brand-03">{isShow ? "更少" : "更多"}</p>
            <span className="material-symbols-outlined icon-fill text-brand-03 align-middle">{isShow ? "keyboard_arrow_up" : "expand_more"}</span>
            </div>
        )}
        </>
    );
    }
    ShowMoreBtn.propTypes = {
    text: PropTypes.string,
    initialShow: PropTypes.bool,
    maxCharacter: PropTypes.number,
    };

    ```
</details>

1. Create a `ShowMoreBtn.stories.js` inside the `stories` folder
    
    ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/storybook%2Fstep3%20-%20image.png?alt=media&token=9c35d1d0-7e18-4ae4-9dba-5c9b4b894637)
    
2. Import your component and give the story a title
3. Define your case by providing different arguments. “text” and “maxCharacter” are the props of the original component.
    
    ```jsx
    import ShowMoreBtn from "@/components/common/ShowMoreButton";
    
    //Import your component and give the story a title
    export default {
      title: "Show More Button",
      component: ShowMoreBtn,
    };
    
    //Define different case below
    export const LongText = {
      args: {
        text: "Lorem ipsum dolor sit amet consectetur adipisicing elit. Culpa, perspiciatis, dolore corrupti dolores obcaecati officia molestiae atque iusto animi doloremque saepe sit eveniet hic ex amet consectetur iste, ipsam minus.",
        maxCharacter: 20,
      },
    };
    
    export const ShortText = {
      args: {
        text: "Lorem  dolorminus.",
        maxCharacter: 20,
      },
    };
    
    ```
    
4. Open the storybook by typing `npm run storybook`. You are able to see your case in your storybook. 
    
    ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/storybook%2Fstep3%20-%20image%201.png?alt=media&token=94487583-ca0d-413d-a2ec-1760ad8c5b13)
    
5. You could interact with your case inside the canvas. You could also adjust the control to test with different situation. For example, I could set the maxCharacter to 100 directly in the control UI.
    
    ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/storybook%2Fstep3%20-%20image%202.png?alt=media&token=48ca0d03-3eff-4fc8-b881-70df2b341b47)
    
    - Tips: Setting the propTypes to your component could help storybook to understand the type and provide with the correct control to you. For example, in my original component, I have set `initialShow` as boolean in propTypes so it will show false or true in the storybook control. If you do not provide, it will become set object. It is useful if you have already defined specific word for the props e.g. different sizes for button.
        
        ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/storybook%2Fstep3%20-%20image%203.png?alt=media&token=848059fb-ca21-41ca-a4ff-f3b4c0ed71a7)

<a href="/blog/posts/setup-storybook-for-react-project-step4/"> Go to Step 4 </a>