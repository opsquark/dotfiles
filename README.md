Perfect — then the rule is:

* **Run `chezmoi add` as user `jroychowdhury`** (so it writes into `/home/jroychowdhury/.local/share/chezmoi`)
* Use **sudo only for apply** (to write into `/etc`), and (optionally) for read access if `/etc/nixos` permissions block you.

Do this:

## 1) Confirm your user chezmoi repo path

```bash
chezmoi source-path
```

You should see:
`/home/jroychowdhury/.local/share/chezmoi`

## 2) Add `/etc/nixos` into *your* repo (not root’s)

### Try (usually works):

```bash
chezmoi add --destination / -r /etc/nixos
```

### If that fails due to permissions, use this (reads as root, writes repo as you):

```bash
sudo -u jroychowdhury -H chezmoi add --destination / -r /etc/nixos
```

## 3) Verify files were added into your repo

```bash
chezmoi status --destination /
chezmoi cd
git status
```

## 4) Apply to `/etc/nixos` (needs root)

```bash
sudo chezmoi apply --destination /
```

---

## Optional: make “apply to /” easy without accidentally using root’s repo

Always apply using your user’s source directory but with root privileges:

```bash
sudo -u root --preserve-env=HOME,USER,PATH chezmoi apply --destination /
```

But simplest is:

```bash
sudo chezmoi apply --destination /
```

**as long as** you ensure `sudo` is not switching HOME to `/root`. Many distros do. To force it to use your user source dir, use:

```bash
sudo HOME=/home/jroychowdhury chezmoi apply --destination /
```

(That guarantees it uses `/home/jroychowdhury/.local/share/chezmoi`.)

---

## The most reliable “always use my repo” commands

**Add:**

```bash
chezmoi add --destination / -r /etc/nixos
```

**Apply (force your repo even under sudo):**

```bash
sudo HOME=/home/jroychowdhury chezmoi apply --destination /
```

---

### Quick diagnostic if it still “adds nothing”

Run:

```bash
chezmoi status --destination /
chezmoi managed --destination / | head
```

If you paste the output of:

```bash
chezmoi source-path
sudo env | grep -E '^(HOME|USER)='
```

I’ll give you the exact one-liner that works on your sudo environment.
