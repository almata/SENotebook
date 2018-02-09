# Get element's position in array given a key-value pair

Say we have an array of questions, each of which are objects with this structure:

```javascript
{
    id: 0,
    type: 'datastructure',
    category: 'Array',
    concept: 'Average Access Time',
    complexity: 'Θ(1)'
}
```

And say elements in the array are ordered, such that object at position 0 has `id: 0`, object at position 1 has `id: 1`, etc.

Then if we need to know the index in the array for element with `id: 42` it's pretty obvious that it's just 42. But if we don't want to rely on objects being ordered (or if we want to search using a different key-value pair), the way to get that index is as follows:

```javascript
const index = allQuestions.map(q => q.id).indexOf(id);
```

That is, we use `map()` to specify which key-value pair we will be taking into account and then `indexOf()` to find the index for our element.
