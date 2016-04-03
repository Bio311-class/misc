

## Packages

* Install graph package

```{r}
source("https://bioconductor.org/biocLite.R")
biocLite("graph")
```

* Install Rgraphviz package

```{r}
biocLite("Rgraphviz")
```

* Install RBGL

```{r}
biocLite("RBGL")


## Load and explore the data

```{r}
lee <- read.csv("lee-etal-data.csv")
```

* Confirm understanding of data structure

```{r}
dim(lee)
names(lee)
lee$MATA1[1:15]  # first 15 elems of MATA1 column
```

* Copy data and remove some columns from table

```{r}
tlee <- lee
tlee$Common.Name <- NULL
tlee$Annotation <- NULL
```

* How you would find all the targets of a given TF

```{r}
tlee$MATA1[1:10]
```

We want to filter out all edges below a certain p-value (Lee et al. used p = 0.001 as cutoff)

```{r}
tlee$Name[tlee$MATA < 0.001]
```

However we also need to deal with the NA case

```{r}
mata.targets <- tlee$Name[(tlee$MATA < 0.001) & !is.na(tlee$MATA)]
mata.targets
```

If we wanted the targets as strings, rather than "factors"
```{r}
as.character(mata.targets)
```

* How do you find all the targets of all the TFs

Let's generalize the above steps with a function

First, write fxn to get targets of 1 TF
```{r}
get.targets <- function(tf, names, pval=0.001){
  targets <- names[(tf < pval) & !is.na(tf)]
  return(as.character(targets))
}
```

Test that function:

```{r}
get.targets(tlee$MATA1, tlee$Name)
```


Now write function to loop over all tfs

```{r}
get.all.targets <- function(tfs, names, pval=0.001){
  target.list <- list()
  tf.names <- names(tfs)
  for(tf in tf.names){
    target.list[[tf]] <- get.targets(tfs[[tf]], names, pval)
  }
  return(target.list)
}
```

Test and debug that new fxn:

```{r}
tft <- get.all.targets(tlee[,2:ncol(tlee)], tlee$Name)
```

```
length(tft)
```

```
tft
```

Get counts for each TF using `lapply` fxn:

```
cts <- lapply(tft, length)
cts
```

Use `unlist` to simplify TF counts list:

```
unlist(cts)
```

```
tf.cts <- unlist(cts)
hist(tf.cts,breaks=seq(0,400,20), xlab="Number of Promoter Regions Bound", ylab="Number of Regulators", main="TF Target Distn")


# Let's turn this into a graph structure


```
tf.names <- names(tlee)[2:length(names(tlee))]
```



```
from.list <- function(tft){
  from <- c()
  tf.names <- names(tft)
  for(tf in tf.names){
    tf.from <- rep(tf, length(tft[[tf]]))
    from <- c(from, tf.from)
  }
  return(list(from=from))
}
```

```
to.list <- function(tft){
  to <- c()
  for(tf in names(tft)){
    to <- c(to, tft[[tf]])
  }
  return(list(to=to))
}
```

Create data frame to pass the graphBAM

```
fl <- from.list(tft)
tl <- to.list(tft)
len(fl)
length(fl)
bam <- data.frame(fl, tl)
names(bam)
bam$weight <- rep(1, length(fl))
```

Create graph
```
g <- graphBAM(bam, "directed", ignore_dup_edges = TRUE)
```