# How it works
![](concept_of_renuo_upload.jpg)

### Upload

1. Request to an app for uploading some file(s)
The app just need to include the Renuo-Upload-JS with an API key to upload

2. Delivered Renuo-Upload-JS from the app creates a request with the API key to the image upload app for the credential (policy, signature, etc.)

3. The image upload app generates the credentials if the api key is right and return them

4. The form builded by the Renuo-Upload-JS creats a request per file to upload it on S3

5. S3 respond with a status if successful or not

6. The Renuo-Upload-JS handles the respond status of S3 (todo)

7. The app shows the result

###Part 2 Displaying (& Thumbnails)
1. Vistor request an image on cloudfront

2. If the image exist, cloudfront returns the image -> end

2. If the image dosen't exist on cloudfront, the request is forwarded to the image processing app

3. The image processing request Renuo-Thumbor with the forwarded request which was adapted and signed to fit Renuo-Thumbor

4. Renuo-Thumbor manipulates the image and returns it

5. The image processing server return the manipulated image to cloudfront

6. Cloudfront returns the manipulated image

##The single apps/libraries
![](upload full.png)

###[Client (Renuo Upload Client)](https://github.com/renuo/renuo-upload)
The Renuo Upload Client is build on top of the [Dropzone Library](http://www.dropzonejs.com/).
The upload handles a multi cors upload to S3 and provides a user interface. For uploading to S3, a signed policy, signature, etc. is needed. 
The singing process must be safe and secure so it can not be handled in the JS. For this, Renuo Upload Signing is needed.

The first version of Renuo Upload Client is written in CoffeeScript. The second version will be written in [TypeScript](http://www.typescriptlang.org/).

### Renuo Upload Signing
[Renuo Upload Signing](https://github.com/renuo/renuo-upload-signing) is a ruby sinatra app. It is only accessed by the Renuo Upload Client and needed to create a signed policy and the other credentials. It also creates a prefix for every upload, so that nothing can be overwritten by another upload.

Why a ruby sniatra app?<br>
-Because the first version of the signing part for S3 (policy) was written and tested in ruby.<br>
In a second version it can be rewritten in any language, because the application is that small.

### Renuo Thumbs Proxy
[Renuo Thumbs Proxy](https://github.com/renuo/renuo-thumbs-proxy) is a python flask app. The application stands between Cloudfront and Renuo-Thumbor. It is needed to generate the correct signed URL for Renuo-Thumbor.

Why a python flask app?<br>
-Because it's a good chance to try something new.
When it wouldn't work well it can be rewritten in any language, because the application is that small.

### Renuo Thumbor
[Renuo Thumbor](https://github.com/renuo/renuo-thumbor) is just a configuration application to set up [Thumbor](https://github.com/thumbor/thumbor), an image manipulation service. ([Thumbor](https://github.com/thumbor/thumbor) is open source and written in python)

### Cloudfront
[Cloudfront](https://aws.amazon.com/cloudfront/) caches all files on S3 which are requested. If a file doesn't exist, the request is forward to the Renuo Thumbs Proxy.
It can't be forwarded directly to Renuo-Thumbor, because Renuo-Thumbor requires a signed Request. For this, Renuo Thumbs Proxy is needed.

### Amazon S3
There exists one [Amazon S3](https://aws.amazon.com/s3/) bucket for all applications that use the upload. Every app has an API key that is a folder in this bucket.