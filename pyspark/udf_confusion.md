Creating user defined functions (UDFs) in spark has proven to be a bit of a challenge for me. I leaned that there are two ways to create these functions. 

## SQL UDFs

The first way to create a UDF is better documented -- to a point where it took me a long time to figure out how to do the second. Here you creat function and then give you use the `sqlContext.registerFunction()` to register it.

```python
from pyspark.sql.functions import udf

def get_hour_of_day(tstamp):
  return (datetime.fromtimestamp(float(tstamp)).hour)

sqlContext.registerFunction("hour", get_hour_of_day, IntegerType())
```

Here we define the function and then pass it as one of three arguments that the `registerFunction` function takes-- those three being the name we want to call that UDF, the name of the function we defined that will be run, and the return type. We have to specify the return type because even though python is dynamically typed, Spark is Java based.

If we wanted to save time and not define a conventional python functionusing `def` because, in this case the function is pretty short and simple, we could define the function body and pass call `registerFunction` in the same line or call using lambda notation -- just defining the function inline.

```python
sqlContext.registerFunction("hour", 
                            lambda x: datetime.fromtimestamp(float(x)).hour,
                            IntegerType())
```

Once we define the function, we can use it as follows on a dataframe of impression (online ad impressions), called "imps."

```python
imps.registerTempTable("imps")
sqlContext.sql("SELECT tstamp, day(tstamp) as day FROM imps").head()
```

Notice that we had to register our dataframe to use it in the SQL context. Then we can use the UDF as we would in pretty much any SQL in the select statement. We call head so that Spark executes the command and we can see the result.

At this point, we can only use the UDF in the select statement here, not on as a function on a typical dataframe operation. If you would prefer to chain function together to transform your dataframes instead of writing long SQL statements, then there is another approach

## Dataframe UDFs

If we want to pass columns to a UDF in outside of the SQL context, then we need to use another function in pyspark to register these functions.  

```python
from pyspark import UserDefinedFunction

day = UserDefinedFunction(lambda x: datetime.fromtimestamp(float(x)).weekday(), IntegerType())
```

Then we can make the following function call:

```python
imps.select("tstamp").withColumn("day_of_week", day("tstamp")).head()
```