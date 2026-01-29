# 🐧 Day 1: Linux User Setup
### Topic: Non-Interactive Shells for Service Accounts

---

## 🎯 Problem Statement
To accommodate the backup agent tool's specifications, the system admin team at **xFusionCorp Industries** requires a user with a non-interactive shell.
* **Username:** `jim`
* **Server:** App Server 1

---

## 💡 Learning: Shell Types


### 1. Interactive Shell (`/bin/bash`)
* **The "Conversation":** You type a command, the computer answers, and waits for your next move.
* **Analogy:** Like a chat with a friend—back and forth.

### 2. Non-Interactive Shell (`/sbin/nologin`)
* **The "Task-Only":** The shell is used only to run a specific script or background program. There is no prompt.
* **Analogy:** Like a **Vending Machine**. You press a button (trigger the script), it drops the snack (runs the code), and it's done. You don't "talk" to it.

---

## 🛠️ The Solution

### Step 1: Create the user
Use the `-s` flag to specify the non-login shell.
```bash
sudo useradd -s /sbin/nologin jim
```
### Step 2: Configuration Verification
Check the user entry in the system password file.

```bash

grep jim /etc/passwd
```
Expected Output:

```bash
jim:x:1002:1002::/home/jim:/sbin/nologin
```

🔍 Detailed Explanation
The Breakdown
useradd: The "Create User" button.

-s /sbin/nologin: The "Lock the Door" part. It tells the system: "If this user tries to log in with a screen and keyboard, tell them 'No' and kick them out."

jim: The account name.

### Why do this?
Imagine you have a Robot Vacuum (a background service).

The vacuum needs an account so it has permission to move around your digital floor.

It doesn't have fingers or a brain—it never needs to "log in" to type commands.

By using /sbin/nologin, you ensure that even if a hacker figures out Jim's password, they cannot get in because the "front door" is nailed shut.
