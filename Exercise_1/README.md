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

To obtain the requested file we can first create intermediate files for both Maize and Teosinte that contain the information we want. It is important to keep the headers to be able to join the files later.

```
grep -E "(Sample_ID|ZMMIL|ZMMLR|ZMMMR)" fang_et_al_genotypes.txt > maize_genotypes.txt
grep -E "(Sample_ID|ZMPBA|ZMPIL|ZMPJA)" fang_et_al_genotypes.txt > teosinte_genotypes.txt
```

However to be able to join the 

As mentioned in the exercise, to obtain the files wanted they need to be transposed with the script available:

```
awk -f transpose.awk teosinte_genotypes.txt > transposed_teosinte_genotypes.txt
awk -f transpose.awk maize_genotypes.txt > transposed_maize_genotypes.txt
```

We can then join the files we want:

For Maize:


For Teosinte

10 files (1 for each chromosome) with SNPs ordered based on increasing position values and with missing data encoded by this symbol: ?

```
awk '$3 == "1"' snp_position.txt | cut -f 1,4 | sort | tee chr1_ID.txt | join -1 1 -2 1 chr1_ID.txt <(sort transposed_teosinte_genotypes.txt) | sort -k2,1 > chr1_teosinte.txt

### STILL FUCKED UP BUT I WILL GET IT SOON

awk '$3 == "2"' snp_position.txt | cut -f 1 | sort | tee chr2_ID.txt | join -1 1 -2 1 chr2_ID.txt <(sort transposed_teosinte_genotypes.txt) > chr2_teosinte.txt

awk '$3 == "3"' snp_position.txt | cut -f 1 | sort | tee chr3_ID.txt | join -1 1 -2 1 chr3_ID.txt <(sort transposed_teosinte_genotypes.txt) > chr3_teosinte.txt

awk '$3 == "4"' snp_position.txt | cut -f 1 | sort | tee chr4_ID.txt | join -1 1 -2 1 chr4_ID.txt <(sort transposed_teosinte_genotypes.txt) > chr4_teosinte.txt

awk '$3 == "5"' snp_position.txt | cut -f 1 | sort | tee chr5_ID.txt | join -1 1 -2 1 chr5_ID.txt <(sort transposed_teosinte_genotypes.txt) > chr5_teosinte.txt

awk '$3 == "6"' snp_position.txt | cut -f 1 | sort | tee chr6_ID.txt | join -1 1 -2 1 chr6_ID.txt <(sort transposed_teosinte_genotypes.txt) > chr6_teosinte.txt

awk '$3 == "7"' snp_position.txt | cut -f 1 | sort | tee chr7_ID.txt | join -1 1 -2 1 chr7_ID.txt <(sort transposed_teosinte_genotypes.txt) > chr7_teosinte.txt

awk '$3 == "8"' snp_position.txt | cut -f 1 | sort | tee chr8_ID.txt | join -1 1 -2 1 chr8_ID.txt <(sort transposed_teosinte_genotypes.txt) > chr8_teosinte.txt

awk '$3 == "9"' snp_position.txt | cut -f 1 | sort | tee chr9_ID.txt | join -1 1 -2 1 chr9_ID.txt <(sort transposed_teosinte_genotypes.txt) > chr9_teosinte.txt

awk '$3 == "10"' snp_position.txt | cut -f 1 | sort | tee chr10_ID.txt | join -1 1 -2 1 chr10_ID.txt <(sort transposed_teosinte_genotypes.txt) > chr10_teosinte.txt

awk '$3 == "multiple"' snp_position.txt | cut -f 1 | sort | tee multiple_ID.txt | join -1 1 -2 1 multiple_ID.txt <(sort transposed_teosinte_genotypes.txt > multiple_teosinte.txt

awk '$3 == "unknown"' snp_position.txt | cut -f 1 | sort | tee unknown_ID.txt | join -1 1 -2 1 unknown_ID.txt <(sort transposed_teosinte_genotypes.txt) > unknown_teosinte.txt
```





