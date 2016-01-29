If you're like me, you like to do things like run hive queries from R. I will have another post about how I do this without have to deal with the Thrift by just sshing in and running your commands in the master node and grabbing the results printed to stdout and reading them into R with the read.csv function.

In any case, when you're doing this, special characters require special attention. I struggled creating a string for a query that has regex with single quotes and slashes:

```sql
select * 
from runadtag_logs_v2_b 
where dt > 20151205 and dt < 20160129 and placement_id IN (80438, 80400)
AND REGEXP_EXTRACT(querystring, 'R_UID\=([^\&]+)') IN (...

```
Ultimately, I was able to make this work by excaping the slashes with another slash but ended up having to use the `paste0` function to concatinate the single quote characters and the rest of the string together.  The `paste0` function is same thing as `paste` except that it combines strings with no space without having to override the default `sep=0`) It was a little hacky, but works:

```r
query=paste0('select * 
from runadtag_logs_v2_b 
where dt > 20151205 and dt < 20160129 and placement_id IN (80438, 80400)
AND REGEXP_EXTRACT(querystring,', "'", 'R_UID\\=([^\\&]+)', "'", ') IN (user_ids)'
)
```

I can then use the `gsub` function to insert the string with made from the list of ids that I want to include in the query. 