# A genome
## Convert to fastq from weird format
```
perl ./converts_weird_fasta_to_fastq.pl /mnt/scratch/Manju/assembly/denisovan/raw_files/FR_raw.fasta > deni_FR_raw_fakequality.fastq
```
## Sample every two out of four lines
```
awk 'NR%4==1 || NR%4==2' test.nmea > new.file
```
## Replace @ with >

```
sed -i -e 's/@/>/g' hello.txt
```

## Make kmer library

```
cookiecutter make_library -i numt848.fa -o numt848.txt -l 29
```

# Getting reads A genome

```
cookiecutter extract -i ../../altai_S_all_raw_fakequality.fastq -f numt848.txt -o numt848_reads 
```

```
cookiecutter extract -i /mnt/scratch/Manju/assembly/altai_denovo/raw_fastq_files/S_all_raw.fasta -f kmers_for_1413_1450_sima_deni_mtDNA_in_Altai_noendogenousmtDNA.txt -o kmers_for_1413_1450_sima_deni_mtDNA_in_Altai_noendogenousmtDNA.reads.fa
```

# Get paired ends A genome

So I got the first read from cookie cutter, but ideally we need both reads for paired end reads that were not merged.  I did this using grep plus a script that also greps.  First, get the header of the reads: `grep '@' numt*/numt*FR/*filtered.fastq`

Then use this script to get both reads (`./Gets_paired_seqs_from_cookiecutter_kmers.pl paired_headers_from_cookiecutter paired_reads_allnumts.out`)

```
#!/usr/bin/env perl
use strict;
use warnings;


# This program reads in headers from one of the paired end reads that were pulled using 
# kmers and cookiecutter.  These headers can be obtained like this:
# grep '@' numt*/numt*FR/*filtered.fastq

# The program will read in these reads and then grep the original fastafile and make two new
# files that have each pair

my $inputfile = $ARGV[0];
my $outputfile = $ARGV[1];
my $status;
my $commandline;

unless (open DATAINPUT, $inputfile) {
	print "Can not find the input file.\n";
	exit;
}

unless (open(OUTFILE, ">$outputfile"))  {
	print "I can\'t write to $outputfile\n";
	exit;
}
print "Creating output file: $outputfile\n";


my @headers;
my @temp;

while ( my $line = <DATAINPUT>) {
	chomp($line);
	@temp=split(':@',$line);
	push (@headers, $temp[1]);
}		
close DATAINPUT;


foreach my $header (@headers){
	$commandline = "grep -A 2 \'@".$header."\' /mnt/scratch/Manju/assembly/altai_denovo/raw_fastq_files/FR_all.fasta >> ".$outputfile;
	print $commandline,"\n";
	#$status = system($commandline);
}

```

# Getting rid of extra stuff
awk 'NR%7==1 || NR%7==2 || NR%7==5 || NR%7==6' paired_reads_allnumts.out > new.file

# Replace @ with >

```
sed -i -e 's/@/>/g' hello.txt
```


# Assemble reads

I had to fix the reads like this (for single end)
```
sed -i '/@/ s_$_/1_' temp.fastq
```

```
/mnt/expressions/ben_evans/bin/trinityrnaseq/Trinity --seqType fq --single temp.fastq --no_normalize_reads --max_memory 10G --KMER_SIZE 29
```

for paired reads in one file (should figure out how to separate these to tun in tandem)

```
/mnt/expressions/ben_evans/bin/trinityrnaseq/Trinity --seqType fq --single altai_FR_all_raw_fakequality.filtered.fastq --run_as_paired --no_normalize_reads --max_memory 10G --KMER_SIZE 29
```

for singleend reads in one file 

```
/mnt/expressions/ben_evans/bin/trinityrnaseq/Trinity --seqType fq --single altai_S_all_raw_fakequality.filtered.fastq --run_as_paired --no_normalize_reads --max_memory 10G --KMER_SIZE 29
```


# V genome

Data are split up into many fastq files.

Search for kmers:
```

```

