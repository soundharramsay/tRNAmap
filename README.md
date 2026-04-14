# tRNAmap
/home/sor4003/store_sor4003/RNAseq_results_fastq/run_19_tRNA_map

## conda activate bowtie2
# bowtie2                  /home/sor4003/store_sor4003/anaconda3/envs/bowtie2

##3
reference ----
wget "https://raw.githubusercontent.com/alessandrolaferlita/tRFuniverse-STAR-protocol/refs/heads/main/Human_hg38lift_tsRNA_and_5leader_final_edited.fasta"

## index build 
(bowtie2) [sor4003@scu-login01 run_19_tRNA_map]$ bowtie2-build ./Human_hg38lift_tsRNA_and_5leader_final_edited.fasta ./tRF_index

 mkdir -p sam filtered logs


##(bowtie2) [sor4003@scu-login01 run_19_tRNA_map]$ for fq in *_R1_*.fastq.gz; do     base=$(basename "$fq" .fastq.gz);      bowtie2 -L 10 -p 15     -x tRF_index     -U "$fq"     --un filtered/${base}_unmapped.fq     -S sam/${base}.sam; done

 mkdir -p sam_filt

 for sam in ./sam/*.sam; do     base=$(basename "$sam" .sam);      samtools view -@ 15 -h -F 4 "$sam"     > ./sam_filt/${base}_filt.sam; done


