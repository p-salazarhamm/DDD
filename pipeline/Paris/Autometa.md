### Step 1: Filtering contigs by length (<1500 bp) 
```
conda activate autometa
export OPENBLAS_NUM_THREADS=1

ASMDIR=$1

for asm in $(ls $ASMDIR/*.fa); do

asmbase=$(basename $asm)
filtasm_out=${asmbase}.lenfilt
asmgc_out=${asmbase}.gc

if [ ! -f ${filtasm_out} && ! -f ${asmgc_out} ]
	then 
	
# --cutoff (Default: 3000)
autometa-length-filter \
    --assembly ${asm} \
    --cutoff 1500 \
    --output-fasta ${filtasm_out} \
    --output-gc-content ${asmgc_out}

fi
done
```
### Step 2: Determine coverages from assembly read alignments
```
conda activate autometa
export OPENBLAS_NUM_THREADS=1

BAMdir=$1

for bam in $(ls $BAMdir); do

base=$(echo ${bam} | sed 's/.bam//') 

#Outputs
bed=${base}.coverages.bed.tsv
coverages=${base}.coverages.tsv

if [ ! -f $coverages ]
then
autometa-bedtools-genomecov --ibam ${BAMdir}${bam} --bed ${bed} --output ${coverages}
fi  
done
```
### Step 3: Annotate markers
```
conda activate autometa
export OPENBLAS_NUM_THREADS=1

ProkkaDir=$1
kingdoms=(archaea bacteria)

while read R1 R2 ASM LTP ID phylum class order family; do
   for kingdom in ${kingdoms[@]}; do
  	if [[ "$kingdom" == "archaea" ]];
  	then db=~/archaea_odb10_combined.hmm
        else db=~/bacteria_odb10_combined.hmm
        fi
        orfs=${ProkkaDir}/${LTP}_prokka/${LTP}.faa
        hmmscan=${LTP}.${kingdom}.hmmscan.tsv
        markers=${LTP}.${kingdom}.markers.tsv

if [ ! -f ${hmmscan} && ! -f ${markers} ]
	then 	
	
autometa-markers --orfs ${orfs} --kingdom ${kingdom} --hmmdb ${db} --hmmscan ${hmmscan} --out ${markers} --parallel --cpus 4 --seed 42
fi 
done
```	
