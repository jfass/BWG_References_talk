
## Broader Reference Issues

### Is your genome complete?

- DNA
    - completeness
        - missing regions, known ('NNNNNNN') or unknown
        - fragmentation (repeats)
    - correctness
        - single individual (ploidy?) vs population
        - natural variation? hypervariable regions?
    - stability? (limits of current technology)
- not DNA ... but, we can learn about it by _sequencing_ and _aligning_
    - DNA-binding proteins (regulation...)
    - DNA modification
    - chromatin structure
    - ?
    - *how do we get this kind of information*

---

### An Exercise: how "complete" is the _C elegans_ genome?

Let's grab a couple _C elegans_ versions to play with:

```
for v in {077,100,150,200,236}; do
  curl -O ftp://ftp.wormbase.org/pub/wormbase/species/c_elegans/sequence/genomic/c_elegans.WS${v}.genomic.fa.gz
  gunzip c_elegans.WS${v}.genomic.fa.gz
done
```

What sequences does each contain ... and how long are they?

```
for v in {077,100,150,200,236}; do
  echo $v
  cat c_elegans.WS${v}.genomic.fa \
    | tr "\n" "\t" \
    | tr ">" "\n" \
    | grep -v ^$ \
    | perl -ane '$id=shift @F; print ">$id\n".join("",@F)."\n"' \
    | perl -ne '$h=$_; $s=<>; chomp $h; chomp $s; print "$h\t".length($s)."\n"'
done
```

(The first perl snippet puts every chromosome's sequence on one line; the second reports the lengths of those lines)

This gives us the following:

```
077
>CHROMOSOME_I	15080475
>CHROMOSOME_II	15174006
>CHROMOSOME_III	13855084
>CHROMOSOME_IV	17493985
>CHROMOSOME_V	20916337
>CHROMOSOME_X	17749735
100
>CHROMOSOME_I	15080473
>CHROMOSOME_II	15279303
>CHROMOSOME_III	13783268
>CHROMOSOME_IV	17493790
>CHROMOSOME_V	20922238
>CHROMOSOME_X	17705013
150
>CHROMOSOME_I	15072418
>CHROMOSOME_II	15279313
>CHROMOSOME_III	13783317
>CHROMOSOME_IV	17493785
>CHROMOSOME_MtDNA	13794
>CHROMOSOME_V	20922233
>CHROMOSOME_X	17718851
200
>CHROMOSOME_I	15072421
>CHROMOSOME_II	15279324
>CHROMOSOME_III	13783682
>CHROMOSOME_IV	17493784
>CHROMOSOME_V	20924143
>CHROMOSOME_X	17718854
>CHROMOSOME_MtDNA	13794
236
>CHROMOSOME_I	15072434
>CHROMOSOME_II	15279421
>CHROMOSOME_III	13783801
>CHROMOSOME_IV	17493829
>CHROMOSOME_V	20924180
>CHROMOSOME_X	17718942
>CHROMOSOME_MtDNA	13794
```

So we can see that the lengths have changed for the chromosomes over time. But what about the content of those chromosomes?

```
for v in {077,100,150,200,236}; do
  echo $v
  cat c_elegans.WS${v}.genomic.fa \
    | grep -v ">" \
    | grep -o . \
    | perl -ne 'chomp; $h{$_}++ }{ foreach $k (sort keys %h) {print "$k\t$h{$k}\n"}'
done
```

Which gives us nucleotide breakdowns of each version:

```
077
N	95300
a	32338453
c	17762054
g	17739785
n	96
t	32333934
100
a	32364258
c	17778488
g	17755882
n	95
t	32365362
150
a	32371045
c	17781590
g	17758491
t	32372585
200
a	32371754
c	17781986
g	17758997
t	32373265
236
a	32371810
c	17782105
g	17759142
t	32373344
```

Obviously things like GC content can be important (if it's off, could indicate contamination?), but also the fraction of unknown nucleotides (N,n) indicates a level of completeness. Version 077 still had 95kbp of unknown sequence, which was largely resolved by version 100.

A less crude way of (quickly) examining sequence content involves breaking each genome down into k-mers ... i.e.:

ThisSentenceIsGenomeSequence

At k=21, here are all the k-mers of that sequence:

ThisSentenceIsGenomeS  
 hisSentenceIsGenomeSe  
  isSentenceIsGenomeSeq  
   sSentenceIsGenomeSequ  
    SentenceIsGenomeSeque  
     entenceIsGenomeSequen  
      ntenceIsGenomeSequenc  
       tenceIsGenomeSequence

One could count the occurrences of every 21-mer that appears in different versions of a genome, but it's quicker to select a random subset of k-mers (this is the "minHash" part) and evaluate how many of them are shared between them. Let's do this with a tool called Mash:

```
# if you're on a Mac:
curl -O https://github.com/marbl/Mash/releases/download/v2.2/mash-OSX64-v2.2.tar
tar -xvf mash-OSX64-v2.2.tar
./mash-OSX64-v2.2/mash dist -S 42 -k 31 -s 1000000 c_elegans.WS077.genomic.fa c_elegans.WS236.genomic.fa
```

This gives us a distance report which contains a "Jaccard distance" and fraction of shared k-mers:

```
c_elegans.WS077.genomic.fa	c_elegans.WS236.genomic.fa	3.02037e-05	0	998130/1000000
```

This is telling us that there's a Jaccard distance of 3e-5 between the two versions, (a p-value of zero; don't ask), and they shared ~998k out of the random set of 1M 31-mers.

So now let's examine those distances across multiple previous versions (compared to the 236 one):

```
for v in {077,100,150,200}; do
  ./mash-OSX64-v2.2/mash dist -S 42 -k 31 -s 1000000 c_elegans.WS${v}.genomic.fa c_elegans.WS236.genomic.fa >> dists.txt
done
paste <(cut -f1 dists.txt | grep -o WS... | cut -c3-) <(cut -f3 dists.txt) > plot.tsv
cat plot.tsv | gnuplot -e "set terminal dumb size 120, 30; plot '-' using 1:2 with lines"
```

The monstrosity this produces:

```
                                                                                                                        
 0.00003 +----------------------------------------------------------------------------------------------------------+   
         |              +               +              +              +              +               +              |   
         |                                                                                    '-' using 1:2 ******* |   
 0.00003 |-+          **                                                                                          +-|   
         |              **                                                                                          |   
         |                *                                                                                         |   
 0.00003 |-+               *                                                                                      +-|   
         |                  **                                                                                      |   
         |                    *                                                                                     |   
 0.00003 |-+                   *                                                                                  +-|   
         |                      *                                                                                   |   
         |                       **                                                                                 |   
 0.00002 |-+                       *                                                                              +-|   
         |                          *                                                                               |   
 0.00002 |-+                         **                                                                           +-|   
         |                             *                                                                            |   
         |                                                                                                          |   
 0.00002 |-+                            **********                                                                +-|   
         |                                        ******                                                            |   
         |                                              *******                                                     |   
 0.00002 |-+                                                   ******                                             +-|   
         |                                                           ******                                         |   
         |                                                                 ***                                      |   
 0.00002 |-+                                                                  *****************************       +-|   
         |                                                                                                 *********|   
         |              +               +              +              +              +               +              |   
 0.00001 +----------------------------------------------------------------------------------------------------------+   
         60             80             100            120            140            160             180            200  
                                                                                                                        
```

I've done this for all _C elegans_ genome versions versus the "current_development" version:

```
                                                                                                                        
  3.5e-05 +---------------------------------------------------------------------------------------------------------+   
          |           +           +          +           +           +           +          +           +           |   
          |                                                                                   '-' using 1:2 ******* |   
          |                                                                                                         |   
    3e-05 |-+       ***                                                                                           +-|   
          |            **                                                                                           |   
          |              **                                                                                         |   
  2.5e-05 |-+              **                                                                                     +-|   
          |                  **                                                                                     |   
          |                    **                                                                                   |   
          |                      *                                                                                  |   
    2e-05 |-+                     ****                                                                            +-|   
          |                           *                                                                             |   
          |                            ******                                                                       |   
          |                                  **********************************************                         |   
  1.5e-05 |-+                                                                              ***********            +-|   
          |                                                                                          ************   |   
          |                                                                                                     *   |   
          |                                                                                                     *   |   
    1e-05 |-+                                                                                                   * +-|   
          |                                                                                                     *   |   
          |                                                                                                      *  |   
    5e-06 |-+                                                                                                    *+-|   
          |                                                                                                      *  |   
          |                                                                                                      *  |   
          |           +           +          +           +           +           +          +           +        *  |   
        0 +---------------------------------------------------------------------------------------------------------+   
          60          80         100        120         140         160         180        200         220         240  
                                                                                                                        
```

This is all leading up to aligning multiple genome versions together. See the UCSC Genome Browser's liftover tool and conservation tracks.

---

### Circularity?

- Generally, tools are "unaware" of the circular nature of plasmids or circular chromosomes.
- Fasta format has no widely accepted solution for circular sequences.
- BWA (popular read aligner) will mark reads aligning across the "break" (end / beginning) as "improper pairs."

### Versions and Sources

- [iGenomes via AWS S3, Ireland region](https://github.com/ewels/AWS-iGenomes)
- [iGenomes via Illumina](https://support.illumina.com/sequencing/sequencing_software/igenome.html)
- [Genomes via UCSC](https://genome.ucsc.edu/goldenPath/help/ftp.html)

### Browsers and Viewers

- [UCSC Genome Browser](https://genome.ucsc.edu/) is a server-based genome browser
- [IGV](https://software.broadinstitute.org/software/igv/) is a desktop / laptop genome browser

### CONSISTENCY.

This is the biggest, most tedious problem that arises when using reference genomes. For example (don't quote me on this exactly), UCSC genomes have chromosome names like {chr1..chr22,chrX,chrY,chrM} ... EMBL's ENSEMBL genomes have {1..22,X,Y,M} ... NCBI's genomes used to have RefGene identifiers {NC_#####,etc.}. Most tools will *not* recognize and use those forms interchangeably, so if your BAM files use "chr1", your GTF file needs to use "chr1". And clearly, the chromosome sequence you aligned to needs to be exactly the same as that used for other tools, like variant callers.


