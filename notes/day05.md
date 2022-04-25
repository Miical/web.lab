## Recap

Note:

- Promises

  ```js
  get("/api/addOne", {input: 5}}).then((six) => {
      return six + 1;
  }).then(((seven) => {
      console.log(seven);
  })).catch(error) => {
    // handle the error
  })
  ```

  

## Intro to Databases

 **What's wrong with data.txt?**

- Write Speed: Saves every story/comment whenever someone posts
- Memory Usage: Still keeps all stories/comments in RAM
- Query Speed: To find story with a particular _id, we linear search through all stories.
- Single Point of Failure: What if your laptop hard drive breaks?
- Concurrency Issues: What if two users post at the exact same time?

**What is a database?**

- Datebase (DB):

  data.txt but better

- DBMS:

  read/writeDateFromFIle() but better

We will use MongoDB as our database

We will run MongoDB in the cloud, using Atlas



## Workshop 6 - Database

Structure

- MongoDB Instance
  - Database
    - Collections
      - Documents (just like a javascript object)
        - Fields

![image-20220422190745573](day05.assets/image-20220422190745573.png)



**Mongoose**

NodeJS library that allows MongoDB integration

wrapper that allows you to interact with MongoDB API

**Schema**

- Schemas define the structure of  your documents
- Organization is key

![image-20220422191430636](day05.assets/image-20220422191430636.png)



**Mongoose Schemas: Processing Documents**

- Means of structuring MongoDB documents
  - Specify fields within a document
- Each collection should have a schema



**Mongoose Schema types**

String, Number, Date, Buffer, Boolean, Mixed, ObjectId, Array.

**Creating a Mongoose Model (Generally)**

1. Create a mongoose.Schema

   ```js
   const UserSchema = new mongoose.Schema({
       name: String, 
       age: Number, 
       pets: [String],
   })
   ```

 2. Create a mongoose.model

    ```js
    const User = mongoose.model("User", UserSchema)
    ```

    

**Creating Documents**

```js
const User = mongoose.model("User", UserSchema)

const Tim = new User({name: "Tim", age: 21, pets: ["cloudy"]});

Tim.save()
	.then((student) => console.log('Added ${student.name}'));
```



**All together**

![image-20220422193447745](day05.assets/image-20220422193447745.png)

**_id**

- Every document is automatically assigned a unique identifier
- The identifier is assigned under the "_id"  field.
- Useful when there's a relationship between documents

**Finding Documents**

```js
// return all
User.find({})
	.then((users) => console.log('Found ${users.length} users'));

User.find({name: "Tim"})

user.find({name: 'Tim', age: 21})
```

**Deleting Documents**

```js
// Deletes the first user in the collection named Tim
User.deleteOne({"name": "Tim"})
	.then((err) => {
    	if (err) return console.log("error");
    	console.log("Delete 1 user");
})

// Deletes all first user in the collection named Tim
User.deleteMany({"name": "Tim"})
	.then((err) => {
    	if (err) return console.log("error");
    	console.log("Delete all user");
})
```



**Mongoose Structure**

![image-20220422195013026](day05.assets/image-20220422195013026.png)

**Mongoose Parameters**

http://mongoosejs.com/docs/schematypes.html (from “All Schema Types”)

More advanced: http://mongoosejs.com/docs/validation.html 

More advanced: http://mongoosejs.com/docs/guide.html



## Workshop: Hook Database to Your Catbook App

### STEP 0: Create Comment and Story Mongoose Models

```js
const mongoose = require("mongoose")

// define story schema for the database
const StorySchema = new mongoose.Schema({
  creater_name: String,
  content: String
});

module.exports = mongoose.model("story", StorySchema);
```

```js
const mongoose = require("mongoose")

// define comment schema for the database
const CommentSchema = new mongoose.Schema({
  creater_name: String,
  parent: String,
  content: String
});

module.exports = mongoose.model("comment", CommentSchema);
```

### STEP 1 SETUP:

**Use api Route for Database Requests**

```js
// import models so we can interact with the database
const Story = require("./models/story")
const Comment = require("./model/comment")
```

```js
router.get("/stories", (req, res) => {
  Story.find({})
       .then( (stories) => {res.send(stories)} );
});

router.post("/story", (req, res) => {
  const newStory = new Story({
    creator_name: "jason",
    content: req.body.content,
  });

  newStory.save();
  
});
```



## Advanced CSS

### Palettes

how many theme colors?

2-3 colors

A website to select color: [coolors.co]()

### Box model

![image-20220425135904726](day05.assets/image-20220425135904726.png)

### Layouts

#### Display

display determines how elements size & sit with/in each other 

**display: block**

big blocks that stretch across, always sit on new lines generally useful, default property for div

(i.e. <div, <section, <ul, <p, <h1-6, <header)

**display: inline**

an element that is part of text, size is always proportional to text they do NOT accept width/height properties and top/bottom margins

(i.e, <span, <a, <img)

**display: none**

**display: flex**

**display: grid**

```css
.container {
    display: grid;
    grid-template-columns: 2fr 1fr 1fr
    grid-templeae-rows: 100px 200px
}
```

#### Position

position determines where an elements sits based on other  elements

**position: static**

renders boxes position based on order in document default property value for div

**position: relative**

positions the element "relative" to where it would be if static useful for slight modifications in position

**position: absolute**

positions the element relative to first ancestor positioned non-statically useful for navbars and sidebars

**position: fixed**

positions the element relative to the screen useful for navbars and bulletins

### Transitions & animations

transition determines how changes in properties show up on your screen 

```css
.card {
    transition: background-color 1s ease-in;
}
```

animation defines keyframes to transition through

```css
.card {
    animation: weblab 2s infinite;
}

@keyframes weblbb { 
    0% {
        color: red;
    }
    100 % {
        width: 300px;
    }
}
```

### Responsive Layouts

**Media queries**

Media queries allow you to run certain CSS only if:

- The device screen is at least certain size
- The device supports hovering (with a mouse)
- The device supports colors
- ... Speech input, aspect ratio, etc

   ```css
   @media (max-width: 800px) {
       .card: {
           background-color: pink;
       }
   }
   ```



### Special CSS Selectors

Recap of Selectors

- Type selectors: a, p, h1, div, etc

- Class selectors: .classname

- ID selectors: #idname 

- Attribute selectors: [attr=value]

  Can also filter with attributes that start with , end with, or contain a value!

- CSS Combinators

  Used to select elements in relation to other elements

  - Siblings 

  - Children

  - Descendants

    - Adjacent siblings: +

      h2 + p {// selects all <p that directly follow an <h2}

    - General siblings: ~

      p ~ span { // selects all <span that follow an <p}

    - Child: >

      ul > li { // selects all <li dirctly inside a <ul}

    - Descendant: ' '(space)

      div span {// selects all <span anywhere inside a <div} 

- CSS Pseudo-classes

  Used to specify a selector that is not direct represented in the HTML.

  ```css
  p::first-line { // first line of a <p>}
  ::selection { // user highlighted text }
  
  a::after {
      // styling placed in a generated element after every <a>
  }
  ```

  

  



