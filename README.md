[![General Assembly Logo](https://camo.githubusercontent.com/1a91b05b8f4d44b5bbfb83abac2b0996d8e26c92/687474703a2f2f692e696d6775722e636f6d2f6b6538555354712e706e67)](https://generalassemb.ly/education/web-development-immersive)

# MongoDB CRUD

Use this template to structure your READMEs for talks. Remove text from this
section, or use it to frame the talk you are giving. Good framing answers the
question "Why am I learning this?".

Be sure to include a recent [`LICENSE`](LICENSE) and Markdown linter
configuration ([`.remarkrc`](.remarkrc)). Also, include an appropriate
`.gitignore`; these are usually found in specific technology templates, for
example [js-template](https://www.github.com/ga-wdi-boston/js-template).

## Prerequisites

- Topics with which developers should be familiar with.
- Prerequisites are "just-in-time", so if I have a prerequisite that mentions
  Sass, I would **not** need to include CSS as a prerequisite.
- [Links to previous materials](https://www.github.com/ga-wdi-boston/example)
  are often useful.

## Objectives

By the end of this, developers should be able to:

* Perform CRUD on documents in a collection using the Mongo CLI
* Build a simple node CLI to query a MongoDB collection

### CLI: Creating a Database

In the Mongo shell, let's create our first database, one which we will be using
to store information about restaurants.

In order to create/connect to a new database, we have to tell Mongo to `use`
a specific database that we want to work with:

```
> use restaurant_db
```
> **Note**: `use` will create the database it received as an argument if not
> already initialized and then connect to it

Verify:

```
> db
restaurant_db
```

> **Note**: the `db` variable is provided by Mongo and will point to the
> database you're currently connected to.

Common gotcha - what happens when we run:

```
$ show dbs
```

> **Note**: we don't see `restaurant_db` listed. It isn't until we add
> a document to our database that our db will show up in `show dbs`!

## CLI: Create a record (15 min / 1:20)

### Insert

Use `insert()` to add documents to a collection.

### Insert A Restaurant

``` json
> db.restaurants.insert({
  "name": "Haikan",
  "address" : {
      "street" : "805 V Street NW",
      "zipcode" : 20001
  },
  "cuisine": "Ramen"
})
```

**Important to note**:

> The `db` is the database we’re connected to. In this case, `restaurant_db`.
> `.restaurants` is then referring to a collection in our `restaurant_db`. We
> use the `.insert()` to add a document to the `restaurants` collection (the
> document inside the parentheses).

> `restaurants` doesn't exist at first, but that's okay. It gets created
> automatically the first time we add a document to it!

### Verify the insert

```bash
> show collections

restaurants
```

`restaurants` was saved as a collection. A collection is really just a group of
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

**Q**. What is surprising/unexpected?

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

New collection:

* If you attempt to add documents to a collection that does not exist, MongoDB will create the collection for you.

## Dropping a Database

```bash
> use database_to_be_dropped
> db.dropDatabase()
```

Drops the **current** database. Go ahead and drop your database now.

### Exercise (5 minutes): Add a few more restaurants.

Using the Mongo Shell CLI, add at least 4 new restaurant documents to your
`restaurants` collection.

**ProTip**: I recommend you construct your statements in your editor and copy
/ paste. It will help you now & later.

> Prompt: Did anyone insert multiple at one time?

Let's recreate the steps together:

<details>
  <summary>How can we tell which database we are connected to currently?</summary>

  > `db`
</details>

1. Create DB
2. Use the appropriate DB
3. Insert multiple restaurants

``` json
db.restaurants.remove({});
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

## [Primary key](http://docs.mongodb.org/manual/reference/glossary/#term-primary-key) (5 min / 1:25)

* A record’s unique immutable identifier generated upon creation of a new instance.
* In relational databases, the primary key is usually an *id* field, the value of which is typically an *Integer*.
* In MongoDB, the *_id* field is usually a *[BSON](http://docs.mongodb.org/manual/reference/glossary/#term-bson) [ObjectId](http://docs.mongodb.org/manual/reference/glossary/#term-objectid)*.

## Break (10 min / 1:35)

## CLI: QUERY for Records (25 min / 2:00)

Breaking down the anatomy of a typical query with Mongo:

    db + collection + operation + modification = results

In order to find all restaurants:
```js
> db.restaurants.find()
```

> **Note**: we can format our output to be a little nicer on the eyes by
> chaining the `.pretty()` method to end of our query like so:
> `db.restaurants.find().pretty()`

### Find by Conditions (like SQL's `where`)

We can add conditions to our query to target documents based on matching
key-value pairs:

```js
> db.restaurants.find({name: "Haikan"});
> db.restaurants.find({"address.zipcode": 20001});
```

### CLI: Update a record(s)

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

> **Note**: the first key value pair is the condition on which to find the
> document you'd like to update, the second is what values you'd like to set,
> and third is any additional options

### You do (15 min):

> Write all these out in your code editor before you run them in the command line.

Take time to think about and execute the appropriate commands so that you:

* Update all restaurants to have a new key-value pair `{state: 'DC'}`
* Add a property of `rating` to at least 2 documents and give it a numerical value between 1-5
* Change the street `address` of a specific restaurant

**Bonus:**

* Add nested sub-documents to each restaurant to that it has many `reviews`
* Store important information about each `review`

> **Note** this what a sample update might look like:

```js
> db.restaurants.update(
  {"name": "Haikan"},
  { $set: { state: "DC" }}
)
```

> **Note**: In order to update multiple documents at a time, make sure to pass
> the `multi` option as true, like so:

```js
db.restaurants.update(
  {},
  {
    $set: { "state": "DC" }
  },
  {multi: true}
)
```

Verify:

```js
> db.restaurants.find().pretty()
```

### CLI: Remove records

```
> db.restaurants.remove({ conditions })
```

### CLI: Add a nested object

> We already did this! (The address 'object' / 'subdocument')

## Additional Resources

* [CRUD Intro](http://docs.mongodb.org/manual/core/crud-introduction/)
* [CRUD Commands](http://docs.mongodb.org/manual/reference/crud/)

## [License](LICENSE)

1. All content is licensed under a CC­BY­NC­SA 4.0 license.
1. All software code is licensed under GNU GPLv3. For commercial use or
   alternative licensing, please contact legal@ga.co.
