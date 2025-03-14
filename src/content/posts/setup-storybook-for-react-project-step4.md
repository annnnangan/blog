---
title: Step 4 Create a story for a component - Setup Storybook to Better Organize your Components for React Project
published: 2025-03-14
description: ''
image: 'https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/storybook%2Fstorybook-logo.jpeg?alt=media&token=19bcacd6-87c1-4e4f-9289-656261d3b61f'
tags: [Storybook, React]
category: 'Storybook'
draft: false 
lang: 'en'
---

<a href="/blog/posts/setup-storybook-for-react-project/"> < Back to see the background and other steps </a>

# Step 4 - Deploy the storybook to chromatic

Now you have your story and we could deploy it to chromatic. 

1. Export storybook as static app by typing `npm run build-storybook` in the terminal. You will see a new folder called `storybook-static` being generated.
    
    ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/storybook%2Fstep4%2Fstep4%20-%20image.png?alt=media&token=48918f8b-6eed-41cc-950f-5070effd57f5)
    
2. Add `storybook-static` in the `gitignore` file as we do not want to push this storybook-static on the github same as the `node_modules` folder 
    
    ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/storybook%2Fstep4%2Fstep4%20-%20image%201.png?alt=media&token=ae1aa187-11c5-456f-8f84-ade804216b58)
    
3. Login Chromatic with your github account and choose the github repo 
    
    ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/storybook%2Fstep4%2Fstep4%20-%20image%202.png?alt=media&token=ad96c6aa-5451-408e-b613-1aeac316e53f)
    
    ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/storybook%2Fstep4%2Fstep4%20-%20image%203.png?alt=media&token=cb9e972b-c744-4640-ad6b-4523e6d0ce48)
    
4. Add Chromatic to your project by typing 
`npm install --save-dev chromatic` in your terminal
5. Follow the instruction and type in the command to the terminal
    
    ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/storybook%2Fstep4%2Fstep4%20-%20image%204.png?alt=media&token=2a06cc5d-9ab7-411b-bc48-051adffc30d3)
    
    ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/storybook%2Fstep4%2Fstep4%20-%20image%205.png?alt=media&token=a11dd0af-f388-4aed-88ea-1655678fd436)
    
6. Now, you will have a link that enter into your storybook
https://67d2f8c8c2b97f80915bc933-objhatgkev.chromatic.com/?path=/story/section-fallback--section-fallback-with-icon

### **Continuous deployment with Chromatic with github action**

We could set up a github action to help us to publish the change automatically when we have any changed on the `stories` folder 

1. Go to your github repo > settings > secrets and variables > actions > New repository secret
    
    ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/storybook%2Fstep4%2Fstep4%20-%20image%206.png?alt=media&token=0e66eaec-94b4-47c5-9d81-f91be2685b00)
    
2. Put `CHROMATIC_PROJECT_TOKEN` as the name and the chromatic project token inside the secret
    
    ![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/storybook%2Fstep4%2Fstep4%20-%20image%207.png?alt=media&token=d38bb373-b716-4b14-b4f2-dbfae6ab5926)
    
3. Create a new folder  `.github/workflows/chromatic.yml`
4. Copy the below script to the `chromatic.yml` file 
    
    ```jsx
    # Workflow name
    name: "Chromatic Deployment"
    
    # Event for the workflow
    on:
      push:
        paths:
          - "src/stories/**"
    
    # List of jobs
    jobs:
      chromatic:
        name: "Run Chromatic"
        runs-on: ubuntu-latest
        # Job steps
        steps:
          - uses: actions/checkout@v4
            with:
              fetch-depth: 0
          - run: yarn
            #👇 Adds Chromatic as a step in the workflow
          - uses: chromaui/action@latest
            # Options required for Chromatic's GitHub Action
            with:
              #👇 Chromatic projectToken, see https://storybook.js.org/tutorials/intro-to-storybook/react/en/deploy/ to obtain it
              projectToken: ${{ secrets.CHROMATIC_PROJECT_TOKEN }}
              token: ${{ secrets.GITHUB_TOKEN }}
    ```
    
5. Push the changes to github 
6. When there is change in the stories folder, it will automatically create a build inside chromatic
