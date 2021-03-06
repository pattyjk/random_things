## ZOTUS pipeline

### Merge fastq Reads from all samples
```
#16S_samples.txt is a file that h
for file in $(<16S_Samples.txt)
do
        usearch64 -fastq_mergepairs ${file}_trimmed.fastq -fastqout merged_trimmed_pairs/${file}_merged_trimmed.fastq -sample ${file} -fastq_pctid 85 -interleaved
done

cat merged_trimmed_pairs/* > merged_trimmed_pairs/combined_merged_trimmed.fastq
```

### Dereplicate sequences
```
usearch64 -fastx_uniques merged_trimmed_pairs/combined_merged_trimmed.fastq -fastqout uniques_combined_merged_trimmed.fastq -sizeout
```

### Cluster OTUs at 97% Identity
```
usearch64 -cluster_otus uniques_combined_merged_trimmed.fastq -otus combined_merged_otus.fa -uparseout combined_merged_otus_uparse.txt -relabel OTU
```
###  Cluster Zero Radius OTUs (ZOTUs)
```
usearch64 -unoise3 uniques_combined_merged_trimmed.fastq -zotus combined_merged_zotus.fa -tabbedout combined_merged_zotus_report.txt
```

### Map reads at 97% to 97% OTUs
```
usearch64 -otutab merged_trimmed_pairs/combined_merged_trimmed.fastq -otus Traditional_OTU/combined_merged_otus.fa -uc map_combined_merged_otus.uc -otutabout table_combined_merged_trimmed_otus.txt -biomout table_combined_merged_trimmed_otus.biom -notmatchedfq unmapped_combined_merged_trimmed.fq
```

### Map reads at 97% to ZOTUs
```
usearch64 -otutab merged_trimmed_pairs/combined_merged_trimmed.fastq -otus Zero_OTU/combined_merged_zotus.fa -uc map_combined_merged_ZOTUs.uc -otutabout table_combined_merged_trimmed_ZOTUs.txt -biomout table_combined_merged_trimmed_ZOTUs.biom -notmatchedfq unmapped_combined_merged_ZOTUs.fq
```
