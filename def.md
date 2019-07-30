
### Reference means what?

#### What do we want to know about an organism's genome?

- Sequence and coordinates on the molecules themselves
  - DNA (chromosomes, plasmids, cDNA?)
  - mRNA
  - protein
- Annotation on the coordinate system
  - genes
  - repeats
  - pseudogenes
  - conserved regions (e.g. alignment to other species)
  - variant regions (normal / diseased sequence variation)
  - regulatory stuff
  - epigenetic marks, chromatin interactions, etc.
  - aligned reads from targeted / shotgun sequencing

#### File formats

- Sequence
  - (nucleotides) ... fasta (*.fasta, *.fa, *.fna)

```
>sequenceName_and|lots of other-junk/with/no\standards #pretty	much
ACGTUacgtuNn-_.*   # line wraps at 80 or any number of characters (\n for Unix/Linux, \r\n or ^M Windows, \r classic MacOS)
MRWSYKVHDBN        # IUPAC Ambiguity Codes
```

  - (amino acids) ... fasta (*.fasta, *.fa, *.faa)

```
>sequenceName
ACDE...WY
```

- Annotation
  - [UCSC Genome Browser File Format FAQ](https://genome.ucsc.edu/FAQ/FAQformat.html)
  - BED (sequence, start, stop ...)

```
chr1	100	200	# tab "\t" separated columns ... minimum 3, but more (defined) columns can be used
```

  - GTF (Gene Transfer Format) ... (8 tab-separated columns same as GFF, with defined *attributes* in column 9)

```
# seqname	source	feature	start	end	score	strand(+-.)	frame(012.)	gene_id ""; transcript_id ""
chr3	MAKER	promoter	100	200	500	+	.	gene_id "fake"; transcript_id "fake_7"
```

  - aligned reads ... BLAST, BLAT, SAM/BAM/CRAM (.bam & .bam.bai | .bai, .cram & .cram.crai | .crai)
    - SAM is text
    - BAM or CRAM are two different binary encodings of SAM (BAM more standard, CRAM more recent and less common)
    - [SAMTools](http://www.htslib.org/)

Each alignment described on a single line:
```
QNAME   FLAG    RNAME   POS     MAPQ    CIGAR   RNEXT   PNEXT   TLEN    SEQ     QUAL    extraTags
```

... but with information in the header (lines starting with '@')
```
@HD	VN:1.6	SO:coordinate
@SQ	SN:phiX	LN:5200
readID	25	phiX	217	60	75M	=	235	96	ATATAGCCGATATACC[...]	IIAIAIII45,(IIIAAA[...]	MD:Z:72A2
```

    - SAM flag:
![SAM slide](./SAMflag.png)

  - sequence variants ... VCF/BCF/gVCF


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


