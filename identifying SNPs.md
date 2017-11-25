# Identifying SNPs in mtDNA

I wrote a script that identifies SNPs in an aligned fasta file (identifies_synapomorphies.pl).  It allows me to vary how many individuals are compared to others in an alignment. For starters I'm doing Sima plus 4 denis compared to humans and neanders.

run like this:
```
./identifies_synapomorphies.pl 4 29 mtDNA_align.fasta simamtDNA_to_Neander_genome.txt
```
```
#!/usr/bin/perl 
use strict;
use List::MoreUtils qw/ uniq /;

# This program will read in aligned fasta data and identify positions that are synapomorphic in 
# the first n sequences in the file.  

# It then will print out the positions of these sequences so I know what I am looking for

# and after that it will print out kmers of size n that are before and after the SNP, including 
# the synapomorphy.

# and then it will execute a script called "jellyfish_query_kmers.pl" that counts the kmers
# upstream and downstream of the interesting site.

# Additionally it will print out the same for the other sequences, plus (eventually) the two other possibilities (for error checking)

# This information will be used to grep dump files from jellyfish

# example commandline:
# ./identifies_synapomorphies.pl 4 29 mtDNA_align.fasta simamtDNA_to_Neander_genome.txt

my $ingroup = $ARGV[0]; # How many of the first n sequences to consider to have fixed differences
my $kmer = $ARGV[1]; # kmer size
my $inputfile = $ARGV[2]; # inputfile
my $outputfile = $ARGV[3]; # output file of query seqs

#### Prepare the input file with sequences

unless (open DATAINPUT, $inputfile) {
	print "Can not find the data input file!\n";
	exit;
}


unless (open(OUTFILE, ">$outputfile"))  {
	print "I can\'t write to $outputfile   $!\n\n";
	exit;
}
print "Creating output file: $outputfile\n";



my %datahash;
my @temp;
my @temp1;
my $key;
my $counter=0;
my @keyarray; # this is a crappy way to keep the keys in order
while ( my $line = <DATAINPUT>) {
	chomp($line);
	# read in the fasta files
	if($line !~ /^\s*$/){ # ignore whitespaces
		@temp = split(' ',$line);
		if($temp[0] =~ />/){
			#this is the header
			$key = $temp[0];
			$counter=0;
			push(@keyarray, $key);
		}
		else{ 
			@temp1 = split('',$line);
			foreach(@temp1){
				$datahash{$key}[$counter]=$_;
				$counter+=1;
			}
		}
	}	
}	#end while
#   close DATAINPUT;
close DATAINPUT;



# now check if each position is variable and if so, whether the first n positions are fixed and different
# go through each position
my $n;
my @characters;
my @slice1;
my @slice2;
my @unique;
my @unique1;
my @unique2;
my @synapomorphies;
for ($n=0; $n < $counter; $n++){
	@characters=(); # reset the array
	foreach $key (@keyarray) {
		push (@characters,$datahash{$key}[$n]);
	}
	@unique = uniq @characters;
	#print @characters,"\n";
	if(scalar(@unique) > 1){ # the site is variable
		@slice1=();
		@slice2=();
		@unique1=();
		@unique2=();
		@slice1=@characters[0..($ingroup-1)];
		@slice2=@characters[$ingroup..$#characters];
		@unique1 = uniq @slice1;
		@unique2 = uniq @slice2;
		if((scalar(@unique1) == 1)&&(scalar(@unique2) == 1)&&($unique1[0] ne $unique2[0])&&($unique1[0] ne "-")&&($unique2[0] ne "-")){
			# the ingroup is fixed and different from the rest; we are forcing the rest to be fixed
			# and missing the ones where the rest are polymorphic but still different
			push(@synapomorphies,$n);

		}
	}
}
my $commandline;
# now print out the Synapomorphic SNP plus minus the kmer
my $sima_data;
my $newneander_data;
my $commandline;
my $status;
my $synapomorphy_base;
my @bases=("A","C","T","G");

foreach(@synapomorphies){
	$sima_data=();
	$newneander_data=();
	print OUTFILE "Sima_deni kmer queries mtDNA position ".$_."\n";
	# Make 57 bp query for jellyfish tiling script
	for ($n=$_-$kmer+1; $n < $_+$kmer; $n++){
		$sima_data=$sima_data.$datahash{$keyarray[0]}[$n];
	}
	print OUTFILE "$sima_data\n";	
	$commandline="qsub -l h_vmem=64g -cwd -b y bash -c \"";
	$commandline=$commandline."./jellyfish_query_kmers.pl ".$sima_data." 29 kmer-raw_S_neanderthal.jf query_".$_."Sima_deni\"";
	print $commandline,"\n";
	#$status = system($commandline);

	# Make three error 57 bp queries for jellyfish tiling script 
	# These differ by only the syanaopmorphy base
	$synapomorphy_base=substr $sima_data,($kmer-1),1;
	foreach my $base (@bases){
	    if ($synapomorphy_base ne $base){
		substr($sima_data,($kmer-1),1) = $base;
		$commandline="qsub -l h_vmem=64g -cwd -b y bash -c \"";
		$commandline=$commandline."./jellyfish_query_kmers.pl ".$sima_data." 29 kmer-raw_S_neanderthal.jf query_".$_."error_".$base."\"";
		print $commandline,"\n";
		#$status = system($commandline);
	    }
	}
	
	# compare to altai mtDNA
	print OUTFILE "Altai mtDNA queries \n";
	for ($n=$_-$kmer+1; $n < $_+$kmer; $n++){
		$newneander_data=$newneander_data.$datahash{$keyarray[22]}[$n];
	}
	print OUTFILE "$newneander_data\n\n";
	$commandline="qsub -l h_vmem=64g -cwd -b y bash -c \"";
	$commandline=$commandline."./jellyfish_query_kmers.pl ".$newneander_data." 29 kmer-raw_S_neanderthal.jf query_".$_."altai\"";
	print $commandline,"\n";
	#$status = system($commandline);
}	


#  close the output file
close OUTFILE;   
print "Closing output file: $outputfile\n";

exit;
```

This script also executes another script that queries a jellyfish file and quantifies kmers.
```
#!/usr/bin/env perl
use strict;
use warnings;
use lib qw(~/perl_modules);
use List::MoreUtils qw/ uniq /;

#  This program will issue grep commands for a given sequence
#  against a kmer dump and save the dump of those kmers
#  to a file for plotting. It will take as input the sequence
#  which should span a SNP in the middle, the kmer size, and
#  the output file name


my $inputsequence = $ARGV[0];
my $kmersize = $ARGV[1];
my $inputile = $ARGV[2];
my $outputfile = $ARGV[3];

unless (open(OUTFILE, ">$outputfile"))  {
	print "I can\'t write to $outputfile\n";
	exit;
}
print "Creating output file: $outputfile\n";


my $search;
my $status;
my $y;
my $command;
for ($y = 0 ; $y <= (length($inputsequence)-$kmersize) ; $y++ ) {
	$search= substr $inputsequence, $y, $kmersize;
	$command="qsub -l h_vmem=2g -cwd -b y bash -c \"";
	$command =$command." jellyfish query ".$inputile." ".$search." >> ".$outputfile."\"";
	print $command,"\n";
#	my $command = shell_quote('grep', '--', $search, "$inputile >> $outputfile");
	$status = system($command);
}

```
