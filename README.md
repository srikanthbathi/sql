# sql

**UNION vs UNION ALL**

UNION merges the contents of two structurally-compatible tables into a single combined table. The difference between UNION and UNION ALL is that UNION will omit duplicate records whereas UNION ALL will include duplicate records.

It is important to note that the performance of UNION ALL will typically be better than UNION, since UNION requires the server to do the additional work of removing any duplicates. So, in cases where is is certain that there will not be any duplicates, or where having duplicates is not a problem, use of UNION ALL would be recommended for performance reasons.

https://www.toptal.com/sql/interview-questions

http://www.tech-recipes.com/rx/57894/sql-server-three-valued-logic-not-clause-null-values/


https://www.edureka.co/blog/interview-questions/sql-interview-questions
