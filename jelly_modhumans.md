# Generate a jf db for kmer counting
```
zcat *.fastq.gz | jellyfish count /dev/fd/0 -m 29 -s 1000000000 -C -o SRR1295425_29mer.jf
```
maybe better to add '--disk' option to avoid errors with merging afterwards

```
zcat *.fastq.gz | jellyfish count /dev/fd/0 -m 29 -s 1000000000 -C --disk -o SRR1295425_29mer.jf
```

Results for numtR5
```
origin	genome	conclusion
Nigeria	ESN_1	NO NUMT
Nigeria	ESN_2 	broken database
Gambia	GWD_1	NUMT present
Gambia	GWD_2	NUMT present
Sierra Leone	MSL_1	NUMT present
Sierra Leone	MSL_2	NUMT present
Kenya	LWK_1	NO NUMT
Kenya	LWK_2	NO NUMT
```

relatively high frequency in several African populations suggests that this numt was not introgressed.
