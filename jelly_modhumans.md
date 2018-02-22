# Generate a jf db for kmer counting
```
zcat *.fastq.gz | jellyfish count /dev/fd/0 -m 29 -s 1000000000 -C -o SRR1295425_29mer.jf
```
maybe better to add '--disk' option to avoid errors with merging afterwards

```
zcat *.fastq.gz | jellyfish count /dev/fd/0 -m 29 -s 1000000000 -C --disk -o SRR1295425_29mer.jf
```
example searches:
```
../../jellyfish_query_kmers.pl ATATATTAGATTTGACCTTCAGCAAGGTCAAAGGGAGTCCGAACTAGTCTCAGGCTT 29 ./ESN_2_SRR1295553.jf query_humannumt_R5_5pjunction
../../jellyfish_query_kmers.pl CTCCTACAAGCCTCAGAGTACTTCGAGGTTAAAATATTAGATATTTCCCCTGATACA 29 ./ESN_2_SRR1295553.jf query_humannumt_R5_3pjunction
../../jellyfish_query_kmers.pl AAGCCTGAGACTAGTTCGGACTCCCTTTGACCTTGCTGAAGGTCAAATCTAATATAT 29 ./ESN_2_SRR1295553.jf query_humannumt_R5_5pjunction_rc
../../jellyfish_query_kmers.pl TGTATCAGGGGAAATATCTAATATTTTAACCTCGAAGTACTCTGAGGCTTGTAGGAG 29 ./ESN_2_SRR1295553.jf query_humannumt_R5_3pjunction_rc
../../jellyfish_query_kmers.pl ATTTTCTTCAGTGGGACTTTTTTGTTTAATATATTAGATTTGACCTTCAGCAAGGTC 29 ./ESN_2_SRR1295553.jf query_humannumt_R5_5pflank
../../jellyfish_query_kmers.pl GTTAAAATATTAGATATTTCCCCTGATACAGGGCTCAATCTTTTTCTTTTTAAAGCA 29 ./ESN_2_SRR1295553.jf query_humannumt_R5_3pflank
../../jellyfish_query_kmers.pl GACCTTGCTGAAGGTCAAATCTAATATATTAAACAAAAAAGTCCCACTGAAGAAAAT 29 ./ESN_2_SRR1295553.jf query_humannumt_R5_5pflank_rc
../../jellyfish_query_kmers.pl TGCTTTAAAAAGAAAAAGATTGAGCCCTGTATCAGGGGAAATATCTAATATTTTAAC 29 ./ESN_2_SRR1295553.jf query_humannumt_R5_3pflank_rc

```
and for the no_numt junction (where the numt is not present):
```
../../jellyfish_query_kmers.pl ATTTTCTTCAGTGGGACTTTTTTGTTTAATATAATATTAGATATTTCCCCTGATACAGGGCTCAATCTTTTTCTTTTTAAAGCA 29 ./ESN_2_SRR1295553.jf query_nonumtjunction
```
Note that the no numt junction is a repetitive region, so this is generally at high coverage, including in individuals that carry the numt.




Results for numtR5
```
origin	genome	conclusion
Nigeria	ESN_1	NO NUMT
Nigeria	ESN_2 	NO NUMT
Gambia	GWD_1	NUMT present
Gambia	GWD_2	NUMT present
Sierra Leone	MSL_1	NUMT present
Sierra Leone	MSL_2	NUMT present
Kenya	LWK_1	NO NUMT
Kenya	LWK_2	NO NUMT
```

relatively high frequency in two (northern) west African populations but not one central West African population or an East African population.  
