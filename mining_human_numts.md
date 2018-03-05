# Coordinate conversion

I blasted 57bp windows with diagnostic numts against human genome and transcripts in NCBI.  I used this tool to convert coordinates:

https://www.ncbi.nlm.nih.gov/genome/tools/remap/JSID_01_390425_130.14.22.21_9000_remap__1517571555

and then used this tool to retrieve the sequences:

https://genome.ucsc.edu/cgi-bin/hgc?hgsid=655142535_al6bhueucilAmKILQTKacLJPxFQX&o=126472482&g=getDna&i=mixed&c=chrX&l=126472482&r=126472620&db=hg38&hgsid=655142535_al6bhueucilAmKILQTKacLJPxFQX

# Weird numt

Steffi made a 1000 genomes file for me to use to check out the weird numt at chr1:564464-570303.  I first examined homozy sites in Altai like this:

```
java -Xmx4g -jar /mnt/expressions/ben_evans/bin/GenomeAnalysisTK-nightly-2017-10-07-g1994025/GenomeAnalysisTK.jar -T SelectVariants -R /mnt/solexa/Genomes/hg19_evan/whole_genome.fa -V chr1_530000-600000_mq25.vcf -select 'vc.getGenotype("AltaiNeandertal").isHomVar()' -o chr1_530000-600000_mq25_homoz_in_Altai.vcf
```

To get the column with the genotypes of a specific genome, use this:
```
awk -F" " '{for(i=1;i<=NF;i++){if ($i ~ /NA19017/){print i}}}' chr1_530000-600000_mq25.vcf
```

Then you can extract the altai and human genotypes like this:
```
awk '$10 == "AltaiNeandertal" {print $1,"\t",$2,"\t",$10,"\t",$1989"\t",$998"\t",$1177"\t",$1136}' chr1_530000-600000_mq25.vcf > temp
awk ' $10 == "1/1" || $10 == "0/1" {print $1,"\t",$2,"\t",$10,"\t",$1989"\t",$998"\t",$1177"\t",$1136}' chr1_530000-600000_mq25.vcf >> temp
```
This checks if the Altai genotype in the 10th column is homozy alt, and if it is, print this genotype and also the human genotype in the four columns for each human genome I looked at with kmers.  It also prints columns 1 and 2 which have the chr and bp.
