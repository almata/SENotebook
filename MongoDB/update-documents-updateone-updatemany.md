# Update documents: updateOne() & updateMany()

Update one document in `movies` collection in current database:

```javascript
> db.movies.updateOne({title: "The Martian"}, {$set: {poster: "#some valid URL here#.jpg"}})
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }
```

The `{title: "The Martian"}` part is the filter to know which document to update. If this filter matches more than one document, only the first one will be updated. If we want to update all of them we use `updateMany()` instead:

```javascript
> db.movies.updateMany({rated: null}, {$unset: {rated: ""}})
{ "acknowledged" : true, "matchedCount" : 1599, "modifiedCount" : 1599 }
```

We use `$set` operator to... yes, set a field, `$unset` to delete a field, `$rename` to rename a field, etc. A list of update operators can be found at https://docs.mongodb.com/manual/reference/operator/update.

To add an item to an existing array field:

```javascript
> db.movies.updateOne({title: "The Martian"}, {$push: {reviews: {rating: 4.5, reviewer: "Albert Mata"}}})
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }
```

But if we want to add many items to an existing array field we have to use `$each` this way: `reviews: {$each: [{rating:... }]}` so each element in our array gets inserted as an element in `reviews` array. Otherwise, our array would be added as a single element in `reviews`.
