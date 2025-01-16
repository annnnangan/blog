---
title: How to preview uploaded images in Next.js before submit
published: 2025-01-16
description: ''
image: 'https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/upload-multiple-images-in-nextjs%2Ffinal-image.png?alt=media&token=d80adec0-063c-47fa-a690-18ce7db2a471'
tags: [Next.js, React, Image Upload]
category: 'Next.js'
draft: false 
lang: 'en'
---

This article explains how could we upload images with HTML image input tag and preview immediately the images that you have just uploaded. In this case, you could let the user previews the images before fetching the API and upload the image to cloud storage. 

This could happen thanks to `URL.createObjectURL()` which creates an object URL representing the image so that we could put that URL in the HTML image src attribute. 

### What we will achieve today
<video controls>
  <source src="https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/upload-multiple-images-in-nextjs%2Ffinal.mp4?alt=media&token=59e483e2-777d-4af1-bdb0-cdb522956dda" type="video/mp4">
</video>

User could upload multiple images and preview immediately. Every time when they upload images, the new images will append behind the existing images. User could delete preview images.


### Step 1: Create components


- ProofUploadAndPreview.tsx
<img src="https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/upload-multiple-images-in-nextjs%2Fupload-images-1.png?alt=media&token=50f63622-b154-492d-8d58-cbb603a012a6" width="500"/>
    - This is just an layout and has no functionality yet. Later, we will pass the images URL from this component to the ImagePreview component using props.
        
        ```tsx
        import { Button } from "@/components/ui/button";
        
        const ProofUploadAndPreview = () => {
          return (
            <>
              <form className="border rounded-lg p-5 mb-5">
                <p className="font-bold mb-2">Upload payout proof</p>
                <input
                  type="file"
                  accept="image/png, image/jpeg, image/jpg"
                  multiple
                  className="text-sm"
                />
        
                <div className="mt-5 mb-20">
                  <p className="font-bold">Payout Proof Preview</p>
                  {/* <ImagePreview images={images} /> */}
                </div>
        
                <Button>Submit</Button>
              </form>
            </>
          );
        };
        
        export default ProofUploadAndPreview;
        ```
        
- ImagePreview.tsx
    - Images get from the props will then appear in a grid layout like below later.
        <img src="https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/upload-multiple-images-in-nextjs%2Fupload-images-step1-2.png?alt=media&token=7efc880a-7bf4-49c8-a24d-fb5800a6ab4d" width="500"/>
        
        
        ```tsx
        import React from "react";
        import Image from "next/image"; // Ensure you are importing the Image component from Next.js
        
        interface Props {
          images: string[];
        }
        
        const ImagePreview = ({ images }: Props) => {
          return (
            <div className="grid grid-cols-2 gap-5 mt-5">
              {images.map((image) => (
                <div key={image} className="relative aspect-[4/3] group">
                  <Image
                    src={image}
                    alt="studio image"
                    className="object-contain"
                    fill
                    sizes="w-auto"
                  />
                </div>
              ))}
            </div>
          );
        };
        
        export default ImagePreview;
        
        ```
        

### Step 2: Update image useState when file upload

**2.1 What element is returned when we upload images?**

- Let’s add a `onChange` property in the input tag and create a function called `handleFileSelect`hat will trigger when user select the images.
    
    ```tsx
    import { Button } from "@/components/ui/button";
    
    const ProofUploadAndPreview = () => {
    
      const handleFileSelect = (e: React.ChangeEvent<HTMLInputElement>) => {
        console.log(e);
      };
    
      return (
        <>
          <form className="border rounded-lg p-5 mb-5">
            <p className="font-bold mb-2">Upload payout proof</p>
            <input
              type="file"
              accept="image/png, image/jpeg, image/jpg"
              multiple
              className="text-sm"
              onChange={handleFileSelect}
            />
    
            <div>
              <p className="font-bold mt-5 mb-32">Payout Proof Preview</p>
              {/* images preview here */}
            </div>
    
            <Button>Submit</Button>
          </form>
        </>
      );
    };
    
    export default ProofUploadAndPreview;
    ```
    
- Let’s check what inside the `event`  when we upload images.
    <img src="https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/upload-multiple-images-in-nextjs%2Fupload-images-2.png?alt=media&token=c1452c36-71f3-43d3-9a5d-7a6040443182" width="500"/>
    
- Expand `target` key and scroll down. You will find `files` and the images you have just uploaded will appear here.
    <img src="https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/upload-multiple-images-in-nextjs%2Fupload-images-3.png?alt=media&token=c7538420-4e9b-4d61-b8df-22dac3408d65" width="500"/>
    
    

**2.2 Try to put the one of the file in `URL.createObjectURL()` and return an object URL**

- By typing `e.target.files[0]`, we could get the first file and then put it in `URL.createObjectURL(e.target.files[0])` . When we console log, we could see an URL
    
    ```tsx
      const handleFileSelect = (e: React.ChangeEvent<HTMLInputElement>) => {
        if (e.target.files) {
          console.log(URL.createObjectURL(e.target.files[0]));
        }
      };
    ```
     <img src="https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/upload-multiple-images-in-nextjs%2Fupload-images-4.png?alt=media&token=3e87020a-f9cb-4343-b9b8-e0291ec7a275" width="500"/>
    
   
**2.3 Generate all the image URLs and put in the image useState**

- In the above step, we are able to generate the object URL with one of the images. In this step, we try to put all the image URLs in an array and then pass it to the image preview component to map each image out.
- In this case, we might first think of using “map” or “forEach” to convert the image to URL and then put it in an array one by one. However, since the `e.target.files` is on type “FileList” which these two javascript method cannot be used.
    <img src=" https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/upload-multiple-images-in-nextjs%2Fupload-images-5.png?alt=media&token=b84d1e91-fbc8-4eab-b796-d8dd1a5bccad" width="500"/>
    
    
- Therefore, we need to convert the `e.target.files` to an array first. Now, when could use “map” to create a new array which contains all the image URL
  <img src="https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/upload-multiple-images-in-nextjs%2Fupload-images-6.png?alt=media&token=c9e823a5-c11a-4ea4-9c42-d843cf8f3489" width="500"/>
    
    
    ```jsx
     const handleFileSelect = (e: React.ChangeEvent<HTMLInputElement>) => {
        if (e.target.files) {
          //convert FileList to array
          const files = Array.from(e.target.files);
          console.log(files.map((file) => URL.createObjectURL(file)));
        }
      };
    ```
    
   
    
- Now, we have the image URLs, to share with the image preview component, we could create a useState - `const [images, setImages] = useState<string[]>([]);`
- Every time, when user select the images, we will append the images after the existing one so we use spread operator to concatenate the existing array and new array `setImages([...images, ...newImageURLs]);`
- After that, we could uncomment the ImagePreview component. The image should be able to preview immediately now.
    <img src="https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/upload-multiple-images-in-nextjs%2Fupload-images-8.png?alt=media&token=daf808e3-803d-468d-a9f3-97c849730489" width="500"/>

    ```tsx
    const ProofUploadAndPreview = () => {
      const [images, setImages] = useState<string[]>([]);
      const handleFileSelect = (e: React.ChangeEvent<HTMLInputElement>) => {
        if (e.target.files) {
          //convert FileList to array
          const files = Array.from(e.target.files);
          const newImageURLs = files.map((file) => URL.createObjectURL(file));
          setImages([...images, ...newImageURLs]);
          //reset input after upload
          e.target.value = "";
        }
      };
    
      return (
        <>
           ...
    
            <div className="mt-5 mb-20">
              <p className="font-bold">Payout Proof Preview</p>
              <ImagePreview images={images} />
            </div>
    
            <Button>Submit</Button>
          </form>
        </>
      );
    };
    
    export default ProofUploadAndPreview;
    ```
    

### Step 3: Delete preview image

Now we are already able to preview the images we have uploaded. The next step is to be able to delete each of the preview image. 

**3.1 Create delete layout design** 

- When we hover over the image, there should be a trash icon appear in the middle.
    <img src=" https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/upload-multiple-images-in-nextjs%2Fupload-images-7.png?alt=media&token=d6dfefc5-047a-4af4-87bb-e321e50a172a" width="500"/>
   
    
    ```tsx
    import React from "react";
    import Image from "next/image"; // Ensure you are importing the Image component from Next.js
    import { Trash2 } from "lucide-react";
    
    interface Props {
      images: string[];
    }
    
    const ImagePreview = ({ images }: Props) => {
      return (
        <div className="grid grid-cols-2 gap-5 mt-5">
          {images.map((image) => (
            <div key={image} className="relative aspect-[4/3] group">
              <Image
                src={image}
                alt="studio image"
                className="object-contain transition-all duration-500 group-hover:brightness-50"
                fill
                sizes="w-auto"
              />
              <div className="absolute inset-0 flex items-center justify-center">
                <button
                  type="button"
                  className="opacity-0 group-hover:opacity-100 transition-opacity duration-300"
                  aria-label="Delete Image"
                >
                  <div className="rounded-full p-2 bg-brand-600 hover:bg-brand-700 transition-all duration-500">
                    <Trash2 className="w-6 h-6 text-white" />
                  </div>
                </button>
              </div>
            </div>
          ))}
        </div>
      );
    };
    
    export default ImagePreview;
    ```
    

**3.2 Create delete functionality**

When user click the trash icon, the preview image should be removed from the screen. 

- Create an onClick property
- The image array that we use in the ImagePreview component is originally from the parent component, so in our ImagePreview component, what we should do is to let the parent component to know (a) when they should remove the image and update the image useState and (b) which image should be removed.
- To achieve this, we should create a handleImageRemove function in the parent components and pass this function to the trash icon onClick property.
- Therefore, when suer click the trash icon, the parent component will know they should trigger the handleImageRemove function and perform the action we need.

In ImagePreview.tsx (i.e. child component):

```tsx
interface Props {
  images: string[];
  onImageRemove: (imageToDelete: string) => void;
}

const ImagePreview = ({ images, onImageRemove }: Props) => {
        return (
            .....
        
             <Trash2
          className="w-6 h-6 text-white"
          onClick={() => onImageRemove(image)}
      />
)
}
export default ImagePreview;
```

In ProofUploadAndPreview.tsx (i.e. parent component): 

- `URL.revokeObjectURL()` is also added to release the unwanted object URL from the javascript storage

```tsx
  const ProofUploadAndPreview = () => {
          const [images, setImages] = useState<string[]>([]);
          
      const handleImageRemove = (imageToDelete: string) => {
        const updatedImages = images.filter((image) => !(image === imageToDelete));
        URL.revokeObjectURL(imageToDelete);
        setImages(updatedImages);
     };
     
     return(
        ....
        
        <div className="mt-5 mb-20">
          <p className="font-bold">Payout Proof Preview</p>
          <ImagePreview images={images} onImageRemove={handleImageRemove} />
        </div>
     )
  
  }
```

### Final Script

ProofUploadAndPreview.tsx

```tsx
import { Button } from "@/components/ui/button";
import { FormEvent, useState } from "react";
import ImagePreview from "./ImagePreview";

const ProofUploadAndPreview = () => {
  const [images, setImages] = useState<string[]>([]);

  const handleFileSelect = (e: React.ChangeEvent<HTMLInputElement>) => {
    if (e.target.files) {
      //convert FileList to array
      const files = Array.from(e.target.files);
      const newImageURLs = files.map((file) => URL.createObjectURL(file));
      setImages([...images, ...newImageURLs]);
      //reset input after upload
      e.target.value = "";
    }
  };

  const handleImageRemove = (imageToDelete: string) => {
    const updatedImages = images.filter((image) => !(image === imageToDelete));
    URL.revokeObjectURL(imageToDelete);
    setImages(updatedImages);
  };

  const handleSubmit = (e: FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    console.log(images);
  };

  return (
    <>
      <form className="border rounded-lg p-5 mb-5" onSubmit={handleSubmit}>
        <p className="font-bold mb-2">Upload payout proof</p>
        <input
          type="file"
          accept="image/png, image/jpeg, image/jpg"
          multiple
          className="text-sm"
          onChange={handleFileSelect}
        />

        <div className="mt-5 mb-20">
          <p className="font-bold">Payout Proof Preview</p>
          <ImagePreview images={images} onImageRemove={handleImageRemove} />
        </div>

        <Button>Submit</Button>
      </form>
    </>
  );
};

export default ProofUploadAndPreview;

```

ImagePreview.tsx

```tsx
import React from "react";
import Image from "next/image"; // Ensure you are importing the Image component from Next.js
import { Trash2 } from "lucide-react";

interface Props {
  images: string[];
  onImageRemove: (imageToDelete: string) => void;
}

const ImagePreview = ({ images, onImageRemove }: Props) => {
  return (
    <div className="grid grid-cols-2 gap-5 mt-5">
      {images.map((image) => (
        <div key={image} className="relative aspect-[4/3] group">
          <Image
            src={image}
            alt="studio image"
            className="object-contain transition-all duration-500 group-hover:brightness-50"
            fill
            sizes="w-auto"
          />
          <div className="absolute inset-0 flex items-center justify-center">
            <button
              type="button"
              className="opacity-0 group-hover:opacity-100 transition-opacity duration-300"
              aria-label="Delete Image"
            >
              <div className="rounded-full p-2 bg-brand-600 hover:bg-brand-700 transition-all duration-500">
                <Trash2
                  className="w-6 h-6 text-white"
                  onClick={() => onImageRemove(image)}
                />
              </div>
            </button>
          </div>
        </div>
      ))}
    </div>
  );
};

export default ImagePreview;

```