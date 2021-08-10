# Overview

[SPAdes](https://cab.spbu.ru/software/spades/) – St. Petersburg genome assembler [*de novo* genome assembly with de Bruijn graphs](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3342519/)

---

## Contents
1. [Genome assembly and *de Bruijn* graphs.](#algorithm)
1. [What’s Spades good for?](#introduction)
1. [Before using Spades.](#Before-using-Spades)
1. [Using Spades.](#Using-Spades)
1. [After using Spades.](#After-using-Spades)

---

## Genome assembly and *de Bruijn* graphs
* Each reads were disseted into *k*-mers of the length *k*. For example, for the sequence `ATGCAT`, when *k*=3, the string can be dissected into four substrings of *3*-mers: <br>`ATG` <br>`TGC` <br>`GCA` <br>`CAT`
* Each *k*-mers were further splitted into two substrings: **prefix** and **suffix**. Both substrings have the length of *k-1*. For example, for *3*-mer `ATG`, the **prefix** is `AT` and the **suffix** is `TG`;
* The *k*-mers that shared a common **prefix** and **suffix** can be connected. For example, the **sufix** of `ATG` and the **prefix** of `TGC` are identical, therefore they can be connected and formed a new sequence `ATGC`
* In this way, more overlapping *k*-mers can be connected and forming a longer new sequence.
* Using the *k*-mers as vertices (AKA nodes or points), the connected *k*-mers can form a graph (*de Bruijn* graph), and conflicts and 'bulges' in multiple connections can be mathematically resolved to form a optimum new sequence, which is called a **sequence contig**.
 <br> <br>![usage-0](https://github.com/jizhang-nz/HTS-training/blob/main/Fig.1.png)

---

## What’s Spades good for?
* Short-reads *de novo* assembly.
* Short-reads + Long-read hybrid *de novo* assembly.
* Smaller size genomes (bacterial, viral, fungal, mitochondrial genomes etc.)

---

## Before using Spades
* Know your sequencing read data: [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/)
	* What is the read length? 
	* Do they contain adaptor sequences?
* Trimming of the sequencing read data such as [Trimmomatic](http://www.usadellab.org/cms/?page=trimmomatic) and [fastp](https://github.com/OpenGene/fastp).
	* Adaptors sequences.
	* Low quality sequences.
	* Very short reads.

* Finding out available version of Spades in the server: 

```bash
$ module avail spades
```

* Loading in the version you’d like to use: 

```bash
$ module load  SPAdes/3.15.2-gimkl-2020a
```

---

## Using Spades
* Finding out available options and default values:

```bash
$ spades.py -h
```

## After using Spades
* Assembly quality assessment with QUAST:

```bash
# loading the module QUAST
$ Module load QUAST/5.0.2-gimkl-2018b

# running QUAST analysis for the assembly
$ quast.py *.fasta -o quast_output
```
---

* Visualization *de novo* assembly graphs to explore connections between the contigs:

```bash
# Loading dependency for Bandage
$ module load Qt5/5.13.2-GCCcore-9.2.0

# Copy the program Bandage to the folder storing the FASTG files:
$ cp /module_4/Bandage /folder

# Read [FASTG](http://fastg.sourceforge.net/FASTG_Spec_v1.00.pdf) file and drew image with Bandage:
$ ./Bandage image *.fastg *.svg

```
Download and inspect the output image file in WINDOWS using browser like `Microsoft Edage`.
* Tip: press the Ctrl and roll the mouse wheel to zoom the image.
