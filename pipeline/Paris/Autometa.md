### Step 1: Filtering contigs by length (<1500 bp) 
```
conda activate autometa
export OPENBLAS_NUM_THREADS=1

ASMDIR=~/genome_asm

for asm in $(ls $ASMDIR/*.fa); do

asmbase=$(basename $asm)
filtasm_out=${asmbase}.lenfilt
asmgc_out=${asmbase}.gc

if [ ! -f $filtasm_out && ! -f $asmgc_out ]
	then 
	
# --cutoff (Default: 3000)
autometa-length-filter \
    --assembly $asm \
    --cutoff 1500 \
    --output-fasta $filtasm_out \
    --output-gc-content $asmgc_out

fi
done
```
