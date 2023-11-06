# rivanna-notes

#### Why Rivanna over the CS servers?
Currently, the CS servers allocate based on machine and not based on GPU, which means you could potentially be sharing GPU memory with other people's programs. (This should be fixed next semester (Spring 2024) though)

### Accessing Rivanna:
Part of my `~/.ssh/config`
```
Host rivanna
  User YOURCOMPUTINGID
  HostName rivanna.hpc.virginia.edu
  ProxyJump YOURCOMPUTINGID@portal.cs.virginia.edu
```

### Comparing CS Servers vs Rivanna
Example CS server slurm script:
```
#!/bin/bash -l

# --- Resource related ---
#SBATCH -t 24:00:00 # Day-Hour:Minute
#SBATCH --partition="gpu"
#SBATCH --gpus-per-node=1
#SBATCH --exclude=lynx05,lynx02,affogato11,adriatic05,lynx10,affogato14,lynx03,adriatic01,adriatic03,ristretto01,cheetah03,adriatic06,ristretto04,lynx07,lynx12,lynx06,affogato15,cheetah02,lynx04,lynx01,sds01,jaguar03,lotus,adriatic02,jaguar02,adriatic04,affogato12,lynx11,affogato13,sds02

# --- Task related ---
#SBATCH --output="MYOUTPUTFILE.output"
#SBATCH --job-name="MYJOBNAME"

echo "Hostname -> $HOSTNAME"

source /etc/profile.d/modules.sh

conda activate ENVNAME
echo "which python -> $(which python)"

nvidia-smi

export HF_DATASETS_CACHE="/p/PROJECTDIRNAME/huggingface/datasets"
export TRANSFORMERS_CACHE="/p/PROJECTDIRNAME/huggingface/hub"

python main.py
```

Example of Rivanna slurm script
```
#!/bin/bash -l

# --- Resource related ---
#SBATCH --ntasks=1
#SBATCH -t 24:00:00 # Day-Hour:Minute
#SBATCH -p gpu
#SBATCH --gres=gpu:a100:1
#SBATCH -C gpupod
#SBATCH --mem-per-cpu=32GB
#SBATCH -A ORGACCOUNTNAME

# --- Task related ---
#SBATCH --output="MYOUTPUTFILE.output"
#SBATCH --job-name="MYJOBNAME"

echo "HOSTNAME -> $HOSTNAME"

nvidia-smi

module load anaconda cuda cudnn tmux

conda activate ENVNAME
echo "which python -> $(which python)"

export HF_DATASETS_CACHE="/scratch/COMPUTINGID/PROJECTDIRNAME/huggingface/datasets"
export TRANSFORMERS_CACHE="/scratch/COMPUTINGID/PROJECTDIRNAME/huggingface/hub"

python main.py
```


