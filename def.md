
### Reference means what?

- Sequence and coordinates on the molecules themselves
  - DNA (chromosomes, cDNA)
  - mRNA
- Annotation on the coordinate system
  - [BioMart](http://uswest.ensembl.org/biomart/martview/edea6a6cf7468c97141ac7db2f8fa1cf)
  - [UCSC Genome Browser](https://genome.ucsc.edu/)

#### Completeness

- completeness, meaning correctness (revisions)
  - single individual (ploidy?) vs population
  - natural variation? hypervariable regions?
- unknown regions
- unplaced regions

#### Circularity?

Generally, tools are "unaware" of the circular nature of plasmids or circular chromosomes. 

Fasta format has no widely accepted solution for circular sequences.

BWA (popular read aligner) will mark reads aligning across the "break" (end / beginning) as "improper pairs."

#### Versions and Sources

- [iGenomes via AWS S3, Ireland region](https://github.com/ewels/AWS-iGenomes)  
- [iGenomes via Illumina](https://support.illumina.com/sequencing/sequencing_software/igenome.html)  
- [Genomes via UCSC](https://genome.ucsc.edu/goldenPath/help/ftp.html)


