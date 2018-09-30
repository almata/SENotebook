# Insert documents: insertOne() & insertMany()

Insert one document into `movies` collection in current database:

```javascript 
> db.movies.insertOne({title: "Some Like It Hot", year: 1959, imdb: "tt0053291"})
{ "acknowledged" : true, "insertedId" : ObjectId("5baf4e87509662ff4c27f123") }
```

Insert many documents into `movies` collection in current database:

```javascript
> db.movies.insertMany([{#JSON for movie 1#}, ..., {#JSON for movie N#}])
```

But the moment the code above finds any problem (i.e. a duplicated `_id`), no more documents are inserted into the collection. If we want to insert all documents (except the ones generating errors, of course), we can add `{ordered: false}`:

```javascript
> db.movies.insertMany([{#JSON for movie 1#}, ..., {#JSON for movie N#}], {ordered: false})
```
