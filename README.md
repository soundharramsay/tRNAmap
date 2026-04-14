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



#### bam
mkdir -p bam

for sam in ./sam_filt/*_filt.sam; do
    base=$(basename "$sam" _filt.sam)

    samtools view -@ 15 -bS "$sam" \
    > ./bam/${base}.bam
done


########

# Create the output folder for sorted BAMs
mkdir -p bam_sorted
# Go back to the parent directory (where you have bam/, filtered/, logs/, etc.)
cd ..

# Create the output folder for sorted BAMs (if not already done)
mkdir -p bam_sorted

# Loop over each BAM in ./bam/
for bam in ./bam/*.bam; do
    # Extract base name without path and .bam extension
    base=$(basename "$bam" .bam)
    echo "Processing $base ..."
    
    # Sort
    samtools sort --threads 15 "$bam" -o "./bam_sorted/${base}_sorted.bam"
    
    # Index (index will be created as ./bam_sorted/${base}_sorted.bam.bai)
    samtools index -@ 15 "./bam_sorted/${base}_sorted.bam"
done



-------------mkdir -p counts logs

for bam in ./bam_sorted/*_sorted.bam; do
    base=$(basename "$bam" _sorted.bam)

    ./bedtools.static.binary multicov -q 30 \
    -bams "$bam" \
    -bed ./Human_hg38lift_tsRNA_and_5leader_final_edited.bed \
    > ./counts/${base}.txt \
    2> ./logs/${base}.log
done





mkdir -p 9_mintmap 9_logs

cd MINTmap-release-v1.0

for fq in ../filtered/*_R1_*_unmapped.fq; do
    base=$(basename "$fq" _concat.trim_unmapped.fq)

    echo "Processing: $base"

    perl MINTmap.pl \
    -f "$fq" \
    -p ../9_mintmap/${base}_R1 \
    > ../9_logs/${base}.log 2>&1
done

cd ..


