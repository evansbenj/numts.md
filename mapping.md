# Prepare ref

```
/mnt/expressions/ben_evans/bin/bwa/bwa index -a bwtsw Trinity.fasta
```
```
java -jar /mnt/expressions/ben_evans/bin/picard/picard.jar CreateSequenceDictionary REFERENCE=Trinity.fasta OUTPUT=Trinity.dict
```
```
samtools faidx Trinity.fasta
```

# Mapping

```
/mnt/expressions/ben_evans/bin/bwa/bwa mem Trinity.fasta ../allreads.fasta | /mnt/expressions/ben_evans/bin/samtools/samtools view -Shu - | /mnt/expressions/ben_evans/bin/samtools/samtools sort - -o Trinity_allreads_sorted.bam
```

Index

...
samtools index -b Trinity_allreads_sorted.bam
...
