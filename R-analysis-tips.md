---
title: Miscellaneous R Tips
...

### Use the R Help

-   `help(function.name)` -- will bring up useful documentation about
    a function. e.g. `help(read.delim)`
-   `example(function.name)` -- will (usually) show some example applications of a function,
    often demonstrating how different argument can be used. e.g. `example(read.delim)`

### Reading a tab-delimited data file in R

```R
> chip <- read.delim("ChIPchip_Bio311_2016_FINAL.txt")
> names(chip)
[1] "Gene"                   "RosR_noH2O2.Gene"       "RosR_wH2O2_10m.Pk.pval" "RosR_wH2O2_20m.Pk.pval"
[5] "RosR_wH2O2_60m.Pk.pval" "Idr1_noFe.Pk.pval"      "Idr1_100uM_Fe.Pk.pval"  "Idr2_noFe.Pk.pval"     
[9] "Idr2_100uM_Fe.Pk.pval"  "TfbA.Pk.pval"           "TfbB.Pk.pval"           "TfbC.Pk.pval"          
[13] "TfbD.Pk.pval"           "TfbE.Pk.pval"           "TfbF.Pk.pval"           "TfbG.Pk.pval"          
[17] "TbpE.Pk.pval"           "TbpF.Pk.pval"           "TrmB.Pk.pval" 
```

### Using the subset function

The R `subset` function is very powerful way to identify patterns of interest.  Below is an example where I've got a Data Frame with genes in rows and conditions in columns.  

```R
# let's create an example data frame
> genes <- c("geneA", "geneB", "geneC", "geneD", "geneE")
> cond1 <- rnorm(5)  # some random data
> cond2 <- rnorm(5)
> cond3 <- rnorm(5)
> df <- data.frame(row.names = genes, Cond1 = cond1, Cond2 = cond2, Cond3 = cond3) 
> df
           Cond1      Cond2      Cond3
geneA  0.8853070 -1.2363205  0.8518042
geneB -0.9164607  0.5998509 -0.7327142
geneC -0.4823146 -0.7570892 -1.2067572
geneD -1.1907350 -2.1571302  1.4457891
geneE -0.1351917  2.1888398  0.5818090

# find all the genes that are more highly expressed in Cond2 than Cond1
> subset(df, Cond1 < Cond2)
           Cond1     Cond2      Cond3
geneB -0.9164607 0.5998509 -0.7327142
geneE -0.1351917 2.1888398  0.5818090


# find all the genes where the absolute value in condition one is > 0.5
> subset(df, abs(Cond1) > 0.5)
           Cond1      Cond2      Cond3
geneA  0.8853070 -1.2363205  0.8518042
geneB -0.9164607  0.5998509 -0.7327142
geneD -1.1907350 -2.1571302  1.4457891
```


### The apply function

Apply is a very useful tool for applying a function repeatedly to each row or column of a data frame.  The code below illustrates some uses of apply.

```R
> help(apply) # read the docs first

> df <- data.frame(row.names=c("geneA","geneB","geneC","geneD","geneE"),
+ Cond1 = rnorm(5), Cond2 = rnorm(5), Cond3=rnorm(5))
> df
           Cond1      Cond2      Cond3
geneA -1.3684561  0.1611493 -0.7708281
geneB  2.5529015  0.5175765  1.1864961
geneC -1.5558155  0.7844484 -2.3073629
geneD -0.4565807 -2.0420910  2.0442841
geneE  0.7138849 -1.0405729 -1.2464061

> apply(df, 1, mean)  # row means
     geneA      geneB      geneC      geneD      geneE 
-0.6593783  1.4189914 -1.0262433 -0.1514625 -0.5243647 

> apply(df, 2, mean) # column means
      Cond1       Cond2       Cond3 
-0.02281316 -0.32389794 -0.21876338 
```

### Reordering rows or columns of a Data Frame

Sometimes it's useful to be re-order the rows or columns of a Data Frame according to some criteria.  For example, if you do a cluster analysis, there's an implied ordering of the genes given by the clustering.  If you want to create a derived Data Frame with the ordering given by the cluster analysis you could do something like the following:

```R
# calculate distance matrix, assuming genes were in rows of data frame
> gene.dist <- as.dist(1-cor(t(df), use="pairwise.complete.obs"))

# carry out average linkage clustering
> gene.clust <- hclust(gene.dist, method="average")

# the ordering of genes according the clustering
> gene.clust$order
[1] 1 3 4 2 5

# create a new data frame with the rows ordered as given by the clustering
# note that in the df[something, ]
# the comma is important because it implies indexing by rows
# if instead you did df[something] (no comma) that would be indexing by columns

> df[gene.clust$order, ]
           Cond1      Cond2      Cond3
geneA -1.3684561  0.1611493 -0.7708281
geneC -1.5558155  0.7844484 -2.3073629
geneD -0.4565807 -2.0420910  2.0442841
geneB  2.5529015  0.5175765  1.1864961
geneE  0.7138849 -1.0405729 -1.2464061
```

### Determining genes that are differentially expressed from WT, unstressed in Halo data

In the Halobacterium salinarum gene expression data set you're working with, you've only been given a single replicate of each experimental condition.  However, there are multiple experiments that correspond to "unstressed, wildtype" conditions. For the purposes of the epxression analyses, you can consider the following experiments as
"unstressed wildtype" conditions.

* -40min_H2O2_ura3
* -20min_H2O2_ura3
* ura3_0.448
* ura3_0uM_Fe_20min	
* ura3_0uM_Fe_40min	
* ura3_0uM_Fe_60min
    
We can use these data to estimate the distribution 



