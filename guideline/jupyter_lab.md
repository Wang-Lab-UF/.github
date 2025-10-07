# Start JupyterLab on HiPerGator

This guide shows two supported workflows: Open OnDemand (browser) and SLURM-based CLI launch.

---

## Option 1: Open OnDemand (recommended)
1. Visit the RC OnDemand portal: `https://ood.rc.ufl.edu`.
2. Log in with GatorLink and Duo.
3. Go to Interactive Apps → JupyterLab.
4. Choose:
   - Partition: `hpg-default` or lab-specific partition
   - Cores/GPUs, Memory, Time limit
   - Conda environment/module (see below)
5. Launch and wait for the session link.

---

## Option 2: Launch via SLURM (terminal)
1. SSH to HiPerGator and load your environment:

```bash
# Example: use conda
module load conda
conda activate myenv

# or load a module stack
module spider python
module load python/3.10
```

2. Start an interactive job and run JupyterLab binding to a node port:

```bash
# CPU example
salloc --partition=hpg-default --cpus-per-task=4 --mem=16gb --time=04:00:00

# GPU example (adjust GPU and partition as allowed for your group)
salloc --partition=gpu --gpus=1 --cpus-per-task=8 --mem=32gb --time=04:00:00

# On the allocated node
hostname
PORT=$(shuf -i 10000-60000 -n 1)
export PORT
jupyter lab --no-browser --ip=0.0.0.0 --port=$PORT
```

3. In a second local terminal, create an SSH tunnel from your machine to the compute node:

```bash
# Replace <node> with the hostname shown above (e.g., c1234a-s1.rc.ufl.edu)
ssh -N -L 8888:localhost:$PORT <gatorlink>@hpg.rc.ufl.edu -J <gatorlink>@hpg.rc.ufl.edu ssh <node> -L $PORT:localhost:$PORT
```

If ProxyJump is not available, first hop to `hpg`, then tunnel to the node. Alternatively, set an SSH config with `ProxyJump` and use a single command.

4. Open your browser at `http://localhost:8888` and paste the Jupyter token.

---

## Using Conda Environments
- Create once:

```bash
module load conda
conda create -n myenv python=3.10 -y
conda activate myenv
pip install jupyterlab ipykernel
python -m ipykernel install --user --name myenv --display-name "Python (myenv)"
```

- In OnDemand, pick the kernel "Python (myenv)".
- In CLI, `conda activate myenv` before launching.

---

## GPU Sessions
- Ensure your allocation allows GPU partitions.
- Load CUDA/cuDNN as needed via `module spider cuda` then `module load cuda/XX.Y`.
- Use `--gpus=1` (or more) and consider `--constraint` flags if specific GPU models are required.

---

## Common Issues
- Port in use: pick another port (`PORT=$(shuf -i 10000-60000 -n 1)`).
- Permission denied: verify group membership and allocation.
- Kernel not showing: ensure the ipykernel is installed in the environment.

---

Next: [SLURM Jobs](./slurm_jobs.md) and [Data Storage](./data_storage.md).
