# Setup


## Set up Renuo Upload for Ruby on Rails

### Set up Renuo Upload Signing
In Renuo Upload Signing you need to add a hash into the Environment Variable 'API_KEYS' for every project that uses it.
```rb
API_KEYS: {"key":"key","app_name":"app-name","env": "environment"};{"key":"key","app_name":"app-name","env": "environment"};...
```

The key needs to be 32 character long and should contain only numbers and small letters. (The use of other characters is not tested)<br>
Key-Example (don't use it!): wj871bfa22z0e7f3d8d4ud6f4derbfg0

Renuo Upload Signing will create the full name of your application like this: "app_name"-"env". Keep this in mind when you set up the Renuo Upload inside your app.

For further information look at the readme on githup:<br>
https://github.com/renuo/renuo-upload-signing#configuration

###Set Up your Ruby On Rails Project
We recommend to use environment variables to configure the Renuo Upload:
```rb
RENUO_UPLOAD_API_KEY: 'key'
RENUO_UPLOAD_SIGNING_URL: 'url-to-renuo-upload-signing'
RENUO_UPLOAD_CDN_HOST: 'url-to-renuo-upload-singning/generate_policy'
RENUO_UPLOAD_APP_NAME: 'full-app-name'
```
Look above to see, how exactly the full-app-name should look like.

For further information and examples on how to use Renuo Upload in your project, look at the readme on githup:<br>
https://github.com/renuo/renuo-upload#renuo-upload-prototype