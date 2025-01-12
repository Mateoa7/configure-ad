<p align="center">
  <img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>

'll show you how to configure On-premises Active Directory.
<br />

<h2>Environments and Technologies Used</h2>
<ul>
  <li>Microsoft Azure (Virtual Machines/Compute)</li>
  <li>Remote Desktop</li>
  <li>Active Directory Domain Services</li>
  <li>PowerShell</li>
</ul>

<h2>Step 1: Setup Domain Controller (DC-1) in Azure</h2>

<h3>1.1 Create a Resource Group</h3>
<ol>
  <li>Go to the Azure Portal.</li>
  <li>Click Resource Groups > Create.</li>
  <li>Enter:
    <ul>
      <li>Name: Choose a descriptive name (e.g., "MateoGroup").</li>
      <li>Region: Select the region closest to you or suitable for your project.</li>
    </ul>
  </li>
  <li>Click Review + Create > Create.</li>
</ol>

<h3>1.2 Create a Virtual Network and Subnet</h3>
<ol>
  <li>In the Azure Portal, go to Virtual Networks > Create.</li>
  <li>Enter:
    <ul>
      <li>Name: (e.g., "MateoVn").</li>
      <li>Region: Same as your Resource Group.</li>
      <li>Subnet: Add a new subnet (e.g., "MatSubnet").</li>
    </ul>
  </li>
  <li>Click Review + Create > Create.</li>
</ol>

<h3>1.3 Create the Domain Controller VM (DC-1)</h3>
<ol>
  <li>Go to Virtual Machines > Create.</li>
  <li>Enter:
    <ul>
      <li>Name: DC-1.</li>
      <li>Region: Same as your Virtual Network.</li>
      <li>Image: Windows Server 2022 Datacenter.</li>
      <li>Size: Choose an appropriate size (e.g., Standard_B2s).</li>
      <li>Username: Mateo1.</li>
      <li>Password: Password1!.</li>
    </ul>
  </li>
  <li>Attach DC-1 to the LabVNet and LabSubnet.</li>
  <li>Click Review + Create > Create.</li>
</ol>

<h3>1.4 Set DC-1’s NIC Private IP to Static</h3>
<ol>
  <li>After DC-1 is created, go to its Networking settings.</li>
  <li>Click on the NIC (Network Interface).</li>
  <li>Under IP Configurations, set the Private IP Address to Static.</li>
</ol>

<h3>1.5 Disable Windows Firewall on DC-1</h3>
<ol>
  <li>Log into DC-1 via Remote Desktop.</li>
  <li>Open the Control Panel > Windows Defender Firewall > Turn Windows Defender Firewall On or Off.</li>
  <li>Disable the firewall for all profiles (Domain, Private, Public).</li>
</ol>

<h2>Step 2: Setup Client-1 in Azure</h2>

<h3>2.1 Create Client VM (Client-1)</h3>
<ol>
  <li>Go to Virtual Machines > Create.</li>
  <li>Enter:
    <ul>
      <li>Name: Client-1.</li>
      <li>Region: Same as your Virtual Network.</li>
      <li>Image: Windows 10 Pro.</li>
      <li>Size: Choose a suitable size (e.g., Standard_B2s).</li>
      <li>Username: Mateo1.</li>
      <li>Password: Password1!.</li>
    </ul>
  </li>
  <li>Attach Client-1 to the LabVNet and LabSubnet.</li>
  <li>Click Review + Create > Create.</li>
</ol>

<h3>2.2 Configure DNS for Client-1</h3>
<ol>
  <li>After Client-1 is created, go to its Networking settings.</li>
  <li>Set the DNS Server to DC-1’s private IP.</li>
  <li>Restart Client-1 from the Azure Portal.</li>
</ol>

<h3>2.3 Test Connectivity</h3>
<ol>
  <li>Log into Client-1 via Remote Desktop.</li>
  <li>Open Command Prompt and run:
    <pre>ping <DC-1’s private IP></pre>
    Ensure the ping is successful.</li>
  <li>Run <pre>ipconfig /all</pre> and confirm the DNS settings show DC-1’s private IP.</li>
</ol>

<h2>Step 3: Install Active Directory</h2>

<h3>3.1 Install Active Directory on DC-1</h3>
<ol>
  <li>Log into DC-1 via Remote Desktop.</li>
  <li>Open Server Manager > Add Roles and Features.</li>
  <li>Select Active Directory Domain Services (AD DS) and complete the installation.</li>
</ol>

<h3>3.2 Promote DC-1 to a Domain Controller</h3>
<ol>
  <li>After installation, in Server Manager, click the notification to Promote this server to a domain controller.</li>
  <li>Select Add a new forest:
    <ul>
      <li>Domain Name: mydomain.com.</li>
    </ul>
  </li>
  <li>Complete the configuration and restart DC-1.</li>
</ol>

<h2>Step 4: Configure Active Directory</h2>

<h3>4.1 Create Organizational Units (OUs)</h3>
<ol>
  <li>Log into DC-1 as mydomain.com\mateo1.</li>
  <li>Open Active Directory Users and Computers (ADUC).</li>
  <li>Create the following OUs:
    <ul>
      <li>_EMPLOYEES</li>
      <li>_ADMINS</li>
    </ul>
  </li>
</ol>

<h3>4.2 Create a Domain Admin User</h3>
<ol>
  <li>In ADUC, create a new user in _ADMINS:
    <ul>
      <li>Name: Mateo A.</li>
      <li>Username: Mateo_Are.</li>
      <li>Password: Password1!.</li>
    </ul>
  </li>
  <li>Add Mateo_Are to the Domain Admins security group.</li>
  <li>Log out of DC-1 and log back in as mydomain.com\Mateo_Are.</li>
</ol>

<h2>Step 5: Join Client-1 to the Domain</h2>

<h3>5.1 Join Client-1</h3>
<ol>
  <li>Log into Client-1 as Mateo1.</li>
  <li>Open System Properties > Change Settings > Join Domain.</li>
  <li>Enter the domain: mydomain.com.</li>
  <li>Provide credentials for Mateo_Are when prompted.</li>
  <li>Restart Client-1.</li>
</ol>

<h3>5.2 Verify in ADUC</h3>
<ol>
  <li>Log into DC-1 as Mateo_Are.</li>
  <li>Open ADUC and verify Client-1 appears under the Computers container.</li>
  <li>Move Client-1 to the _CLIENTS OU.</li>
</ol>

<h2>Part 2: Additional Configuration</h2>

<h3>Enable Remote Desktop for Domain Users</h3>
<ol>
  <li>Log into Client-1 as mydomain.com\Mateo_Are.</li>
  <li>Open System Properties > Remote Desktop.</li>
  <li>Allow access for Domain Users.</li>
  <li>Test logging in as a non-admin user.</li>
</ol>

<h3>Create Additional Users</h3>
<ol>
  <li>Log into DC-1 as Mateo_Are.</li>
  <li>Open PowerShell_ise as an administrator.</li>
  <li>Use a script to create bulk users (ensure the script places users in the _EMPLOYEES OU).</li>
  <li>Test logging into Client-1 with one of these users.</li>
</ol>
