 - ### First Attempt
 
The first attempt was to query NCBI genome databases iteratively, attempting to match the 
geographic location of the isolates containing each EAC country name using the bash script below. This was insufficient
because many important pathogens were not discovered. For example, no single genome for 
*Streptococcus pneumoniae* was publicly available in any of the EAC countries. 
This is unlikely because when I searched NCBI manually for *Streptococcus pneumoniae* 
in Kenya, Uganda, and Tanzania, I found few genomes seqeunces. The script searching for the country 
name did not find them because the metadata to the genome submission does not include the country 
name where the samples were isolated, or the country is spelled differently.
 
 - Firs attempt using ncbi_datasets utility functions: 


```python
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
