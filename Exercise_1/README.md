## Exercise 1 EEOB 546X 

Finn Piatscheck

## Data inspection

The two files to inspect are located in the Exercise folder.

```
cd BCB546X-Fall2017/UNIX_Assignment/
```

The files are `fang_et_al_genotypes.txt`and `snp_position.txt`. First the files information should be inspected.

```
ls -lh *
```

We learn that `fang_et_al_genotypes.txt` is a large file (11M) compared to `snp_position.txt` (81K) and might run into some issues if we try to open it.

The following commands bring us some more details about the files.

```
file fang_et_al_genotypes.txt
wc fang_et_al_genotypes.txt
file snp_position.txt
wc snp_position.txt
```

We learn that `fang_et_al_genotypes.txt` has a large number of columns. By using the command `wc -l` on both files we know that `fang_et_al_genotypes.txt` and `snp_position.txt` have 2783 and 984 rows respectively. We also make sure that the files are in ASCII format.

We can obtain the number of colums with:

```
awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt
awk -F "\t" '{print NF; exit}' snp_position.txt
```

Because the large number of colums of `fang_et_al_genotypes.txt`, the command `head` will not be the most appropriate here. To explore the files, we will prefer `less`. Let's explore the files.

```
less fang_et_al_genotypes.txt
less snp_position.txt
```

The exploration of the file allow us to see that in both file the first line contains the headers (no additional information before the data set). The functions `head` and `cut` allow to print on the screen the first colums of the first rows. Combined with the command `column` we should have a readable preview of the file.

```
cut -f 1,2,3,4,5,6 fang_et_al_genotypes.txt | column -t | head -n 5
```

The first column seems to be the sample IDs, the third column a particular group which the sample belongs and the following colums ressemble SNP locus data (the second column is unknown for now).

Since `snp_position.txt` have only 15 columns we can use:

```
head -n 10 snp_position.txt | column -t
```

We learn that this file contains the SNP IDs, their chromosome location, their position on the chromosome, the marker IDs and other features that remain obscure for now. This does not teach us more than the information provided by the Exercise. However, we know that maize has 10 chromosome so we assume that there should be SNPs on not more than 10 chromosomes. Let's check if this is correct:

```
tail -n +2 snp_position.txt | cut -f 3 | sort | uniq
```

Indeed SNPs are located on 10 chromosomes. Also we learn that some are located on "multiple" chromosomes and some have an unknown position. With the same commands we learn that the samples belong to 16 groups in the file `fang_et_al_genotypes.txt`:

```
TRIPS
ZDIPL
ZLUXR
ZMHUE
ZMMIL
ZMMLR
ZMMMR
ZMPBA
ZMPIL
ZMPJA
ZMXCH
ZMXCP
ZMXIL
ZMXNO
ZMXNT
ZPERR 
```

We can now proceed the data.

## Data processing

To obtain the requested files we can first create intermediate files for both Maize and Teosinte that contain the information we want. It is important to keep the headers to be able to join the files later.

```
grep -E "(Sample_ID|ZMMIL|ZMMLR|ZMMMR)" fang_et_al_genotypes.txt > maize_genotypes.txt
grep -E "(Sample_ID|ZMPBA|ZMPIL|ZMPJA)" fang_et_al_genotypes.txt > teosinte_genotypes.txt
```

As mentioned in the exercise, to obtain the files wanted they need to be transposed with the script available:

```
awk -f transpose.awk teosinte_genotypes.txt > transposed_teosinte_genotypes.txt
awk -f transpose.awk maize_genotypes.txt > transposed_maize_genotypes.txt
```

From `snp_position.txt` we want to keep the SNPs IDs (first column) to be able to match the corresponding genotypes and the positions (fourth column) to be able to order the files as wanted. The third column will indicate the chromosomes on which the SNPs are and will help build intermediate files that will allow us to join later the SNPs IDs, nucleotide positions and the genotypes from the transposed files. ALL THE OTHER COLUMNS WILL NOT BE JOINED.

To be joined the files need to be sorted. In the first command that we will use, the SNPs IDs and the nucleotide positions cut from `snp_position.txt` will first be sorted by the first column and stored in intermediate files by chromosomes (`chr*_ID.txt`). This files will be helpful to verify if the final outputs contain the correct information.

Then we join the intermediate SNPs IDs by chromosome files and the genotype file. It is important that transposed genotype files get sorted too. The final output needs then to be sorted by nucleotide positions, thus another `sort` command is later used (with the numerical values).

The output files will be in the following format:

`"ChromosomePosition"_"BiologicalOrganisms"_"SortingOrder".txt`

Unknown and Multiple chromosome positions outputs will not have the sorting order in text file names.

## Let's proceed

### For Maize:

* 10 files (1 for each chromosome) with SNPs ordered based on increasing position values and with missing data encoded by this symbol: ?

```
for i in {1..10}; do awk '$3=='$i'' snp_position.txt | cut -f 1,4 | sort -k1,1 >  chr"$i"_ID.txt; done 
for i in {1..10}; do join -t $'\t' -1 1 -2 1 chr"$i"_ID.txt <(sort -k1,1 transposed_maize_genotypes.txt) | sort -k2n > chr"$i"_maize_increasing.txt; done
```

> (I spend hours trying to fit everything in a single command, somehow Chromosome 1 file would come out empty and 1 row was missing in 3 other files)

* 10 files (1 for each chromosome) with SNPs ordered based on decreasing position values and with missing data encoded by this symbol: -

```
for i in {1..10}; do sort -k2 -n -r chr"$i"_maize_increasing.txt | sed 's/?/-/g' > chr"$i"_maize_decreasing.txt; done
```

* 1 file with all SNPs with unknown positions in the genome (these need not be ordered in any particular way)

```
awk '$3 == "unknown"' snp_position.txt | cut -f 1,4 | sort -k1,1 | tee unknown_ID.txt | join -t $'\t' -1 1 -2 1 unknown_ID.txt <(sort -k1,1 transposed_maize_genotypes.txt) > unknown_maize.txt
```

* 1 file with all SNPs with multiple positions in the genome (these need not be ordered in any particular way)

```
awk '$3 == "multiple"' snp_position.txt | cut -f 1,4 | sort -k1,1 | tee multiple_ID.txt | join -t $'\t' -1 1 -2 1 multiple_ID.txt <(sort -k1,1 transposed_maize_genotypes.txt) > multiple_maize.txt
```


### For Teosinte:

Intermediate files with SNPs IDs based on chromosome position are already created, thus the command can be simplified.

* 10 files (1 for each chromosome) with SNPs ordered based on increasing position values and with missing data encoded by this symbol: ?

```
for i in {1..10}; do join -t $'\t' -1 1 -2 1 chr"$i"_ID.txt <(sort -k1,1 transposed_teosinte_genotypes.txt) | sort -k2n > chr"$i"_teosinte_increasing.txt; done
```


* 10 files (1 for each chromosome) with SNPs ordered based on decreasing position values and with missing data encoded by this symbol: -

```
for i in {1..10}; do sort -k2 -n -r chr"$i"_teosinte_increasing.txt | sed 's/?/-/g' > chr"$i"_teosinte_decreasing.txt; done
```

* 1 file with all SNPs with unknown positions in the genome (these need not be ordered in any particular way)

```
awk '$3 == "unknown"' snp_position.txt | cut -f 1,4 | sort -k1,1 | tee unknown_ID.txt | join -t $'\t' -1 1 -2 1 unknown_ID.txt <(sort -k1,1 transposed_teosinte_genotypes.txt) > unknown_teosinte.txt
```

* 1 file with all SNPs with multiple positions in the genome (these need not be ordered in any particular way)

```
awk '$3 == "multiple"' snp_position.txt | cut -f 1,4 | sort -k1,1 | tee multiple_ID.txt | join -t $'\t' -1 1 -2 1 multiple_ID.txt <(sort -k1,1 transposed_teosinte_genotypes.txt) > multiple_teosinte.txt
```

## Let's explore the files

The `chr*_ID.txt` files contain the SNPs IDs for each live. Thus, if the joined files `"ChromosomePosition"_"BiologicalOrganisms"_"SortingOrder".txt` contain as many lines as the ID files then the merging worked. This can simply be checked with:

```
wc chr*
wc mul*
wc unk*
```

All the files for a particular chromosome (or unknown or multiple) have the same number of lines. 

We also see that the increasing and decrasing files have the same number of characters (that was expected);

Finally, to check if the files are ordered as wanted and if the missing values have the character requested we will use again:

```
cut -f 1,2,3,4,5,6,7,8,9 chr1_maize_decreasing.txt | column -t | head -n 50
```

with a maize file as an example here.

## Copy the file in the Github repository and push







