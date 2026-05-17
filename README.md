# Multiple Git User SSH Setup (Linux)

Dokumentasi setup multiple akun Git menggunakan SSH di Linux.

Contoh:
- Akun personal → GitHub
- Akun kerja → GitLab

Setup ini memungkinkan:
- SSH key terpisah
- Identity commit berbeda
- Aman untuk multi project/client
- Tidak perlu logout/login akun Git

---

# Struktur Final

```text
~/.ssh/
├── config
├── id_ed25519_personal
├── id_ed25519_personal.pub
├── id_ed25519_gitlab_work
└── id_ed25519_gitlab_work.pub
```

---

# 1. Generate SSH Key

## Personal GitHub

Generate SSH key:

```bash
ssh-keygen -t ed25519 -C "personal@email.com"
```

Saat muncul prompt:

```text
Enter file in which to save the key (/home/fahmi/.ssh/id_ed25519):
```

Isi dengan:

```bash
~/.ssh/id_ed25519_personal
```

---

## Work GitLab

Generate SSH key:

```bash
ssh-keygen -t ed25519 -C "work@company.com"
```

Saat muncul prompt:

```text
Enter file in which to save the key
```

Isi dengan:

```bash
~/.ssh/id_ed25519_gitlab_work
```

---

# 2. Jalankan SSH Agent

```bash
eval "$(ssh-agent -s)"
```

---

# 3. Tambahkan SSH Key ke Agent

## Tambahkan personal key

```bash
ssh-add ~/.ssh/id_ed25519_personal
```

## Tambahkan work key

```bash
ssh-add ~/.ssh/id_ed25519_gitlab_work
```

## Verifikasi key

```bash
ssh-add -l
```

---

# 4. Upload Public Key ke Git Provider

## Copy public key personal

```bash
cat ~/.ssh/id_ed25519_personal.pub
```

Copy hasilnya lalu tambahkan ke:

https://github.com/settings/keys

---

## Copy public key work

```bash
cat ~/.ssh/id_ed25519_gitlab_work.pub
```

Copy hasilnya lalu tambahkan ke:

https://gitlab.com/-/user_settings/ssh_keys

---

# 5. Setup SSH Config

Buka file config:

```bash
nano ~/.ssh/config
```

Isi file:

```ssh
# Personal Github
Host github-personal
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_personal
    IdentitiesOnly yes

# Work GitLab
Host gitlab-work
    HostName gitlab.com
    User git
    IdentityFile ~/.ssh/id_ed25519_gitlab_work
    IdentitiesOnly yes
```

Simpan file:
- CTRL + O
- Enter
- CTRL + X

Set permission:

```bash
chmod 600 ~/.ssh/config
```

---

# 6. Test SSH Connection

## Test GitHub

```bash
ssh -T git@github-personal
```

Jika berhasil:

```text
Hi username! You've successfully authenticated...
```

---

## Test GitLab

```bash
ssh -T git@gitlab-work
```

Jika berhasil:

```text
Welcome to GitLab, @username!
```

---

# 7. Clone Repository Menggunakan Alias

## GitHub Personal

Original URL:

```text
git@github.com:fahmi/frontend.git
```

Clone menggunakan alias:

```bash
git clone git@github-personal:fahmi/frontend.git
```

---

## GitLab Work

Original URL:

```text
git@gitlab.com:company/backend.git
```

Clone menggunakan alias:

```bash
git clone git@gitlab-work:company/backend.git
```

---

# 8. Set Git Identity Per Repository

Masuk ke repository:

```bash
cd repository
```

---

## Untuk repository personal

```bash
git config user.name "Nama Personal"
git config user.email "personal@email.com"
```

---

## Untuk repository kerja

```bash
git config user.name "Nama Kerja"
git config user.email "work@company.com"
```

---

# 9. Verifikasi Config

## Cek git config

```bash
git config --list
```

---

## Cek remote repository

```bash
git remote -v
```

Harus menggunakan alias seperti:

```text
git@github-personal:...
```

atau

```text
git@gitlab-work:...
```

---

# 10. Best Practice

## Jangan gunakan sudo

Gunakan user biasa.

Benar:

```text
/home/fahmi/.ssh/
```

Salah:

```text
/root/.ssh/
```

---

## Jangan gunakan global email untuk multi akun

Hindari:

```bash
git config --global user.email ...
```

Lebih aman set per repository.

---

## Gunakan nama key yang jelas

Contoh:

```text
id_ed25519_personal
id_ed25519_gitlab_work
id_ed25519_company_a
id_ed25519_client_b
```

---

# Troubleshooting

## Permission denied (publickey)

Cek key yang aktif:

```bash
ssh-add -l
```

Jika kosong:

```bash
ssh-add ~/.ssh/id_ed25519_personal
```

atau:

```bash
ssh-add ~/.ssh/id_ed25519_gitlab_work
```

---

## SSH config tidak terbaca

Cek permission:

```bash
chmod 600 ~/.ssh/config
```

---

## Clone masih memakai akun salah

Biasanya karena clone masih menggunakan:

```text
github.com
```

atau:

```text
gitlab.com
```

Harus menggunakan alias:

```text
github-personal
gitlab-work
```

---

# Contoh Final Workflow

## Clone personal repo

```bash
git clone git@github-personal:fahmi/my-app.git
```

---

## Clone work repo

```bash
git clone git@gitlab-work:company/backend.git
```

---

# Kesimpulan

Setup ini memberikan:
- SSH key terpisah
- Identitas commit terpisah
- Aman untuk multi akun
- Mudah scalable untuk banyak client/perusahaan
- Tidak perlu logout/login akun Git lagi
