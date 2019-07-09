# RNAIndel

RNAIndel calls coding indels from tumor RNA-Seq data and 
classifies them as somatic, germline, and artifactual.
You can also use RNAIndel as a postprocessor to
classify indels called by your own caller. 
RNAIndel supports GRCh38.<br> 
(GRCh37 to be supported soon)

## Dependencies
* [python>=3.5.2](https://www.python.org/downloads/)
    * [pandas>=0.23.0](https://pandas.pydata.org/) 
    * [scikit-learn>=0.18.1](http://scikit-learn.org/stable/install.html#)
    * [pysam>=0.13.0](https://pysam.readthedocs.io/en/latest/index.html)
* [java>=1.8.0](https://www.java.com/en/download/) 

## Setup
Install RNAIndel. Be sure that the [dependencies](#dependencies) are satisfied before pip installation.  
```
pip install rnaindel
```
Test the installation.
```
rnaindel -h
```

Download data package ([GRCh38](http://ftp.stjude.org/pub/software/RNAIndel/data_dir_38.tar.gz)) and 
unpack it in a convenient directory on your system. 

```
tar xzvf data_dir_38.tar.gz  # for GRCh38
```

## Usage 
RNAIndel has 6 subcommands:
* ```analysis``` analyze RNA-Seq data for indel discovery
* ```feature``` calculate features for training
* ```nonsomatic``` make a non-somatic indel panel
* ```reclassification``` reclassify false positives by non-somatic panel
* ```recurrence``` annotate false positives by recurrence
* ```training``` train and update the models

Subcommands are invoked:
```
rnaindel subcommand [subcommand-specific options]
```

### Discover somatic indels ([demo](./sample_data))

#### Use the built-in caller
RNAIndel calls indels by the [built-in caller](https://academic.oup.com/bioinformatics/article/27/6/865/236751), which is optimized 
for RNA-Seq indel calling, and classifies detected indels as somatic, germline, and artifactual. 
```
rnaindel analysis -i BAM -o OUTPUT_VCF -r REFERENCE -d DATA_DIR [other options]
```
#### Use your caller 
RNAIndel can be used as a postprocessor for indel calls generated by your caller such as 
[GATK-HaplotypeCaller](https://software.broadinstitute.org/gatk/documentation/tooldocs/4.0.8.0/org_broadinstitute_hellbender_tools_walkers_haplotypecaller_HaplotypeCaller.php), 
[Mutect2](https://software.broadinstitute.org/gatk/documentation/tooldocs/4.0.8.0/org_broadinstitute_hellbender_tools_walkers_mutect_Mutect2.php)
and [freebayes](https://github.com/ekg/freebayes). Specify the input VCF file with ```-v```.
```
rnaindel analysis -i BAM -v INPUT_VCF -o OUTPUT_VCF -r REFERENCE -d DATA_DIR [other options]
```
#### Options
* ```-i``` input [STAR](https://academic.oup.com/bioinformatics/article/29/1/15/272537)-mapped BAM file (required)
* ```-o``` output VCF file (required)
* ```-r``` reference genome FASTA file (required)
* ```-d``` [data directory](#setup) contains trained models and databases (required)
* ```-v``` VCF file from user's caller (default: None)
* <details>
    <summary>other options (click to open)</summary><p>
    
    * ```-q``` STAR mapping quality MAPQ for unique mappers (default: 255)
    * ```-p``` number of cores (default: 1)
    * ```-m``` maximum heap space (default: 6000m)
    * ```-l``` directory to store log files (default: current)
    * ```-n``` user-defined panel of non-somatic indels in tabixed VCF format (default: built-in reviewed indel set)
    * ```-g``` user-provided germline indel database in tabixed VCF format (default: built-in database in data dir) <br>
    &nbsp;   &nbsp;   &nbsp;   &nbsp;use only if the model is trained with the user-provided database ([more](./rnaindel/training_lib)).      
    * ```--exclude-softclipped-alignments``` softclipped indels will not be used for analysis if added (default: False)

</p></details>

### Train RNAIndel
Users can [train](./docs/training) RNAIndel with their own training set. 

### Filter false positives
RNAIndel supports [custom filtering](./docs/filtering) to refine the predicted results.

## Limitations
1. RNAIndel does not perform well for samples with microsatellite instability such as colon adenocarcinoma hypermutated subtype. 

## Reference
1. Hagiwara, K., Ding, L., Edmonson, M.N., Rice, S.V., Newman, S., Meshinchi, S., Ries, R.E., Rusch, M., Zhang, J. 
RNAIndel: a machine-learning framework for discovery of somatic coding indels using tumor RNA-Seq data.
([preprint](https://www.biorxiv.org/content/early/2019/01/07/512749?rss=1))  

