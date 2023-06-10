## Lab Outline: Azure Vulnerability Management

### Prerequisites
- Computer with the Internet
- Free Azure Account - Possible to do with Free Subscription

### Create our Free Azure Account
<img width="886" alt="image" src="https://github.com/joshmadakor1/openvas/assets/39254979/e49a10a8-6f86-4c0a-9baf-5f3e9f6858dd">

1. Sign up: [Azure Free Account](https://azure.microsoft.com/en-us/free/)
2. Login: [Azure Portal](https://portal.azure.com)

### Prepare Vulnerability Management Scanner
<img width="890" alt="image" src="https://github.com/joshmadakor1/openvas/assets/39254979/8740ab1b-e972-4741-a7df-bdc7d883c24a">

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
<img width="752" alt="image" src="https://github.com/joshmadakor1/openvas/assets/39254979/493ed685-70f8-4260-84fb-037bd01c53fa">

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
<img width="730" alt="image" src="https://github.com/joshmadakor1/openvas/assets/39254979/8b3ca917-86d7-404c-94f6-b6402d391af4">

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
<img width="508" alt="image" src="https://github.com/joshmadakor1/openvas/assets/39254979/2ce2ea53-b67b-4206-8050-fecce0a11c52">

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
<img width="722" alt="image" src="https://github.com/joshmadakor1/openvas/assets/39254979/474cb714-19ad-4c99-8e60-945a2a12d7e5">

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
<img width="1126" alt="image" src="https://github.com/joshmadakor1/openvas/assets/39254979/83467078-e917-4eba-9757-745fe47b4ebf">

<img width="861" alt="image" src="https://github.com/joshmadakor1/openvas/assets/39254979/3b5cf494-829c-4eb0-bcfd-6d4372c97cd6">

1. Within Greenbone / OpenVAS, go to Scans > Tasks.
2. CLONE the "Scan - Azure Vulnerable VMs" Task and Edit it.
3. Name / Comment: "Scan - Azure Vulnerable VMs - Credentialed".
4. Targets: Azure Vulnerable VMs - Credentialed Scan.
5. Save.
6. Click the Play button to launch the new Credentialed Scan and wait for it to finish.

### Remediate Vulnerabilities
<img width="799" alt="image" src="https://github.com/joshmadakor1/openvas/assets/39254979/6b3835dc-11b1-4e00-b7a5-baa8049182f7">

1. Log back into your Win10-Vulnerable VM.
2. Uninstall Adobe Reader, VLC Player, and Firefox.
3. Restart the VM.
4. Re-initiate the "Scan - Azure Vulnerable VMs - Credentialed" scan and observe the results.

### Verify Remediations
<img width="1120" alt="image" src="https://github.com/joshmadakor1/openvas/assets/39254979/9efbd193-002a-49c2-9d9e-51f2cf1dc2af">

1. Note that there are no longer Vulnerabilities for FireFox, VLC Player, or Adobe Reader!


