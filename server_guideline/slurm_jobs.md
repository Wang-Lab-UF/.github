# SLURM: Run and Submit Jobs on HiPerGator

This guide covers interactive sessions, batch jobs, job arrays, and monitoring. see more: https://docs.rc.ufl.edu/scheduler/

---

## Partitions and Resources
- Check available partitions and limits: `sinfo -s` and RC documentation.
- Common flags:
  - `--partition=<name>` (e.g., `hpg-default`, `hpg-b200`)
  - `--cpus-per-task=<n>`
  - `--mem=<size>` (e.g., `16gb`)
  - `--time=HH:MM:SS`
  - `--gpus=<n>` for GPU partitions

---

## Interactive Session
```bash
# CPU example
salloc --partition=hpg-default --cpus-per-task=4 --mem=16gb --time=02:00:00 --pty -u bash -i

# GPU example
salloc --partition=hpg-b200 --gpus=1 --cpus-per-task=8 --mem=32gb --time=04:00:00 --pty -u bash -i

# After allocation, load env and run
module load conda && conda activate myenv
python script.py --arg value
```

---

## Batch Job (sbatch)
Create `job.sbatch`:

```bash
#!/bin/bash
#SBATCH --job-name=example
#SBATCH --mail-type=ALL
#SBATCH --mail-user=email@ufl.edu
#SBATCH --partition=hpg-default
#SBATCH --cpus-per-task=4
#SBATCH --mem=16gb
#SBATCH --time=04:00:00
#SBATCH --output=logs/%x-%j.out

module load conda
source activate myenv

python train.py --epochs 10 --batch-size 64
```
see more: https://docs.rc.ufl.edu/scheduler/sample_job_scripts/

Submit and monitor:

```bash
mkdir -p logs
sbatch job.sbatch
squeue -u $USER
sacct -j <jobid> --format=JobID,JobName,Partition,State,Elapsed,MaxRSS,ReqMem
```

---

## Job Arrays
Use arrays for parameter sweeps:

```bash
#!/bin/bash
#SBATCH --job-name=array-demo
#SBATCH --array=0-9
#SBATCH --cpus-per-task=2
#SBATCH --mem=4gb
#SBATCH --time=01:00:00
#SBATCH --output=logs/%x-%A_%a.out

PARAMS=(0.1 0.2 0.3 0.4 0.5 0.6 0.7 0.8 0.9 1.0)
LR=${PARAMS[$SLURM_ARRAY_TASK_ID]}

module load conda
source activate myenv
python train.py --lr $LR
```

---

## GPU Jobs
```bash
#SBATCH --partition=hpg-b200
#SBATCH --gpus=1

module load cuda/12.1  # adjust per RC documentation
module load conda
source activate torch
python gpu_script.py
```

- Verify GPU visibility: `nvidia-smi` and `echo $CUDA_VISIBLE_DEVICES`.

---

## Best Practices
- Always write to scratch or project spaces, not home, for large I/O.
- Set `--time` and memory realistically; underestimates cause cancellations.
- Use `sacct` for post-mortem resource usage auditing.
- Seed randomness and checkpoint periodically to tolerate preemptions.

---

Next: [Data Storage](./data_storage.md). See also [JupyterLab](./jupyter_lab.md).
