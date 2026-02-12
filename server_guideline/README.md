# HiPerGator Usage Guidelines

A concise handbook for Wang Lab members using UF Research Computing's HiPerGator (HPC).

- [Connect to HiPerGator](./connect_server.md)
- [Data Storage and Best Practices](./data_storage.md)
- [Start JupyterLab](./jupyter_lab.md)
- [Run and Submit Jobs with SLURM](./slurm_jobs.md)

---

## Quick Start

- SSH: `ssh <gatorlink>@hpg.rc.ufl.edu`
- Check storage: `du -sh ~` (see [Storage](./data_storage.md))
- JupyterLab via OnDemand: https://ood.rc.ufl.edu (see [JupyterLab](./jupyter_lab.md))
- Submit job: `sbatch job.sbatch` (see [SLURM](./slurm_jobs.md))


> If you need access, request a HiPerGator account tied to the Wang Lab allocation and Duo/SSH key set up. See the connection guide first.

---

## SLURM Cheat Sheet (HiPerGator)
- List partitions (summary): `sinfo -s`
- Your jobs in queue: `squeue -u $USER`
- Interactive CPU session: `salloc --partition=hpg-default --cpus-per-task=4 --mem=16gb --time=02:00:00 --pty bash -i`
- Interactive GPU session: `salloc --partition=hpg-b200 --gpus=1 --cpus-per-task=8 --mem=32gb --time=04:00:00 --pty bash -i`
- Submit batch job: `sbatch job.sbatch`
- Cancel job: `scancel <jobid>`
- Job/accounting info: `sacct -j <jobid> --format=JobID,JobName,Partition,State,Elapsed,MaxRSS,ReqMem`

Partition | Time Limit | Notes
--- | --- | ---
hpg-default | 31 days | General use
bigmem | 31 days | Large-memory jobs
hpg-dev | 12h | Development / short jobs
hpg-milan | 31 days | Large cluster for computation
hwgui | 4 days | Hardware accelerated GPU partition for visualization applications with NVIDIA L4 cards
hpg-b200 | 14 days | GPU partition, NVIDIA DGX B200 SuperPod
hpg-turin | 14 days | Regular HPG4 nodes integrated with 3 NVIDIA L4 cards per node

---

## Cursor Connection
This guide details how to connect to HiPerGator using Cursor (or VS Code) and how to use Tmux to protect your antibody folding sessions from disconnection.

### Easy Setup (via login node)
Connect Cursor to the login node directly (Don't run any heavy computation in login node)

Using srun to open more computation source and use that node for job running:
```bash
srun --partition=hpg-turin --gpus=1 --mem=64gb --time=04:00:00
# you then ask agent to connect to the compute node and run command
srun --jobid=24721927 python my_script.py
```

Or using sbatch:
```bash
sbatch my_job.sbatch
```

### Directly Connect to Compute Node
To allow Cursor to "jump" through the login node and land on a compute node, add this to your `~/.ssh/config`:
#### 1. ssh config
```text
# The Gateway (Login Node)
Host hpg
  HostName hpg.rc.ufl.edu
  User <gatorlink>
  IdentityFile <your_ssh_key>
  Port 2222
  ServerAliveInterval 60

# The Target (Compute Nodes)
Host c*
  ProxyJump hpg
  User <gatorlink>
```

#### 2. Login Node
```bash
ssh hpg
srun --partition=hpg-turin --gpus=1 --mem=64gb --time=04:00:00 --pty bash
```

#### 3. Open Cursor
Press Cmd + Shift + P -> Remote-SSH: Connect to Host...

Type your node name (e.g., c1102a-s1).

