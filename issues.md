
### Broader Reference Issues

#### Is your genome complete?

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
    -?


Other coordinate systems (put below exercise) ... gene sets, protein sets (depend on annotation on genome)

---

An Exercise: how "complete" is the _C elegans_ genome?

```
for v in {077,100,150,200,236}; do
  curl -O ftp://ftp.wormbase.org/pub/wormbase/species/c_elegans/sequence/genomic/c_elegans.WS${v}.genomic.fa.gz
  gunzip c_elegans.WS${v}.genomic.fa.gz
done
curl -O http://last.cbrc.jp/last-983.zip
unzip last-983.zip
cd last-983/
make
ln -s src/lastal .
ln -s src/lastdb .
cd ..

# Mash (Jaccard) distance
curl -O https://github.com/marbl/Mash/releases/download/v2.2/mash-OSX64-v2.2.tar
tar -xvf mash-OSX64-v2.2.tar

# me
./mash-OSX64-v2.2/mash sketch -S 42 -k 31 -s 1000000 -o current_development c_elegans.canonical_bioproject.current_development.genomic.fa
cat versions | while read v; do
  ./mash-OSX64-v2.2/mash dist -S 42 -k 31 -s 1000000 c_elegans.WS${v}.genomic.fa current_development.msh >> dists.txt
done
mv dists.txt alldists.txt

# for hands-on
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

I've done this for all _C elegans_ genome versions:

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

---

#### Circularity?

- Generally, tools are "unaware" of the circular nature of plasmids or circular chromosomes.
- Fasta format has no widely accepted solution for circular sequences.
- BWA (popular read aligner) will mark reads aligning across the "break" (end / beginning) as "improper pairs."

#### Versions and Sources

- [iGenomes via AWS S3, Ireland region](https://github.com/ewels/AWS-iGenomes)
- [iGenomes via Illumina](https://support.illumina.com/sequencing/sequencing_software/igenome.html)
- [Genomes via UCSC](https://genome.ucsc.edu/goldenPath/help/ftp.html)

#### Browsers and Viewers

- [UCSC Genome Browser](https://genome.ucsc.edu/) is a server-based genome browser
- [IGV](https://software.broadinstitute.org/software/igv/) is a desktop / laptop genome browser



