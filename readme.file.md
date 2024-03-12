## imageUpload.ts - add this file to lib folder 
    import {
        S3Client,
        PutObjectCommand,
        DeleteObjectCommand,
    } from "@aws-sdk/client-s3";
    
    const Bucket = process.env.BUCKET;
    
    const s3 = new S3Client({
        region: 'us-east-2',
        credentials: {
        accessKeyId: process.env.AWS_ACCES_KEY_ID as string,
        secretAccessKey: process.env.AWS_SECRET_ACCESS_KEY as string,
        },
    });
    
    const uploadImgToAWS = async (file: any, folder: String) => {
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
    }
    
    export {
        uploadImgToAWS,
        deleteImgFromAWS,
    }
  


  <!-- call  -->
  ## call function for upload 
    const res = await uploadImgToAWS(img, 'e3global/blog/');
    console.log(res.key);
    console.log(res.url);


## call function delete 
    await deleteImgFromAWS('Your Key')
