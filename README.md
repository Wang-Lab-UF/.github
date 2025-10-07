# Wang Lab Organization Repository

This repo hosts shared documentation and resources for the Wang Lab at UF.

## Contents
- Server Guidelines: see [`server_guideline/`](./server_guideline/) for HiPerGator usage
  - Connect to HiPerGator
  - Start JupyterLab
  - Run/Submit SLURM jobs
  - Data storage best practices
- Profiles: see [`profile/`](./profile/) for lab overview and links

## Quick Links
- HiPerGator Server Guidelines index: [`server_guideline/README.md`](./server_guideline/README.md)
- Lab profile: [`profile/README.md`](./profile/README.md)

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

## Contributing
- Use concise, task-focused markdown.
- Prefer examples that work on HiPerGator.
- Open a PR for changes; keep guides up to date with RC policies.

## Contact
For access or questions, contact **Yiquan Wang (yiquan.wang@ufl.edu)**.
