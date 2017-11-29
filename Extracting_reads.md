# Make kmer library

```
cookiecutter make_library -i numt848.fa -o numt848.txt -l 29
```

# Getting reads

```
cookiecutter extract make_library -i /mnt/scratch/Manju/assembly/altai_denovo/raw_fastq_files/S_all_raw.fasta -f numt848.txt -o numt848_reads.fa 
```

```
cookiecutter extract -i /mnt/scratch/Manju/assembly/altai_denovo/raw_fastq_files/S_all_raw.fasta -f kmers_for_1413_1450_sima_deni_mtDNA_in_Altai_noendogenousmtDNA.txt -o kmers_for_1413_1450_sima_deni_mtDNA_in_Altai_noendogenousmtDNA.reads.fa
```
