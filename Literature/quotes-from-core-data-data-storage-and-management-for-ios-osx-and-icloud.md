# Quotes from _Core Data. Data Storage and Management for iOS, OS X, and iCloud_

I've just finished reading the book _Core Data. Data Storage and Management for iOS, OS X, and iCloud_ by Marcus Zarra (The Pragmatic Programmers, 2013). And it's a really remarkable book. It's that kind of book where you read a paragraph and you think "oh, this makes a lot of sense now, I finally understand this... but then, what about the situation when this and that happen?" And you keep reading and immediately find the answers for the questions you've just thought about. Really, this book is that good.

As the only small negative thing to say, I feel most developers (specially iOS developers) can totally skip the last three chapters. But the first eight ones and the two appendixes are pure gold.

More information: [http://pragprog.com/book/mzcd2/core-data][1]

[1]: http://pragprog.com/book/mzcd2/core-data

> Although it is easy to think of Core Data as a database API, we must remember that it is not and that structuring the data with data normalization may not yield the most efficient results. In many cases, denormalizing the data can yield greater performance gains.
> [...]
> Although the most powerful persistent store available for Core Data is a database, we must always be conscious of the fact that Core Data is not just a database. Core Data is an object hierarchy that can be persisted to a database format. The difference is subtle but important. Core Data is first a collection of objects that we use to display data in a user interface of some form and allow the user to access that data. Therefore, although database normalization might be the first place to look for performance improvements, we should not take it too far. There are six levels of database normalization, but a Core Data repository should rarely, if ever, be taken beyond the second level. There are several cases where we can gain a greater performance benefit by denormalizing the data.

> Every time an NSFetchRequest is executed, it hits the disk. This is an important point to keep in mind when we are working with NSManagedObject objects. If we are doing joins, adding objects to a relationship, and so on, it might seem easier and cleaner to perform an NSFetchRequest to check whether the object is already in the relationship or check for a similar function, but that action can hurt performance significantly. Even if we have all the relevant objects in memory, an NSFetchRequest is still going to hit the disk. It is far more efficient for us to use that NSPredicate against a collection that is already in memory.

> Core Data has several logging levels that we can turn on to watch all of the SQL calls that are generated during an application's life cycle. There are currently three levels to this debugging log, with level 1 being the least chatty and level 3 being the most chatty. We can set this debug output by adding the runtime parameter com.apple.CoreData.SQLDebug to our application and pass along with it the level we want to be set.
>
> In addition to the Core Data debug setting, we can also turn on an additional setting to watch all of the chatter between Core Data and iCloud. That additional setting is com.apple.coredata.ubiquity.logLevel. For an unknown reason, this logging code responds only to level 3.