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
ssh-keygen -t ed25519 -C "<gatorlink>@ufl.edu"
cat ~/.ssh/id_ed25519.pub
```

Add the public key in the RC portal, then test:

```bash
ssh <gatorlink>@hpg.rc.ufl.edu
```

If Duo is required, follow prompts. For first-time login, you may be placed on a login node such as `login2.chpc.rc.ufl.edu`.

---

## Helpful SSH Config
Create `~/.ssh/config` locally to simplify commands:

```bash
Host hpg
  HostName hpg.rc.ufl.edu
  User <gatorlink>
  IdentityFile ~/.ssh/id_ed25519
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
scp -r ./project hpg:/blue/<pi-group>/<gatorlink>/project

# Download
scp -r hpg:/blue/<pi-group>/<gatorlink>/results ./results

# Efficient sync
rsync -avh --progress ./project hpg:/blue/<pi-group>/<gatorlink>/project
```

See storage locations in the [Data Storage](./data_storage.md) guide.

---

## Next Steps
- Start an interactive session or JupyterLab: see [JupyterLab](./jupyter_lab.md)
- Submit batch jobs: see [SLURM Jobs](./slurm_jobs.md)
