# 🚀 Azure VM Deployment via ARM Template

This guide explains how to create an Azure Linux Virtual Machine (VM) using an ARM template with either **SSH key login** or **username/password** authentication.

---

## 🔐 Option 1: SSH Key Login

### Step 1: Generate SSH Key Pair

#### 🪟 For Windows

1. Open **PowerShell** or **Command Prompt**
2. Run the following command:
   ```powershell
   ssh-keygen
3. When prompted for a file location, press Enter to accept the default.
4. After generation:

    Navigate to the SSH folder (usually at 
    ``` C:\Users\<YourUser>\.ssh)```

    Open the .pub file (e.g., id_rsa.pub)

    Copy the entire key (starts with ssh-rsa)

### Step 2: Create parameters.json
- Create a parameters.json (which is in git repo). 
- Do not include your private key. Only the .pub key goes here.

### Deploy the VM
Run the following command in PowerShell or terminal:
```
az deployment group create --resource-group vscode --template-file vm-resource.json --parameters "@parameters.json"

```
- Note: Use quotes around @parameters.json in PowerShell.

### Step 3: Connect to the VM
After deployment completes, find the public IP address from Azure Portal or with:

```az vm show --resource-group vscode --name <vm-name> --show-details --query publicIps -o tsv ```

- Then connect using:
```ssh -i /path/to/your/private-key azureuser@<public-ip>```

# 🔑 Option 2: Azure VM Login Using Username and Password

This guide walks you through creating and accessing an Azure Linux Virtual Machine using **username and password** instead of SSH keys.

---

## 📝 Step 1: Update `parameters.json`

Replace the SSH key fields with the following parameters:

```json
{
  "parameters": {
    "location": {
      "value": "eastus"
    },
    "vmSize": {
      "value": "Standard_B1s"
    },
    "adminUsername": {
      "value": "azureuser"
    },
    "adminPassword": {
      "value": "YourSecurePassword123!" // Use a strong password
    }
  }
}
```

### Step 2: Update osProfile in the ARM Template
In your vm-resource.json ARM template, make sure the osProfile section is updated like this:

```json
"osProfile": {
  "computerName": "[variables('vmName')]",
  "adminUsername": "[parameters('adminUsername')]",
  "adminPassword": "[parameters('adminPassword')]",
  "linuxConfiguration": {
    "disablePasswordAuthentication": false
  }
}
```

### Step 3: Connect to the VM
Once the VM is deployed and running, get the public IP address from the Azure Portal or run:

```bash
az vm show --resource-group <your-resource-group> --name <your-vm-name> --show-details --query publicIps -o tsv
```
Then connect using:
```
ssh azureuser@<public-ip>

```

Note:- When prompted, enter the password you provided in the parameters.json.

### ✅ Notes
- Ensure SSH Port 22 is open in your Network Security Group (NSG).

- 🔐 Avoid using weak or common passwords.

- ⚙️ Use SSH key login in production environments for better security and automation.


