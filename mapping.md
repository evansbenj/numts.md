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

Disable seeding with `-i 10000`

```
/mnt/expressions/ben_evans/bin/bwa/bwa mem Trinity.fasta ../allreads.fasta -i 10000 | /mnt/expressions/ben_evans/bin/samtools/samtools view -Shu - | /mnt/expressions/ben_evans/bin/samtools/samtools sort - -o Trinity_allreads_sorted.bam
```

Index

...
samtools index -b Trinity_allreads_sorted.bam
...

Or with bwa aln/samse and disabling seeding
```
/mnt/expressions/ben_evans/bin/bwa/bwa aln Trinity.fasta ../allreads.fasta -l 10000 > allreads.sai
```
```
/mnt/expressions/ben_evans/bin/bwa/bwa samse Trinity.fasta allreads.sai ../allreads.fasta > allreads.sam
```
```
/mnt/expressions/ben_evans/bin/samtools/samtools view -Shu allreads.sam | /mnt/expressions/ben_evans/bin/samtools/samtools sort - -o Trinity_allreads_sorted.bam
```
...
samtools index -b Trinity_allreads_sorted.bam
...
