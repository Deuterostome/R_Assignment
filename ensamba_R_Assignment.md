---
title: "ensamba_R-assignment"
author: "Emmanuel Nsamba"
date: "2.23.17"
output:
  html_document: default
  pdf_document: default
---

```{r set up, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```
###Uploading files and convert it to dyplyer for efficient analysis
  **-fang_et_al_genotypes**
```{}
> fang_et_al_genotypes.txt <- read.table("https://raw.githubusercontent.com/EEOB-BioData/BCB546X-Fall2017/master/UNIX_Assignment/fang_et_al_genotypes.txt", header = TRUE)
> View(fang_et_al_genotypes.txt)
```{r}

```

library (dplyer)
fang_et_al_genotypes.txt_df<- tbl_df(fang_et_al_genotypes.txt)
View(fang_et_al_genotypes.txt_df)

```
 **snp_position.txt**
 
```{r}
snp_position.txt <- read.delim("https://raw.githubusercontent.com/EEOB-BioData/BCB546X-Fall2017/master/UNIX_Assignment/snp_position.txt", header = TRUE)
 View(snp_position.txt)
snp_position_df <- tbl_df(snp_position.txt)
View(snp_position_df)

```
#Data Inspection:
*fang_et_al_genotypes.txt`:*

File size: 10.5 MB

Structure:
```{r}
str(fang_et_al_genotypes.txt_df)
```

* It is a data frame with 2782 observations and 986 variables total


Number of columns and rows:
```{r}
ncol(fang_et_al_genotypes.txt_df)      #986 columns
nrow(fang_et_al_genotypes.txt_df)      #2782 rows
```
dim(fang_et_al_genotypes.txt_df):
```{}
2782  986
```

Names of each variable:
```{r}
names_genotype <- names(fang_et_al_genotypes.txt_df)
names_genotype
```

Summary:
```{r}
genotype_summary <- summary(fang_et_al_genotypes.txt_df)
genotype_summary
```
typeof(fang_et_al_genotypes.txt):
```{}
[1] "list"
```
class(fang_et_al_genotypes.txt):
[1] "data.frame"

> is.data.frame(fang_et_al_genotypes.txt)
[1] TRUE
```{}
````
*`snp_position.txt`*

File size: 80.8 KB

Structure: 
```{r}
str(snp_position_df)
```

* data frame with 983 observations of 15 variables 

Number of rows and columns: 
```{r}
rows_snp_position_df <- nrow(snp_position_df) # 983 rows
columns_snp_position_df <- ncol(snp_position_df) # 15 columns 
```
dim(snp_position_df):
```{}
[983  15]
```
Names of each variable:
```{r}
names_snps <- names(snp_position_df)
names_snps
```

Summary:
```{r}
snp_summary <- summary(snp_position_df)
snp_summary
```
# Data Proccessing 
###Extracting specific Maize and Teosinte IDs

* First, separate out the groups of interest we want for maize (ZMMIL, ZMMLR, and ZMMMR) and teosinte (ZMPBA, ZMPIL, and ZMPJA) from `fang_et_al_genotypes.txt_df`:

```{r, message=FALSE}
library(dplyr)
```
**MAIZE SNP IDS:**
```{}
Maize_SNP_ids <- filter(fang_et_al_genotypes.txt_df, Group == "ZMMIL" | Group == "ZMMLR" | Group == "ZMMMR")
View(Maize_SNP_ids)
```
typeof(Maize_SNP_ids)
[1] "list"

> class(Maize_SNP_ids)
[1] "tbl_df"     "tbl"        "data.frame"
```{}
```
 **TEOSINTE SNP IDS**
```{r}
 Teosinte_SNP_ids <- filter(fang_et_al_genotypes.txt_df, Group == "ZMPBA" | Group == "ZMPIL" | Group == "ZMPJA")
 View(Teosinte_SNP_ids)
``` 
 typeof(Teosinte_SNP_ids)
[1] "list"
> class(Teosinte_SNP_ids)
[1] "tbl_df"     "tbl"        "data.frame"

#####Second step is to transpose the data so that the columns becomes rows before joining them.
**I will use the t. command**

*Maize*
```{r}
t_Maize_SNP_ids <- t(Maize_SNP_ids)
head(t_Maize_SNP_ids, 2)
```
 *Teosinte*
```{r}
t_Teosinte_SNP_ids <- t(Teosinte_SNP_ids)
head(t_Teosinte_SNP_ids, 2)
```
*Only need certain columns from the `snp_position.txt` file for the analysis so we can cut those out by using the `select` function in dplyr:*
```{r, message=FALSE}
library(dplyr)
```
snp_position_df <- tbl_df(snp_position.txt)
```{}
snp_position_sorted <- select(snp_position_df, SNP_ID, Chromosome, Position)
View(snp_position_sorted)
```
Rename rownames in `snp_position.sorted`:
This will help to create common columns to use during the  matching.,

```{r}
rownames(snp_position_sorted) <-snp_position_sorted$SNP_ID
```
* Now we need to join the `snp_position.sorted` with each of the maize group transposed and teosinte group transposed data files:
**Maize**
```{r}
joined_Maize_SNP.txt <-merge(snp_position_sorted, t_Maize_SNP_ids, by ="row.names", all = FALSE )
View(joined_Maize_SNP.txt)
```
**Teosinte**
```{r}
joined_Teosinte_SNP.txt <-merge(snp_position_sorted, t_Teosinte_SNP_ids, by ="row.names", all = FALSE )
View(joined_Teosinte_SNP.txt)
```
**Data cleaning**
Remove the column named row.names
```{r}
```
**Maize**
```{r}
Maize_data_joined <- joined_Maize_SNP.txt[,-1]
View(Maize_data_joined)
```
Removing multiple and unknown chromosome names from ** Maize**
```{r}
Maize_data_joined_u <- Maize_data_joined[!Maize_data_joined$Chromosome=="unknown"]

Maize_data_C <- Maize_data_joined_u[!Maize_data_joined_u$Chromosome=="multiple"]
```

**2. Teosinte**
```{r}
Teosinte_data_joined <- joined_Teosinte_SNP.txt[, -1]
View(Teosinte_data_joined)
```
Removing multiple and unknown chromosome names from **Teosinte**
```{r}
Maize_data_joined_u <- Maize_data_joined[!Maize_data_joined$Chromosome=="unknown"]

Maize_data_C <- Maize_data_joined_u[!Maize_data_joined_u$Chromosome=="multiple"]
```
* For all **maize** and **Teosinte** files that are chromosome specific (chromosome 1-10), with **ascending** chromosome position values, and have missing data encoded by a **?** : 
```{}
```
Arrange the cleaned data in ascending order by the SNP positions:
```{}
```
**Maize**:
```{}
Maize_data_C_Asc <- arrange(Maize_data_C, Position)
View(Maize_data_C_Asc)
```
**Teosinte**:
```{}
Teosinte_data_joined_Asc <- arrange(Teosinte_data_joined, Position)
> View(Teosinte_data_joined_Asc)
```
### To answer the first part of the question; I used tried two separate codes of which only one was very robust and its output very convincing.
```{}
```
1. *Splitting data by Chromosome*
 This was the first trial code applied on both Maize and Teosinte IDs.
 *Note*
The output was very good however, the readable version looked not well formated.
```{}
```
** Maize**
```{}
Split_Maize_group_chr <-split(Maize_data_C_Asc, Maize_data_C$Chromosome)
View (Split_Maize_group_chr)

sapply(names(Split_Maize_group_chr), function(x) write.table(Split_Maize_group_chr[[x]], file=paste(x, "txt", sep = ".")))
```
***Teosinte**
```{}
Split_Teosinte_group_chr <-split(Teosinte_data_joined_Asc, Teosinte_data_joined_Asc$Chromosome)
View (Split_Maize_group_chr)
sapply(names(Split_Teosinte_group_chr), function(x) write.table(Split_Teosinte_group_chr[[x]], file=paste(x, "txt", sep = ".")))
```{r}
```
2. **Second line of Code**

```{}
```
Assorted data in ascending order by position and converted it to dyplyer
```{}
```{r, message=FALSE}
library(dplyr)
```
1.**Maize**
```{}
Maize_data_C_Asc <- arrange(Maize_data_C, Position)
View(Maize_data_C_Asc)
```{}
```{r, message=FALSE}
library(dplyr)

Maize_data_C_Asc_df <- tbl_df(Maize_data_C_Asc)
View(Maize_data_C_Asc_df)
```
*Chromosome #; Where # refers to Chromosomes 1 to 10*
```{r}
Maize_Chr#_increase.txt <- Maize_data_C_Asc_df[Maize_data_C_Asc_df$Chromosome ==#,] View(Maize_Chr#_increase.txt)
```
*Arranging Chromosomes by increasing SNP position values*
```{r}
chr#_Maize_Asc <- Maize_Chr#_increase.txt[order(as.numeric(as.character(Maize_Chr#_increase.txt$Position))),] 
```
*Writing to a file and push on a repository*
```{r}
write.csv(chr1_Maize_Asc, file = "R_Assignment_ensamba/chr#_Maize_Asc.csv", row.names = F)
```
2. *Teosinte*
```{r}
library(dplyr)
Teosinte_data_joined_Asc_df <-tbl_df(Teosinte_data_joined_Asc)
View(Teosinte_data_joined_Asc_df)
```
**Chromosome #.**
*Select Chr# from the arranged data set*
```{}
Teosinte_chr#.txt <- Teosinte_data_joined_Asc_df[Teosinte_data_joined_Asc_df$Chromosome ==#,]
View (Teosinte_chr#.txt)
```
**Arrange in ascending order with missing values replaced with '?' view and write to a file**
```{}
Teosinte_Chr#_Asc <- Teosinte_chr#.txt[order(as.numeric(as.character(Teosinte_chr#.txt$Position))),]
> View(Teosinte_Chr#_Asc)
```
*Writing to a file*
```{}
write.csv(Teosinte_Chr#_Asc, file = "R_Assignment_ensamba/Teosinte_Chr1_Asc.csv", row.names = F)
```
For all the **maize** and **Teosinte** files that are chromosome specific (chromosome 1-10), with **decending** chromosome position values, and missing data encoded by a **-** :
1. **Maize**
First sorted it by decreasing SNP position values
*Note*
#; Refers to Chromosomes 1 to 10.
```{r}
chr#_Maize_decr <- Maize_Chr#_increase.txt[order(as.numeric(as.character(Maize_Chr#_increase.txt$Position)), decreasing = TRUE),] View(chr#_Maize_decr)
```
And missing data encoded by a **-** :
```{r}
 Maize_Chr#_Decr <- as.data.frame(lapply(chr#_Maize_decr, FUN = function(x)as.character(gsub("\\?", "-", x))))  View(Maize_Chr#_Decr)
```
*Writing to a file and push on a repository*
```{r}
write.csv(Maize_Chr#_Decr, file = "R_Assignment_ensamba/Maize_Chr#_Decr.csv", row.names = F)
```
2. **Teosinte**
*Arrange in descending order with missing values replaced with '-' view and write to a file*
```{}
Teosinte_Chr#_Decr <- Teosinte_chr#.txt[order(as.numeric(as.character(Teosinte_chr#.txt$Position)), decreasing = TRUE),]
> View(Teosinte_Chr#_Decr)
```
And missing data encoded by a **-** :
```{}
> Teosinte_Chr#_Decr.txt <- as.data.frame(lapply(Teosinte_Chr#_Decr, FUN = function(x)as.character(gsub("\\?", "-", x))))
> View(Teosinte_Chr#_Decr.txt)
```
*Writing to a file and push on a repository*
```{r}
> write.csv(Teosinte_Chr#_Decr.txt, file = "R_Assignment_ensamba/Teosinte_Chr#_Decr.txt.csv", row.names = F )
```
```{r}
library(dplyr)
```
**Part II**
Reshape raw data with `melt` using `reshape2`:
library(reshape2)

Re-downloaded the data from the internet and this time converted unknown strings to na.
*fang_et_al.txt <- read.table("https://raw.githubusercontent.com/EEOB-BioData/BCB546X-Fall2017/master/UNIX_Assignment/fang_et_al_genotypes.txt", header = TRUE, na.strings = "?/?")*
```{}
```
 *Reshape raw data with `melt` using `reshape2`:*
 tidy_fang <- melt(fang_et_al.txt, c(1,3), c(4:986))
 View(tidy_fang)
 
 *Melt also the snp.position.txt file*
 tidy_snp <- melt(snp_position.txt, "Chromosome", "SNP_ID")
```{}
```
*Plot total SNPs per chromosome:*
1. Whole data set.
```{r}
library(ggplot2)
```
total_snps_per_chromosome <- ggplot(data = tidy_snp, aes(x = Chromosome)) + geom_bar() + ylab("SNPs per chromosome") + xlab("Chromosome Number")

print(total_snps_per_chromosome)

** It seems that the most SNPs map to chromosome 1 between both maize and teosinte.*
```{}
```
2. *Maize*
```{}
Maize_ids_melted <-melt(Maize_data_joined)
> View(Maize_ids_melted)

total_snps_per_chromosome_Maize <- ggplot(data = Maize_ids_melted, aes(x = Chromosome)) + geom_bar() + ylab("SNPs per chromosome") + xlab("Chromosome Number")
> Print (total_snps_per_chromosome_Maize)
```
``{r pressure, echo=FALSE}
plot(pressure)
```
```
### _Missing data and amount of heterozygosity
```{}
value <-tidy_fang_df$value
> View(value)
```
##Homozygous SNPs converted them to 0 and Heterozygous SNP to 1 as well as NA for missing SNPs
```{r}
 tidy_fang_df[tidy_fang_df=="A/A"| tidy_fang_df=="C/C"| tidy_fang_df== "G/G"| tidy_fang_df =="T/T"] <- "0"
> tidy_fang_df[tidy_fang_df == "A/T" | tidy_fang_df == "A/G" | tidy_fang_df == "G/C" | tidy_fang_df == "G/A" | tidy_fang_df == "G/T" | tidy_fang_df == "C/A" | tidy_fang_df == "C/T" | tidy_fang_df == "C/G" | tidy_fang_df == "T/A" | tidy_fang_df == "T/C" | tidy_fang_df == "T/G" | tidy_fang_df == "A/C"] <- "1"
> tidy_fang_df$heterozygosity <- tidy_fang_df$value
> tidy_fang_df$value<- value
```{r}
#Sorted my dataframe using Group and Species_ID values and write to a file.
```{}
tidy_fang_sorted <-arrange(tidy_fang_df, Group, Sample_ID)
> View(tidy_fang_sorted)
homo_het_tidy_fang <-tidy_fang_sorted
View(homo_het_tidy_fang) 

> write.csv(homo_het_tidy_fang, file = "R_Assignment_ensamba/homo_het_tidy_fang.csv")

> library(ggplot2)
homo_hetero<-ggplot(tidy_fang_sorted) + geom_bar(aes(x=heterozygosity))

```{}
```{r setup, include=FALSE}

```
*My own visualization*
```{}
Teosinte_ids_melted <-melt(Teosinte_data_joined)
View(Teosinte_ids_melted)

ggplot(Teosinte_ids_melted) + geom_point(aes(x=SNP_ID, y=Chromosome))


This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:

```{r cars}
summary(cars)
```

## Including Plots

You can also embed plots, for example:

```{r pressure, echo=FALSE}
plot(pressure)
```

Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot.
