# Coordinate conversion

I blasted 57bp windows with diagnostic numts against human genome and transcripts in NCBI.  I used this tool to convert coordinates:

https://www.ncbi.nlm.nih.gov/genome/tools/remap/JSID_01_390425_130.14.22.21_9000_remap__1517571555

and then used this tool to retrieve the sequences:

https://genome.ucsc.edu/cgi-bin/hgc?hgsid=655142535_al6bhueucilAmKILQTKacLJPxFQX&o=126472482&g=getDna&i=mixed&c=chrX&l=126472482&r=126472620&db=hg38&hgsid=655142535_al6bhueucilAmKILQTKacLJPxFQX

# Weird numt

Steffi made a 1000 genomes file for me to use to check out the weird numt.  I first examined homozy sites in Altai like this:

```
java -Xmx4g -jar /mnt/expressions/ben_evans/bin/GenomeAnalysisTK-nightly-2017-10-07-g1994025/GenomeAnalysisTK.jar -T SelectVariants -R /mnt/solexa/Genomes/hg19_evan/whole_genome.fa -V chr1_530000-600000_mq25.vcf -select 'vc.getGenotype("AltaiNeandertal").isHomVar()' -o chr1_530000-600000_mq25_homoz_in_Altai.vcf
```
