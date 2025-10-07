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
