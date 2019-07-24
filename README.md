# Bird Genome Size Project :bird:

## Background

Variation in animal genome size is a difficult subject to study. The primary issue arises in determining the accuracy of existing methods of estimating size. The method used for many years was based around measuring the size of the nuclei of somatic cells. Genome size is also measured by quantifying the amount of DNA in a cell using approaches like flow cytometry and feulgen density. A more modern approach is to use assembly length, but this method has a tendency to erroneously reduce genome size due to SSRs throughout the genome no being properly aligned.The best data to use *in silico* for genome size estimation from genomic data are the raw reads themselves, which do not have the biases associated with whole genome assembly algorithms.


##  Project goals

The primary goal of this project is to use k-mer depth analysis to establish more accurate estimates of bird genome sizes.

## Step 1: Automated download of datasets

inspiration code for downloading palaeonagnathae metadata from ENA

```
wget -qO- "https://www.ebi.ac.uk/ena/data/view/Taxon:8783&portal=assembly&subtree=true&display=text" | awk '{ print $NF }' | while read assembly; do biosample=`wget -qO- "https://www.ebi.ac.uk/ena/data/view/${assembly}&display=xml" | grep -A5 "<SAMPLE_REF>" | grep "<PRIMARY_ID>" | awk -F "<|>" '{ print $3 }'`; species=`wget -qO- "https://www.ebi.ac.uk/ena/data/view/${assembly}&display=xml" | grep -A5 "<TAXON>" | grep "<SCIENTIFIC_NAME>" | awk -F "<|>" '{ print $3 }'| tr " " "-"`; echo -e "$assembly\t$biosample\t$species"; done`
```

### Cid V1

START

READ<ASSEMBLY> metadata

FOR sample_acession != null

wget http://www.ebi.ac.uk/ena/data/view/<sample_acession>&display=xml

grep nominal_length

grep read_count

IF variable nominal_length < 1000 AND read_count > 20000
THEN wget http://www.ebi.ac.uk/ena/data.veiw/<run_acession>&display=xml


### Daren’s version

Inputs:
NCBI/EBI taxon ID number ( = 8783 for Paleognaths)

1. Retrieve list of all target taxa genome assemblies using taxon ID ( = all Paleognath genome metadata)
2. For each assembly:
    * Retrieve associated sample accession (sometimes called BioSamples)
    * Use BioSample to retrieve run accession metadata tables
    * Filter runs based on fragment length (called “nominal_length”) - keeping short-insert libraries only
    * For each “passing” run:
            * Generate species-specific storage location path
            * Download the paired read data to the generated storage location path

### Cid V2

1. Retrieve list of all target taxa with genome assemblies using taxon ID ( = all Paleognath genome metadata)
2. For each species
    * Generate species-specific storage location path
    * Retrieve list of all assemblies
    * Determine best/most recent assembly
    * For each assembly
        * Retrieve associated sample accession (sometimes called BioSamples)
        * Use BioSample to retrieve run accession metadata tables
        * Download to species-specific folder
        * Open run table
        * calculate total coverage and ensure it is above minimum coverage threshold
        * add include/exclude column for population
        * for each record in run table
           * if insert length (called “nominal_length”) <= TBD - keeping short-insert libraries only
               * Download the paired read data to the generated storage location path by proportionally subsampling all short-insert libraries
               * Mark run_accession data from metadata table as included
           * else (insert length > TBD - long-insert libraries that we do not want)
               * Mark run_accession data from metadata table as excluded

#### Probable Biases/concerns

1. Short fragment lengths
2. Inconsistent sampling of target species
3. Potential issue with relative accuracy of different sequencing methods
