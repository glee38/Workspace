#Table Relations
##Relational Databases

Let's say that you've been hired by a big and important company to do the payroll for all of their employees. We'll call it MyFace (inspired by nothing in particular). Every two weeks, you need to look up each and every employee and how much they get paid, and send them a check *and* send a notice of that check to their manager (managers, after all, should know when their employees are getting paid).

In addition, let's say that managers get paid every month, instead of every two weeks. So, every month we need to go through the spreadsheet again and find *just the managers* this time, and send them *their* checks. In such a situation, we would need a place to store all of the managers and employees.

Using a spreadsheet, your storage system might look something like this:

![storage](http://readme-pics.s3.amazonaws.com/Screen%20Shot%202015-09-03%20at%205.12.12%20PM.png)

So every two weeks, we would have to look through every single entry in this spreadsheet, send each person their check and then, figure out a way to identify an employee's manager to send that manager a confirmation that each employee has been paid. We need some way to *associate* the employees to their manager. We could add a "Manager" column to the spreadsheet that would be filled out with the name of that person's manager (if that person is an employee and not a manager themselves). This is getting messy. Not only do we have to do a lot of searching through the spreadsheet and manual detection of who is an employee and who is a manager, but we also have to match each employee with the name of their manager. If only there was some way to simplify our system!

Enter relational databases. A relation database, simply put, is **a database structured to recognize relations among stored items of information**. In such a system, it would be easy to tell an employee that they *belong to* a certain manager and to tell a manager that they *have many* employees. This might sound familiar if you've built object oriented Ruby programs in which instances of a class are related to one another. A relational database will allow us to store representations of our Ruby objects and preserve the relationships between those objects when we store them.

**RELATIONAL DATABASE STRUCTURE**

Continuing with our payroll example from earlier, employees and managers would be stored in their own tables. A table is like a spreadsheet, it has columns and rows.

Our managers table would look something like this:

![manageers](http://readme-pics.s3.amazonaws.com/Screen%20Shot%202015-09-04%20at%2011.17.31%20AM.png)

And our employees table would look something like this:

![employees](http://readme-pics.s3.amazonaws.com/Screen%20Shot%202015-09-04%20at%2011.17.25%20AM.png)

Our employees table has a "Manager ID" column, filled with the ID number of that person's manager. In a relational database, every row has a number, called a **primary key**. Relationships between tables can be established by using a **foreign key** column, like our "Manager ID" column, that uses that primary key of another table to refer to a member of that table.

Why should our foreign key, our point of reference between an employee and his or her manager, be a number? Why not just use the manager's name? Well, names are very rarely unique. What if MyFace hires a new manager, also named Steve? It's a popular name, after all. How would our database know *which* Steve manages which employees. Primary keys, on the other hand, **are always unique**!

Additionally, what if Steve gets sick of sharing his name with all of the other Steves out there and decides to change his name to Brittany? We would have to look up every single employee that had Steve as a manager and change a "Manager Name" column to "Brittany". We are programmers, we're lazy, we like to code for the future and our databases are no exception. If we use primary keys, i.e. numbers that never change and always refer to the same manager, our database can accommodate something like a manager's name change with ease.