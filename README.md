
# PyTorch ML Template

This project provides a starter template for PyTorch-based machine learning projects. It includes a recommended structure and setup instructions, particularly useful when working on remote servers (like Lambda Labs, Runpod, or any cloud instance) and needing SSH access.

# Installation Guide

## Remote Server Setup (Optional)

If you're developing on a remote server, these steps will help you set up secure and convenient SSH access.

### 1. Configure SSH Key Access (Local to Remote)

Using SSH keys simplifies logins.

**a. Generate an SSH Key Pair (if needed):**

On your *local* machine, open a terminal:

```bash
# Creates a secure ed25519 key pair. Replace the email with yours.
ssh-keygen -t ed25519 -C "your_email@example.com"
```

Press Enter to accept the default save location (`~/.ssh/id_ed25519`). You can optionally add a passphrase for extra security, but you can probably skip it.

**b. Copy Your Public Key to the Remote Server:**

First, display your public key on your *local* machine:

```bash
cat ~/.ssh/id_ed25519.pub
```

Copy the *entire output* (starting with `ssh-ed25519...`). Now, SSH into your *remote* server using your usual method (e.g., password) and append the copied key to the `~/.ssh/authorized_keys` file:

```bash
# On the remote server:
mkdir -p ~/.ssh         # Create .ssh directory if it doesn't exist
chmod 700 ~/.ssh        # Set correct permissions
echo "paste_your_public_key_here" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys # Set correct permissions
```

**c. Configure Your Local SSH Client (for convenience):**

On your *local* machine, edit or create the file `~/.ssh/config`:

```
# ~/.ssh/config

Host box_name          # An alias you'll use to connect
    HostName your_server_ip_or_domain  # The actual IP or domain
    User your_username                # Your username on the remote server
    IdentityFile ~/.ssh/id_ed25519      # Path to the private key you generated
    Port 22                          # Usually 22, change if needed
```

Replace `box_name`, `your_server_ip_or_domain`, `your_username`, and `~/.ssh/id_ed25519` (if you used a different path) with your actual details.

Now, you can connect easily from your local terminal:

```bash
ssh box_name
```

VS Code's Remote-SSH extension and other tools can also use this configuration.

### 2. Set Up GitHub Access (Remote to GitHub)

To allow your *remote server* to securely interact with GitHub (e.g., clone private repos, push changes), you need a separate SSH key on the remote machine.

**a. Generate a New SSH Key Pair:**

Log into your *remote server* via SSH (`ssh box_name`). Then, generate a new key pair *on the remote server*:

```bash
# On the remote server:
ssh-keygen -t ed25519 -C "your_github_email@example.com"
```

You may have are two separate ssh key pairs: one to login to the box, another to login to GitHub.

Accept the defaults (usually saves to `/home/your_username/.ssh/id_ed25519` or `/root/.ssh/id_ed25519` if you're root). **Do not overwrite your *server access* key if it's in the same location unless intended.** You can specify a different filename if needed (e.g., `~/.ssh/github_ed25519`).

**b. Add the Public Key to GitHub:**

Display the new *public* key on the *remote server*:

```bash
# Use the correct path if you didn't use the default
cat ~/.ssh/id_ed25519.pub
```

Copy the entire output. Go to your GitHub account settings: navigate to [https://github.com/settings/keys](https://github.com/settings/keys). Click `New SSH key`, give it a descriptive title (e.g., "My Remote Box"), paste the public key, and save.

**c. Configure Git User:**

Still on the *remote server*, tell Git who you are:

```bash
git config --global user.email "your_github_email@example.com"
git config --global user.name "Your Name"
```

This ensures your commits are correctly attributed on GitHub (`git` will complain if you dont do this).

## Installing the Project

Whether on your local machine or the configured remote server:

### 1. Clone the Repository

```bash
# Replace with the actual repository URL (SSH or HTTPS)
git clone git@github.com:YourUsername/YourRepoName.git
# Or: git clone https://github.com/YourUsername/YourRepoName.git

cd YourRepoName # Navigate into the cloned directory
```

### 2. Set Up Your Python Environment

Using a virtual environment is helpful to isolate project dependencies.

**a. On Linux (using Miniconda - Recommended):**

Download miniconda:
```bash
curl -sL \
  "https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh" > \
  "Miniconda3.sh"
# Run the installer (follow prompts)
bash Miniconda3.sh
# Close and reopen your terminal after installation
```

Create and activate a new conda environment:

```bash
# Create an environment named 'ml_env' with Python 3.12
conda create -y -n ml_env python=3.12

# Activate the environment
conda activate ml_env
```

You'll see `(ml_env)` prepended to your shell prompt.

Remember to run `conda activate ml_env` every time you start a new session to work on this project.

**b. On macOS (or Linux without Conda - using venv):**

Python's built-in `venv` module works well too.

```bash
# Create a virtual environment named 'env' in the current directory
python3 -m venv env

# Activate the environment
source env/bin/activate
```

You'll see `(env)` prepended to your shell prompt.

Remember to run `source env/bin/activate` every time you start a new session.

### 3. Install Project Dependencies

Make sure your virtual environment is activated (`(ml_env)` or `(env)` should be visible in your prompt). Then, from the root directory of the cloned repository (`YourRepoName`):

```bash
# Installs the project itself and dependencies listed in setup.py/pyproject.toml
# The '-e' flag makes it an "editable" install, linking directly to your source code.
pip install -e .
```
