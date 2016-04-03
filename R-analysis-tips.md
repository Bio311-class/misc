Miscellaneous Analysis Tips
---------------------------

## Use the R Help

-   `help(function.name)` -- will bring up useful documentation about
    a function. e.g. `help(read.delim)`
-   `example(function.name)` -- will (usually) show some example applications of a function,
    often demonstrating how different argument can be used. e.g. `example(read.delim)`

## Reading a tab-delimited data file in R

    > chip <- read.delim("ChIPchip_Bio311_2016_FINAL.txt")
    > names(chip)
     [1] "Gene"                   "RosR_noH2O2.Gene"       "RosR_wH2O2_10m.Pk.pval" "RosR_wH2O2_20m.Pk.pval"
     [5] "RosR_wH2O2_60m.Pk.pval" "Idr1_noFe.Pk.pval"      "Idr1_100uM_Fe.Pk.pval"  "Idr2_noFe.Pk.pval"     
     [9] "Idr2_100uM_Fe.Pk.pval"  "TfbA.Pk.pval"           "TfbB.Pk.pval"           "TfbC.Pk.pval"          
    [13] "TfbD.Pk.pval"           "TfbE.Pk.pval"           "TfbF.Pk.pval"           "TfbG.Pk.pval"          
    [17] "TbpE.Pk.pval"           "TbpF.Pk.pval"           "TrmB.Pk.pval" 

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


## The apply function

Apply is a very useful tool for applying a function repeatedly to each row or column of a data frame.  The code below illustrates some uses of apply

```R
> df
           Cond1      Cond2      Cond3
geneA  0.8853070 -1.2363205  0.8518042
geneB -0.9164607  0.5998509 -0.7327142
geneC -0.4823146 -0.7570892 -1.2067572
geneD -1.1907350 -2.1571302  1.4457891
geneE -0.1351917  2.1888398  0.5818090

```