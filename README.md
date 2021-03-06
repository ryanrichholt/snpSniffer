# snpSniffer

Tool for checking genotype concordance between multiple assays

This tools was developed by Venkata "Teja" Yellapantulla as part of his PhD thesis work in the Keats Lab at Translational Genomics Research Institute.


## Usage

- To generate genotypes from a bam:
java -jar snpSniffer.jar -genotype <fullFilePath/reference> <fullFilePath/BAM>
        Alternately, genotypes can be generated using:

geno <fullFilePath/reference> <fullFilePath/BAM>

 *Users should make sure bam is indexed

- To add genotypes from a vcf:

java -jar snpSniffer.jar -add <fullFilePath/VCF fileName> <fullFilePath/database.ini>

- To view all samples:

java -jar snpSniffer.jar -check Samples <fullFilePath/database.ini>

- To check concordance of genotypes for a sample:

java -jar snpSniffer.jar -check <sampleName> <fullFilePath/database.ini>

- For help:

java -jar snpSniffer.jar -help


## Example usage

1) Generate the genotypes in a vcf format at specific genomic loci:
    java -jar ~/local/bin/snpSniffer.jar -genotype /lustre/vyellapa/reference.fa /lustre/vyellapa/sample1.bam

2) Adding the genotypes generated to a flat file "database.ini," provided
    Step 1, will generate a vcf having the same name as the bam in the same directory, this will be added to database.ini with same name:
    java -jar ~/local/bin/snpSniffer.jar -add /lustre/vyellapa/sample1.vcf /lustre/vyellapa/database.ini

3) Compare the genotypes for samples of interest(after 2 or more vcf's are added), examine the snpSniffer output and infer if any mixups occurred:
    java -jar ~/local/bin/snpSniffer.jar -check sample1 /lustre/vyellapa/database.ini


## Example output

Step 3 above should generate lines of output, depending on number of samples, similar to the one below. It shows that between sample1 and sample2, 171 genotypes were obtained with good quality out of which 169 positions match. The "ratio" field is the ratio of match to count and ratio>0.8 signifies that the two samples match.

In the output given below, sample1 and sample2 have a ratio of ~0.98 suggesting both sequences come from the same individual. However, sample1 and sample3 have have a ratio of ~0.32 suggesting that the sequences do not come from the same individual.

sample1 & sample2 count=171.0 match=169.0 ratio=0.9883040935672515

sample1 & sample3 count=325.0 match=107.0 ratio=0.3292307692307692


### SnpSniffer Wrapper Usage Notes

This package contains a wrapper script that leverages the power of the SnpSniffer JAVA tool to identify sample mix-ups and cross-contaminations

It can be launched from any directory as the first step is a find for any VCF files in the directory structure below the starting directory

Dependancies:
SnpSniffer.jar (Included)
Run_SnpSniffer.sh (Included)
SnpSniffer_Graph.R (Included)
R
GGPLOT2 Library


### USAGE:

1) Update the absolute paths for the VARIABLES in Run_SnpSniffer.sh
	EMPTY_DATABASE=/data/tools/snpSniffer.v5/databaseV5.ini
	SNP_SNIFFER_JAR=/data/tools/snpSniffer.v5/snpSnifferV5.jar
	SnpSniffer_Graph_R=/data/jkeats/scripts/SnpSniffer_Graph.R

2) Execute the wrapper <./Run_SnpSniffer.sh>

	This will produce a number of tables and graphs.
	The table to focus on the most is "SnpSniffer_NotExpected_Results_SharedCalls20plus.txt"
		Comparisons of the same patient with ratios less than 0.8 are included
			These are likely mixups, as even tumor-normal comparisons typically have higher values
		Comparisons of different patients with ratios above 0.8 are included
			This is unexpected and one of the two samples is likely a mixup
	Graphs are created in triplicate to filter for number of possible matches
		100 and 50 should be considered high confidence
		20 are often still valuable but can have false positives