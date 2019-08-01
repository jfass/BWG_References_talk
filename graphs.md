
### Reference Graphs

In some applications, like HLA typing, some tools have implemented the storage and searching of reference sequences in _graph structures_. Here's a population reference graph for a section of the yeast genome (citation?) ... the inset shows alignment of various (strains?) on the base pair level:

![pop ref graph](HLALA_PRG.png)

Graph data structures have been widely used for genome assembly (which constructs graphs that include all the sequencing errors and natural variation in high throughput sequencing datasets before simplifying tha graphs - as much as possible - into linear references). Heng Li ([Twitter](https://twitter.com/lh3lh3), [GitHub](https://github.com/lh3)), a bioinformatician who has developed aligners (MAQ, BWA, minimap) and assemblers (fermi, miniasm), and was involved in the 1000 Genomes Project, has recently advocated for moving to graph-based population reference graphs ([blog post 1](http://lh3.github.io/2019/07/08/on-a-reference-pan-genome-model), [blog post 2](http://lh3.github.io/2019/07/12/on-a-reference-pan-genome-model-part-ii)), and released some tools to manipulate such graphs.

Graphs have recently been used to perform variant calling, and Erik Garrison (author of Freebayes ... [Twitter](https://twitter.com/erikgarrison), [GitHub](https://github.com/ekg)) and others (the "vgteam") have released a set of tools (vg) to do this.

Graph structures can store all the variation in a population, but also specify a primary path ... this could be an agreed upon coordinate system that might be needed for annotation, visualization, etc., while allowing implicit "liftover" to other paths / coordinate systems. The advantages seem clear enough, but it's a big shift for the community to make. Will the future be graph-based?

