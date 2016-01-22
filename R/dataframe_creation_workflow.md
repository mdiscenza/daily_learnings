##  Assembling R Dataframes from vectors

Making dataframes in R from various vectors is something that I do a lot.   There are also many ways to do this. 

Let's start with three sample vectors here:

```r
vector_1 <- c(1,2,3,4,5)
vector_2 <- c(5,4,3,2,1)
vector_3 <- c(3,2,5,1,7)
```


#### Method 1: cbind

One could for instance follow this workflow:

* insert vectors into this matrix with `cbind()`
* create a dataframe from the matrix

```r
df <- as.data.frame(cbind(vector_1, vector_2, vector_3)) 

```
Note that here, the dataframe takes the same column names of the vectors that we used to create it.

#### Method 2: by column name

Another way that we can do the same thing is creating a dataframe out of a one-dimensional matrix and then adding columns to it:

* create a dataframe with out of a matrix with the right number of rows, but now columns
* add vectors sequentially to the dataframe by name (add the first first, and later columns after)

```r
df2 <- data.frame(matrix(nrow = 5))
df2$v1 <- vector_1
df2$v2 <- vector_2
df2$v3 <- vector_3
```

This second method is better if the vectors that you're adding to the dataframe are not named in a way that you'd want in the resulting dataframe. This saves you the hassle of modifying the column names by setting elements of the `colnames()` after the dataframe is created.
