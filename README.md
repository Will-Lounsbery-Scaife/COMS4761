### Data download links ###

Reference genome (ancestral line): https://www.ncbi.nlm.nih.gov/nuccore/CP000819.1/


Control group: https://www.ncbi.nlm.nih.gov/Traces/study/?WebEnv=MCID_644ecdcf674f404c6fb28958&query_key=5
Control group was contained the following SRR run IDs:

SRR2584703
SRR2584705
SRR2584774
SRR2591040

SRR2584782
SRR2584784
SRR2584821
SRR2584822

SRR2589058
SRR2589059
SRR2584927
SRR2589067

SRR2589071
SRR2584936
SRR2584407
SRR2584406

Test Group: https://www.ncbi.nlm.nih.gov/Traces/study/?WebEnv=MCID_644ecdcf674f404c6fb28958&query_key=6&GALAXY_URL=https%3A%2F%2Fusegalaxy.org%2Ftool_runner%3Ftool_id%3Dsra_source
Test group contained all runs from the above link.


## Quality control ##
### QC Report prior to trimming ###

Please note that you will have to change the paths in the QC scripts depending on your file locations

To get the QC report on all .fastq files prior to trimming, run the following bash script:

#!/bin/bash
cd /path/to/directory/containing/fastq/files

for file in *.fastq
do
  fastqc "$file" -o /path/to/your/output/qc/report/files
done



### Running trimmomatic ###

Install trimmomatic and add it to your conda environment
The run the trimming.sh script in the directory containing all of your .fastq files

After running trimmomatic, run the fastqc command from the previous step on all the trimmed files. This will allow you to see how trimmomatic increased the quality of each fastq file.

### Running breseq ###

Install breseq: https://github.com/barricklab/breseq

Run the script titled "run_breseq.slurm"
Make sure to update the file paths, file names, and conda environment names as necessary

### Generate consensus files ###

Run the following script, changing any filenames as necessary

#!/bin/bash

# Define the reference genome file
REFERENCE="REL606.fasta"

# Loop through all breseq_output directories in the directory
for dir in breseq_output_*; do
    # Check if the directory exists and is a directory
    if [ -d "$dir" ]; then
        # Extract the sample name from the directory name
        sample_name="${dir#breseq_output_}"
        echo "Processing sample: $sample_name"

        # Construct the output file name
        consensus_file="consensus_${sample_name}.fasta"

        # Run gdtools APPLY command
        gdtools APPLY -f FASTA -r "$REFERENCE" -o "$consensus_file" "${dir}/output/output.gd"
    fi
done

echo "All consensus sequences generated."


### Running MAFFT for MSA ###

Install MAFFT and seqret

Concatenate all conensus sequence files into a single file

Run the script run_mafft_bu.slurm

* The script "speed_mafft.slurm" shows an alternative MAFFT command that is optimized for speed over accuracy.
* For this project, we used run_mafft_bu.slurm


### Running RAxML ###

Install RAxML (https://github.com/stamatak/standard-RAxML)

Once MAFFT has finished, run RAxML on the .phylip file containing the multiple sequence alignments


