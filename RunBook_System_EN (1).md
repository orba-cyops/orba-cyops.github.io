# Windows Server Lab RunBook — LABKERNEL Domain

> **Lab Environment:** VMware VMs running Windows Server
> **Domain:** `labkernel.com` | NetBIOS: `LABKERNEL`
> **All PowerShell commands must be run as Administrator.**

---

# 0) Quick Preparation (Recommended Before Everything)

**In VMware:** Take a Snapshot of every VM before configuring AD/DNS/DHCP.
This gives you a safe restore point if something breaks.

**Machine Names & IPs:**

| Machine | IP Address | DNS Server |
|---------|-----------|------------|
| DC1-labkernel | `192.168.30.10` | `127.0.0.1` (itself) |
| DC2-labkernel | `192.168.30.20` | `192.168.30.10` (DC1) |
| win10-1 | DHCP (assigned later) / temp: `192.168.30.100` | `192.168.30.10` |

---

# 1) Network Config (PowerShell) — DC1 / DC2

> Run PowerShell as **Administrator**.
> If you're unsure of the network adapter name, run: `Get-NetAdapter`

## DC1 — Set IP + DNS to 127.0.0.1 (itself)

```powershell
# Get the name of the active network adapter automatically
$if = (Get-NetAdapter | ? Status -eq Up).Name

# Assign static IP, subnet mask, and default gateway
New-NetIPAddress -InterfaceAlias $if -IPAddress 192.168.30.10 -PrefixLength 24 -DefaultGateway 192.168.30.254

# DC1 points DNS to itself (127.0.0.1) because it will host the DNS role
Set-DnsClientServerAddress -InterfaceAlias $if -ServerAddresses 127.0.0.1

# Rename the computer and restart
Rename-Computer -NewName "DC1-labkernel" -Force -Restart
```

## DC2 — Set IP + DNS pointing to DC1

```powershell
$if = (Get-NetAdapter | ? Status -eq Up).Name

New-NetIPAddress -InterfaceAlias $if -IPAddress 192.168.30.20 -PrefixLength 24 -DefaultGateway 192.168.30.254

# DC2 points to DC1 for DNS resolution
Set-DnsClientServerAddress -InterfaceAlias $if -ServerAddresses 192.168.30.10

Rename-Computer -NewName "DC2-labkernel" -Force -Restart
```

---

# 2) Create Forest + Promote DC1 (PowerShell)

## DC1 — Install Roles + Promote to Domain Controller

```powershell
# Install Active Directory Domain Services + DNS role with management tools
Install-WindowsFeature AD-Domain-Services,DNS -IncludeManagementTools

# Create a new forest and promote DC1 as the first Domain Controller
# The server will reboot automatically after this
Install-ADDSForest `
 -DomainName "labkernel.com" `
 -DomainNetbiosName "LABKERNEL" `
 -InstallDNS `
 -Force
```

> **What happens:** DC1 becomes the first DC in the `labkernel.com` forest, installs DNS, creates the default AD structure (Users, Computers, Domain Controllers OUs), and reboots.

---

# 3) Add DC2 as an Additional Domain Controller (PowerShell)

## DC2 — Join Domain, then Promote

```powershell
# Step 1: Install the AD DS and DNS roles
Install-WindowsFeature AD-Domain-Services,DNS -IncludeManagementTools

# Step 2: Join DC2 to the labkernel.com domain
# (You will be prompted for Domain Admin credentials)
Add-Computer -DomainName "labkernel.com" -Restart
```

After the restart (back on DC2):

```powershell
# Step 3: Promote DC2 to a Domain Controller (Additional DC in existing domain)
Install-ADDSDomainController `
 -DomainName "labkernel.com" `
 -InstallDNS `
 -Force
```

> **Why add a second DC?** Redundancy — if DC1 goes down, DC2 can still authenticate users and serve DNS. Replication keeps both DCs synchronized.

---

# 4) Verify Replication Between DCs (Quick CLI)

Run these on each DC to confirm replication is healthy:

```powershell
repadmin /replsummary # High-level summary — look for 0 failures
repadmin /showrepl # Detailed per-partner replication status
dcdiag /v # Full domain controller diagnostics
```

**Expected output from `repadmin /replsummary`:**
```
Replication Summary Start Time: ...
Beginning data collection for replication summary, this may take awhile...

Source DSA largest delta fails/total %% error
DC1-labkernel 00:00:05 0 / 5 0
DC2-labkernel 00:00:07 0 / 5 0
```
Zero failures = replication is healthy.

---

# 5 + 6) OU Structure + Users + Groups + Admin OU + Delegation (PowerShell on DC1)

> **What this script does:**
> 1. Creates an OU tree: Jerusalem, Tel Aviv, Haifa — each with Users, Groups, Computers sub-OUs
> 2. Creates an "Admin Users" OU with 2 admin users and a "Sysadmin Users" security group
> 3. Creates 10 regular users per location and adds them to location-based groups
> 4. Delegates "Reset Password" and "Change Password" rights to the "Sysadmin Users" group on all Users OUs

```powershell
Import-Module ActiveDirectory

# ===================== CONFIGURATION =====================
$baseDN = "DC=labkernel,DC=com"

# OU names per requirement (Tel Aviv includes a space)
$locations = @("Jerusalem","Tel Aviv","Haifa")

# Lab password for all users
$pwdPlain = "P@ssw0rd123!"
$pwd = ConvertTo-SecureString $pwdPlain -AsPlainText -Force

# ===================== HELPER FUNCTIONS =====================

# Creates an OU only if it doesn't already exist (idempotent)
function Ensure-OU {
 param(
 [Parameter(Mandatory)] [string] $Name,
 [Parameter(Mandatory)] [string] $Path
 )
 $dn = "OU=$Name,$Path"
 $exists = Get-ADOrganizationalUnit -LDAPFilter "(distinguishedName=$dn)" -ErrorAction SilentlyContinue
 if (-not $exists) {
 New-ADOrganizationalUnit -Name $Name -Path $Path -ProtectedFromAccidentalDeletion $false | Out-Null
 Write-Host "Created OU: $dn"
 } else {
 Write-Host "OU exists: $dn"
 }
 return $dn
}

# Creates a user only if they don't already exist
function Ensure-User {
 param(
 [Parameter(Mandatory)] [string] $Name,
 [Parameter(Mandatory)] [string] $Sam,
 [Parameter(Mandatory)] [string] $Path,
 [Parameter(Mandatory)] [securestring] $Password
 )
 $u = Get-ADUser -Filter "SamAccountName -eq '$Sam'" -ErrorAction SilentlyContinue
 if (-not $u) {
 New-ADUser -Name $Name -SamAccountName $Sam -UserPrincipalName "$Sam@labkernel.com" `
 -AccountPassword $Password -Enabled $true -Path $Path -ChangePasswordAtLogon $false | Out-Null
 Write-Host "Created User: $Sam ($Path)"
 } else {
 Write-Host "User exists: $Sam"
 }
}

# Creates a security group only if it doesn't already exist
function Ensure-Group {
 param(
 [Parameter(Mandatory)] [string] $Name,
 [Parameter(Mandatory)] [string] $Sam,
 [Parameter(Mandatory)] [string] $Path
 )
 $g = Get-ADGroup -Filter "SamAccountName -eq '$Sam'" -ErrorAction SilentlyContinue
 if (-not $g) {
 New-ADGroup -Name $Name -SamAccountName $Sam -GroupScope Global -GroupCategory Security -Path $Path | Out-Null
 Write-Host "Created Group: $Name ($Path)"
 } else {
 Write-Host "Group exists: $Name"
 }
}

# Get the full Distinguished Name (DN) of a group by name within a search base
function Get-GroupDN {
 param(
 [Parameter(Mandatory)] [string] $GroupName,
 [Parameter(Mandatory)] [string] $SearchBase
 )
 $g = Get-ADGroup -SearchBase $SearchBase -LDAPFilter "(name=$GroupName)" -Properties DistinguishedName -ErrorAction Stop
 return $g.DistinguishedName
}

# ===================== 1) CREATE OU STRUCTURE =====================
# Creates: Jerusalem\Users, Jerusalem\Groups, Jerusalem\Computers (and same for Tel Aviv, Haifa)
foreach ($loc in $locations) {
 $locOU = Ensure-OU -Name $loc -Path $baseDN
 Ensure-OU -Name "Users" -Path $locOU | Out-Null
 Ensure-OU -Name "Groups" -Path $locOU | Out-Null
 Ensure-OU -Name "Computers" -Path $locOU | Out-Null
}

# Create Servers OU and Admin Users OU at the domain root
Ensure-OU -Name "Servers" -Path $baseDN | Out-Null
$adminOUdn = Ensure-OU -Name "Admin Users" -Path $baseDN

# ===================== 2) ADMIN USERS + SYSADMIN GROUP =====================
Ensure-User -Name "Admin-User1" -Sam "Admin-User1" -Path $adminOUdn -Password $pwd
Ensure-User -Name "Admin-User2" -Sam "Admin-User2" -Path $adminOUdn -Password $pwd

Ensure-Group -Name "Sysadmin Users" -Sam "SysadminUsers" -Path $adminOUdn

# IMPORTANT: Add members using the group's DN (not just its name)
$sysGroupDN = Get-GroupDN -GroupName "Sysadmin Users" -SearchBase $adminOUdn
Add-ADGroupMember -Identity $sysGroupDN -Members "Admin-User1","Admin-User2" -ErrorAction Stop
Write-Host "Added Admin users to Sysadmin Users."

# ===================== 3) LOCATION USERS + GROUPS + MEMBERSHIP =====================
foreach ($loc in $locations) {

 $locKey = ($loc -replace '\s','') # "Tel Aviv" "TelAviv"
 $usersOU = "OU=Users,OU=$loc,$baseDN"
 $groupsOU = "OU=Groups,OU=$loc,$baseDN"

 $groupName = "$locKey-Users"
 $groupSam = "$locKey" + "Users" # e.g., JerusalemUsers, TelAvivUsers

 Ensure-Group -Name $groupName -Sam $groupSam -Path $groupsOU

 # Get the group's DN for membership assignment
 $locGroupDN = Get-GroupDN -GroupName $groupName -SearchBase $groupsOU

 # Create users 01–10 for this location and add them to the group
 1..10 | ForEach-Object {
 $n = "{0:D2}" -f $_ # Zero-padded: 01, 02, ... 10
 $userName = "$locKey-User$n"
 $userSam = "$locKey" + "User$n" # e.g., JerusalemUser01

 Ensure-User -Name $userName -Sam $userSam -Path $usersOU -Password $pwd

 # Add user to their location group by DN
 Add-ADGroupMember -Identity $locGroupDN -Members $userSam -ErrorAction Stop
 }

 Write-Host "Finished $loc: created users + added to $groupName"
}

# ===================== 4) DELEGATION: RESET PASSWORD =====================
# Delegate "Reset Password" and "Change Password" rights to Sysadmin Users
# on the Users OU of each location

# GUIDs for AD extended rights:
# Reset Password: 00299570-246d-11d0-a768-00aa006e0529
# Change Password: ab721a53-1e2f-11d0-9819-00aa0040529b
$resetPwdGuid = "00299570-246d-11d0-a768-00aa006e0529"
$changePwdGuid = "ab721a53-1e2f-11d0-9819-00aa0040529b"

foreach ($loc in $locations) {
 $usersOUdn = "OU=Users,OU=$loc,$baseDN"

 # Grant Reset Password on user objects under this OU
 dsacls $usersOUdn /I:T /G "labkernel\Sysadmin Users:CA;$resetPwdGuid;user" | Out-Null

 # Also grant Change Password (recommended)
 dsacls $usersOUdn /I:T /G "labkernel\Sysadmin Users:CA;$changePwdGuid;user" | Out-Null

 Write-Host "Delegation applied on: $usersOUdn"
}

Write-Host "`nDONE — Everything created + memberships + delegation applied."
```

---

# 7) DHCP on DC1 (PowerShell)

> **What this does:** Installs the DHCP Server role, authorizes it in Active Directory, creates a scope that hands out IPs from .100 to .200, and sets the gateway and DNS options.

```powershell
# Install DHCP Server role with management tools
Install-WindowsFeature DHCP -IncludeManagementTools

# Authorize the DHCP server in Active Directory
# (Unauthorized DHCP servers are blocked in AD environments)
Add-DhcpServerInDC -DnsName "DC1-labkernel.labkernel.com" -IpAddress 192.168.30.10

# Create the DHCP scope
Add-DhcpServerv4Scope `
 -Name "LABKERNEL-SCOPE" `
 -StartRange 192.168.30.100 `
 -EndRange 192.168.30.200 `
 -SubnetMask 255.255.255.0

# Set scope options: default gateway (router), DNS server, and domain name
Set-DhcpServerv4OptionValue `
 -ScopeId 192.168.30.0 `
 -Router 192.168.30.254 `
 -DnsServer 192.168.30.10 `
 -DnsDomain "labkernel.com"
```

**Quick verification:**
```powershell
Get-DhcpServerv4Scope # Shows the configured scope
Get-DhcpServerv4Lease # Shows currently leased IPs
```

---

# 8) Win10-1: Get DHCP IP + Join Domain (Quick)

Run on win10-1 as Administrator:

```powershell
# Rename the machine
Rename-Computer -NewName "win10-1" -Force

# Release and renew IP address (switch from static to DHCP)
ipconfig /release
ipconfig /renew

# Join the domain — will prompt for Domain Admin credentials, then reboot
Add-Computer -DomainName "labkernel.com" -Restart
```

After reboot: Log in with a domain user account, for example:
`LABKERNEL\Jerusalem-User01`

---

# 9) Password Policy (PowerShell on DC1)

## Default Domain Password Policy

```powershell
Set-ADDefaultDomainPasswordPolicy `
 -PasswordHistoryCount 10 ` # Remember last 10 passwords (can't reuse)
 -MaxPasswordAge (New-TimeSpan -Days 90) ` # Password expires after 90 days
 -MinPasswordAge (New-TimeSpan -Days 0) ` # Can change immediately
 -MinPasswordLength 8 ` # Minimum 8 characters
 -ComplexityEnabled $true ` # Must include uppercase, lowercase, number, symbol
 -LockoutDuration (New-TimeSpan -Minutes 30) ` # Locked out for 30 minutes
 -LockoutObservationWindow (New-TimeSpan -Minutes 30) `
 -LockoutThreshold 5 # 5 wrong attempts triggers lockout
```

**Verify the policy was applied:**
```powershell
Get-ADDefaultDomainPasswordPolicy
```

---

## Fine-Grained Password Policy for Sysadmin Users (PSO)

> **Why use PSO?** The default policy applies to all users. A PSO (Password Settings Object) lets you apply stricter rules to privileged accounts like sysadmins.

```powershell
# Create a stricter PSO for sysadmins
New-ADFineGrainedPasswordPolicy `
 -Name "PSO-SysAdmins" `
 -Precedence 1 ` # Lower number = higher priority
 -PasswordHistoryCount 20 `
 -MaxPasswordAge (New-TimeSpan -Days 30) ` # Sysadmins must change every 30 days
 -MinPasswordAge (New-TimeSpan -Days 0) `
 -MinPasswordLength 10 ` # Stricter: minimum 10 characters
 -ComplexityEnabled $true `
 -LockoutDuration (New-TimeSpan -Minutes 30) `
 -LockoutObservationWindow (New-TimeSpan -Minutes 30) `
 -LockoutThreshold 5
```

**Apply the PSO to the Sysadmin Users group:**
```powershell
Add-ADFineGrainedPasswordPolicySubject `
 -Identity "PSO-SysAdmins" `
 -Subjects "SysAdmins Users"
```

**Verify:**
```powershell
Get-ADFineGrainedPasswordPolicy -Identity "PSO-SysAdmins"
```

---

# 10) Audit Policy in Default Domain Controllers Policy (DC1)

> **Note:** The cleanest way to configure audit policy is through the GUI (GPMC), as it lives under Security Settings.

**GUI Steps (30-second summary):**

1. Open **GPMC** (Group Policy Management Console)
2. Navigate to: `Default Domain Controllers Policy` **Edit**
3. Go to: `Computer Configuration Policies Windows Settings Security Settings Advanced Audit Policy`
4. Enable auditing for:
 - **Account Logon** — tracks authentication events
 - **Logon/Logoff** — tracks interactive sign-in/out events

---

# 11) GPO: "Sysadmin Policy" — Add Sysadmin Users to Local Administrators (DC1)

> **Goal:** All members of the "Sysadmin Users" group become local admins on all domain-joined computers via GPO.

**GUI Steps (fastest method for exam):**

1. Open **GPMC** Create a new GPO named `Sysadmin Policy`
2. Link it to the domain (or to the Computers OU if organized)
3. Edit the GPO: `Computer Configuration Preferences Control Panel Settings Local Users and Groups`
4. New **Local Group** Action: Update Group: **Administrators**
5. Click **Add…** enter `LABKERNEL\Sysadmin Users`

**Apply and verify on win10-1:**
```powershell
gpupdate /force # Force GPO refresh
whoami /groups # Check current user's groups
net localgroup administrators # List local admins
```

---

# 12) DNS: Reverse Lookup Zone + A Record for OpenFiler (PowerShell on DC1)

```powershell
# Create a reverse lookup zone for the 192.168.30.0/24 network
Add-DnsServerPrimaryZone -NetworkId "192.168.30.0/24" -ReplicationScope Forest

# Create an A record for the OpenFiler server
Add-DnsServerResourceRecordA -ZoneName "labkernel.com" -Name "OpenFiler" -IPv4Address 192.168.30.50
```

**Verify the record resolves correctly:**
```powershell
nslookup openfiler.labkernel.com
# Expected output:
# Server: DC1-labkernel.labkernel.com
# Address: 192.168.30.10
#
# Name: openfiler.labkernel.com
# Address: 192.168.30.50
```

---

# 13) NTFS Shares + Home Folders + Permissions (PowerShell on DC1)

## 13.1 Base Directories + SMB Shares

```powershell
# Create the root share and homes directories
New-Item -Path "C:\Shares" -ItemType Directory -Force | Out-Null
New-Item -Path "C:\Homes" -ItemType Directory -Force | Out-Null

# Create per-location shares with NTFS permissions
$locations = @("Jerusalem","TelAviv","Haifa")
foreach($loc in $locations){
 $p = "C:\Shares\$loc"
 New-Item -Path $p -ItemType Directory -Force | Out-Null

 # Create the SMB share (accessible as \\DC1-labkernel\Jerusalem-Share, etc.)
 New-SmbShare -Name "$loc-Share" -Path $p -FullAccess "LABKERNEL\Domain Admins" -ErrorAction SilentlyContinue | Out-Null

 # Set NTFS permissions:
 # - Remove inherited permissions first
 # - Grant Modify to the location's user group
 # - Grant Full Control to Domain Admins and SYSTEM
 icacls $p /inheritance:r | Out-Null
 icacls $p /grant "LABKERNEL\$loc-Users:(M)" "LABKERNEL\Domain Admins:(F)" "SYSTEM:(F)" | Out-Null
}
```

## 13.2 Automatic Home Folder per User

> **What this does:** Creates a personal folder for each user under `C:\Homes\`, sets ownership permissions, and maps it to the user's `H:` drive via AD.

```powershell
Import-Module ActiveDirectory

# Get all users matching the naming pattern (*-User*)
$users = Get-ADUser -Filter * -SearchBase "DC=labkernel,DC=com" | ? SamAccountName -like "*-User*"

foreach($u in $users){
 $homePath = "C:\Homes\$($u.SamAccountName)"
 New-Item -Path $homePath -ItemType Directory -Force | Out-Null

 # Remove inherited permissions, grant user Modify + admins Full Control
 icacls $homePath /inheritance:r | Out-Null
 icacls $homePath /grant "$($u.SamAccountName):(M)" "LABKERNEL\Domain Admins:(F)" "SYSTEM:(F)" | Out-Null

 # Map the home folder in Active Directory (appears as H: drive on login)
 Set-ADUser $u -HomeDirectory "\\DC1-labkernel\Homes$\$($u.SamAccountName)" -HomeDrive "H:"
}

# Optional: Create a hidden admin-accessible share for the Homes root
New-SmbShare -Name "Homes$" -Path "C:\Homes" -FullAccess "LABKERNEL\Domain Admins" -ErrorAction SilentlyContinue | Out-Null
```

---

# 14) Daily Backup at 18:00 to Share on DC2 + Shadow Copies Twice a Day

## 14.1 On DC2 — Create the Backup Share

```powershell
# Create the backup directory and share it (hidden share with $ suffix)
New-Item -Path "D:\Backups" -ItemType Directory -Force | Out-Null
New-SmbShare -Name "Backups$" -Path "D:\Backups" -FullAccess "LABKERNEL\Domain Admins" -ErrorAction SilentlyContinue | Out-Null
```

## 14.2 On DC1 — Install Windows Server Backup + Schedule Daily Backup

```powershell
# Install the Windows Server Backup feature
Install-WindowsFeature Windows-Server-Backup -IncludeManagementTools

# Define the backup target (the share on DC2)
$target = "\\DC2-labkernel\Backups$"

# Create a scheduled task to run backup every day at 18:00
schtasks /Create /TN "DailyBackup_1800" /SC DAILY /ST 18:00 /RL HIGHEST `
 /TR "wbadmin start backup -backupTarget:$target -include:C: -quiet"
```

**Run an immediate test backup and verify it succeeded:**
```powershell
wbadmin start backup -backupTarget:\\DC2-labkernel\Backups$ -include:C: -quiet
wbadmin get versions -backupTarget:\\DC2-labkernel\Backups$
```

## 14.3 Shadow Copies Twice a Day (08:00 and 17:00)

> Shadow Copies allow users to restore previous versions of files directly from Windows Explorer ("Previous Versions" tab).

```powershell
# Shadow Copy at 08:00
schtasks /Create /TN "ShadowCopy_0800" /SC DAILY /ST 08:00 /RL HIGHEST `
 /TR "wmic shadowcopy call create Volume='C:\\'"

# Shadow Copy at 17:00
schtasks /Create /TN "ShadowCopy_1700" /SC DAILY /ST 17:00 /RL HIGHEST `
 /TR "wmic shadowcopy call create Volume='C:\\'"
```

**Verify shadow copies were created:**
```powershell
vssadmin list shadows
```

---

# 15) PowerShell Tasks — Export CSV (on DC1)

## 15.0 Ensure Output Folder Exists (Required Before Any Export)

```powershell
New-Item -Path "C:\Temp" -ItemType Directory -Force | Out-Null
```

## 15.1 Export All Windows Services to CSV

```powershell
New-Item -Path "C:\Temp" -ItemType Directory -Force | Out-Null

Get-Service |
 Select-Object Name, DisplayName, Status, StartType |
 Export-Csv "C:\Temp\DC_Services.csv" -NoTypeInformation -Encoding UTF8
```

## 15.2 Export All AD Users to CSV

```powershell
New-Item -Path "C:\Temp" -ItemType Directory -Force | Out-Null

Import-Module ActiveDirectory -ErrorAction Stop

Get-ADUser -Filter * -Properties DisplayName, Enabled, LastLogonDate, PasswordLastSet |
 Select-Object SamAccountName, DisplayName, Enabled, LastLogonDate, PasswordLastSet |
 Export-Csv "C:\Temp\AD_Users.csv" -NoTypeInformation -Encoding UTF8
```

**Quick success check:**
```powershell
Test-Path "C:\Temp\DC_Services.csv" # Returns True if file exists
Test-Path "C:\Temp\AD_Users.csv" # Returns True if file exists
```

---

# Final Verification Tests (Quick)

### On win10-1:
```powershell
whoami # Shows current logged-in user (e.g., LABKERNEL\Jerusalem-User01)
nltest /dsgetdc:labkernel.com # Finds a reachable Domain Controller
gpresult /r # Shows applied GPOs for user and computer
```

### On DC1 / DC2:
```powershell
repadmin /replsummary # Replication health summary
repadmin /showrepl # Detailed replication info
nslookup dc1-labkernel.labkernel.com # Verify DC1 DNS record
nslookup openfiler.labkernel.com # Verify OpenFiler DNS record
```

---

# Checkout — Full Status Script (Run on DC1 as Admin)

> This script checks every major component and prints PASS or FAIL for each.

```powershell
# LABKERNEL - Quick Status Summary (PASS/FAIL) | Run on DC1 as Admin
$ErrorActionPreference = "SilentlyContinue"
Import-Module ActiveDirectory

$domain = "labkernel.com"
$netbios = "LABKERNEL"
$dc1 = "DC1-labkernel"
$dc2 = "DC2-labkernel"
$backupShare = "\\DC2-labkernel\Backups$"

function Line($name,$ok,$detail=""){
 $s = if($ok){"PASS "}else{"FAIL "}
 "{0,-45} {1,-9} {2}" -f $name,$s,$detail
}

$lines = @()

# 1) Domain / AD basic
$d = Get-ADDomain
$lines += Line "AD Domain detected" ($d.DNSRoot -eq $domain) "($($d.DNSRoot))"

# 2) DCs registered + core services running
$dcs = (Get-ADDomainController -Filter * | Select-Object -Expand HostName)
$lines += Line "DC1 registered in AD" ($dcs -contains "$dc1.$domain")
$lines += Line "DC2 registered in AD" ($dcs -contains "$dc2.$domain")

$svcOk = @( "NTDS","DNS" | ForEach-Object { (Get-Service $_).Status -eq "Running" } ) -notcontains $false
$lines += Line "Core services running (NTDS, DNS)" $svcOk

# 3) Replication health
$repl = (repadmin /replsummary 2>$null)
$replOk = ($repl -notmatch "fails|failure|error") -and ($repl -match "Source DSA")
$lines += Line "Replication healthy (repadmin /replsummary)" $replOk

# 4) DNS records
$dns1 = (Resolve-DnsName "$dc1.$domain" -Type A 2>$null)
$dns2 = (Resolve-DnsName "$dc2.$domain" -Type A 2>$null)
$lines += Line "DNS A record: DC1" ($dns1 -ne $null)
$lines += Line "DNS A record: DC2" ($dns2 -ne $null)

$open = (Resolve-DnsName "openfiler.$domain" -Type A 2>$null)
$lines += Line "DNS A record: OpenFiler (optional)" ($open -ne $null)

$rev = (Get-DnsServerZone 2>$null | Where-Object { $_.IsReverseLookupZone -eq $true -and $_.ZoneName -like "*30.168.192.in-addr.arpa*" })
$lines += Line "Reverse Lookup Zone (192.168.30.0/24) (optional)" ($rev -ne $null)

# 5) DHCP service + scope
$dhcpSvc = (Get-Service DHCPServer).Status -eq "Running"
$lines += Line "DHCP service running on DC1" $dhcpSvc
$scope = (Get-DhcpServerv4Scope 2>$null | Where-Object { $_.StartRange -eq "192.168.30.100" -and $_.EndRange -eq "192.168.30.200" })
$lines += Line "DHCP Scope 192.168.30.100–200 exists" ($scope -ne $null)

# 6) OU structure verification
$ouBase = "DC=labkernel,DC=com"
$ouAdmin = Get-ADOrganizationalUnit -LDAPFilter "(distinguishedName=OU=Admin Users,$ouBase)"
$lines += Line "OU: Admin Users exists" ($ouAdmin -ne $null)

$locs = @("Jerusalem","Tel Aviv","Haifa")
foreach($l in $locs){
 $ou = Get-ADOrganizationalUnit -LDAPFilter "(distinguishedName=OU=$l,$ouBase)"
 $lines += Line "OU: $l exists" ($ou -ne $null)
 $u = Get-ADOrganizationalUnit -LDAPFilter "(distinguishedName=OU=Users,OU=$l,$ouBase)"
 $g = Get-ADOrganizationalUnit -LDAPFilter "(distinguishedName=OU=Groups,OU=$l,$ouBase)"
 $c = Get-ADOrganizationalUnit -LDAPFilter "(distinguishedName=OU=Computers,OU=$l,$ouBase)"
 $lines += Line "OU children for $l (Users/Groups/Computers)" (($u -and $g -and $c) -ne $null)
}

# 7) Users and groups
$sysGroup = Get-ADGroup -LDAPFilter "(cn=Sysadmin Users)" -SearchBase "OU=Admin Users,$ouBase"
$lines += Line "Group: Sysadmin Users exists" ($sysGroup -ne $null)

$admin1 = Get-ADUser -Identity "Admin-User1" 2>$null
$admin2 = Get-ADUser -Identity "Admin-User2" 2>$null
$lines += Line "Admin users exist (Admin-User1/2)" (($admin1 -and $admin2) -ne $null)

foreach($l in $locs){
 $key = ($l -replace '\s','')
 $count = (Get-ADUser -Filter "SamAccountName -like '$key`User*'" -SearchBase "OU=Users,OU=$l,$ouBase" 2>$null | Measure-Object).Count
 $lines += Line "Users in $l = 10" ($count -eq 10) "(found $count)"
}

# 8) Password policies
$ddp = Get-ADDefaultDomainPasswordPolicy
$lines += Line "Default Domain Policy: minLen=8" ($ddp.MinPasswordLength -eq 8)
$lines += Line "Default Domain Policy: maxAge=90d" ($ddp.MaxPasswordAge.Days -eq 90)
$lines += Line "Default Domain Policy: lockout=5/30m" (($ddp.LockoutThreshold -eq 5) -and ($ddp.LockoutDuration.TotalMinutes -eq 30))

$pso = Get-ADFineGrainedPasswordPolicy -Filter "Name -eq 'PSO-SysAdmins'" 2>$null
$lines += Line "PSO exists: PSO-SysAdmins" ($pso -ne $null)
if($pso){
 $lines += Line "PSO: minLen=10 + maxAge=30d" (($pso.MinPasswordLength -eq 10) -and ($pso.MaxPasswordAge.Days -eq 30))
 $applies = (Get-ADFineGrainedPasswordPolicySubject -Identity "PSO-SysAdmins" 2>$null) -ne $null
 $lines += Line "PSO applied to at least 1 subject" $applies
}

# 9) SMB Shares
$share1 = Get-SmbShare -Name "Jerusalem-Share" 2>$null
$share2 = Get-SmbShare -Name "TelAviv-Share" 2>$null
$share3 = Get-SmbShare -Name "Haifa-Share" 2>$null
$lines += Line "SMB Shares exist (Jerusalem/TelAviv/Haifa)" (($share1 -and $share2 -and $share3) -ne $null)

$homes = Get-SmbShare -Name "Homes$" 2>$null
$lines += Line "SMB Share exists: Homes$" ($homes -ne $null)

# 10) Backup + scheduled tasks
$wbs = Get-WindowsFeature Windows-Server-Backup
$lines += Line "Windows Server Backup installed" ($wbs.Installed -eq $true)

$bk = Test-Path $backupShare
$lines += Line "Backup target reachable: \\DC2\Backups$" $bk

$task1 = (schtasks /Query /TN "DailyBackup_1800" 2>$null) -match "DailyBackup_1800"
$task2 = (schtasks /Query /TN "ShadowCopy_0800" 2>$null) -match "ShadowCopy_0800"
$task3 = (schtasks /Query /TN "ShadowCopy_1700" 2>$null) -match "ShadowCopy_1700"
$lines += Line "Scheduled tasks exist (Backup + ShadowCopy)" ($task1 -and $task2 -and $task3)

# 11) Export CSV files
$lines += Line "Exports exist: C:\Temp\DC_Services.csv" (Test-Path "C:\Temp\DC_Services.csv")
$lines += Line "Exports exist: C:\Temp\AD_Users.csv" (Test-Path "C:\Temp\AD_Users.csv")

# Print results
"`n================ LABKERNEL - STATUS SUMMARY ================`n"
$lines | ForEach-Object { $_ }
"`n============================================================`n"
```

**Expected successful output:**
```
AD Domain detected PASS (labkernel.com)
DC1 registered in AD PASS 
DC2 registered in AD PASS 
Core services running (NTDS, DNS) PASS 
Replication healthy (repadmin /replsummary) PASS 
...
```
