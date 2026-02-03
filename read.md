
# GitLab CE Backup, Restore, and Upgrade Guide (16.9.6 → 18.x)

This document captures the **exact commands and steps** used to:
- Take a GitLab CE backup on a **source VM**
- Move the backup using a **detached disk**
- Restore GitLab on a **new VM**
- Upgrade GitLab CE safely from **16.9.6 to 18.x**

---

## Part 1: Source VM – Backup Creation

### Change Backup Path
```bash
sudo vi /etc/gitlab/gitlab.rb
```
Update:
```ruby
gitlab_rails['backup_path'] = "/backup"
```

```bash
sudo gitlab-ctl reconfigure
```

### Create Backup
```bash
sudo gitlab-backup create STRATEGY=copy
```

### Copy Critical Config Files
```bash
sudo cp /etc/gitlab/gitlab-secrets.json /gitlab-backup/
sudo cp /etc/gitlab/gitlab.rb /gitlab-backup/gitlab.rb.source
```

### Verify Backup Files
```bash
ls -lh /backup
```

Example:
```text
1769960707_2026_02_01_16.9.6_gitlab_backup.tar
gitlab-secrets.json
gitlab.rb.source
```

### Unmount Disk
```bash
sudo umount /gitlab-backup
```

Detach this disk from the **source VM** and attach it to the **new VM**.

---

## Part 2: New VM – Restore from Backup

### Prepare Backup Directory and Mount Disk
```bash
sudo mkdir /backup
# Attach disk and mount it to /backup
```

### OS Update
```bash
sudo apt update && sudo apt upgrade -y
```

### Install Same GitLab Version as Source (16.9.6)
```bash
EXTERNAL_URL="https://public-IP" sudo apt install gitlab-ce=16.9.6-ce.0 -y
```

### Update Backup Path
```bash
sudo vi /etc/gitlab/gitlab.rb
```
```ruby
gitlab_rails['backup_path'] = "/backup"
```

```bash
ls -lh /backup
```

### Stop GitLab Services
```bash
sudo gitlab-ctl stop puma
sudo gitlab-ctl stop sidekiq
```

### Restore Secrets File
```bash
sudo cp /backup/gitlab-secrets.json /etc/gitlab/gitlab-secrets.json
sudo chmod 600 /etc/gitlab/gitlab-secrets.json
```

### Reconfigure and Restore Backup
```bash
sudo gitlab-ctl reconfigure
sudo gitlab-backup restore BACKUP=1769960707_2026_02_01_16.9.6
```

```bash
sudo gitlab-ctl reconfigure
sudo gitlab-ctl restart
```

---

## Reset Root Password (Optional)
```bash
sudo gitlab-rails console
```
```ruby
u = User.find_by_username('root')
u.password = 'StrongNewPassword@123'
u.password_confirmation = 'StrongNewPassword@123'
u.save!
exit
```

---

## Part 3: GitLab CE Upgrade Path (16.9.6 → 18.x)

### Upgrade to 16.11
```bash
sudo gitlab-backup create
sudo apt update && sudo apt upgrade -y
sudo reboot

sudo apt install gitlab-ce=16.11.* -y
sudo gitlab-ctl reconfigure
sudo gitlab-ctl status
sudo gitlab-rake gitlab:env:info
```

---

### Upgrade to 17.3
```bash
sudo gitlab-backup create
sudo apt install gitlab-ce=17.3.* -y
sudo gitlab-ctl reconfigure
sudo gitlab-rake gitlab:env:info
```

---

### Upgrade to 17.5
```bash
sudo gitlab-backup create
sudo apt install gitlab-ce=17.5.* -y
sudo gitlab-ctl reconfigure
sudo gitlab-rake gitlab:env:info
```

---

### Upgrade to 17.8
```bash
sudo gitlab-backup create
sudo apt install gitlab-ce=17.8.* -y
sudo gitlab-ctl reconfigure
sudo gitlab-rake gitlab:env:info
```

---

### Upgrade to 17.11 (Mandatory Before 18)
```bash
sudo gitlab-backup create
sudo apt install gitlab-ce=17.11.* -y
sudo gitlab-ctl reconfigure
sudo gitlab-rake gitlab:env:info
```

---

## Pre‑Check Before GitLab 18

### Ensure No Active Background Migrations
```bash
sudo gitlab-rake gitlab:background_migrations:status | grep active
```

### Confirm Sidekiq Is Running
```bash
sudo gitlab-ctl status | grep sidekiq
```

Proceed **only when no active jobs are listed**.

---

## Final Upgrade to 18.x
```bash
sudo gitlab-backup create
sudo apt install gitlab-ce=18.0.* -y
sudo gitlab-ctl reconfigure
sudo gitlab-rake gitlab:env:info
```

---

## Notes
- Never skip required intermediate versions
- Always wait for background migrations to finish
- Keep VM snapshots for rollback safety
- This procedure is tested for **GitLab CE on Ubuntu**

---

**End of Document**
