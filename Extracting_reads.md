# Convert to fastq from weird format
```
perl ./converts_weird_fasta_to_fastq.pl /mnt/scratch/Manju/assembly/denisovan/raw_files/FR_raw.fasta > deni_FR_raw_fakequality.fastq
```
# Sample every two out of four lines
```
awk 'NR%4==1 || NR%4==2' test.nmea > new.file
```

# Make kmer library

```
cookiecutter make_library -i numt848.fa -o numt848.txt -l 29
```

# Getting reads

```
cookiecutter extract -i /mnt/scratch/Manju/assembly/altai_denovo/raw_fastq_files/S_all_raw.fasta -f numt848.txt -o numt848_reads.fa 
```

```
cookiecutter extract -i /mnt/scratch/Manju/assembly/altai_denovo/raw_fastq_files/S_all_raw.fasta -f kmers_for_1413_1450_sima_deni_mtDNA_in_Altai_noendogenousmtDNA.txt -o kmers_for_1413_1450_sima_deni_mtDNA_in_Altai_noendogenousmtDNA.reads.fa
```
