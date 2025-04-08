# Remote JupyterLab on ScienceCloud

This guide walks you through setting up a remote JupyterLab environment on a ScienceCloud Ubuntu instance. You'll be able to use JupyterLab in your local browser, while everything runs remotely on the university's servers.

---

## 1. Generate Your SSH Key

You’ll first need to create a secure way to connect to your server. This is done using an SSH key.

Open a terminal on your computer:

- **Mac/Linux**: Open **Terminal**
- **Windows 10+**: Open **PowerShell** or **Windows Terminal**

Then run:

```bash
ssh-keygen -t rsa -b 4096
```

- You'll see a prompt asking where to save the key.  
  **Press Enter** to accept the default location.
- Next, you’ll be asked for a passphrase.  
  **Press Enter twice** to skip setting one or choose a password if you like (optional).
- This will create:
  - **Private key**: `~/.ssh/id_rsa`
  - **Public key**: `~/.ssh/id_rsa.pub`

---

## 2. Share Your Public Key

Now you’ll need to send your public key to your IT coordinator so they can give you access.

Still in your terminal, run:

```bash
cat ~/.ssh/id_rsa.pub
```

Copy the full line starting with `ssh-rsa`. It looks something like this:

```
ssh-rsa AAAAB3... your_username@your_computer
```

Send this key (the entire line) to your IT coordinator.

---

## 3. Log Into Your Cloud Instance

After receiving your SSH key, the IT coordinator will create your Ubuntu instance on the ScienceCloud and give you an IP address.

On your computer, then run:

```bash
ssh ubuntu@<your-instance-ip>
```

Replace `<your-instance-ip>` with the IP address you received.

Once you're logged in, your terminal prompt will show something like `ubuntu@...:`

> **Note:** You must be connected to the university network or VPN to access the ScienceCloud.

---

## 4. Set Up a Python Virtual Environment

Now that you're inside the server, you’ll create a virtual Python environment. This keeps your Python packages isolated from the system installation.

Update package info:

```bash
sudo apt update
```

Install virtual environment tools:

```bash
sudo apt install -y python3-venv
```

Create a virtual environment:

```bash
python3 -m venv venv
```

Activate it:

```bash
source venv/bin/activate
```

You should now see `(venv)` at the start of your command line.

---

## 5. Install JupyterLab

Now let's install JupyterLab in this environment.

Upgrade pip:

```bash
pip install --upgrade pip
```

Install JupyterLab:

```bash
pip install jupyterlab
```

This may take a minute or two.

---

## 6. Add Local Binaries to PATH

If you get an error like `jupyter: command not found` in the following step ([7. Configure JupyterLab](#7-configure-jupyterlab)), you can fix it with the following.

Add the folder to your PATH:

```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
```

Apply the change:

```bash
source ~/.bashrc
```

---

## 7. Configure JupyterLab

We'll now create a configuration and set a password.

Generate a config file:

```bash
jupyter lab --generate-config
```

Set a password for web login:

```bash
jupyter lab password
```

You’ll be asked to enter the password twice. This is what you’ll use later to access Jupyter in your browser.

---

## 8. Run JupyterLab in the Background

To keep JupyterLab running even after closing your terminal, we use `tmux`.

Install `tmux`:

```bash
sudo apt install tmux
```

Start a `tmux` session:

```bash
tmux
```

Start JupyterLab:

```bash
jupyter lab --no-browser --ip=127.0.0.1 --port=8888
```

Detach from `tmux` by pressing:

```
Ctrl + B, then D
```

JupyterLab will now continue running in the background.

> **Tip:** You can leave JupyterLab running between sessions — there’s no need to shut it down.

---

## 9. Tunnel the Jupyter Server to Your Laptop

You’ll now create an SSH tunnel from your computer to your server, so your browser can talk to JupyterLab.

On your local machine (in a new terminal), run:

```bash
ssh -L 8888:localhost:8888 ubuntu@<your-instance-ip>
```

This creates a secure connection from your laptop to the JupyterLab server.

> **Note:** You must keep this terminal window open while you work.

---

## 10. Access Jupyter in Your Browser

Now that the tunnel is active, open your browser and go to:

```
http://localhost:8888
```

Log in with the password you set earlier.

You're now running JupyterLab — but on the ScienceCloud server.

---

## 11. Stop or Resume the Session

You don't need to stop anything between sessions. JupyterLab can stay running in the background. However, if you still want to stop JupyterLab, do the following.

Reconnect to the server:

```bash
ssh ubuntu@<your-instance-ip>
```

Reattach to `tmux`:

```bash
tmux attach
```

Stop JupyterLab by pressing:

```
Ctrl + C
```

Exit the `tmux` session:

```bash
exit
```

To leave the virtual environment:

```bash
deactivate
```

---

## ⚡ Quick Start 

If you've already set everything up and JupyterLab is still running, simply do the following to reconnect to your JupyterLab session.

Tunnel to your instance (from your computer):

```bash
ssh -L 8888:localhost:8888 ubuntu@<your-instance-ip>
```

Open JupyterLab in your browser:

```
http://localhost:8888
```

You're done!

If for any reason JupyterLab isn't running anymore, restart it like this:

```bash
ssh ubuntu@<your-instance-ip>
```

```bash
source venv/bin/activate
```

```bash
tmux attach
```

```bash
jupyter lab --no-browser --ip=127.0.0.1 --port=8888
```
