# Terraform Installation Guide

## 1. Prerequisites
Before installing, ensure you have:
* A stable internet connection.
* Administrative privileges on your machine (for installing packages).
* A terminal or command prompt interface.

---

## 2. Installation Methods

### A. Windows
The easiest method for Windows is using a package manager like **Chocolatey**. If you do not have Chocolatey, you can install the binary manually.

#### Option 1: Using Chocolatey (Recommended)
1.  Open **PowerShell** as Administrator.
2.  Run the following command:
    ```powershell
    choco install terraform
    ```
3.  Type `y` to confirm if prompted.

#### Option 2: Manual Installation
1.  Download the latest Windows binary from the [Terraform Downloads page](https://developer.hashicorp.com/terraform/install).
2.  Extract the ZIP file to a dedicated folder (e.g., `C:\terraform`).
3.  Add this folder to your **Path** environment variable:
    * Search for "Environment Variables" in Windows Search.
    * Click **Environment Variables** > Select **Path** in "System variables" > Click **Edit**.
    * Click **New** and paste the path (e.g., `C:\terraform`).
    * Click **OK** to save.

---

### B. macOS
The standard way to install Terraform on macOS is via **Homebrew**.

1.  Open your terminal.
2.  Install the HashiCorp tap (repository):
    ```bash
    brew tap hashicorp/tap
    ```
3.  Install Terraform:
    ```bash
    brew install hashicorp/tap/terraform
    ```
4.  To update Terraform in the future, run:
    ```bash
    brew upgrade hashicorp/tap/terraform
    ```

---

### C. Linux (Ubuntu/Debian)
1.  Ensure `gnupg`, `software-properties-common`, and `curl` are installed:
    ```bash
    sudo apt-get update && sudo apt-get install -y gnupg software-properties-common curl
    ```
2.  Add the HashiCorp GPG key:
    ```bash
    wget -O- [https://apt.releases.hashicorp.com/gpg](https://apt.releases.hashicorp.com/gpg) | \
    gpg --dearmor | \
    sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null
    ```
3.  Add the official HashiCorp Linux repository:
    ```bash
    echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
    [https://apt.releases.hashicorp.com](https://apt.releases.hashicorp.com) $(lsb_release -cs) main" | \
    sudo tee /etc/apt/sources.list.d/hashicorp.list
    ```
4.  Update and install Terraform:
    ```bash
    sudo apt-get update && sudo apt-get install terraform
    ```

---

### D. Linux (CentOS/RHEL)
1.  Install `yum-utils`:
    ```bash
    sudo yum install -y yum-utils
    ```
2.  Add the HashiCorp repository:
    ```bash
    sudo yum-config-manager --add-repo [https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo](https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo)
    ```
3.  Install Terraform:
    ```bash
    sudo yum -y install terraform
    ```

---

## 3. Verify Installation
Once installed, verify that Terraform is accessible by checking the version.

1.  Open a new terminal or command prompt window.
2.  Run the command:
    ```bash
    terraform -version
    ```
3.  You should see an output similar to:
    ```text
    Terraform v1.9.0
    on linux_amd64
    ```

---

## 4. Post-Installation (Optional)

### Enable Tab Autocomplete
Terraform has a built-in command to enable tab completion for Zsh and Bash shells.

1.  Run the install command:
    ```bash
    terraform -install-autocomplete
    ```
2.  Restart your shell or source your configuration file (e.g., `source ~/.bashrc` or `source ~/.zshrc`).

---

## Troubleshooting Common Issues

| Issue | Solution |
| :--- | :--- |
| **Command not found** | Ensure the Terraform binary path is added to your system's `PATH` variable. |
| **Permission denied** | Run the terminal as Administrator (Windows) or use `sudo` (Linux/macOS) during installation. |
| **Old version installed** | If using a package manager, run the upgrade command (e.g., `brew upgrade` or `choco upgrade`). |
