# Cleanup of the tick genome assembly

This repository records commands used to clean up the tick genome assembly submitted to NCBI's GenBank.

The genome was first rejected because the genome size is not within the expected range.  
This was due to the fact that the genome was not a haploid assembly.
To fix this issue, we ran [purge_dups](./01_cleanup_genome_for_ncbi_deposition.ipynb) on the assembly.
The assembly was then rejected for having small (<200 bp) present.
To fix this issue, we [removed short contigs](./00_purge_duplicates_from_assembly.md).

The raw sequencing reads are available on the SRA under BioProject [PRJNA932813](https://www.ncbi.nlm.nih.gov/bioproject/PRJNA932813).
The unphased assembly and the output of purge_dups (`purged` and `haplotigs`) are available on [Zenodo](https://doi.org/10.5281/zenodo.7747101).
The contig-filtered assembly has been submitted to GenBank and is available on [Zenodo](https://doi.org/10.5281/zenodo.7783367) prior to acceptance.
More information about this project can be found in our pub: https://doi.org/10.57844/arcadia-9b6j-q683.
