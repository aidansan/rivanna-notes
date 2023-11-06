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

### Scratch directory:
You should try to put as much stuff as you can into the `/scratch/YOURCOMPUTINGID`, because reading and writing is faster, but the people at rivanna delete unaccessed data.
The workflow that I use is...
1. Do development on the cs servers
2. Push data to rivanna
3. Commit to github
4. Pull code from github into the scratch directory on the rivanna servers 
5. Run code on rivanna
6. Use rsync to fetch results from rivanna back to the cs servers

> Rivanna’s scratch file system has a limit of 10TB per user. This policy is in place to guarantee the stability and performance of the scratch file system. Scratch is intended as a temporary work directory. It is not backed up and files that have not been accessed for more than 90 days are marked for deletion. Users are encouraged to back up their important data. Home directories and leased storage are not subject to this policy.


### Moving data to Rivanna:
You should generally try to put as much stuff as you can into the `/scratch/YOURCOMPUTINGID` directory
I use a command called `rsync`, but `scp` or filezilla would probably work too
I added a few commands to my `~/.bashrc` to make syncing easier
```
alias pushrivd="rsync -avzh /p/PROJECTDIRNAME/data/ COMPUTINGID@rivanna.hpc.virginia.edu:/scratch/COMPUTINGID/PROJECTDIRNAME/data/ --exclude={}"
alias pullrivlog="rsync -avzh COMPUTINGID@rivanna.hpc.virginia.edu:/scratch/COMPUTINGID/PROJECTDIRNAME/logfiles/ /p/PROJECTDIRNAME/logfiles/ --exclude={}"
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

Example of Rivanna slurm script:
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

(This script uses GPUs on the GPUPOD https://www.rc.virginia.edu/userinfo/rivanna/basepod/)


