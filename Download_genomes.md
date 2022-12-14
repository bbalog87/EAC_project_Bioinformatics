  ### 1. First Attempt

 - Firs attempt using ncbi_datasets utility functions: 


```python
## Example fecthing data for Tanzania
#!/usr/bin/bash
for ACC in $(cut -f1 glassPatho.txt); do

echo "#### Working on $ACC ...#####"
 
datasets summary genome taxon $ACC | jq -r '.assemblies[].assembly 
| select((.biosample.attributes[].name == "geo_loc_name") and (.biosample.attributes[].value|contains("Tanzania"))) 
| .assembly_accession' > ${ACC}_accessions.txt


datasets download genome accession \
   --inputfile ${ACC}_accessions.txt \
   --dehydrated \
   --filename ${ACC}_accessions_dehydrated.zip
  

unzip ${ACC}_accessions_dehydrated.zip -d ${ACC}_Tanzania

datasets rehydrate --directory ${ACC}_Tanzania

echo "#### Working on $ACC COMPLTED !! #####"

done 
```

 
The first attempt was to query NCBI genome databases iteratively, attempting to match the 
geographic location of the isolates containing each EAC country name using the bash script below. This was insufficient
because many important pathogens were not discovered. For example, no single genome for 
*Streptococcus pneumoniae* was publicly available in any of the EAC countries. 
This is unlikely because when I searched NCBI manually for *Streptococcus pneumoniae* 
in Kenya, Uganda, and Tanzania, I found few genomes seqeunces. The script searching for the country 
name did not find them because the metadata to the genome submission does not include the country 
name where the samples were isolated, or the country is spelled differently.
 
 
 ### 2. Second Approach

 In the this approach I  will :
 - Step 1: Get all available genomes informations for all  GLASS pathogens assemblies (chromosomes, Scaffolds, contigs) in GenBank
 - Step 2: Parse the BioSample IDs 
 - Step 3 : use the Biosample IDs to parse the the country name in metadata
 - step 4: Retain only assemblies whose samples were isloated in the EAC region
 
 #### Step 1:  Download all in Genbank available GLASS pathogens assemblies (in Progress ...)
 
 ```bash
 #!/usr/bin/bash


## Loop through accession-Nr of the GLASS species
for ACC in $(cut -f1 glassPatho.txt); do

echo "#### Working on $ACC....#####"

mkdir -p NCBI_TaxID_${ACC}

### Get all available genomes informations for all assembly levels (chromosomes, Scaffolds, contigs) in GenBank database
perl getSequenceInfo.pl -k bacteria -taxid $ACC -getSummaries NCBI_TaxID_${ACC}/NCBI_TaxID_${ACC}_summary.log \
                        -date 2003-01-01 -output NCBI_TaxID_${ACC} -dir genbank 

mv assembly_summary.txt NCBI_TaxID_${ACC}/NCBI_TaxID_${ACC}_summary.txt


echo "#### Working on $ACC COMPLTED !! #####"

done 
```


