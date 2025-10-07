# HiPerGator Usage Guidelines

A concise handbook for Wang Lab members using UF Research Computing's HiPerGator (HPC).

- [Connect to HiPerGator](./connect_server.md)
- [Data Storage and Best Practices](./data_storage.md)
- [Start JupyterLab](./jupyter_lab.md)
- [Run and Submit Jobs with SLURM](./slurm_jobs.md)

---

## Quick Start

- SSH: `ssh <gatorlink>@hpg.rc.ufl.edu`
- Check storage: `lquota` and `mmlsquota` (see [Storage](./data_storage.md))
- JupyterLab via SLURM (interactive): see [JupyterLab](./jupyter_lab.md)
- Submit job: `sbatch job.sbatch` (see [SLURM](./slurm_jobs.md))


> If you need access, request a HiPerGator account tied to the Wang Lab allocation and Duo/SSH key set up. See the connection guide first.

---

## SLURM Cheat Sheet (HiPerGator)
- List partitions (summary): `sinfo -s`
- Your jobs in queue: `squeue -u $USER`
- Interactive CPU session: `salloc --partition=hpg-default --cpus-per-task=4 --mem=16gb --time=02:00:00`
- Interactive GPU session: `salloc --partition=gpu --gpus=1 --cpus-per-task=8 --mem=32gb --time=04:00:00`
- Submit batch job: `sbatch job.sbatch`
- Cancel job: `scancel <jobid>`
- Job/accounting info: `sacct -j <jobid> --format=JobID,JobName,Partition,State,Elapsed,MaxRSS,ReqMem`

Partition | Time Limit | Notes
--- | --- | ---
hpg-default | 31 days | General use
bigmem | 31 days | Large-memory jobs
hpg-dev | 12h | Development / short jobs
hpg-milan | 31 days | Large cluster for computation
hwgui | 4 days | GUI jobs / small interactive tasks
