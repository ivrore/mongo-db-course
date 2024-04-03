# Mongo DB Interaction
Was created to solve relational databases problems, that is why it has similar features as them:

- Dev and Ops oriented
- Easy to scale horizontally (also Vertically)
- Business Critical
- Lessons learned from 50 years of RDBMS

The two fundamental RDBMS features that are hard to do efficiently:
- Joins
- Transactions

<b>LFI -> Normalization relational databases</b>

MongoDB works with BSON (binary) but normally visually we see the documents as JSON
In MongoDB if we don't have data is better to not save it as <i>null</i> because this uses space but MongoDB shows as <i>null</i> if that field is empty.

Is not neccesary that documents have the same structure.

## Comparison Relational vs Non-Relational

|Relational| MongoDB|
|---|---|
|Database|Database|
|Table|Collection|
|Row|Document|
|Column|Field|
|Index|Index|
|Namespace|Namespace|

## Usability

- Command-line
- GUI to access the database
- Drivers for Java,-net, C, C++, JavaScript, etc...

## Availability and Scalability
### High Avalability (Replication)
- Multiple copies of data
- Different host / locations
- Continuous replication
### Horizontal scalability (Shards)
- Partition data over multiple Replica Sets
- Provides unlimited hardware scaling

## When should be used MongoDB
- Need high-speed acces to complex objects
- Value fast development
- Have large data volumes (no big up-front payment)
- Want store larger data structures together (large arrays, text fields, binary data)
- Need to store structures of varying shapes (application-defined schemas)
- Want distributed data (worldwide low latency, legal obligation for locations, real-time distribution)

## Atlas Platform
### Connect
```
mongosh "mongodb+srv://cluster0.1os2kce.mongodb.net/" --apiVersion 1 --username xxxx
```
### Services
- Database
- Search
- Vector Search (AI)
- Charts
- Data Federation
- APIs, Triggers, Functions
- Device Sync (Mobile devices synchronization)
- Online Archive
### Excercise
1. Create account in Atlas Platform
2. Create deployment M0 - Select region and cloud provider
3. Set connection IP address to connect (Add a connection IP address). Initial access is restricted to own IP.
4. Create user for MongoDB "Create database user"
5. Database > Load sample dataset > Check in collections if loaded
6. Atlas Search (left menu)
7. Add field mapping > Save changes
8. Create search index > Wait until the index is built
9. Query
10. Press 'edit query' to extract the query to use it in Mongo Shell

## Using Mongo Shell (Mongosh)

```javascript
show dbs // See databases in mongo
db // Check current selected database
use sample_airbnb // Change db
Show collections // Show collections in current DB
```
Creations are 'on the fly'.
Create database called 'db', create collection 'coches' and insert document all at once:
```javascript
db.coches.insertOne({"name": "ibiza"})
```
- Useful commands
```javascript
db.coches.drop() // To delete the created collection
db.employees.find({}) // Query all
db.employees.find({"hungry": true}) // Query with a filter
db.employees.find({"department": null}) // Filter all the ones that doesn't have the field
db.employees.find({"year": {$gt: 1991}}) // Filter with a 'year' greater than n
```
- To insert a new document with a variable:

```javascript
// Create a new var
var employee = {"name":"edgar", "hungry": true, "title": "director", "year": 1991}
// Insert a new employee
db.employees.insertOne(employee)
```
# Mongo Crud
MongoDB APIs allow us to perform Create Read Update and Delete operations options to perform single or multiple operations.

|            | Single Document            | Multiple Documents        |
|------------|----------------------------|---------------------------|
| **C**reate | insertOne(doc)             | insertMany([doc,doc,doc]) |
| **R**ead   | findOne(query, projection) | find(query, projection)   |
| **U**pdate | updateOne(query,change)    | updateMany(query, change) |
| **D**elete | deleteOne(query)           | deleteMany(query)         |

### Excercise
Add four documents to a collection called diaries using the commands shown here.
```javascript
use test
db.diaries.drop()
db.diaries.insertMany([
    {name: "dug", day: ISODate("2014-11-04"), txt: "went for a walk"},
    {name: "dug", day: ISODate("2014-11-06"), txt: "saw a squirrel"},
    {name: "ray", day: ISODate("2014-11-06"), txt: "met dug in the park"},
    {name: "dug", day: ISODate("2014-11-09"), txt: "got a treat"} 
])
```

## Querying values in nested documents
They need to have "" when querying nested fields
For example
```javascript
// This will work
db.people.findOne({"address.city","New York"})
// This WON'T work
db.people.findOne({address.city,"New York"})
```
## Query by range of values

Operators meaning
```
&gt -> Greater than
&gte -> Greater or equal to 
&lte -> Less or equal to
&ne -> Not equal
&in -> Index
&nin -> Not index
&eq -> Equal to position
pos: -> One position
```
Example:

```javascript
for(x=0; x<200; x++) { 
    db.queue.insertOne({pos:x})
}
db.queue.find({pos: { $gt : 25 }})
db.queue.find({pos: { $gte: 25 }})
db.queue.find({pos: { $lt: 25 }})
db.queue.find({pos: { $lte: 25 }})
db.queue.find({pos: { $gt: 25 , $lt:30 }})
db.queue.find({pos: { $ne: 3 }})
db.queue.find({pos: { $in: [1,3,6] }})
db.queue.find({pos: { $nin: [2,4,7] }})
db.queue.find({pos: { $eq: 6 }})
db.queue.find({pos: 6})
```


### Execise Range and Logic
- In the MongoDB shell, change to using the database **sample_training**
- See in <b>grades</b> collection, hoy many documents have studendt_id less than or equal 65(Answe: 660)
```javascript
use sample_training
db.grades.find({"student_id":{$lte: 65}}).count()
```
- In the <b>inspections</b> collection, how many documents have result as "Pass" or "Fail" (Write this in two different ways) Answer: 16609
```javascript
db.inspections.find({$or:  [{result: "Pass"},{result:"Fail"}]}).count()
// or
db.inspections.find({result: {$in: ["Pass","Fail"]}}).count()
```

## Query values in arrays

```javascript
// do the insert
db.people.insertOne({
    "name":"John",
    hobbies: ["cars","robots","gardens"]
})
db.people.find({"hobbies.0":"cars"})
```

### Excercise: Combined Query
- Show <b>name and <b>number of employees</b> from the first company with less than 200 employees.
```javascript
db.companies.find({"number_of_employees":{$lt : 200}},{name:1,number_of_employees:1}).sort({number_of_employees:-1}).limit(1)
```
- Comparing internal data with other data. Find the ones tat totmatoes rating is greater than imdb rating
```javascript
use sample_mflix
db.movies.find({
    $expr:{
        $gt: ["$tomatoes.viewer.rating","$imdb.rating"]
    }
})
```
## Using upsert
- Upsert property should be used when we want to insert a document if doesn't exist or update it if already exists.
```javascript
db.banners.drop()
db.banners.updateOne({name:"joe"},{$inc:{click:1}},{upsert:true})
db.banners.findOne()
```
If we run again
```javascript
db.banners.updateOne({name:"joe"},{$inc:{click:1}},{upsert:true})
```
We will have 2 clicks for joe

### Exercise Updates
1. In the **inspections** collection, let’s do a little data cleaning: Replace the “**Completed**” inspection **result** to use only “**No Violation Issued**” for those inspections. Update all the cases accordingly.
```javascript
db.inspections.updateMany({result:"Completed"},{$set:{result:"No Violation Issued"}});
```
2.Set fine value
```javascript
db.inspections.updateMany({result:"Fail"},{$set:{fine:100}});
```
3.Increase fines in ROSEDALE
```javascript
db.inspections.updateMany({result:"Fail","address.city":"ROSEDALE"},{$inc:{fine:150}});
```
Check the result if worked
```javascript
db.inspections.find({"address.city": "ROSEDALE",result:"Fail"})
```

## Deleting Documents
- deleteOne() and deleteMany() work the same way as updateOne() or updateMany()
- Rather than taking a mutation - simply remove the document from the database

## Overwriting documents

## Querying Arrays
```
$elemMatch -> Finds document if all elements of array match one value or expression
```
## Exercise: $elemMatch
- Find all restaurants that have a C grade in a date greater than "2013-12-31"

```javascript
db.restaurants.find({ "grades.grade": "C", "grades.date": {$gt:ISODate("2013-12-31")}}) // This is incorrect as finds one that hasn't had a C since 2014
db.restaurants.find({grades:{$elemMatch: {grade:"C", "date": {$gt:ISODate("2013-12-31")}}}}).count() // This is correct
```

## Updating Arrays
### Excercise - Array Updates
Use the sample_training database and update the grades collection:
1. If anyone got >90% for any homework, reduce their score by 10
```javascript
let query = {scores: {$elemMatch: {type:"homework", score:{$gt: 90}}}}
db.grades.findOne(query)
let update = {$inc: {"scores.$[filter].score": -10}}
let arrayFilter = {arrayFilters: [{"filter.type":"homework","filter.score" :{$gt: 90}}]}
db.grades.updateMany(query, update, arrayFilter)
db.grades.findOne(query)
```
2. Add a new field (average) containing their mean score (scores.score) in each class
```javascript
db.grades.updateMany({}, [{$set: {"avg" :{$avg:"$scores.score" }}}])
```
3. Drop each student's worst score. Use two updates to do this

```javascript
db.grades.updateMany({}, {$push: {scores: {$each: [], $sort: {score: 1}}}})
db.grades.updateMany({}, {$pop: {scores:-1}})
```

# Mongo Indexes

## Why?
1. Speed up queries
2. Avoid write/read in disk
   
## When?
- Should support every query
- Scanning records is very inefficient
- The developer determines the best index to use writing the query
- Up to 64 indexes but from 16 goes slower (normally a good number is ~4)
- Write performance degrades when there are more than 20-30 

## Index types
- Single-field
- Compound
- Multikey
- Geospatial
- Text
- Hashed
- Wildcard
   
How to determine best-index?
1. Checks in PlanCache if an optimal index has been chosen before
2. If not, then checks with a query


We can see what Mongo is doing with <b>.explain()</b>

## Creating an index

```javascript
use sample_airbnb
db.listingsAndReviews.find({number_of_reviews: 50}).explain("executionStats")
// If we create the index
db.listingsAndReviews.find({number_of_reviews: 50}).explain("executionStats")
// Then winning plan is not 'COLSCAN' is 'FETCH' with IXSCAN (indexScan)

// Important fields in the output
queryPlan.stage
executionStats.nReturned
executionStats.totalKeysExamined
executionStats.totalDocsExamined
executionStats.executionTimeMillis
```

### Exercise - Indexing
Use the sample_airbnb database and the listingsAndReviews collection:
- Find the host_name of the host with the most total listings (host.host_total_listings_count).
- Tips, use:
  * find() with projection
  * sort()
  * limit()
  * explain()
- Create an index to support the query
- Calculate how much more efficient it is now with this index
Answer:
```javascript

[{_id: '12902610',host: { host_name: 'Sonder', host_total_listings_count: 1198 }}]
```
```javascript
// Find with projection
db.listingsAndReviews.findOne({},{id:1, host:{host_name:1, host_total_listings_count:1}})

// Explain the query
db.listingsAndReviews.find({},{_id:0, host:{host_name:1, host_total_listings_count:1}}).sort({"host.host_total_listings_count":-1}).limit(1).explain()

// Ordered ASC
db.listingsAndReviews.createIndex({"host.host_total_listings_count":-1})

// Ordered DESC is better for our query for host with LARGEST listings 
db.listingsAndReviews.createIndex({"host.host_total_listings_count":-1})

// See all plans to compare
db.listingsAndReviews.find({},{_id:0, host:{host_name:1, host_total_listings_count:1}}).sort({"host.host_total_listings_count":-1}).limit(1).explain("allPlansExecution")

// To see query execution stats
db.listingsAndReviews.find({},{_id:0, host:{host_name:1, host_total_listings_count:1}}).sort({"host.host_total_listings_count":-1}).limit(1).explain("executionStats")
```
## Partial index
```javascript
// create partial index
db.orders.createIndex({ custid: 1 }, { partialFilterExpression: { archived: false } } )
// get indexes
db.orders.getIndexes()
```

## Compound index
When using a query in a compound index is very very important to include the first field from the compound index. It doesn't matter the order in the query but to have the first index

```javascript
db.people.createIndex({lastname:1, firstname:1, score:1})
db.people.find({lastname:"Chaves"}) // This will be indexScan
db.people.find({firstname:"George", lastname:"Chaves"}) // This will be indexScan as well, order inverted
db.people.find({firstname:"Chaves"}) // This WON'T be indexScan, but CollScan that takes more resources
```
### Exercise - Compound indexes
```javascript
use sample_airbnb

let query = {amenities: "Waterfront","bed_type" : { $in :["Futon", "Real Bed" ] }, first_review : { $lt: ISODate("2018-12-31") },last_review : { $gt : ISODate("2019-02-28") }
};

let project = { bedrooms: 1 , price: 1, _id: 0, "address.country":1}
let order = {bedrooms: -1, price: 1}
db.listingsAndReviews.find(query,project).sort(order).explain("executionStats") // ExecutionTimeMillis = 18
let index = {amenities:1, bed_type:1, bedrooms:-1, price: 1, first_review:1, last_review:-1};
let index = {amenities:1, bed_type:1, bedrooms:-1, price: 1, first_review:1, last_review:1};
let index = {amenities:1, bedrooms:-1, price:1, bed_type:1, first_review:1, last_review:1};
// The order of the index is super important
db.listingsAndReviews.createIndex(index) 
```

# Mongo Aggregations

## Basic Aggregation Stages

The most **common** stages are:

| **Name** | **Description**                    |
|----------|------------------------------------|
| $match   | equivalent to find({})             |
| $project | equivalent to find({}, projection) |
| $sort    | equivalent to find().sort(order)   |
| $limit   | equivalent to find().limit(num)    |
| $skip    | equivalent to find().skip(num)     |
| $count   | equivalent to find().count()       |

### Exercise - Max property price

Use the Compass aggregation builder to work with the sample_airbnb database and the listingsAndReviews collection and answer for these questions:

Scenario: The base price for the basic rental is $price that price includes the number of guests provided in <b> $guests_included </b> field. But a property may take more guests in total ($accommodates). Those extra guests pay an $extra_people cost per person for every person more than the number of guests included.
1. How many extra guests can each property have? Add that as a new field with $set
2. How much it would cost with these extra guests? Use $project to show the basic price and the maximum price if fully occupied with $accommodates people

Solution
1. Stage1 - $projection
```javascript
{
  name:1,
  price:1,
  guests_included:1,
  accommodates:1,
  extra_people:1
}
```
1. Stage2 -$addFields
```javascript
{
  num_guests_extra: {
    $subtract: [
      "$accommodates",
      "$guests_included",
    ],
  },
}
```
1. Stage3 - $set
```javascript
{
  extra_cost: {
    $multiply: [
      "$num_guests_extra",
      "$extra_people"
    ]
  }
}
```
1. Stage4 - $set
```javascript
{
  max_price: {
    $add: ["$price","$extra_cost"]
  }
}
```

If we add this as the first stage, then the query time it will be faster (from 36 ms to 20ms). It can be seen in 'explain' query in MongoDB Compass

First Stage - $match
```javascript
{
  "address.country": "United States"
}
```

## Group

Used with $group operator

### The $group stage
- Groups documents by a **group key**
- Each unique **group key** value represents one **group** 
- Output is **one document per group** 
- Additional fields can contain results of **accumulator expressions**


Stage1 - $project

```javascript
{
  address: 1,
  name: 1
}
```
Stage2 - $group

```javascript
{
  _id: "address.country",
  total: {
    $sum: 1
  }
}
```
Or
```javascript
{
  _id: "address.country",
  total: {
    $sum: 1
  }
}
```
