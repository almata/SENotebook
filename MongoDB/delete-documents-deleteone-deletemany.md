# Delete documents: deleteOne() & deleteMany()

Delete the first document matching criteria:

```javascript 
> db.movies.deleteOne({year: 1959})
```

Delete all documents matching criteria:

```javascript 
> db.movies.deleteMany({year: 1959})
```
