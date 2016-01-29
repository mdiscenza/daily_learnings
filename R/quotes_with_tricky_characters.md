If you're like me, you like to do things like run hive queries from R. I will have another post about how I do this without have to deal with the Thrift by just sshing in and running your commands in the master node and grabbing the results printed to stdout and reading them into R with the read.csv function.

In any case, when you're doing this, special characters require special attention. I struggled creating a string for a query that has regex with single quotes and slashes:

```sql
select * 
 from runadtag_logs_v2_b 
 where dt > 20151205 and dt < 20160129 and placement_id IN (80438, 80400)
 AND REGEXP_EXTRACT(querystring, 'R_UID\=([^\&]+)') IN (...

```
When you're making a string out out of this kind of query in R you have to make sure to do the following:
 * use single quotes
 * escape slashes with an extra slash
 * make quotes literal quotes in the string by using two quote (works for single and double quutes)

 The above query then becomes:

```r
query='
select * 
 from runadtag_logs_v2_b 
 where dt > 20151205 and dt < 20160129 and placement_id IN (80438, 80400)
 AND REGEXP_EXTRACT(querystring, ''R_UID\\=([^\\&]+)'') IN ( _user_ids_)
'
```

I can then use the `gsub` function to insert the string with made from the list of ids that I want to include in the query. 