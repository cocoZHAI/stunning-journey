## Batch AF3 Script
### Prep: Generate Your JSON File List
- Before submitting this script, run the following command once:
```
ls $HOME/af3_input_files/citrate_hits/*.json > $HOME/af3_input_files/citrate_hits_list.txt

```

- Then confirm the number of JSONs:
```
wc -l $HOME/af3_input_files/citrate_hits_list.txt
```

- Save this as something like ```submit_af3_json_jobs.bash```

```#!/bin/bash
#BSUB -q gpu
#BSUB -J "af3_json[1-1293]"   # ⬅️ Update "1293" to the actual number of JSON files
#BSUB -gpu "num=1:mode=exclusive_process:j_exclusive=yes"
#BSUB -R "rusage[mem=52G]"
#BSUB -n 8
#BSUB -W 8:00

# Set Alphafold3 Singularity container path
export ALPHAIMG=/share/pkg/containers/alphafold/alphafold_unified-mem-3.0.0.sif

# Path to list of JSON input files (you’ll generate this list)
JSON_LIST="$HOME/af3_input_files/json_list.txt"

# Get this job’s JSON file path using its array index
JSON_PATH=$(sed -n "${LSB_JOBINDEX}p" "$JSON_LIST")
BASENAME=$(basename "$JSON_PATH" .json)

# Redirect logs
exec > "$HOME/af3_output_files/${BASENAME}.out" 2> "$HOME/af3_output_files/${BASENAME}.err"

# Run AlphaFold3 using the JSON input
singularity exec --nv --bind \
/home/qing.yu-umw/public_databases:/databases,\
/home/qing.yu-umw/models:/models,\
$HOME/af3_input_files/citrate_hits:/input,\
$HOME/af3_output_files:/output \
${ALPHAIMG} \
env XLA_FLAGS="--xla_gpu_cuda_data_dir=/usr/local/cuda-12.2 --xla_disable_hlo_passes=custom-kernel-fusion-rewriter" \
python /app/alphafold/run_alphafold.py \
--db_dir=/databases \
--model_dir=/models \
--json_path="/input/$(basename "$JSON_PATH")" \
--output_dir=/output \
--flash_attention_implementation xla
```
