# Overview

[SPAdes](https://cab.spbu.ru/software/spades/) (St. Petersburg genome assembler) - *de novo* genome assembly with *de Bruijn* graphs.

---

## Contents
1. [Genome assembly and *de Bruijn* graphs.](#algorithm)
1. [What’s SPAdes good for?](#introduction)
1. [Before using SPAdes.](#Before-using-SPAdes)
1. [Using SPAdes.](#Using-SPAdes)
1. [After using SPAdes.](#After-using-SPAdes)

---

## Genome assembly and *de Bruijn* graphs
* Each read sequences can be disseted into *k*-mers of the length *k*. For example, for the sequence `ATGCAT`, when *k*=3, the string can be dissected into four overlapping substrings of *3*-mers: 
<br>`ATG` 
<br>`TGC` 
<br>`GCA` 
<br>`CAT`
* Each *k*-mers were further splitted into two substrings: **prefix** and **suffix**. Both substrings have the length of *k-1*. For example, the *3*-mer `ATG` can be splitted into **prefix** `AT` and **suffix** `TG`;
* The *k*-mers that shared a common **prefix** and **suffix** can be connected. For example, the **sufix** of `ATG` and the **prefix** of `TGC` are identical, therefore they can be connected and formed a new sequence `ATGC`
* In this way, more overlapping *k*-mers can be connected and forming a longer new sequence.
* Using the *k*-mers as vertices (A.K.A. nodes or points), the connected *k*-mers can form multiple graphs (*de Bruijn* graphs), and conflicts and bulge/tip/chimeric read in multiple connections can be removed to form a simplied *de Bruijn* graph.
 <br> <br> <br> <br>
![usage-0](https://github.com/jizhang-nz/HTS-training/blob/main/Fig.1.png) <br> <br>
**Fig. 1.** Standard and multisized *de Bruijn graph*. A circular Genome CATCAGATAGGA is covered by a set Reads consisting of nine 4-mers, {ACAT, CATC, ATCA, TCAG, CAGA, AGAT, GATA, TAGG, GGAC}. Three out of 12 possible 4-mers from Genome are missing from Reads (namely {ATAG,AGGA,GACA}), but all 3-mers from Genome are present in Reads. (A) The outside circle shows a separate black edge for each 3-mer from Reads. Dotted red lines indicate vertices that will be glued. The inner circle shows the result of applying some of the glues. (B) The graph DB(Reads, 3) resulting from all the glues is tangled. The three h-paths of length 2 in this graph (shown in blue) correspond to h-reads ATAG, AGGA, and GACA. Thus Reads3,4 contains all 4-mers from Genome. (C) The outside circle shows a separate edge for each of the nine 4-mer reads. The next inner circle shows the graph DB(Reads, 4), and the innermost circle represents the Genome. The graph DB(Reads, 4) is fragmented into 3 connected components. (D) The multisized *de Bruijn* graph DB(Reads, 3, 4). <br> <br>
From [Bankevich et. al 2012](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3342519/).
 <br> <br>
---

## What’s SPAdes good for?
* Short-reads *de novo* assembly.
* Short-reads + Long-read hybrid *de novo* assembly.
* Smaller size genomes (< 100 Mb, such as bacterial, viral, fungal, mitochondrial genomes etc.)

---

## Before using SPAdes
* Know your sequencing read data using [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/)
	* What is the sequencing platform?
	* What is the read length? 
	* Do they contain adaptor sequences?
	* etc.
* Trimming of the sequencing read data such as [Trimmomatic](http://www.usadellab.org/cms/?page=trimmomatic) and [fastp](https://github.com/OpenGene/fastp).
	* Adaptors sequences.
	* Low quality sequences.
	* Very short reads.
	* etc.

* Finding out available version of SPAdes in the server: 

```bash
$ module avail spades
```

* Loading in the version you’d like to use. For example: 

```bash
$ module load  SPAdes/3.15.2-gimkl-2020a
```

---

## Using SPAdes
The test data is part of the 2×150 bp Illumina HiSeq sequencing reads from the whole-genome sequencing project of (supposingly) a brown marmorated stink bug (BMSB). The mitochondrial genome reads were extracted, and the reads were trimmed to Q >= 30 and free from adaptor sequences.

```bash
# make a working directory for SPAdes
$ mkdir /nesi/project/comm00008/PHEL_HTS_TRAINING/USERS/your_name/spades/

# copy the sequencing reads to the SPAdes folder
$ cp /nesi/project/comm00008/PHEL_HTS_TRAINING/module_3/spades/*.fq /nesi/project/comm00008/PHEL_HTS_TRAINING/USERS/your_name/spades/

# or
$ cp /nesi/project/comm00008/PHEL_HTS_TRAINING/module_3/spades/T18-02537.R1_paired.fq /nesi/project/comm00008/PHEL_HTS_TRAINING/USERS/your_name/spades/
$ cp /nesi/project/comm00008/PHEL_HTS_TRAINING/module_3/spades/T18-02537.R1_unpaired.fq /nesi/project/comm00008/PHEL_HTS_TRAINING/USERS/your_name/spades/
$ cp /nesi/project/comm00008/PHEL_HTS_TRAINING/module_3/spades/T18-02537.R2_paired.fq /nesi/project/comm00008/PHEL_HTS_TRAINING/USERS/your_name/spades/
$ cp /nesi/project/comm00008/PHEL_HTS_TRAINING/module_3/spades/T18-02537.R2_unpaired.fq /nesi/project/comm00008/PHEL_HTS_TRAINING/USERS/your_name/spades/

```
 
* Having a look at the available options and default values:
```bash
$ spades.py -h
```

* SPAdes is a multi-k genome assembler, we can just use the recommanded *k*-mers setting. For example, for a sequencing result of 150bp read length, we could set k-mer lengths to `-k 21, 33, 55, 77` .
* `--careful` option is recommanded to minimize number of mismatches in the final assembly results.
* `--threads` option should be consistant with the requested CPUs in the SLURM script, such as  `--threads 2`. By default, the program will use 16 threads.
* `--memory` option should be consistant with the requested memory in the SLURM script, such as  `--memory 4`. By default, the program will use 250 Gb of memory (program terminates if exceeded).
* A example SLURM script to run SPAdes looks like:
```bash
#!/bin/bash -e

#SBATCH --account       nesi03181               #Project_Code/Account_Code for tracking
#SBATCH --job-name      SPAdes              	#An indentifier for the user
#SBATCH --time          00:10:00                #Walltime/Runtime assigned by the user : dd-hh:mm:ss
#SBATCH --cpus-per-task 2                       #Number of CPUS requested per task
#SBATCH --mem           4G                      #Amount of memory (TOTAL)
#SBATCH --output        SPAdes-%j.out           #An output file generated by SLURM
#SBATCH --mail-type     END
#SBATCH --mail-user   your_name@mpi.govt.nz

#Loading the requed software
module purge
module load  SPAdes/3.15.2-gimkl-2020a

#execute the required command
spades.py -1 /nesi/project/comm00008/PHEL_HTS_TRAINING/USERS/your_name/spades/T18-02537.R1_paired.fq -2 /nesi/project/comm00008/PHEL_HTS_TRAINING/USERS/your_name/spades/T18-02537.R2_paired.fq --s1 /nesi/project/comm00008/PHEL_HTS_TRAINING/USERS/your_name/spades/T18-02537.R1_unpaired.fq --s2 /nesi/project/comm00008/PHEL_HTS_TRAINING/USERS/your_name/spades/T18-02537.R2_unpaired.fq -o /nesi/project/comm00008/PHEL_HTS_TRAINING/USERS/your_name/spades/T18-02537 -k 21, 33, 55, 77 --careful --thread 2 --memory 4 
```

* Save and run the SLURM script:
`sbatch run_spades.sh &`

## After using SPAdes
* Assembly quality assessment with QUAST:
```bash
# loading the module QUAST
$ module load QUAST/5.0.2-gimkl-2018b

# running QUAST analysis for the assembly
$ quast.py /nesi/project/comm00008/PHEL_HTS_TRAINING/USERS/your_name/spades/contigs.fasta -o /nesi/project/comm00008/PHEL_HTS_TRAINING/USERS/your_name/spades/quast_output/
```
---

* Visualization *de novo* assembly graphs to explore connections between the contigs:

```bash
# Loading dependency for Bandage
$ module load Qt5/5.13.2-GCCcore-9.2.0

# copy the program Bandage to your folder
$ cp /nesi/project/comm00008/PHEL_HTS_TRAINING/module_3/spades/Bandage /nesi/project/comm00008/PHEL_HTS_TRAINING/USERS/your_name/spades/T18-02537/
```

* Read the [FASTG](http://fastg.sourceforge.net/FASTG_Spec_v1.00.pdf) file and drew image with Bandage:
```bash
# go to the SPAdes output directory
$ cd /nesi/project/comm00008/PHEL_HTS_TRAINING/USERS/your_name/spades/
# draw graph
$ ./Bandage image assembly_graph.fastg T18-02537_assembly_graph.svg
```

* Download and inspect the output image file in WINDOWS using browser like `Microsoft Edage`.
	* Tip: press the Ctrl and roll the mouse wheel to zoom the image.
