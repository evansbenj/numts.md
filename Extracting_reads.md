# Convert to fastq from weird format
```
perl ./converts_weird_fasta_to_fastq.pl /mnt/scratch/Manju/assembly/denisovan/raw_files/FR_raw.fasta > deni_FR_raw_fakequality.fastq
```
# Sample every two out of four lines
```
awk 'NR%4==1 || NR%4==2' test.nmea > new.file
```
# Replace @ with >

```
sed -i -e 's/@/>/g' hello.txt
```

# Make kmer library

```
cookiecutter make_library -i numt848.fa -o numt848.txt -l 29
```

# Getting reads

```
cookiecutter extract -i ../../altai_S_all_raw_fakequality.fastq -f numt848.txt -o numt848_reads 
```

```
cookiecutter extract -i /mnt/scratch/Manju/assembly/altai_denovo/raw_fastq_files/S_all_raw.fasta -f kmers_for_1413_1450_sima_deni_mtDNA_in_Altai_noendogenousmtDNA.txt -o kmers_for_1413_1450_sima_deni_mtDNA_in_Altai_noendogenousmtDNA.reads.fa
```

# Assemble reads

I had to fix the reads like this (for single end)
```
sed -i '/@/ s_$_/1_' temp.fastq
```

```
/mnt/expressions/ben_evans/bin/trinityrnaseq/Trinity --seqType fa --single altai_S_all_raw_fakequality.filtered.fastq, --no_normalize_reads --max_memory 10G --KMER_SIZE 29
```

for paired reads in one file (should figure out how to separate these to tun in tandem)

```
/mnt/expressions/ben_evans/bin/trinityrnaseq/Trinity --seqType fa --single altai_FR_all_raw_fakequality.filtered.fastq --run_as_paired --no_normalize_reads --max_memory 10G --KMER_SIZE 29
```

for singleend reads in one file 

```
/mnt/expressions/ben_evans/bin/trinityrnaseq/Trinity --seqType fa --single altai_S_all_raw_fakequality.filtered.fastq --run_as_paired --no_normalize_reads --max_memory 10G --KMER_SIZE 29
```


