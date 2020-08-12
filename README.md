[TOC]

# High-throughput cultivation and identification of bacteria from the plant root microbiota (Culturome)

This is the computational part of the detailed protocol for high-throughput bacterial isolation and cultivation. Now it is prepared for Nature Protocols.
The overview of the workflow is in the following figures. 


![image](http://210.75.224.110/github/Culturome/script/fig/fig1.jpg)

**Fig. 1 | Overview of the high-throughput bacterial cultivation and identification system.**

![image](http://210.75.224.110/github/Culturome/script/fig/fig4.jpg)

**Fig. 4 | Workflow of bioinformatic analysis to identify cultivated bacteria**. a, Summary of the steps from obtaining raw data to generating clean sequences of the amplified 16S rRNA gene region. The amplified clean sequences are obtained by merging paired-end reads, demultiplexing plate and well barcodes, and removing PCR primer sequences. b, Workflow to identify amplicon sequence variants (ASVs) and taxonomy. Clean sequences are dereplicated with VSEARCH, denoised with USEARCH to identify ASV sequences, and taxonomically classified using the RDP database. c, Sequence and taxonomy information for cultivated bacteria in 96-well cell culture plates. The ASV table shows bacterial 16S rRNA gene sequence information for each well in a 96-well cell culture plate. The annotated ASV list shows information for wells with bacteria belonging to the corresponding ASV, including purity, sequence count, and taxonomy. 

![image](http://210.75.224.110/github/Culturome/script/fig/fig6.jpg)

**Fig. 6 | Anticipated results for bacterial cultivation from the Oryza sativa L. roots.**
a. Bar plot showing the distribution of read counts in each plate generated by the two-sided barcode system. Read counts of amplified 16S rRNA gene sequences in each plate of cultivated bacteria reveal the depth and evenness of Illumina sequencing. This figure includes sequencing data from 45 cell culture plates of cultivated bacteria derived from Nippornbare rice roots. The original data have been used in Nature biotechnology11. b. Read counts for the negative (nuclease-free water as the PCR template) and positive controls (E. coli DNA as the PCR template). c. Rarefaction curve of ASVs based on the number of wells containing bacteria. The curve reaches the plateau stage, indicating that a sufficient number of plates were used for high-throughput bacterial isolation. The iteration number for each well number is 30. The horizontal bars within boxes represent medians. The tops and bottoms of boxes represent the 75th and 25th percentiles, respectively. The upper and lower whiskers extend to data no more than 1.5 × the interquartile range from the upper edge and lower edge of the box. d. Distribution of wells regarding the purity based on evaluation of ASVs of the bacterial 16S rRNA gene. Consistent with the assumption that low abundance sequences may come from multiple polymorphic copies of the 16S rRNA gene and sequencing errors, we found that cultivated bacterial with a purity greater than 95% are likely to be pure when we performed streaking on agar plates. The ratio of wells with purify greater than 95% to the rest wells containing multiple bacteria is 3.6:1. e. An example of the purity of wells containing cultivated bacteria in a 96-well cell culture plate. f. Cladogram showing the taxonomic distribution and occurrence frequency of cultivated bacteria. The inner ring represents the dereplicated ASVs from cultivated root bacteria. The heat map in the outer ring represents the log2-transformed number of cultivated bacterial isolates belonging to the corresponding ASV.

## Equipment

Personal computer running with 64-bit Linux Ubuntu 16.04+ or CentOS 7.5+, at least dual-core 2.4 GHz, 4 GB of RAM (8 GB preferred) and 30 GB space. The analysis pipeline is mainly based on QIIME, USEARCH, VSEARCH and home-made scripts (Culturome pipeline). All the home-made scripts and manual is deposit in https://github.com/YongxinLiu/Culturome. The dependency databases is RDP. To simplify the installation procedure for all computational software, we provide a fully installed VirtualBox image http://210.75.224.110/culturome/download/QIIME-1.9.1-amd64.zip (6 GB). Additionally, a graphic user interface Webserver have be construct for user to analysis culturomics data online http://bailab.genetics.ac.cn/culturome/.

### Computer requirements

- Operational system requirements: 64-bit Linux, such as Ubuntu 16.04+ or CentOS 7.5+.
- Hardware requirements: > 4 GB of RAM and 30 GB free disk space.

### Software

- Conda 4.8.3, software management system (https://repo.anaconda.com/miniconda/).
- R 3.4+ (https://cran.r-project.org). The analysis was tested using R 3.5.1. Packages include ggplot243, pheatmap, dplyr, stringr should be installed. 
- QIIME version 1.9.1 http://qiime.org/ for split barcodes
- USEARCH v10.0.240 http://www.drive5.com/usearch
- VSEARCH v.2.7.1 or later https://github.com/torognes/vsearch
- GraPhlAn 1.1.3-1, for visualizing taxonomic tree
- Culturome v1.0 analysis pipeline, related scripts, and example results are available at https://github.com/YongxinLiu/Culturome

### Input data files

- Sequencing Data：The raw sequence data used in this paper have been deposited in the Genome Sequence Archive (GSA) in BIG Data Center, under accession numbers CRA002517. 
- RDP train set 16 as taxonomy database. The USEARCH formatted taxonomy is in http://www.drive5.com/sintax


## Equipment setup

For Linux system, conda (https://docs.conda.io/en/latest/miniconda.html) is importantance to setup the pipeline. We tested the pipeline on Ubuntu 16.04 and 18.04.

To install Conda, download the latest version from https://docs.conda.io/en/latest/miniconda.html, install by bash, and configure the Bioconda58 channel for easy-install software in biology: 

    wget -c https://repo.continuum.io/miniconda/Miniconda2-latest-Linux-x86_64.sh
    bash Miniconda2-latest-Linux-x86_64.sh
    
According the tip: type `yes` to bash Miniconda2-latest-Linux-x86_64.sh, then Press `ENTER` to confirm the location. After install finished, close and re-open your current terminal to effect the changes.

    conda config --add channels bioconda

(Optinal) For Chinese user only, add tsinghua mirror to accelerate download.

    conda config --add channels conda-forge
    site=https://mirrors.tuna.tsinghua.edu.cn/anaconda
    conda config --add channels ${site}/cloud/conda-forge/
    conda config --add channels ${site}/cloud/bioconda/

To install the QIIME pipeline by conda:

    conda install qiime=1.9.1
    
If can't install it due to can't solving the environment, try to create a new environment.
    
	conda create -n qiime1 python=2.7 qiime matplotlib=1.4.3 mock nose -c bioconda
	conda activate qiime1

More install methods or troubleshooting in http://qiime.org/install/install.html

If you install with problem, a QIIME VirtualBox image can be downloaded from http://qiime.org/install/virtual_box.html, and run in Windows/Mac/Linux system following the instructions.

To install Cultrome pipeline, download them from Github, unzip the zip file and add the directory to your PATH environment variable: 

    wget -c https://github.com/YongxinLiu/Culturome/archive/master.zip
    unzip master.zip
    cd Culturome-master
    export PATH=`pwd`/script:$PATH
    chmod +x script/*

(Optional) Add environment variables permanently

    echo export PATH=`pwd`/script:\$PATH >>  ~/.bashrc
    
To install USEARCH 10.0, download usearch10.0.240 Linux version to the script folder and rename it as "usearch":

Please visit http://www.drive5.com/usearch/download.html, and download [usearch10.0.240_i86linux32.gz](https://drive5.com/downloads/usearch10.0.240_i86linux32.gz), rename as "usearch", and put it in "script" directory.

Add executable permissions

    wget -c https://drive5.com/downloads/usearch10.0.240_i86linux32.gz -O script/usearch.gz
    gunzip script/usearch.gz
    chmod +x script/usearch

To install VSEARCH, visit https://github.com/torognes/vsearch and follow the "Download and install" section:

    wget https://github.com/torognes/vsearch/archive/v2.14.2.tar.gz
    tar xzf v2.14.2.tar.gz
    cd vsearch-2.14.2
    ./autogen.sh
    ./configure
    make
    # as root or sudo make install
    sudo make install  

To install R language and related packages:

    conda install r-base r-dplyr r-optparse r-stringr r-ggplot2 r-pheatmap
    
[To install GraPhlAn 1.1.3-1 by conda](https://bitbucket.org/nsegata/graphlan)

    conda install graphlan=1.1.3-1

Prepare input fastq file. If you want to use our web server, we recommend depositing data in GSA (gsa.big.ac.cn), which greatly reduces the downloading time. Alternative public data centers such as the National Center for Biotechnology Information (NCBI) or the European Bioinformatics Institute (EBI) can also provide downloading links in online analysis mode. Here, we use CRR127980 from GSA as an example to test the pipeline and provide an overview of the results:


    # Download CRR127980 and rename as L1
    wget -c ftp://download.big.ac.cn/gsa3/CRA002517/CRR127980/CRR127980_f1.fq.gz -O L1_1.fq.gz
    wget -c ftp://download.big.ac.cn/gsa3/CRA002517/CRR127980/CRR127980_r2.fq.gz -O L1_2.fq.gz
    # decompress
    gunzip *.gz

? TROUBLESHOOTING

Download the RDP taxonomy database; the USEARCH compatible format is available at http://www.drive5.com/sintax

    wget -c http://www.drive5.com/sintax/rdp_16s_v16_sp.fa.gz
    gunzip *.gz

  (Optional) To simplify the installation procedure for all computational software, we provide a fully installed VirtualBox image https://github.com/YongxinLiu/Culturome

## Analysis protocols

If you have only one sequencing library, please follow the instruction in pipeline.md. If you have more than one sequencing library, please follow the instruction in pipeline_multiple.md.

## Reference

1. Jingying Zhang, Yong-Xin Liu, Na Zhang, Bin Hu, Tao Jin, Haoran Xu, Yuan Qin, Pengxu Yan, Xiaoning Zhang, Xiaoxuan Guo, Jing Hui, Shouyun Cao, Xin Wang, Chao Wang, Hui Wang, Baoyuan Qu, Guangyi Fan, Lixing Yuan, Ruben Garrido-Oter, Chengcai Chu & Yang Bai. NRT1.1B is associated with root microbiota composition and nitrogen use in field-grown rice. Nature Biotechnology. 2019, 37: 676-684. doi:10.1038/s41587-019-0104-4
2. J. Gregory Caporaso, Justin Kuczynski, Jesse Stombaugh, Kyle Bittinger, Frederic D. Bushman, Elizabeth K. Costello, Noah Fierer, Antonio Gonzalez Peña, Julia K. Goodrich, Jeffrey I. Gordon, Gavin A. Huttley, Scott T. Kelley, Dan Knights, Jeremy E. Koenig, Ruth E. Ley, Catherine A. Lozupone, Daniel McDonald, Brian D. Muegge, Meg Pirrung, Jens Reeder, Joel R. Sevinsky, Peter J. Turnbaugh, William A. Walters, Jeremy Widmann, Tanya Yatsunenko, Jesse Zaneveld & Rob Knight. QIIME allows analysis of high-throughput community sequencing data. Nature Methods. 2010, 7: 335-336. doi:10.1038/nmeth.f.303
3. Robert C. Edgar. Search and clustering orders of magnitude faster than BLAST. Bioinformatics. 2010, 26: 2460-2461. doi:10.1093/bioinformatics/btq461
4. Torbjørn Rognes, Tomáš Flouri, Ben Nichols, Christopher Quince & Frédéric Mahé. VSEARCH: a versatile open source tool for metagenomics. PeerJ. 2016, 4: e2584. doi:10.7717/peerj.2584
5. James R. Cole, Qiong Wang, Jordan A. Fish, Benli Chai, Donna M. McGarrell, Yanni Sun, C. Titus Brown, Andrea Porras-Alfaro, Cheryl R. Kuske & James M. Tiedje. Ribosomal Database Project: data and tools for high throughput rRNA analysis. Nucleic Acids Research. 2014, 42: D633-D642. doi:10.1093/nar/gkt1244
6. Francesco Asnicar, George Weingart, Timothy L. Tickle, Curtis Huttenhower & Nicola Segata. Compact graphical representation of phylogenetic data and metadata with GraPhlAn. PeerJ. 2015, 3: e1029. doi:10.7717/peerj.1029
7. Yanqing Wang, Fuhai Song, Junwei Zhu, Sisi Zhang, Yadong Yang, Tingting Chen, Bixia Tang, Lili Dong, Nan Ding, Qian Zhang, Zhouxian Bai, Xunong Dong, Huanxin Chen, Mingyuan Sun, Shuang Zhai, Yubin Sun, Lei Yu, Li Lan, Jingfa Xiao, Xiangdong Fang, Hongxing Lei, Zhang Zhang & Wenming Zhao. GSA: Genome Sequence Archive*. Genomics, Proteomics & Bioinformatics. 2017, 15: 14-18. doi:10.1016/j.gpb.2017.01.001
8. National Genomics Data Center Members Partners. Database Resources of the National Genomics Data Center in 2020. Nucleic Acids Research. 2020, 48: D24-D33. doi:10.1093/nar/gkz913
9. Eric W Sayers, Jeff Beck, J Rodney Brister, Evan E Bolton, Kathi Canese, Donald C Comeau, Kathryn Funk, Anne Ketter, Sunghwan Kim, Avi Kimchi, Paul A Kitts, Anatoliy Kuznetsov, Stacy Lathrop, Zhiyong Lu, Kelly McGarvey, Thomas L Madden, Terence D Murphy, Nuala O’Leary, Lon Phan, Valerie A Schneider, Françoise Thibaud-Nissen, Bart W Trawick, Kim D Pruitt & James Ostell. Database resources of the National Center for Biotechnology Information. Nucleic Acids Research. 2020, 48: D9-D16. doi:10.1093/nar/gkz899
10. Charles E Cook, Oana Stroe, Guy Cochrane, Ewan Birney & Rolf Apweiler. The European Bioinformatics Institute in 2020: building a global infrastructure of interconnected data resources for the life sciences. Nucleic Acids Research. 2019, 48: D17-D23. doi:10.1093/nar/gkz1033

## ? TROUBLESHOOTING

wget download and rename problem, result only 8kb.
Try download and rename seperate.

    wget -c ftp://download.big.ac.cn/gsa3/CRA002517/CRR127980/CRR127980_f1.fq.gz
    mv CRR127980_f1.fq.gz L1_1.fq.gz
    wget -c ftp://download.big.ac.cn/gsa3/CRA002517/CRR127980/CRR127980_r2.fq.gz
    mv CRR127980_r2.fq.gz L1_2.fq.gz
    gunzip *.gz
 
## Contact

Dr. Yong-Xin Liu

Yang Bai's lab, State Key Laboratory of Plant Genomics (SKLPG),
Institute of Genetics and Developmental Biology, Chinese Academy of Sciences

No.1 West Beichen Road, Chaoyang District, Beijing, 100101, China

Tel: +86-10-64808722

E-mail: yxliu@genetics.ac.cn

Wechat: meta-genomics
