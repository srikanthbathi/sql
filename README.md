# sql

**UNION vs UNION ALL**

UNION merges the contents of two structurally-compatible tables into a single combined table. The difference between UNION and UNION ALL is that UNION will omit duplicate records whereas UNION ALL will include duplicate records.

It is important to note that the performance of UNION ALL will typically be better than UNION, since UNION requires the server to do the additional work of removing any duplicates. So, in cases where is is certain that there will not be any duplicates, or where having duplicates is not a problem, use of UNION ALL would be recommended for performance reasons.


In a small table, query engine can load all data in just one shot but in a large table, it's not possible, which means more IO and more time to process those data.

Normally, a full table scan is used when your query doesn't have a WHERE clause i.e. you want more or less every record from a table e.g. following query will use a full table scan:

Index Scan
If your table has a clustered index and you are firing a query which needs all or most of the rows i.e. query without WHERE or HAVING clause, then it uses an index scan. It works similar to the table scan, during the query optimization process, the query optimizer takes a look at the available index and chooses the best one, based on information provided in your joins and where clause, along with the statistical information database keeps.

Once the right index is chosen, SQL Query processor or engine navigates the tree structure to the point of data that matches your criteria and again extract only the records it needs. See SQL Performance Explained by Markus Winand to learn more about how indexes work in different databases.

CTE -- 
MongoDb rec -- > https://docs.mongodb.com/manual/reference/operator/aggregation/graphLookup/

https://www.youtube.com/watch?v=o1dMJ6-CKzU

https://use-the-index-luke.com/sql/where-clause/the-equals-operator/concatenated-keys
'

**nested-loop join, merge join, and hash join**


https://www.quora.com/How-do-indexes-work-on-JOIN-query
http://www.java67.com/2017/12/difference-between-table-scan-index.html

Read more: http://www.java67.com/2017/12/difference-between-table-scan-index.html#ixzz5W9Nr1ZJ3

Read more: http://www.java67.com/2017/12/difference-between-table-scan-index.html#ixzz5W9N5d7HY

https://www.toptal.com/sql/interview-questions

http://www.tech-recipes.com/rx/57894/sql-server-three-valued-logic-not-clause-null-values/

https://www.softwaretestinghelp.com/50-popular-sql-interview-questions-for-testers/


https://www.edureka.co/blog/interview-questions/sql-interview-questions


https://www.geeksforgeeks.org/sql-general-functions-nvl-nvl2-decode-coalesce-nullif-lnnvl-nanvl/

https://www.geeksforgeeks.org/sql-interview-questions-set-2/


**Delete Duplicate Rows** -- Employees(EmployeeID,EmployeeName,ManagerID) :
DELETE FROM Employees WHERE EmployeeID IN (SELECT * FROM (SELECT s.EmployeeID FROM Employees s JOIN (SELECT MIN(EmployeeID) as EmployeeID,EmployeeName,ManagerID FROM Employees GROUP BY EmployeeName,ManagerID HAVING COUNT(EmployeeId)>1) AS e ON s.EmployeeID != e.EmployeeID AND e.EmployeeName = s.EmployeeName AND e.ManagerID = s.ManagerID) AS ali);

**Org Hierarchy** -- Use Recursive CTE: 
SET @ID = 4;
WITH RECURSIVE EmplyeeCTE AS (SELECT * FROM Employees WHERE EmployeeID = @ID UNION ALL SELECT s.* FROM Employees s JOIN EmployeeCTE e ON e.ManagerID = s.EmployeeID) SELECT * FROM EmployeeCTE;

**MONGO DB** db.Employees.aggregate([{$match:{"name":"Ron"}},{
    $graphLookup:{
        from:"Employees",
        startWith:"$reportsTo",
        connectFromField:"reportsTo",
        connectToField:"name",
        as:"reportingHierarchy"
        }
    },{$project:{"name":1,"reportingHierarchy":"$reportingHierarchy.name"
        }}
    ])

**Level Of Employee** -- 
WITH RECURSIVE EmployeeCTE AS (SELECT EmployeeID,EmployeeName,1 AS level,ManagerID FROM Employees WHERE ManagerID IS NULL UNION ALL SELECT s.EmployeeID,s.EmployeeName,e.level+1,s.ManagerID AS level FROM EmployeeCTE e JOIN Employees s ON e.EmployeeID = s.ManagerID) SELECT * FROM EmployeeCTE;

**IMPORTANT FOR UPDATE TABLES** 

https://stackoverflow.com/questions/45494/mysql-error-1093-cant-specify-target-table-for-update-in-from-clause
https://mariadb.com/kb/en/library/derived-table-merge-optimization/
https://mariadb.com/kb/en/library/why-is-order-by-in-a-from-subquery-ignored/
https://www.xaprb.com/blog/2006/06/23/how-to-select-from-an-update-target-in-mysql/
If you do this:

DELETE FROM story_category
WHERE category_id NOT IN (
        SELECT DISTINCT category.id AS cid FROM category 
        INNER JOIN story_category ON category_id=category.id
)
you are going to get an error.

But if you wrap the condition in one more select:

DELETE FROM story_category
WHERE category_id NOT IN (
    SELECT cid FROM (
        SELECT DISTINCT category.id AS cid FROM category 
        INNER JOIN story_category ON category_id=category.id
    ) AS c
)
it would do the right thing!!

Explanation: The query optimizer does a derived merge optimization for the first query (which causes it to fail with the error), but the second query doesn't qualify for the derived merge optimization. Hence the optimizer is forced to execute the subquery first.




