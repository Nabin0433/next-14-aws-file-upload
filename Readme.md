# AWS S3 Image Upload and Delete Utility

This utility provides functions for uploading and deleting images to and from an Amazon S3 bucket using the AWS SDK for JavaScript.

## Setup

1. Install dependencies:

   ```bash
   npm install @aws-sdk/client-s3

## Set up your AWS credentials and bucket information in your environment variables:
    export BUCKET=your-bucket-name
    export AWS_ACCESS_KEY_ID=your-access-key-id
    export AWS_SECRET_ACCESS_KEY=your-secret-access-key


# AWS S3 Image Upload and Delete Utility

`imageUpload.ts` is a utility file that provides functions for uploading and deleting images to and from an Amazon S3 bucket using the AWS SDK for JavaScript.

## Table of Contents

- [Setup](#setup)
- [Usage](#usage)
  - [Upload Image](#upload-image)
  - [Delete Image](#delete-image)
- [Contributing](#contributing)
- [License](#license)

## `imageUpload.ts`

```typescript
import { S3Client, PutObjectCommand, DeleteObjectCommand } from "@aws-sdk/client-s3";

const Bucket = process.env.BUCKET;

const s3 = new S3Client({
    region: 'us-east-2',
    credentials: {
        accessKeyId: process.env.AWS_ACCES_KEY_ID as string,
        secretAccessKey: process.env.AWS_SECRET_ACCESS_KEY as string,
    },
});

const uploadImgToAWS = async (file: any, folder: string) => {
    try {
        if (!file || file.size === 0) {
            throw new Error('Invalid file');
        }
        const Body = (await file.arrayBuffer()) as Buffer;
        const ACL = 'public-read';
        const key = `${folder}${Date.now()}${file.name}`;
        const ContentType = file.type || 'application/octet-stream';
        await s3.send(new PutObjectCommand({ Bucket, Key: key, Body, ACL, ContentType }));
        const url = `https://${Bucket}.s3.amazonaws.com/${key}`;
        return { url, key };
    } catch (error) {
        console.log(error);
        throw error;
    }
};

const deleteImgFromAWS = async (key: string) => {
    try {
        await s3.send(new DeleteObjectCommand({ Bucket, Key: key }));
        console.log(`Successfully deleted object with key: ${key}`);
    } catch (error) {
        console.error(`Error deleting object with key ${key}:`, error);
        throw error;
    }
};

export {
    uploadImgToAWS,
    deleteImgFromAWS,
};
```

# Usage

## Upload Image
    
```typescript
        // Example usage for uploading an image
        const res = await uploadImgToAWS(img, 'e3global/blog/');
        console.log(`Image uploaded successfully. Key: ${res.key}`);
        console.log(`Image URL: ${res.url}`);
```

## Delete Image

```typescript
        // Example usage for deleting an image
        await deleteImgFromAWS('Your Key');
        console.log(`Image deleted successfully.`);
```


# Contributing
    Feel free to contribute to this project by creating issues or submitting pull requests.

# License
    This project is licensed under the MIT License.

#### Copy and paste this content into your existing `readme.md` file to update it. Adjust the formatting and content as needed.

# Thank you