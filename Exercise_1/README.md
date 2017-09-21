## Exercise 1 EEOB 546X 

Finn Piatscheck

## Data inspection

The two files to inspect are:

`fang_et_al_genotypes.txt`
`snp_position.txt`

First the files information should be inspected.

> ls -lh *

We learn that `fang_et_al_genotypes.txt` is a large file and might run into some issues if we try to open it.

The following commands bring us some more details about the files.

```
file fang_et_al_genotypes.txt
```
```
wc fang_et_al_genotypes.txt
```

```
file snp_position.txt
```
``` 
wc snp_position.txt
```

We learn that `fang_et_al_genotypes.txt` has a large number of columns. By using the command `wc -l` on both files we know that `fang_et_al_genotypes.txt`