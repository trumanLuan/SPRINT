# SPRINT
SNP-free RNA editing Identification Toolkit

# Reference:
Feng Zhang, Yulan Lu, Sijia Yan, Qinghe Xing, Weidong Tian; SPRINT: an SNP-free toolkit for identifying RNA editing sites, Bioinformatics, Volume 33, Issue 22, 15 November 2017, Pages 3538–3548, https://doi.org/10.1093/bioinformatics/btx473

# Requirement:
# SAMTOOLS == 1.2 & BWA == 0.7.12
# Unix & Python2.7

# Installation:
Directly use the binary file in https://github.com/jumphone/SPRINT/tree/master/bin, or

python setup.py install

# Usage:

sprint prepare |  [options]  | reference_genome(.fa) | bwa_path



sprint main  |  [options]  |  reference_genome(.fa) |   output_path  |  bwa_path  |  samtools_path



sprint_from_bam  |  [options]  |  aligned_reads(.bam)  |  reference_genome(.fa)  |  output_path  |  samtools_path


Details about "options" are in SPRINT_manual.pdf (https://github.com/jumphone/SPRINT/blob/master/SPRINT_manual.pdf)

Repeat Annotation Files are in http://sprint.tianlab.cn/SPRINT/dbrep/

# Attention:  

a. Before using "sprint_from_bam", BAM file should be sorted by using samtools ("samtools sort"). 

b. You can use changeSAMmapQ.py (https://github.com/jumphone/SPRINT/tree/master/utilities) to change the MAPQ of your SAM files generated by other aligner (except BWA). Please remember that sprint_from_bam only uses reads with proper MAPQ (from 20 to 200) to detect RESs.

c. The reference_genome used by sprint_from_bam should be the same with that used by aligner

d. “sprint_from_bam” was designed for aligned reads without using any aligner. In exchange, “sprint_from_bam” doesn’t include the function of detecting hyper RESs, because detecting hyper RESs needs to use aligner. However, in order to detect hyper RESs from BAM format, users can use SAMTOOLS to extract unaligned reads (BAM format) with command options of “samtools view -f4 -b”, and then convert it into FASTQ format with command options of “samtools bam2fq”. Finally, users can apply SPRINT to the unaligned reads (FASTQ format) to obtain hyper RESs and hyper-edited reads.


# Tips:

Scripts are in https://github.com/jumphone/SPRINT/tree/master/utilities

# 1. Edited reads can be extracted from "tmp/all_combined.zz".

all_combined.zz :

| Chr | SAM_Flag | MapQ | Loc | SNV | BaseQ | Read-loc | Seq | Read-name | Fragment-loc |

Users can use zz2sam.py to convert 'tmp/all_combined.zz' into BAM format: 

Step 1:  Please move to the output-directory of SPRINT;

Step 2:  Download zz2sam.py in https://github.com/jumphone/SPRINT/tree/master/utilities;

Step 3: "python zz2sam.py tmp/all_combined.zz";

Step 4: "samtools view -H tmp/genome/all.bam > SAMheader.txt";

Step 5: "cat SAMheader.txt tmp/all_combined.zz.sam > all_combined.zz.sam.header";

Step 6: "samtools view -bS all_combined.zz.sam.header > all_combined.zz.bam";

Step 7: "samtools sort all_combined.zz.bam -f all_combined.zz.sorted.bam".

________________________

# 2. Get A-to-I RESs from the ouput directory of SPRINT.

Script is in https://github.com/jumphone/SPRINT/tree/master/utilities

Users can use getA2I.py to extract A-to-I RESs from the output of SPRINT (version>=0.1.7)

python   |  getA2I.py   |  0 (1 for strand-specific data)   |  SPRINT_OUT   |   A_to_I_OUT

________________________

# 3. About "Supporting reads" and "AD":

For a given RES,

supporting reads (regular-RES): mapped high-quality reads (MQ>=20 AND BASEQ >=25 AND fragment-loc >5);

supporting reads (hyper-RES): remapped high-quality reads (BASEQ >=25 AND fragment-loc >5 AND Poly(N) <10 AND n(C)+n(T)<20);

AD (all RES): mapped reads (without the restriction of quality) + remapped reads (without the restriction of quality).
________________________

# 4. Change RepeatMasker File (rmsk) into BED file (used by SPRINT):

Script is in https://github.com/jumphone/SPRINT/tree/master/utilities

Step 1:  Users can get RepeatMasker file from UCSC Table Browser (http://genome.ucsc.edu/cgi-bin/hgTables).

Step 2: python  | rp2bed.py  |  hg38.rmsk  |  hg38_repeat.bed

________________________

# 5. About A-to-I (A-to-G) rate:

For strand-specific data, “AG +” and "TC -"  are all thought to be A-to-G (A-to-I editing). 

For non-strand-specific data, there are two ways to estimate the A-to-G rate:

a. "AG +/-" + "TC +/-". Disadvantages: overestimate the A-to-G rate

b. assign the strand of sites by using GENE annotations (e.g. ENSEMBL, REFSEQ, GENCODE, etc.) , and then: "AG +" + "TC -".  Disadvantages: gene region only, underestimate the A-to-G rate (the opposite strand may also have transcript )
________________________
