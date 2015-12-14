# How it works

###Upload
1. Request to an app for uploading some file(s)
The app just need to include the Renuo-Upload-JS with an api-key to upload

2. Delivered Renuo-Upload-JS from the app creates a request with the api key to the image upload app for the credential (policy, signature, etc.)

3. The image upload app generates the credentials if the api key is right and return them

4. The form builded by the Renuo-Upload-JS creats a request per file to upload it on S3

5. S3 respond with a status if successful or not

6. The Renuo-Upload-JS handles the respond status of S3 (todo)

7. The app shows the result

###Part 2 Displaying (& Thumbnails)
8. Vistor request an image on cloudfront

9. If the image exist, cloudfront returns the image -> end

9. If the image dosen't exist on cloudfront, the request is forwarded to the image processing app

10. The image processing request Renuo-Thumbor with the forwarded request which was adapted and signed to fit Renuo-Thumbor

11. Renuo-Thumbor manipulates the image and returns it

12. The image processing server return the manipulated image to cloudfront

13. Cloudfront returns the manipulated image

##The single apps/libraries
###Renuo-Upload-JS¶
Renuo-Upload-Js is a build on top of Dropzone Library.
The upload handles a multi cors upload to S3 and displaying. For uploading to S3 a signed policy, signature, etc. is needed. 
The singing process must be safe and secure so it can not happen in the Js. For that the image upload app is needed.
Renuo-Upload-Js first version is written in CoffeeScript, the second will be written in TypeScript.

###The image upload app¶
The image upload app is a ruby sinatra app. The app is only accessed by the Renuo-Upload-JS.
The app is needed to create a signed policy and the other credentials. Another reason is to create a prefix for every upload so nothing can be overwritten by another upload.
Why a ruby sniatra app? -Because the the signing part for S3 (policy) in the first version was written and tested in ruby. 
In a second version it can be rewritten in any language, because the app is that small.
###The image processing app
The images processing app is a python flask app. The app stands between Cloudfront and Renuo-Thumbor.
The app is needed to generate the correct signed url for Renuo-Thumbor.
Why a python flask app? -Because it's a good chance to try something new.
When it wouldn't work well it can be rewritten in any language, because the app is that small.
###Renuo-Thumbor¶
Renuo-Thumbor is just a configuration for setting up Thumbor a image manipulation service. (Thumbor is open source and written in python)
###Cloudfront
Cloudfront caches all files on s3 which are requested, if a file dosen't exist the request is forward t to the image processing app.
It can not be forwarded directly to Renuo-Thumbor because Renuo-Thumbor requires a signed Request. For that the image procesisng app is needed.
###S3
It exist 1 bucket for all application which uses the upload, every app has an api key which is a folder in this bucket