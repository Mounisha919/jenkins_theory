# 🔗 Jenkins Master-Slave (Agent) SSH Connection Setup

This guide explains how to set up a Jenkins **master-slave (agent)** connection over SSH using key-based authentication.

---

## 📍 Prerequisites

- Jenkins is installed and running on the **Master Node**
- Java is installed on the **Slave Node**
- SSH access is available between Master and Slave
- Both Master and Slave VMs have external IPs and open port 22 for SSH

---

## 🔐 Step 1: Generate SSH Key Pair on Master

On the **master node terminal**:

```bash
ssh-keygen -t rsa -b 2048 -f ~/.ssh/id_rsa
```

* This generates:

  * `~/.ssh/id_rsa` (private key)
  * `~/.ssh/id_rsa.pub` (public key)

---

## 📥 Step 2: Copy Public Key to Slave Node

Copy the master’s public key to the slave’s `authorized_keys` file:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub <slave-username>@<slave-public-ip>
```

Or manually:

```bash
cat ~/.ssh/id_rsa.pub | ssh <slave-username>@<slave-ip> 'mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys'
```

✅ Now the master can SSH into the slave without a password.

---

## ⚙️ Step 3: Configure Jenkins Node (Slave) in Jenkins UI

1. Go to **Manage Jenkins → Manage Nodes and Clouds → New Node**

2. Enter node name (e.g., `slave-1`) and choose **Permanent Agent**

3. Fill in details:

   * **Remote root directory**: `/home/<slave-username>`
   * **Labels**: `linux`, `slave`, etc.
   * **Launch method**: `Launch agents via SSH`

4. Fill in SSH details:

   * **Host**: `<slave-public-ip>`
   * **Credentials**:

     * Add new credentials:

       * **Kind**: SSH Username with private key
       * **Username**: `<slave-username>`
       * **Private Key**: Choose “Enter directly” and paste content of `~/.ssh/id_rsa` from the master

5. Save and launch.

---

## ✅ Verification

* Go back to **Manage Nodes**
* Your slave node should show status: **Connected**
* Console log should show:

  ```
  [SSH] Opening SSH connection to <IP>:22
  [SSH] Authentication successful.
  Agent successfully connected and online
  ```

---

## 🧠 Notes

* Jenkins **must be able to reach the slave over public IP** on port 22
* Only **Java is required on the slave**, Jenkins doesn't need to be installed there
* Ensure firewall rules allow SSH from master to slave

---

## ✅ Sample Declarative Pipeline Using Agent Label

Below is a minimal pipeline that runs on the slave with label `linux` and creates a folder on that slave machine:

```groovy
pipeline {
    agent { label 'linux' }

    stages {
        stage('Create Folder') {
            steps {
                sh 'mkdir -p /tmp/jenkins-example-folder'
                echo 'Folder created on the slave!'
            }
        }
    }
}
```

> 💡 Replace `'linux'` with whatever label you gave to your slave during configuration.

---

## ✅ Pipeline stage view plugin-- to view stages

