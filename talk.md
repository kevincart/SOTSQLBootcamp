Hiya, so welcome to your summer of tech 2016 Databases.

For starters I work for Microsoft on the Azure Batch team where we work on a product called Azure Batch. Where we enable large-scale parallel and high performance computing applications in the cloud. I have a design and computer science degree from VUW and managed to enter Microsoft through the Summer of Tech program.

First I would like to ask the fun question who in the room has worked with any form of relational databases or SQL before?

Yes I’m sure there are a few people in the room who know a bit about SQL, I’ll try and share some information you probably don’t know about databases.

I would say a majority of software of application uses some type of database. The most common storage mechanism would be a relational database or some type of flat storage database. When we say relational we normally talk about the big ones MySQL, PostgreSQL or microsoftSQL.

To describe a basic application we have a user or some type of input interacting with an application. That application records and responds to the user input based on the application. Events and information is recorded and processed. Those events or data is written somewhere. 

Most of the big programming languages support using native SQL statements where you can write SQL statements and query the database that way. Or we can use an Object Relational Mapper or an ORM as they are often called. DotNet has the Entity Framework, Ruby on Rails has DataMapper or ActiveRecord. Basically an ORM is used for describing entities inside a table.  You write a class such as Student and a student contains values such as ID, Name, Start year. That Object can be modified on the fly and then the object can get saved to a database when the appropriate time is called for.

So we won’t be looking at ORMs today but I highly recommend finding out more about them. We’ll be covering the underlining language that is used for accessing, creating and dealing with databases Called SQL.

So for starters I would like to thank the father of relational database Edgar F. Codd. Around the 1970s when he was working for IBM came up with the concept of relational database because he was unhappy with the lack of searching 
databases. 

Basically he came up was this idea of a "relation" which was basically a set of tuples, like a collection of smaller arrays of related data, where those smaller collections all had a common set of attributes.  Which is where we can start talking about foreign keys.


The example we're going to be using today is a grades database, records a student and a course and a grades.

So in a modern database the concept of the "set of values" sort of looks like this, which we call a table, and then in the table you have columns which define those attributes, then going across we have individual rows that represent sort of separate entity in this table in each row

The key part of this table is the primary key. The concept of a primary key is very common across all types of storage mechanism. It basically says I can’t have another entity in my database that has the same primary key. We use also use primary key for doing look ups to if we wanted to ask the question “what did Frank get for Comp 102”.

But this is sort of not a very good model we've developed here, because there's a bunch of duplication in our table, like, half these values are actually just there to describe an individual student, which is sort of crazy redundant and also really uh fragile right, because it's like, we have to make sure that all of these values are always in sync with one another


and so if we have to update one of these rows then we have to update all of them at the same time just to make sure we aren't going to have anything inconsistent going on -- and so the way we describe this data is labelling it "denormalised"

so codd and some other people back in the day basically wanted to remove this redundance in your models, and they came up with these things called "normal forms" which basically can be used to describe how "normalised" the data is

so this is currently in the first normal form, which is sort of the lowest possible level of normalisation -- and so in general what that means is that our relations aren't quite cohesive enough, and we're going to have to do a little uh refactoring i guess to split some of this data out

and so what we can do is derive two tables here, one that describes students and another one that describes grades -- and we can see that they have this little relation in here that basically joins these two together, and we call that a "foreign key" relation

and so you can see that this is called foreign key because the target columns over here basically have to be the primary key, because that's what we have to use to uniquely identify basically the student each of these grades is associated with
	
but now that we've split these rows into two different tables if we want to be able to do something with information from either table that doesn't appear in the intersecting columns, like, find all the grades for a student by name or something then we need to be able to join these two tables back together

So we use Joins are basically just set operations, and so when we want to join these two tables back together based on the student id thats called an inner join -- and so when you look at what that looks like, it's literally just the intersection of the two sets

and so all of the join operations basically just turn out to be these set operations between the relations, so like there's a thing called left outer join, which in our case would take the all the rows from the students table, and append all the grades that they have in the other table, if they exist, and so that just ends up looking like this basically

and so we basically have this really elegant model that codd came up with way back in you know the 70s or whatever that basically allows us to store data in a consistent way, with very little redundancy or duplication, and then operate over that data in a really pleasant way, which is like, pretty amazing when you think about all the other sort of major paradigm shifts that have occurred in computing over that period.

all relational models follow these principals called ACID which are basically some guarantees around how the database will behave in these sort of annoyingly wrinkly edge cases


the first one is atomicity -- and so the idea here is that an atom is supposed to be like, the smallest indivisible element in a system right -- and so the idea of having an atomic database basically says that transactions within the database must be atomic. either the whole thing succeeds or the whole thing fails. and so this kind of guarantee helps us out a lot with that issue of what happens if the system crashes underneath us right, because we now have this guarantee that we still have referential integrity because we know no writes can sort of just be half done if that makes sense

the next one is consistency and its quite closely related, but it basically says that if you make some assertions about what a consistent state is in your database with like primary keys for uniqueness, and foreign keys for that referential integrity, then this principle basically says you can only ever transition from consistent states to new consistent states.

so if you had two writes, one that inserted a grade and another that inserted a student, its not possible for you to insert that grade before the student exists, and likewise you can't delete a student when they still have grades, unless you explicitly make that delete cascade back to the referring entities

the next one is isolation -- and this is basically aimed at that multi-user problem i was talking about, where the idea is that all operations in a database need to be able to be made serializable so that broadly means that you can always put things into the right order so that two transactions running at the same time can interact with one another

and finally the last one is about durability, and this is a key one for a persistent data store, but it basically says that any acknowledged write in the database needs to end up on disk -- so it should be impossible for example for me to update a record in memory, have the system completely crash, then be unable to retrieve that updated row from the database when i restart

and so those four principles make some pretty strong assertions about the consistency of the database basically, and that helps us as developers of applications basically in knowing that our data we're working on is consistent.



Some of the big challenges has come from has been limitation on the physical machine the database is located over. So we introduce CAP Theorem. That basically takes the concept of instead of buying a bigger machine to host your database on we start distributing your database across multiple machines. This brings in a huge amount of new challenges and this area is extremely fascinating that I could probably do another talk on this subject but the basic concept is. 

Consistency, Availability and Partial Tolerance.  You have to identify the problem you are currently solving and slot that problem into the triangle.  Case in point:
story from GreenButton:

