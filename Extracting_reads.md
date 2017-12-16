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
```
awk 'NR%7==1 || NR%7==2 || NR%7==5 || NR%7==6' paired_reads_allnumts.out > new.file
```
# Make fasta file out of forward reads and add /1 at end

```
awk 'NR%4==1 || NR%4==2' allS.fastq > allS.fasta
```
```
sed -i -e 's/@/>/g' allS.fasta
```
```
sed -i '/>/ s_$_/1_' allS.fasta
```

# Make forward reads from output of perl grep script above
```
awk 'NR%4==1 || NR%4==2' paired_reads_simadeninumts.fasta > paired_reads_simadeninumts_forward.fasta
```
```
sed -i -e 's/@/>/g' paired_reads_simadeninumts_forward.fasta
```
```
sed -i '/>/ s_$_/1_' paired_reads_simadeninumts_forward.fasta
```

# Make reverse reads
```
awk 'NR%4==3 || NR%4==0' paired_reads_simadeninumts.fasta > paired_reads_simadeninumts_reverse.fasta
```
```
sed -i -e 's/@/>/g' paired_reads_simadeninumts_reverse.fasta
```
```
sed -i '/>/ s_$_/2_' paired_reads_simadeninumts_reverse.fasta
```

# Location of SimaDeni reads

simadeni only forward
```
/mnt/scratch/ben_evans/numts/Sima_Deni_cookiecutter/allS.fasta
```
simadeni only paired
```
/mnt/scratch/ben_evans/numts/Sima_Deni_cookiecutter/paired_reads_simadeninumts_forward.fasta
/mnt/scratch/ben_evans/numts/Sima_Deni_cookiecutter/paired_reads_simadeninumts_reverse.fasta 
```

# Location of Sima only plus other reads
sima_only_forward
```
/mnt/scratch/ben_evans/numts/sima_only_mtDNA_in_neanders/Sima_only_cookiecutter/simaonly_all_forwardreads_cat.fasta
```
sima only paired
```
/mnt/scratch/ben_evans/numts/sima_only_mtDNA_in_neanders/Sima_only_cookiecutter/simaonlypaired_reads_allnumts_forward.fasta
/mnt/scratch/ben_evans/numts/sima_only_mtDNA_in_neanders/Sima_only_cookiecutter/simaonlypaired_reads_allnumts_reverse.fasta
```

# Location of combined reads
Contatenated merged_forward
```
/mnt/scratch/ben_evans/numts/Trinity_Assembly_Simaonly_and_SimaDeni/Sima_and_SimaDeni_forwardmerged_cat.fasta
```
Concatenated paired
```
/mnt/scratch/ben_evans/numts/Trinity_Assembly_Simaonly_and_SimaDeni/Sima_and_SimaDeni_paired_forward_cat.fasta
/mnt/scratch/ben_evans/numts/Trinity_Assembly_Simaonly_and_SimaDeni/Sima_and_SimaDeni_paired_reverse_cat.fasta
```

# Assemble reads

I added the merged forward reads to the left list after the forward paired with a comma separating them.
```
/mnt/expressions/ben_evans/bin/trinityrnaseq/Trinity --seqType fa --left Sima_and_SimaDeni_paired_forward_cat.fasta,Sima_and_SimaDeni_forwardmerged_cat.fasta --right Sima_and_SimaDeni_paired_reverse_cat.fasta --no_normalize_reads --max_memory 10G --KMER_SIZE 29
```

Or added the merged forward reads to the right list after the forward paired with a comma separating them.
```
/mnt/expressions/ben_evans/bin/trinityrnaseq/Trinity --seqType fa --left Sima_and_SimaDeni_paired_forward_cat.fasta --right Sima_and_SimaDeni_paired_reverse_cat.fasta,Sima_and_SimaDeni_forwardmerged_cat.fasta --no_normalize_reads --max_memory 10G --KMER_SIZE 29
```

Or combined all reads as single
```
/mnt/expressions/ben_evans/bin/trinityrnaseq/Trinity --seqType fa --single allreads.fasta --no_normalize_reads --max_memory 10G --KMER_SIZE 29
```

The most contigs were from the first option (41, 40, and 39) but it is not yet clear which has the longest combined contig length.


# V genome

Data are split up into many fastq files and there are no paired reads.  

Search for kmers:
```

```

