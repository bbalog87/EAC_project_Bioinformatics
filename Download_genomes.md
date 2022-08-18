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
 - Step 1:  Download all in Genbank avaible GLASS pathogens assemblies 
 - Step 2: Parse the BioSample IDs 
 - Step 3 : use the Biosample IDs to parse the the country name in metadata
 - step 4: Retain only assembly assocaited with the EAC region
 
 # Step 1:  Download all in Genbank avaible GLASS pathogens assemblies 
