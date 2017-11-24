# Identifying SNPs in mtDNA

I wrote a script that identifies SNPs in an aligned fasta file (identifies_synapomorphies.pl).  It allows me to vary how many individuals are compared to others in an alignment. For starters I'm doing Sima plus 4 denis compared to humans and neanders.

run like this:
```
./identifies_synapomorphies.pl 4 29 mtDNA_align.fasta simamtDNA_to_Neander_genome.txt
```

This script also executes another script that queries a jellyfish file and quantifies kmers.
