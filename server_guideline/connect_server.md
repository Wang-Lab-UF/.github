# Connect to HiPerGator

This guide shows how to request access and connect to UF Research Computing's HiPerGator (HPC) for Wang Lab members.

---

## Request Access
- Ensure you have a valid UF GatorLink account and Duo 2FA.
- Request a HiPerGator account associated with the Wang Lab allocation.
- Contact: **Yiquan Wang (yiquan.wang@ufl.edu)** to be added to the appropriate groups.

---

## Set Up SSH Keys (recommended)
Generate a key locally and upload the public key to HiPerGator account portal.

```bash
ssh-keygen -o -a 100 -t ed25519 -f ~/.ssh/id_ed25519 -C "<gatorlink>@ufl.edu"
```

Run the following command to copy your public key to HiPerGator:

```bash
ssh-copy-id -i ~/.ssh/id_ed25519 <gatorlink>@hpg.rc.ufl.edu
```

Congratulations your key is now authorized to login to your account on HPG.

To SSH into HiPerGator using your SSH keys, run the following command:

```bash
ssh -p 2222 <gatorlink>@hpg.rc.ufl.edu
```

If Duo is required, follow prompts. More questions, please see https://docs.rc.ufl.edu/access/ssh_keys/

---

## Helpful SSH Config
Create `~/.ssh/config` locally to simplify commands:

```bash
Host hpg
  HostName hpg.rc.ufl.edu
  User <gatorlink>
  IdentityFile ~/.ssh/id_ed25519
  Port 2222
  ServerAliveInterval 60
  ServerAliveCountMax 120
```

Then connect with:

```bash
ssh hpg
```

---

## Transfer Files
Use `scp` or `rsync`:

```bash
# Upload
scp -r ./project hpg:/blue/wang.yiquan/<gatorlink>/project

# Download
scp -r hpg:/blue/wang.yiquan/<gatorlink>/results ./results

# Efficient sync
rsync -avh --progress ./project hpg:/blue/wang.yiquan/<gatorlink>/project
```

See storage locations in the [Data Storage](./data_storage.md) guide.

---

## Next Steps
- Start an interactive session or JupyterLab: see [JupyterLab](./jupyter_lab.md)
- Submit batch jobs: see [SLURM Jobs](./slurm_jobs.md)
