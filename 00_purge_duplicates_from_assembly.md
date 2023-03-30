This file records the commands we ran to "purge duplicates" from our unphased assembly
We used purg_dups v1.2.6 and minimap2 v2.17-r941.
```
git clone https://github.com/dfguan/purge_dups.git
cd purge_dups/src && make
```

Retrieve the data
```
# Download FASTQ files
curl -JLO ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR233/090/SRR23374290/SRR23374290_subreads.fastq.gz
curl -JLO ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR233/090/SRR23374290/SRR23374291_subreads.fastq.gz

# Download the unphased assembly
curl -JLo Arcadia_Amblyomma_americanum_asm001.fasta https://zenodo.org/record/7747102/files/Arcadia_Amblyomma_americanum_asm001.fasta?download=1
```

Step 1. Run minimap2 to align pacbio data and generate paf files, then calculate read depth histogram and base-level read depth.

```
pri_asm="Arcadia_Amblyomma_americanum_asm001_purged.fasta"
for i in SRR23374290_subreads.fastq.gz SRR23374291_subreads.fastq.gz
do
	minimap2 -xasm20 $pri_asm $i | gzip -c - > $i.paf.gz
done
bin/pbcstat *.paf.gz (produces PB.base.cov and PB.stat files)
bin/calcuts PB.stat > cutoffs 2>calcults.log
```

Step 2. Split an assembly and do a self-self alignment. Commands are following:
```
bin/split_fa $pri_asm > $pri_asm.split
minimap2 -xasm5 -DP $pri_asm.split $pri_asm.split | gzip -c - > $pri_asm.split.self.paf.gz
```

Step 3. Purge haplotigs and overlaps with the following command.
```
bin/purge_dups -2 -T cutoffs -c PB.base.cov $pri_asm.split.self.paf.gz > dups.bed 2> purge_dups.log
```

Steps 4. Get purged primary and haplotig sequences from draft assembly.
```
bin/get_seqs -e dups.bed $pri_asm
```

This outputs two files:
1. Purged assembly: [Arcadia_Amblyomma_americanum_asm001_purged.fasta](https://zenodo.org/record/7747102/files/Arcadia_Amblyomma_americanum_asm001_purged.fasta?download=1)
2. Haplotigs: [Arcadia_Amblyomma_americanum_asm001_hap.fasta](https://zenodo.org/record/7747102/files/Arcadia_Amblyomma_americanum_asm001_hap.fasta?download=1)