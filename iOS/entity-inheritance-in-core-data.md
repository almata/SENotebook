# Entity inheritance in Core Data

When using entity inheritance in databases there are two main possible implementations. Assuming this structure:

```
ParentEntity <att1, att2>
FirstSubentity <att3, att4>
SecondSubentity <att5, att6>    
```

First implementation in tables consists in creating one table for each entity, as follows:

```
ParentEntity <id, att1, att2>
FirstSubentity <id, idParent, att3, att4>
SecondSubentity <id, idParent, att5, att6>
```

And second implementation consists in flattening the structure, creating one large table that includes all the properties for the parent entity as well as its children:

```
SingleEntity <id, att1, att2, att3, att4, att5, att6>
```

First approach doesn't waste space, but it needs join operations every single time all the information for FirstSubentity or SecondSubentity is requested. Second approach doesn't need any join, but it wastes a lot of space as it leads to a wide and tall table with many NULL values.

Core Data uses the second approach. It's good to keep that in mind and avoid treating entity inheritance as an equal to object inheritance, as that could cause a performance penalty.
