# tRNAmap
/home/sor4003/store_sor4003/RNAseq_results_fastq/run_19_tRNA_map

## conda activate bowtie2
# bowtie2                  /home/sor4003/store_sor4003/anaconda3/envs/bowtie2

##3
reference ----
wget "https://raw.githubusercontent.com/alessandrolaferlita/tRFuniverse-STAR-protocol/refs/heads/main/Human_hg38lift_tsRNA_and_5leader_final_edited.fasta"

## index build 
(bowtie2) [sor4003@scu-login01 run_19_tRNA_map]$ bowtie2-build ./Human_hg38lift_tsRNA_and_5leader_final_edited.fasta ./tRF_index

