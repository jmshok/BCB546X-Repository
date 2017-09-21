## Exercise 1 EEOB 546X 

Finn Piatscheck

## Data inspection

The two files to inspect are:

`fang_et_al_genotypes.txt`
`snp_position.txt`

First the files information should be inspected.

```
ls -lh *
```

We learn that `fang_et_al_genotypes.txt` is a large file and might run into some issues if we try to open it.

The following commands bring us some more details about the files.

```
file fang_et_al_genotypes.txt
wc fang_et_al_genotypes.txt
file snp_position.txt
wc snp_position.txt
```

We learn that `fang_et_al_genotypes.txt` has a large number of columns. By using the command `wc -l` on both files we know that `fang_et_al_genotypes.txt` and `snp_position.txt` have 2783 and 984 rows respectively.

Because the large number of headers of `fang_et_al_genotypes.txt`, the command `head` will not be the most appropriate here. To explore the files, we will prefer `less`. But we can obtain the number of rows with:

```
awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt
awk -F "\t" '{print NF; exit}' snp_position.txt
```

Let's explore the files.

```
less fang_et_al_genotypes.txt
```

The exploration of the file tell us

less snp_position.txt
```

The exploration of the file tell us  that  Sample_ID	JG_OTU	Group 







