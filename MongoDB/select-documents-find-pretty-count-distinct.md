# Select documents: find(), pretty(), count(), distinct() 

We use `find()` to get all fields from all documents (and `pretty()` to visually improve the result):

```javascript
> db.movies.find().pretty()
```

We use projections when we want to retrieve only some fields. In the following code, first query returns `_id` and `title`, as `_id` is always returned unless explicitly excluded. Second query returns only `title` as `_id` has been explicitly excluded. Third query returns all fields except the three explicitly excluded.

```javascript
> db.movies.find({}, {title: 1})
> db.movies.find({}, {title: 1, _id: 0})
> db.movies.find({}, {_id: 0, cast: 0, plot: 0})
```

We use `distinct()` to get all distinct values for a field in a collection:

```javascript
> db.movies.distinct("year")
```

We specify filtering criteria using standard JSON notation:

```javascript
> db.movies.find({mpaaRating: "PG-13", year: 2009}).pretty()
```

When dealing with arrays (in this example, the `cast` field), we may want a total coincidence (all elements in the exact same order):

```javascript
> db.movies.find({cast: ["Jeff Bridges", "Tim Robbins"]})
```

Or we may want to be sure the array contains one specific element (maybe among others):

```javascript
> db.movies.find({cast: "Jeff Bridges"})
```

We could also want the array to contain one specific element in one exact position (again, we don't care about other elements in the array):

```javascript
> db.movies.find({"cast.0": "Jeff Bridges"})
```

Some examples of filters using operators:

```javascript
{"birth year": {$gte: 1965, $lt: 1970}}
{coordinates: {$geoWithin: {$centerSphere: [[-117.1747, 52.6871], 0.6951]}}}
```

Finally, we use `count()` to know how many documents match all our filters:

```javascript
> db.movies.find({"awards.wins": 2, "awards.nominations": 6}).count()
```
