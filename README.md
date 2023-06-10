## Lab Outline: Azure Vulnerability Management

### Prerequisites
- Computer with the Internet
- Free Azure Account - Possible to do with Free Subscription

### Create our Free Azure Account
1. Sign up: [Azure Free Account](https://azure.microsoft.com/en-us/free/)
2. Login: [Azure Portal](https://portal.azure.com)

### Prepare Vulnerability Management Scanner
1. Go to [Azure Portal](https://portal.azure.com)
2. Navigate to the Marketplace and search for "OpenVAS secured and supported by HOSSTED"
3. Choose the "Start with a pre-set configuration" option and select the weakest configuration.
4. Click "Continue to Create VM"
5. Configure the VM:
   - Resource Group: Vulnerability-Management
   - VM Name: OpenVAS (Take note of the region and Vnet–consider East US 2)
   - Authentication: Username → azureuser / Cyberlab123!
   - Monitoring: Disable Boot Diagnostic
6. Click "Create" to create the VM.
7. Once the VM is created, SSH into it using PowerShell (Windows) or Terminal (MacOS) with the provided credentials.
8. Wait until the deployment of OpenVAS is complete.

### Create Client Virtual Machine and Make it Vulnerable
1. Go to [Azure Portal](https://portal.azure.com)
2. Search for Virtual Machines and create a new Virtual Machine.
3. Configure the VM:
   - Resource Group: Vulnerability-Management
   - VM Name: Win10-Vulnerable
   - Region: Same as the OpenVAS VM (East US 2)
   - Virtual Network: Same as OpenVAS
   - Image: Windows 10 Pro
   - Size: Any size with 2 vCPUs
   - Username: azureuser / Cyberlab123!
   - Networking: Same Vnet as OpenVAS
4. Create the VM.
5. Once the VM is created, ensure you can RDP into it with the provided credentials.
6. After logging in, make the VM vulnerable:
   - Disable the Windows Firewall
   - Gather up some [Old Software](https://drive.google.com/drive/folders/1n83ilCjZWZulbDdYnUe9wQPK2buY47_U?usp=sharing)
   - Install an Old Version of FireFox: Firefox Setup 97.0b5
   - Install an Old Version of VLC Player: vlc-1.1.7-win32
   - Install an Old Version of Adobe Reader: 10.0_AdbeRdr1000_en_US_1_
   - Restart the VM.

### Configure OpenVAS to Perform First Unauthenticated Scan against our Vulnerable VM
1. Login to OpenVAS and navigate to Assets > Hosts > New Host.
2. Add the Client VM PRIVATE IP Address.
3. Create a New Target from the Host, name it "Azure Vulnerable VMs".
4. Take note of the credentials. We will add SMB credentials later.
5. Create a new Task:
   - Name & Comment: "Scan - Azure Vulnerable VMs"
   - Scan Targets: "Azure Vulnerable VMs"
6. Save the Task.
7. Start the "Scan - Azure Vulnerable VMs" Task.
8. Once the scan is finished, click the date under "Last Report" to see the results.
9. Take note of the Tabs, especially the "Results" tab.

### Make Configurations for Credentialed Scans (Within VM)
1. Disable Windows Firewall.
2. Disable User Account Control.
3. Enable Remote Registry.
4. Set Registry Key:
   - Launch Registry Editor (regedit.exe) in "Run as administrator" mode.
   - Navigate to HKEY_LOCAL_MACHINE hive.
   - Open SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System key.
   - Create a new DWORD (32-bit) value with the following properties:
     - Name: LocalAccountTokenFilterPolicy
     - Value: 1
   - Close Registry Editor.
5. Restart the VM.

### Make Configurations for Credentialed Scans (OpenVAS)
1. Go to Configuration > Credentials > New Credential.
2. Name / Comment: "Azure VM Credentials".
3. Allow Insecure Use: Yes.
4. Username: azureuser.
5. Password: Cyberlab123!
6. Save.
7. Go to Configuration > Targets > CLONE the Target we made before.
8. NEW Name / Comment: "Azure Vulnerable VMs - Credentialed Scan".
9. Ensure the Private IP is still accurate.
10. Credentials > SMB > Select the Credentials we just made: Azure VM Credentials.
11. Save.

### Execute Credentialed Scan against our Vulnerable Windows VM
1. Within Greenbone / OpenVAS, go to Scans > Tasks.
2. CLONE the "Scan - Azure Vulnerable VMs" Task and Edit it.
3. Name / Comment: "Scan - Azure Vulnerable VMs - Credentialed".
4. Targets: Azure Vulnerable VMs - Credentialed Scan.
5. Save.
6. Click the Play button to launch the new Credentialed Scan and wait for it to finish.

### Remediate Vulnerabilities
1. Log back into your Win10-Vulnerable VM.
2. Uninstall Adobe Reader, VLC Player, and Firefox.
3. Restart the VM.
4. Re-initiate the "Scan - Azure Vulnerable VMs - Credentialed" scan and observe the results.


### Remediate Vulnerabilities
1. Log back into your Win10-Vulnerable VM.
2. Uninstall Adobe Reader, VLC Player, and Firefox.
3. Restart the VM.
4. Re-initiate the "Scan - Azure Vulnerable VMs - Credentialed" scan and observe the results.

### Verify Remediations
1. Re-initiate the “Scan - Azure Vulnerable VMs - Credentialed” scan.
2. Observe the results.

