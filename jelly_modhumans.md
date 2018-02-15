# Generate a jf db for kmer counting
```
zcat *.fastq.gz | jellyfish count /dev/fd/0 -m 29 -s 1000000000 -C -o SRR1295425_29mer.jf
```
