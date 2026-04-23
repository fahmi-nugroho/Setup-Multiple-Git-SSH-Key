# 🔐 Complete SSH Setup Guide for Multi Git Accounts (Linux)

Panduan lengkap untuk setup SSH key di Linux untuk banyak akun (GitHub / GitLab), termasuk best practice dan troubleshooting.

---

## 📌 Tujuan

* Menggunakan banyak akun Git tanpa konflik
* Menghindari error SSH & permission
* Workflow development yang clean & aman

---

## 🧠 Konsep Dasar

* SSH key digunakan untuk autentikasi ke Git server
* 1 akun Git = 1 SSH key (**best practice**)
* SSH key ≠ email Git (email hanya untuk commit identity)

---

## 🔑 1. Generate SSH Key

Buat key untuk setiap akun:

```bash
ssh-keygen -t ed25519 -C "personal" -f ~/.ssh/id_ed25519_github_personal
ssh-keygen -t ed25519 -C "work" -f ~/.ssh/id_ed25519_github_work
ssh-keygen -t ed25519 -C "gitlab" -f ~/.ssh/id_ed25519_gitlab
```

**Best Practice:**

* Gunakan `ed25519`
* Selalu pakai passphrase

---

## 📤 2. Tambahkan Public Key ke Git

```bash
cat ~/.ssh/id_ed25519_github_personal.pub
```

Tambahkan ke:

* GitHub → Settings → SSH Keys
* GitLab → Preferences → SSH Keys

---

## ⚙️ 3. Konfigurasi SSH (`~/.ssh/config`)

```bash
Host github-personal
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_github_personal
    IdentitiesOnly yes

Host github-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_github_work
    IdentitiesOnly yes

Host gitlab
    HostName gitlab.com
    User git
    IdentityFile ~/.ssh/id_ed25519_gitlab
    IdentitiesOnly yes
```

---

## 🔑 4. Jalankan SSH Agent

```bash
eval "$(ssh-agent -s)"
```

Tambah key:

```bash
ssh-add ~/.ssh/id_ed25519_github_personal
ssh-add ~/.ssh/id_ed25519_github_work
ssh-add ~/.ssh/id_ed25519_gitlab
```

---

## ⚡ 5. Auto Load SSH Key

Tambahkan ke `.zshrc` / `.bashrc`:

```bash
if [ -z "$SSH_AUTH_SOCK" ]; then
    eval "$(ssh-agent -s)"
fi

if ! ssh-add -l >/dev/null 2>&1; then
    ssh-add ~/.ssh/id_ed25519_github_personal
    ssh-add ~/.ssh/id_ed25519_github_work
    ssh-add ~/.ssh/id_ed25519_gitlab
fi
```

---

## 🔗 6. Clone Repository

Gunakan alias:

```bash
git clone git@github-work:company/repo.git
git clone git@github-personal:user/repo.git
```

---

## 🧪 7. Test Koneksi

```bash
ssh -T github-work
```

---

## 👤 8. Set Git Identity

```bash
git config user.name "Your Name"
git config user.email "your@email.com"
```

---

## 📁 9. Struktur Folder (Opsional)

```
~/projects/work/
~/projects/personal/
```

Auto config:

```bash
[includeIf "gitdir:~/projects/work/"]
    path = ~/.gitconfig-work
```

---

## 🔐 10. Permission

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/*
```

---

## 🚫 Hal yang Harus Dihindari

```bash
sudo git clone
sudo git pull
```

---

## ⚠️ Troubleshooting

### ❌ Permission denied (publickey)

* Key belum ditambahkan ke Git
* SSH config salah
* Key belum di-load (`ssh-add -l`)

---

### ❌ Could not open a connection to your authentication agent

```bash
eval "$(ssh-agent -s)"
```

---

### ❌ No such identity

Pastikan file key ada:

```bash
ls ~/.ssh
```

---

### ❌ Dubious ownership

Fix:

```bash
sudo chown -R $USER:$USER ~/projects
```

---

### ❌ SSH pakai `/root/.ssh`

Penyebab:

* pakai `sudo`

Solusi:

* jangan gunakan `sudo` untuk Git

---

## 🧹 Struktur Ideal `.ssh`

```
~/.ssh/
├── config
├── id_ed25519_github_personal
├── id_ed25519_github_work
├── id_ed25519_gitlab
```

---

## 🧪 Debug Tools

```bash
ssh-add -l
ssh -T github-work
ssh -vT git@github.com
```

---

## 🎯 Best Practice Ringkasan

* 1 akun = 1 key
* Gunakan SSH config
* Gunakan host alias
* Jangan gunakan sudo
* Gunakan passphrase
* Pisahkan work & personal

---

## 🚀 Hasil Akhir

Dengan setup ini:

* Multi akun berjalan lancar
* Tidak ada konflik SSH
* Tidak ada error permission
* Workflow lebih profesional & scalable

---
