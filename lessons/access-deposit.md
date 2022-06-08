## Accessing genomic reference data

During an RNA-seq experiment, the nucleotide sequences stored inside the raw FASTQ files, or "sequence reads", need to be mapped or aligned to the reference genome or reference transcriptome to determine from where these sequences originated. The reference genome or transcriptome will be in FASTA format.

In addition, if mapping against a genome (for QC or expression quantification) you will need to know where the known genes and their exons are located in the reference. These analyses require reference data containing specific information about the genomic coordinates of various genomic “features” (exons, UTRs etc.), and this information can be found in GTF or GFF files. 

To download reference data, there are a few different sources online:

- **General biological databases:** Ensembl, NCBI, and UCSC
- **Organism-specific biological databases:** Wormbase, Flybase, etc. (often updated more frequently, so may be more comprehensive)
- **Reference data collections:** Illumina's iGenomes, one location to access genome reference data from **Ensembl, UCSC and NCBI**
- **Local access:** shared databases on the FAS-RC cluster or HMS-RC's O2 cluster with access to genome reference data from **Ensembl, UCSC and NCBI**

*Note that these reference data sources are relevant to most types of genomic analyses, not just RNA-seq analyses.

### General biological databases

Biological databases for gene expression data store genome assemblies and provide annotations regarding where the genes, transcripts, and other genomic features are located on the genome. 

Genome assemblies give us the **nucleotide sequence of the reference genome**. Although the Human Genome Project was "completed" in 2003, small gaps in the sequence remained (estimated 1% of gene-containing portions). As technology improves and more genomes are sequenced, these gaps are filled, mistakes are corrected and alternate alleles are provided. Therefore, every several years a **new genome build** is released that contains these improvements. 

The **current genome build** is GRCh38/hg38 for the human, which was released in 2013 and is maintained by the Genome Reference Consortium (GRC). Usually the biological databases will include the updated versions as soon as they are stably released, in addition to access to archived versions.

Genome databases incorporate these genomes and generate the gene annotations with the following **similarities/differences**:

- **Ensembl, NCBI, and UCSC** all use the **same genome assemblies or builds** provided by the GRC
	- GRCh38 = hg38; GRCh37 = hg19
	- Patches or minor revisions of the genome, which don't change the genome coordinates, are frequently provided by the GRC. Each database makes the patches available for users at different intervals. If the user applies the patches, the genome reference sequence may differ between databases.
		- GRCh38p1 != GRCh38p2

- Each biological database **independently determines the gene annotations**; therefore, gene annotations between these databases can differ, even though the genome assembly is the same. Naming conventions are also different (chr1=1) between databases.

- **Always use the same biological database for all reference data!**

#### Ensembl

[*Ensembl*](http://useast.ensembl.org/index.html) provides a website that acts as a **single point of access to annotated genomes** for vertebrate species. For all other organisms there are additional Ensembl databases available through [Ensembl Genomes](http://ensemblgenomes.org/); however, they do not include viruses (NCBI does).

- Genome assemblies/builds (reference genomes)
	- New genome builds are released every several years or more depending on the species
	- Genome assemblies are updated every two years to include patches, or less often depending on the species

- Gene annotations
	- Gene annotations are created or updated using a variety of sources (ENA, UniProtKB, NCBI RefSeq, RFAM, miRBase, and tRNAscan-SE databases)
	- Automatic annotation is performed for all species using identified proteins and transcripts
	- Manual curation by the HAVANA group is performed for human, mouse, zebrafish, and rat species, providing better confidence of transcript annotations
	- Directly imports annotations from FlyBase, WormBase and SGD

##### Ensembl identifiers

When using Ensembl, note that it uses the following format for biological identifiers:
	
- **ENSG###########:**	Human Gene ID
- **ENST###########:**	Human Transcript ID
- **ENSP###########:**	Human Peptide ID
- **ENSE###########:**	Human Exon ID
	
For non-human species a suffix is added:

- **ENSMUSG###:** MUS (Mus musculus) for mouse 
- **ENSDARG###:** DAR (Danio rerio) for zebrafish
	
### Finding and accessing reference data on Ensembl

The interface for downloading reference data from Ensembl is straight-forward. On the home page, you can click on `Downloads`.

<p align="center">
<img src="../img/ensembl_download_tab.png" width="500">
</p>

Then click on the section to `Download a sequence or region`.

<p align="center">
<img src="../img/ensembl_download_data.png" width="250">
</p>


In the 'Export Data' window, click on the link for the `FTP site`.

<p align="center">
<img src="../img/ensembl_export_data.png" width="500">
</p>

Finally, right-click on the link to the reference genome (DNA FASTA), reference transcriptome (cDNA FASTA), gene annotation file (Gene sets, GTF or GFF), or other required reference data to download. Copy the link address.

<p align="center">
<img src="../img/ensembl_ftp.png" width="700">
</p>

>**NOTE:** If you desired an archived version of the genome, on the Ensembl home page for the organism of interest you would click on the `View in archive site` link in the lower right-hand corner of the page. Then you would navigate as described above.

To run the script, you would use the following `sbatch` command:

Genomic reference data could be downloaded similarly by FTP from the [NCBI FTP (or through Aspera)](https://www.ncbi.nlm.nih.gov/home/download/) or the [UCSC FTP](https://genome.ucsc.edu/goldenpath/help/ftp.html).


### Reference data collections: iGenomes

If working on a commonly analyzed organism, Illumina's iGenomes has facilitated the process of downloading reference data. On the [iGenomes website](https://support.illumina.com/sequencing/sequencing_software/igenome.html) reference data from Ensembl, UCSC and NCBI for various genome builds are available for download. In addition, the download is a compressed file containing the matching reference genome (FASTA) and gene annotation (GTF/GFF) files. 

To download from iGenomes, we can right-click and click on "save link as" to download the files. Please note that these are very large and you might want to download these to the cluster (if they don't already exist there).

> <p align="center">
> <img src="../img/igenomes_download.png" width="500">
> </p>
> 
> ```bash
> #!/bin/bash
> 
> #SBATCH -p shared 	# partition name (small partition on O2)
> #SBATCH -t 0-6:00 	# hours:minutes runlimit after which job will be killed
> #SBATCH -n 1 		# number of cores requested 
> #SBATCH -o %J.out	# File to which standard out will be written
> #SBATCH -e %J.err 	# File to which standard err will be written
> 
> $ wget ftp://igenome:G3nom3s4u@ussd-ftp.illumina.com/Homo_sapiens/NCBI/GRCh38/Homo_sapiens_NCBI_GRCh38.tar.gz
> ```
> 
> After running the script with the `sbatch` command, you could use the `tar` command to unpack it. 
> 
> ```bash
> $ tar -xzf Homo_sapiens_NCBI_GRCh38.tar.gz
> ```
>
> If desired you could just run the `tar` command after the `wget` command in the above script to automatically unpack the reference data. 

### Local access via O2

Downloading the reference data from biological database or iGenomes might not be necessary since the O2 cluster has **shared reference data** downloaded from iGenomes available to its users. 

On O2 shared data is located at `/n/groups/shared_databases/igenome/`. Instead of using storage space inside your folder, give the path to the reference data in these shared databases instead.


### Organism-specific databases

Although the general genomic databases update the genome builds and annotations for all species, organism-specific databases often update the genome patches and gene annotations more frequently. In addition, these databases also offer genomes for other species that may not be present in the general databases. Additional tools and information regarding these organisms are also accessible.

#### Wormbase

As an example, we will explore [WormBase ParaSite](http://parasite.wormbase.org/index.html), which is devoted to the study of *C. elegans* and other nematodes, in addition to, helminths. This site is closely linked with *WormBase*, and incorporates the information for *C. elegans* and other nematodes from this repository. 

On the homepage, there is direct access to WormBase, links to all species genomes available, tools, and news.

<p align="center">
<img src="../img/wormbase_homepage.png" width="500">
</p>

#### Finding and accessing reference data on Wormbase

Downloading reference data from WormBase ParaSite is intuitive and simple. All that is needed is to click on the `Downloads` tab.

<p align="center">
<img src="../img/wormbase_homepage_download.png" width="400">
</p>

This will take you to the FTP site, where you can right-click to "save link as" for the reference data of interest.

<p align="center">
<img src="../img/wormbase_downloads.png" width="700">
</p>

## Depositing Results and Raw Sequence data

### [NCBI's Gene Expression Omnibus (GEO)](https://www.ncbi.nlm.nih.gov/geo/)

You can upload all your data via the GEO submission system, including the raw sequencing data which is automatically submitted to SRA.

<p align="center">
<img src="../img/GEO.png" width="400">
</p>

### [NCBI's Sequence Read Archive (SRA)]()

This is the major repository in the US for **all types** of sequencing data.

<p align="center">
<img src="../img/SRA.png" width="400">
</p>

Both of the tools above require you to have complete information regarding your experiment, including what sequencing chemistry you used and what library prep methods you used.

---
*This lesson has been developed by members of the teaching team at the [Harvard Chan Bioinformatics Core (HBC)](http://bioinformatics.sph.harvard.edu/). These are open access materials distributed under the terms of the [Creative Commons Attribution license](https://creativecommons.org/licenses/by/4.0/) (CC BY 4.0), which permits unrestricted use, distribution, and reproduction in any medium, provided the original author and source are credited.*
