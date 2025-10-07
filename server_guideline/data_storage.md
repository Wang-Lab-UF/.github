# Data Storage on HiPerGator

Understand storage types, quotas, and best practices for Wang Lab.

---

## Storage Locations
- Home: `/home/<gatorlink>`
  - Small quota, backed up. Use for configs and small files only.
- Blue (project space): `/blue/wang.yiquan/<gatorlink>`
  - Group/project space for active work. Backed up per RC policy.
- Scratch: `/orange/wang.yiquan` or `/scratch/local`
  - High-performance, not backed up, purged periodically. For temporary files.


---

## Quotas and Usage
```bash
# Disk usage
du -sh .
```

- Clean up large logs and temporary files regularly.
- Use compressed formats (e.g., `.zst`, `.xz`) for archives when appropriate.

---

## Best Practices
- Keep code in Git; keep data in project/scratch with clear directory structure.
- Do not run heavy I/O in home; use Blue or Scratch.
- For large datasets, stage to scratch during compute, and copy results back to Blue.
- Add `.gitignore` for large outputs; avoid committing binaries.

---

## Data Transfer
- Use `rsync -avh --progress` for robust transfers.
- For Globus endpoints, consult RC docs for HiPerGator endpoint names.

---

## Backups and Retention
- Home and Blue may be backed up; Scratch is not. Confirm policies with RC.
- Keep critical datasets mirrored in lab-managed cloud/object storage if available.

---

Next: [SLURM Jobs](./slurm_jobs.md) and [JupyterLab](./jupyter_lab.md). See also [Connect](./connect_server.md).
