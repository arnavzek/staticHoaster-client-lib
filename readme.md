# upon.one

[![N|Solid](https://www.upon.one/favicon.png)](https://www.upon.one)

# What is upon.one?

An easy way to create powerful web app in minutes without settings up any server environment

# Features

✔ Two click hosting
\
\
✔ User authentication with one line of code
\
\
✔ noSQL database
\
\
✔ Binary upload
\
\
✔ Server side Rendering regardless of the library or framework used

# Hosting

Create App from dashboard and upload the static files

# Setup

Just Include the below library in the main html file. Run the html file.

```
<script src="https://lib.upon.one"></script>
```

Size of library is just around 40kb

How to use with React?

```
npm install uponjs
```

```
import U from "uponjs"

U.settings({name:"Doom"}) //use the name assigned while creating the app
```

# User Authentication

`U.login()`

This is the only piece of code needed to log in users.
It will ask user to either login with upon.one or login with Google.

# Serverside Rendering

For effective SEO SSR is vital, but it takes the fun out of making a web app. Our solution to this is simulating browser behaviour whenever a bot request your website, we do this using Puppetier.

So, you don't have to do anything on your side

# How to check if a user is logged in?

Example code

```
U.getUser('username').then(username=>{

    if(username) return console.log("User is logged in")
    return console.log("no one is logged in")
})
```

# Database

Let's say you want to create a blogging site. For that u will need to create a section in the database where articles will be stored.
Here is most minimal configuration. Copy the below code to the dashboard -> your app -> Edit Backend

```
  db['articles'] = {
    updatable: true,
   	findable: true,
    writable: true,
  }
```

Now u can perform queries against this collection. let's write an Article.

```
U.collection("articles").write({title:"cool grey"} ).then(console.log)
```

Once you run the above code, your article will be written to the DB. Now, let's say you don't want un-authenticated users to write or update the database

```
  function rule(){
    if(!user) return false
    return true
  }

  db['articles'] = {
    updatable: true,
   	findable: rule,
    writable: rule
  }
```

Now, only authenticated users can write and update articles. Note that before asking users to update or write an article call `U.login()`

Now, Let's say you don't want anyone other than you to be able to write or update article. Just update the "updatable" and "writable" rules to the following code.

```
  function rule(){
    if(!user) return false
    if(user.username == "yourUsername") return true
    return false
  }

```

Now, only you can write and update articles. On the back of your head you must be wondering how to read your articles?

Example code for reading from DB

```
U.collection("articles").find().then(console.log)
```

# List of serverside variables that can be used in database rules

| Serverside variables | Description                                           |
| -------------------- | ----------------------------------------------------- |
| `query`              | Contains query fields like: query.where, query.put    |
| `user`               | contains user data, fields: username, name & id       |
| `developer`          | contains developer data, fields: username, name & id  |
| `doc`                | Refers to the document which is to be updated or read |

# Assign default values to for every write query

Schema is optional but it provides many handy features like setting default values.

```
  function defaultValueOfAuthor(){
    return user.id
  }

  db['articles'] = {
    schema:{
      author:{
        default:defaultValueOfAuthor
        }
      }
    updatable: true,
   	findable: true,
    writable: true
  }
```

# Set data type of fields

```
  db['articles'] = {
    schema:{
      author:{type:String},
        content:String,
        likes:Number
      }
    updatable: true,
   	findable: true,
    writable: true
  }

```

Note, there are two ways of assigning type. You can either directly set the data type or provide it along with other properties. Supported data types: Object, Number, String, Boolean. If no data type is assigned String data type is allocated.

# Describe required fields

```
  function defaultValueOfAuthor(){
    return user.id
  }

  db['articles'] = {
    schema:{
      author:{
        required:true
        }
      }
    updatable: true,
   	findable: true,
    writable: true
  }
```

Now, whenever author field is not provided when writing a document, operation will fail

# Add rules for update, read and write to only particular fields.

For example

```
  db['yourCollectionName'] = {
    schema:{
      author:{ updatable:false}
  },
    updatable:true,
    findable:true,
    writable:true
  }
```

Now author field can't be updated once written

# How to query Database?

Let's start with "How to find a document?"

```
collection('articles').find(<where query>,<options>)
```

Similarly, you can update, delete & write a document

| Methods                                           | Description                                                                                   |
| ------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| `write(object)`                                   | Adds document to collection                                                                   |
| `find(whereQuery,options)`                        | Finds documents, options example: {sort:{fieldA:'descending'}, limit:10 }                     |
| `update(whereQuery, putQuery, options)`           | updates the first document that matches the whereQuery                                        |
| `delete(whereQuery)`                              | Deletes the first document that matches the whereQuery but make sure deletable is set to true |
| `search(whereQuery, { fields:[fieldA,fieldB] } )` | Searches documents based on whereQuery                                                        |

# Let users upload binary files

Step 1: Add the below code to dashboard -> your app -> Write Backend

```
  bucket['bucketName'] = {
    updatable:true,
    findable:true,
    writable:true
  }

```

Step 2: Call `U.upload(file,bucketName,fileToReplace).then(console.log)` & u are done

Example Code

```

<body> <input type="file" onchange="upload(event)"> </body>

<script src="https://lib.upon.one"></script>

<script>
 async function upload({target}){

    let file = target.files[0]
    let image = await U.upload(file,'bucketName',fileToReplace)
    window.location = "/cdn/"+image.url

  }
</script>
```

# How to compress file then upload

```
npm i browser-image-compression
```

Example Code

```
import imageCompression from 'browser-image-compression';

  async function upload({target}){

    let file = target.files[0]
    let compressedFile = await imageCompression(file,{maxSizeMB:0.5})
    let image = await U.upload(file,'bucketName',fileToReplace)
    window.location = "/cdn/"+image.url

  }
```

For more about imageCompression go to https://www.npmjs.com/package/browser-image-compression

# What does upon.one means?

Anything upon one is itself. so, yourapp.upon.one just means yourapp. & our goal is to save your time

# Best Practises

When you are done with the app development, make sure to add the folowing configuration

```
  U.settings({released:true})
```

This will allow us to promote your app

# Add custom domain

1. Add a CNAME record with “@” pointing to the yourapp.upon.one
2. Delete the A record
3. Set up Domain Forwarding to point to the “yourapp.upon.one"

We don't suggest using A record because, if we ever migrated to different server, IP will change and your site will stop working. The recommended method is safest.

# How to alert users

Following is a beautiful alternative to alert("message")

```
U.say("Good morning!")
```

# How to ask user to fill a form

Example Code

```
U.ask([
  {h1:"Which is favorite finger?"},
  {input:"finger name"},
  {button:{innerHTML:"Send",onclick:callback}}
  ])

function callback(event,formData){
  console.log( formdata["finger name"] )
}

```
