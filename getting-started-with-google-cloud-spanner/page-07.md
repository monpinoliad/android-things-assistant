## 7. Run your first queries

Now that we've set up our database and loaded information into a table, let's run some simple queries. First, let's verify the values we just wrote to our new table. Select the demo database and then the query tool:

![](https://codelabs.developers.google.com/codelabs/cloud-spanner-first-steps-java/img/1bd14714fdc145b8.png)

Then enter the SQL query `SELECT * FROM Songs;` and click the "Run query" button.

This should show all records we wrote in our simple app.

Now imagine we want to ask the following three questions:

1. How many singles did the Beatles release in the US?
2. How many number one singles did the Beatles release in the US? And what were the titles of those top sellers?
3. In which year did the Beatles release the most US singles and the most number one singles?

We'll answer each of these questions using SQL queries submitted via the Cloud Spanner query tool on the Cloud Console.

To determine the total number of singles released by The Beatles in the US, we simply need to count the number of rows in the `songs` table (since all the songs we wrote into the database are from the Beatles).

Then paste and run this query into the query tool:

```
SELECT count(*) FROM Songs;
```

![](https://codelabs.developers.google.com/codelabs/cloud-spanner-first-steps-java/img/e5316dc0edc108d3.png)

This query tells us The Beatles released 44 singles in the US.

For our next query, we'll count the number of singles released in the US, which rose to the top position on the charts. This is easy to do by extending the query with a "where" clause, like this:

Paste and run this query into the query tool: `SELECT COUNT(*) FROM Songs WHERE peak = 1;`

![](https://codelabs.developers.google.com/codelabs/cloud-spanner-first-steps-java/img/b721b772511c61bb.png)

This query reveals 19 singles rose to the top position on the US charts. If we wanted to identify those top selling singles, we need only replace "`COUNT(*)`" with "`Title,year`", like this:

![](https://codelabs.developers.google.com/codelabs/cloud-spanner-first-steps-java/img/40194ec5eae80f1c.png)

Finally, let's determine the year in which The Beatles released the most singles in the US:

```
SELECT year, count(*) AS releases
FROM Songs
GROUP BY year
ORDER BY COUNT(*) DESC
LIMIT 1;
```

![](https://codelabs.developers.google.com/codelabs/cloud-spanner-first-steps-java/img/42c0997c1fa59366.png)

This shows us that 1964 was a very productive year for The Beatles, as they released 12 singles into the US market, the most of any year in their career. If we limit the query to include only number one hits and remove the limit clause to display all results (not just the top result), we get:

```
SELECT year, count(*) AS hits
FROM Songs
WHERE peak = 1
GROUP BY year
ORDER BY COUNT(*) DESC;
```

![](https://codelabs.developers.google.com/codelabs/cloud-spanner-first-steps-java/img/4cf4b27201b9c947.png)

This query shows us that 1965 was the year in which The Beatles enjoyed their most chart toppers (5), along with the number of top hits for all the other years in which they released singles in the United States.
