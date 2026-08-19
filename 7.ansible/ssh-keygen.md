# Old Server → New Server Passwordless SSH Setup

## Setup

- **Old Server:** Server 1
- **New Server:** New Server
- **PEM Key:** `server.pem`
- **New Server User:** `ec2-user`

---

## 1. Generate SSH Key on Old Server

```bash
ssh-keygen -t ed25519
```

Press **Enter** for all prompts.

Check:

```bash
ls -l ~/.ssh/id_ed25519*
```

---

## 2. Set PEM Permission

```bash
chmod 400 ~/server.pem
```

---

## 3. Test Access to New Server

```bash
ssh -i ~/server.pem ec2-user@NEW_SERVER
```

Exit:

```bash
exit
```

---

## 4. Copy SSH Public Key to New Server

```bash
ssh-copy-id -f \
-i ~/.ssh/id_ed25519.pub \
-o IdentityFile=~/server.pem \
ec2-user@NEW_SERVER
```

---

## 5. Test Passwordless SSH

Do **not** use the PEM now.

```bash
ssh ec2-user@NEW_SERVER
```

---

## 6. Verify SSH Key

```bash
ssh -v ec2-user@NEW_SERVER
```

Look for:

```text
Offering public key: ~/.ssh/id_ed25519
```

---

## Final Setup

```text
OLD SERVER
Server 1
    |
    | id_ed25519
    ↓
NEW SERVER
RHEL
    |
    └── ~/.ssh/authorized_keys
```

The `server.pem` key is required only for the initial connection.