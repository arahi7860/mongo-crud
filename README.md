[![General Assembly Logo](https://camo.githubusercontent.com/1a91b05b8f4d44b5bbfb83abac2b0996d8e26c92/687474703a2f2f692e696d6775722e636f6d2f6b6538555354712e706e67)](https://generalassemb.ly/education/web-development-immersive)

# MongoDB CRUD

Think about databases and communicating with them, what are some ways that we can interact with our data? So far, we have mainly worked with data through third-party APIs, where we can get the data we need through a `fetch()` request and render that data on the page. However, we haven't been able to alter any of the data ourselves. That's where **CRUD** comes in.

## Prerequisites

- Intro to MongoDB

## Objectives

By the end of this, developers should be able to:

* Perform CRUD on documents in a collection using the Mongo CLI
* Build a simple node CLI to query a MongoDB collection

## CRUD

**CRUD** stands for **Create**, **Read**, **Update** and **Delete**. There are the four ways that we as developers can interact with our database. So far, we have only been able to read data. But what if we (or our users) wanted to make some changes to the data in the database? For example, if someone wanted to reset their password or change their username on an application they use. Or delete their account altogether! 

CRUD is a term that you'll be hearing a lot throughout the remainder of the cohort. Today, we'll learn how to implement CRUD on our database through the CLI in the Mongo shell. 

## We Do: Create a Database with the CLI (5 min / 0:10)

In your terminal, run the command `mongod` to start the MongoDB server. In a separate tab, run the command `mongo` to open the Mongo shell.

In the shell, let's create our first database, one which we will be using
to store information about restaurants.

In order to create/connect to a new database, we have to tell Mongo to `use`
a specific database that we want to work with. `use` will create the database it received as an argument if not already initialized and then connect to it.

```
> use restaurant_db
```

Verify. The `db` variable is provided by Mongo and will point to the database you're currently connected to.

```
> db
restaurant_db
```

Common gotcha: what happens when we run:

```
$ show dbs
```

We don't see `restaurant_db` listed. It isn't until we add
a document to our database that our db will show up in `show dbs`!

## CLI: Create a Record (10 min / 0:15)

### Insert

Use `insert()` to add documents to a collection.

### Insert A Restaurant

```bash
> db.restaurants.insert({
  "name": "Haikan",
  "address" : {
      "street" : "805 V Street NW",
      "zipcode" : 20001
  },
  "cuisine": "Ramen"
})
```

Let's break this down:

1. The `db` is the database we’re connected to. In this case, `restaurant_db`.
1. `.restaurants` is then referring to a collection in our `restaurant_db`.
1. We use the `.insert()` to add a document to the `restaurants` collection (the document inside the parentheses).

> `restaurants` doesn't exist at first, but that's okay. It gets created
> automatically the first time we add a document to it!

### Verify the Insert

```bash
> show collections
restaurants
```

`restaurants` is now saved as a collection. A collection is really just a group of
documents. If you want to explore all the things you can do with a collection,
type `db.collection_name.help()`, or in this case: `db.restaurants.help()`

Now type:

```js
db.restaurants.find()
```

That should return a document with the following fields:

* `name`
* `address`
* `cuisine`

**Q**: What is surprising/unexpected?

* Where did `restaurants` come from?
* What is `_id`?

> Note: Documentation on [ObjectId](https://docs.mongodb.org/manual/reference/object-id/)

## Review `insert`

```js
// insert
db.your_collection_name.insert({ data as json })
// find
db.your_collection_name.find()
```

New Record:

* If the document passed to the `insert()` method does not contain the `_id` field the Mongo shell automatically adds the field to the document and sets the field’s value to a generated `ObjectId`.

New Collection:

* If you attempt to add documents to a collection that does not exist, MongoDB will create the collection for you.

## Dropping a Database

```bash
> use database_to_be_dropped
> db.dropDatabase()
```

Drops the **current** database. Go ahead and drop your database now.

## You Do: Add More Documents (10 min / 0:25)

Using the Mongo Shell CLI, add at least 4 new restaurant documents to your
`restaurants` collection.

**ProTip**: I recommend you construct your statements in your editor and copy
/ paste. It will help you now & later.

> Prompt: Did anyone insert multiple at one time?

Let's recreate the steps together:

<details>
  <summary>How can we tell which database we are connected to currently?</summary>

  `db`

</details>

1. Create DB
1. Use the appropriate DB
1. Insert multiple restaurants

```bash
db.restaurants.insert([
  {
    "name": "Haikan",
    "address" : {
      "street" : "805 V Street NW",
      "zipcode" : 20001
    },
    "cuisine": "Ramen"
  },
  {
    "name": "Taqueria Habanero",
    "address": {
      "street": "4710 14th Street NW",
      "zipcode": 20010
    },
    "cuisine": "Mexican"
  },
  {
    "name": "Chicken & Whiskey",
    "address": {
      "street": "1738 14th Street NW",
      "zipcode": 20009
    },
    "cuisine": "Peruvian"
  },
  {
    "name": "The Coupe",
    "address": {
      "street": "3415 11th Street NW",
      "zipcode": 20010
    },
    "cuisine": "American"
  },
  {
    "name": "Da Hong Pao",
    "address": {
      "street": "1409 14th Street NW",
      "zipcode": 20005
    }
  }
])

> db.restaurants.count()
```

> Note that there's no `cuisine` key in the last record. Does that matter?

## [Primary Key](http://docs.mongodb.org/manual/reference/glossary/#term-primary-key) (5 min / 0:30)

* A record’s unique immutable identifier generated upon creation of a new instance.
* In relational databases, the primary key is usually an *id* field, the value of which is typically an *Integer*.
* In MongoDB, the *_id* field is usually a *[BSON](http://docs.mongodb.org/manual/reference/glossary/#term-bson) [ObjectId](http://docs.mongodb.org/manual/reference/glossary/#term-objectid)*.

## CLI: QUERY for Records (15 min / 0:45)

Breaking down the anatomy of a typical query with Mongo:

    db + collection + operation + modification = results

In order to find all restaurants:
```bash
> db.restaurants.find()
```

> **Note**: we can format our output to be a little nicer on the eyes by
> chaining the `.pretty()` method to end of our query like so:
> `db.restaurants.find().pretty()`

### Find by Conditions

We can add conditions to our query to target documents based on matching
key-value pairs:

```bash
> db.restaurants.find({name: "Haikan"});
> db.restaurants.find({"address.zipcode": 20001});
```

### CLI: Update a Record(s)

http://docs.mongodb.org/manual/core/write-operations-introduction/

```
> db.your_collection.update(
  { criteria },
  {
    $set: { assignments }
  },
  { options }
)
```

The first key value pair is the condition on which to find the
document you'd like to update, the second is what values you'd like to set,
and third is any additional options.

This what a sample update might look like:

```bash
> db.restaurants.update(
  {"name": "Haikan"},
  { $set: { state: "DC" }}
)
```

In order to update multiple documents at a time, make sure to pass
the `multi` option as true, like so:

```bash
db.restaurants.update(
  {},
  {
    $set: { "state": "DC" }
  },
  {multi: true}
)
```

Verify:

```bash
> db.restaurants.find().pretty()
```

### CLI: Remove Records

Remove a restaurant with the same architecture:

```
> db.restaurants.remove({ conditions })
```

This what a sample remove might look like:

```bash
> db.restaurants.remove({"name": "Haikan"})
```

### You Do: Update the restaurant_db (30 min / 1:15)

> Write all these out in your code editor before you run them in the command line.

Take time to think about and execute the appropriate commands so that you:

* Update all restaurants to have a new key-value pair `{state: 'DC'}`
* Add a property of `rating` to at least 2 documents and give it a numerical value between 1-5
* Change the street `address` of a specific restaurant
* Add nested sub-documents to each restaurant to that it has many `reviews` (how would you list several reviews here?)
* Store important information about each `review`, such as the name of the reviewer and their comment about the restuarant.

## Additional Resources

* [CRUD Intro](http://docs.mongodb.org/manual/core/crud-introduction/)
* [CRUD Commands](http://docs.mongodb.org/manual/reference/crud/)

## [License](LICENSE)

1. All content is licensed under a CC­BY­NC­SA 4.0 license.
1. All software code is licensed under GNU GPLv3. For commercial use or
   alternative licensing, please contact legal@ga.co.
