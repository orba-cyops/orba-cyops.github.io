# ✅ 0) הכנה מהירה (מומלץ לפני הכל)

**ב-VMware**: קח Snapshot לכל VM לפני AD/DNS/DHCP.

**שמות + IP**

- **DC1-labkernel**: `192.168.30.10` | DNS: `127.0.0.1`
    
- **DC2-labkernel**: `192.168.30.20` | DNS: `192.168.30.10`
    
- **win10-1**: DHCP (בסוף) / זמני: `192.168.30.100` | DNS: `192.168.30.10`
    

---

# ✅ 1) Network Config (PowerShell) – DC1 / DC2

> להריץ ב-PowerShell **Run as Administrator**  
> אם אתה לא בטוח בשם כרטיס הרשת: `Get-NetAdapter`

## DC1 – קבע IP + DNS ל-127.0.0.1

```
$if = (Get-NetAdapter | ? Status -eq Up).Name

New-NetIPAddress -InterfaceAlias $if -IPAddress 192.168.30.10 -PrefixLength 24 -DefaultGateway 192.168.30.254
Set-DnsClientServerAddress -InterfaceAlias $if -ServerAddresses 127.0.0.1

Rename-Computer -NewName "DC1-labkernel" -Force -Restart
```

## DC2 – קבע IP + DNS ל-DC1

```
$if = (Get-NetAdapter | ? Status -eq Up).Name

New-NetIPAddress -InterfaceAlias $if -IPAddress 192.168.30.20 -PrefixLength 24 -DefaultGateway 192.168.30.254
Set-DnsClientServerAddress -InterfaceAlias $if -ServerAddresses 192.168.30.10

Rename-Computer -NewName "DC2-labkernel" -Force -Restart
```

---

# ✅ 2) יצירת Forest + קידום DC1 (PowerShell)

## DC1 – התקנת Roles + Promote

```
Install-WindowsFeature AD-Domain-Services,DNS -IncludeManagementTools

Install-ADDSForest `
  -DomainName "labkernel.com" `
  -DomainNetbiosName "LABKERNEL" `
  -InstallDNS `
  -Force

```

(השרת יאתחל לבד)

---

# ✅ 3) הוספת DC2 כ-Additional Domain Controller (PowerShell)

## DC2 – הצמד לדומיין ואז Promote

```
Install-WindowsFeature AD-Domain-Services,DNS -IncludeManagementTools

# מצמיד את השרת לדומיין (יבקש סיסמה של Domain Admin)
Add-Computer -DomainName "labkernel.com" -Restart
```
אחרי ריסטארט (ב-DC2):
```
Install-ADDSDomainController `
  -DomainName "labkernel.com" `
  -InstallDNS `
  -Force

```

---

# ✅ 4) אימות Replication בין DCs (CLI מהיר)

בכל DC:

```
repadmin /replsummary
repadmin /showrepl
dcdiag /v
```

---
# ✅ 5 + 6) OU + Users + Groups + Admin OU (PowerShell על DC1) - Delegation: Reset Passwords ל-“Sysadmin Users”

## 5+6
```
Import-Module ActiveDirectory

# ===================== CONFIG =====================
$baseDN = "DC=labkernel,DC=com"

# שמות ה-OUs לפי הדרישה (Tel Aviv עם רווח)
$locations = @("Jerusalem","Tel Aviv","Haifa")

# סיסמה למעבדה
$pwdPlain = "P@ssw0rd123!"
$pwd = ConvertTo-SecureString $pwdPlain -AsPlainText -Force

# ===================== HELPERS =====================
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
        Write-Host "OU exists:  $dn"
    }
    return $dn
}

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
        Write-Host "User exists:  $Sam"
    }
}

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
        Write-Host "Group exists:  $Name"
    }
}

function Get-GroupDN {
    param(
        [Parameter(Mandatory)] [string] $GroupName,
        [Parameter(Mandatory)] [string] $SearchBase
    )
    $g = Get-ADGroup -SearchBase $SearchBase -LDAPFilter "(name=$GroupName)" -Properties DistinguishedName -ErrorAction Stop
    return $g.DistinguishedName
}

# ===================== 1) OU STRUCTURE =====================
foreach ($loc in $locations) {
    $locOU = Ensure-OU -Name $loc -Path $baseDN
    Ensure-OU -Name "Users"     -Path $locOU | Out-Null
    Ensure-OU -Name "Groups"    -Path $locOU | Out-Null
    Ensure-OU -Name "Computers" -Path $locOU | Out-Null
}

Ensure-OU -Name "Servers" -Path $baseDN | Out-Null
$adminOUdn = Ensure-OU -Name "Admin Users" -Path $baseDN

# ===================== 2) ADMIN USERS + SYSADMIN GROUP =====================
Ensure-User -Name "Admin-User1" -Sam "Admin-User1" -Path $adminOUdn -Password $pwd
Ensure-User -Name "Admin-User2" -Sam "Admin-User2" -Path $adminOUdn -Password $pwd

Ensure-Group -Name "Sysadmin Users" -Sam "SysadminUsers" -Path $adminOUdn

# ✅ חשוב: להוסיף חברים לפי DN של הקבוצה (לא לפי Name)
$sysGroupDN = Get-GroupDN -GroupName "Sysadmin Users" -SearchBase $adminOUdn
Add-ADGroupMember -Identity $sysGroupDN -Members "Admin-User1","Admin-User2" -ErrorAction Stop
Write-Host "Added Admin users to Sysadmin Users."

# ===================== 3) LOCATION USERS + GROUPS + MEMBERSHIP =====================
foreach ($loc in $locations) {

    $locKey   = ($loc -replace '\s','')                # Tel Aviv => TelAviv
    $usersOU  = "OU=Users,OU=$loc,$baseDN"
    $groupsOU = "OU=Groups,OU=$loc,$baseDN"

    $groupName = "$locKey-Users"
    $groupSam  = "$locKey" + "Users"                   # JerusalemUsers, TelAvivUsers, HaifaUsers

    Ensure-Group -Name $groupName -Sam $groupSam -Path $groupsOU

    # ✅ DN של הקבוצה (חיפוש ממוקד ב-OU Groups של הלוקיישן)
    $locGroupDN = Get-GroupDN -GroupName $groupName -SearchBase $groupsOU

    1..10 | ForEach-Object {
        $n = "{0:D2}" -f $_
        $userName = "$locKey-User$n"
        $userSam  = "$locKey" + "User$n"               # JerusalemUser01 וכו'

        Ensure-User -Name $userName -Sam $userSam -Path $usersOU -Password $pwd

        # ✅ הוספה לקבוצה לפי DN
        Add-ADGroupMember -Identity $locGroupDN -Members $userSam -ErrorAction Stop
    }

    Write-Host "Finished $loc : created users + added to $groupName"
}

# ===================== 4) DELEGATION: RESET PASSWORD =====================
# GUIDs of extended rights:
# Reset Password: 00299570-246d-11d0-a768-00aa006e0529
# Change Password: ab721a53-1e2f-11d0-9819-00aa0040529b (מומלץ)
$resetPwdGuid  = "00299570-246d-11d0-a768-00aa006e0529"
$changePwdGuid = "ab721a53-1e2f-11d0-9819-00aa0040529b"

foreach ($loc in $locations) {
    $usersOUdn = "OU=Users,OU=$loc,$baseDN"

    # Reset Password על user objects מתחת ל-OU
    dsacls $usersOUdn /I:T /G "labkernel\Sysadmin Users:CA;$resetPwdGuid;user" | Out-Null

    # (מומלץ) Change Password
    dsacls $usersOUdn /I:T /G "labkernel\Sysadmin Users:CA;$changePwdGuid;user" | Out-Null

    Write-Host "Delegation applied on: $usersOUdn"
}

Write-Host "`nDONE ✅ Everything created + memberships + delegation applied."

```
# ✅ 7) DHCP על DC1 (PowerShell)

```
Install-WindowsFeature DHCP -IncludeManagementTools

Add-DhcpServerInDC -DnsName "DC1-labkernel.labkernel.com" -IpAddress 192.168.30.10

Add-DhcpServerv4Scope `
  -Name "LABKERNEL-SCOPE" `
  -StartRange 192.168.30.100 `
  -EndRange 192.168.30.200 `
  -SubnetMask 255.255.255.0

Set-DhcpServerv4OptionValue `
  -ScopeId 192.168.30.0 `
  -Router 192.168.30.254 `
  -DnsServer 192.168.30.10 `
  -DnsDomain "labkernel.com"

```

---

# ✅ 8) Win10-1: DHCP + Join Domain (מהיר)

על win10-1 (Admin):

```
Rename-Computer -NewName "win10-1" -Force
ipconfig /release
ipconfig /renew

Add-Computer -DomainName "labkernel.com" -Restart

```

אחרי ריסטארט: להתחבר עם משתמש מירושלים, למשל:  
`LABKERNEL\Jerusalem-User1`

---

# ✅ 9) Password Policy (PowerShell על DC1)

```
Set-ADDefaultDomainPasswordPolicy `
 -PasswordHistoryCount 10 `
 -MaxPasswordAge (New-TimeSpan -Days 90) `
 -MinPasswordAge (New-TimeSpan -Days 0) `
 -MinPasswordLength 8 `
 -ComplexityEnabled $true `
 -LockoutDuration (New-TimeSpan -Minutes 30) `
 -LockoutObservationWindow (New-TimeSpan -Minutes 30) `
 -LockoutThreshold 5

```
בדיקה
```
Get-ADDefaultDomainPasswordPolicy
```

---
SysadminPSO

```
New-ADFineGrainedPasswordPolicy `
 -Name "PSO-SysAdmins" `
 -Precedence 1 `
 -PasswordHistoryCount 20 `
 -MaxPasswordAge (New-TimeSpan -Days 30) `
 -MinPasswordAge (New-TimeSpan -Days 0) `
 -MinPasswordLength 10 `
 -ComplexityEnabled $true `
 -LockoutDuration (New-TimeSpan -Minutes 30) `
 -LockoutObservationWindow (New-TimeSpan -Minutes 30) `
 -LockoutThreshold 5
```
להכיל על הקבוצה
```
Add-ADFineGrainedPasswordPolicySubject `
 -Identity "PSO-SysAdmins" `
 -Subjects "SysAdmins Users"
```
בדיקה
```
Get-ADFineGrainedPasswordPolicy -Identity "PSO-SysAdmins"
```

# ✅ 10) Audit Policy ב-Default Domain Controllers Policy (DC1)

כאן הכי “נקי” זה דרך GUI (GPMC) כי זה נופל תחת Security Settings.  
**אבל** אפשר להגדיר מקומית ולתת ל-GPO “לסחוב” – פחות מומלץ למבחן.

מה לעשות (GUI קצר):

- GPMC → Default Domain Controllers Policy → Edit
    
- Computer Config → Policies → Windows Settings → Security Settings → Advanced Audit Policy
    
    - Account Logon
        
    - Logon/Logoff
        

---

# ✅ 11) GPO: “Sysadmin Policy” ולהוסיף Sysadmin Users ל-Local Administrators (DC1)

כאן הכי מהיר במבחן: **GPP** (GUI), אבל אני נותן לך “תהליך 30 שניות”:

GUI (מומלץ):

- GPMC → Create GPO: **Sysadmin Policy** → Link ל-domain (או ל-OU של Computers אם סידרת)
    
- Edit → Computer Config → Preferences → Control Panel Settings → Local Users and Groups
    
- New → Local Group → **Administrators** → Add… → `LABKERNEL\Sysadmin Users`
    
- `gpupdate /force` על win10-1, ואז בדיקה: `net localgroup administrators`
    

בדיקת אפקט ב-win10-1:

```
gpupdate /force
whoami /groups
net localgroup administrators

```

---

# ✅ 12) DNS: Reverse Lookup + A Record ל-OpenFiler (PowerShell על DC1)

```
Add-DnsServerPrimaryZone -NetworkId "192.168.30.0/24" -ReplicationScope Forest

Add-DnsServerResourceRecordA -ZoneName "labkernel.com" -Name "OpenFiler" -IPv4Address 192.168.30.50

```

בדיקה:

`nslookup openfiler.labkernel.com`

---

# ✅ 13) NTFS Shares + Home Folders + Permissions (PowerShell על DC1)

## 13.1 תיקיות בסיס + שיתופים

```
New-Item -Path "C:\Shares" -ItemType Directory -Force | Out-Null
New-Item -Path "C:\Homes"  -ItemType Directory -Force | Out-Null

# שיתופי מחלקות לפי לוקיישן
$locations = @("Jerusalem","TelAviv","Haifa")
foreach($loc in $locations){
  $p = "C:\Shares\$loc"
  New-Item -Path $p -ItemType Directory -Force | Out-Null

  New-SmbShare -Name "$loc-Share" -Path $p -FullAccess "LABKERNEL\Domain Admins" -ErrorAction SilentlyContinue | Out-Null

  # NTFS: Modify לקבוצת הלוקיישן
  icacls $p /inheritance:r | Out-Null
  icacls $p /grant "LABKERNEL\$loc-Users:(M)" "LABKERNEL\Domain Admins:(F)" "SYSTEM:(F)" | Out-Null
}
```
## 13.2 Home Folder לכל משתמש (אוטומטי)

```
Import-Module ActiveDirectory

$users = Get-ADUser -Filter * -SearchBase "DC=labkernel,DC=com" | ? SamAccountName -like "*-User*"

foreach($u in $users){
  $homePath = "C:\Homes\$($u.SamAccountName)"
  New-Item -Path $homePath -ItemType Directory -Force | Out-Null

  icacls $homePath /inheritance:r | Out-Null
  icacls $homePath /grant "$($u.SamAccountName):(M)" "LABKERNEL\Domain Admins:(F)" "SYSTEM:(F)" | Out-Null

  # אם תרצה גם להגדיר HomeDirectory ב-AD:
  Set-ADUser $u -HomeDirectory "\\DC1-labkernel\Homes$\$($u.SamAccountName)" -HomeDrive "H:"
}

# אופציונלי: שיתוף נסתר ל-Homes
New-SmbShare -Name "Homes$" -Path "C:\Homes" -FullAccess "LABKERNEL\Domain Admins" -ErrorAction SilentlyContinue | Out-Null

```

---

# ✅ 14) Backup יומי 18:00 ל-Share על DC2 + Shadow Copy פעמיים ביום

## 14.1 על DC2 – צור Share ל-Backups

```
New-Item -Path "D:\Backups" -ItemType Directory -Force | Out-Null
New-SmbShare -Name "Backups$" -Path "D:\Backups" -FullAccess "LABKERNEL\Domain Admins" -ErrorAction SilentlyContinue | Out-Null
```

## 14.2 על DC1 – התקנת Windows Server Backup + Scheduled Backup (wbadmin)

```
Install-WindowsFeature Windows-Server-Backup -IncludeManagementTools

# יעד רשת:
$target = "\\DC2-labkernel\Backups$"

# משימה יומית ב-18:00 (wbadmin start backup)
schtasks /Create /TN "DailyBackup_1800" /SC DAILY /ST 18:00 /RL HIGHEST `
  /TR "wbadmin start backup -backupTarget:$target -include:C: -quiet"
```

הרצה ראשונית + בדיקה:

```
wbadmin start backup -backupTarget:\\DC2-labkernel\Backups$ -include:C: -quiet
wbadmin get versions -backupTarget:\\DC2-labkernel\Backups$
```

## 14.3 Shadow Copy פעמיים ביום (08:00, 17:00)

> Shadow Copy הכי קל עם Scheduled Task שמריץ `wmic shadowcopy call create Volume='C:\'`

```
schtasks /Create /TN "ShadowCopy_0800" /SC DAILY /ST 08:00 /RL HIGHEST `
  /TR "wmic shadowcopy call create Volume='C:\\'"

schtasks /Create /TN "ShadowCopy_1700" /SC DAILY /ST 17:00 /RL HIGHEST `
  /TR "wmic shadowcopy call create Volume='C:\\'"
```

בדיקה:

`vssadmin list shadows`

---

# ✅ 15) PowerShell Tasks – Export CSV (DC1) 

## 15.0 Ensure folder exists (חובה לפני Export)

```
New-Item -Path "C:\Temp" -ItemType Directory -Force | Out-Null
```
---

## 15.1 Export Services 

```
New-Item -Path "C:\Temp" -ItemType Directory -Force | Out-Null

Get-Service |
  Select-Object Name, DisplayName, Status, StartType |
  Export-Csv "C:\Temp\DC_Services.csv" -NoTypeInformation -Encoding UTF8

```

---

## 15.2 Export AD Users 

```
New-Item -Path "C:\Temp" -ItemType Directory -Force | Out-Null

Import-Module ActiveDirectory -ErrorAction Stop

Get-ADUser -Filter * -Properties DisplayName, Enabled, LastLogonDate, PasswordLastSet |
  Select-Object SamAccountName, DisplayName, Enabled, LastLogonDate, PasswordLastSet |
  Export-Csv "C:\Temp\AD_Users.csv" -NoTypeInformation -Encoding UTF8
```
---

### בדיקת הצלחה מהירה 

```
Test-Path "C:\Temp\DC_Services.csv"
Test-Path "C:\Temp\AD_Users.csv"
```
---

## ✅ “בדיקות סופיות” (מהירות)

על win10-1:

```
whoami
nltest /dsgetdc:labkernel.com
gpresult /r
```

על DC1/DC2:

```
repadmin /replsummary
repadmin /showrepl
nslookup dc1-labkernel.labkernel.com
nslookup openfiler.labkernel.com
```

# ✅ ChackOut

```
# LABKERNEL - Quick Status Summary (PASS/FAIL)  | Run on DC1 as Admin
$ErrorActionPreference = "SilentlyContinue"
Import-Module ActiveDirectory

$domain = "labkernel.com"
$netbios = "LABKERNEL"
$dc1 = "DC1-labkernel"
$dc2 = "DC2-labkernel"
$backupShare = "\\DC2-labkernel\Backups$"

function Line($name,$ok,$detail=""){
  $s = if($ok){"PASS ✅"}else{"FAIL ❌"}
  "{0,-45} {1,-9} {2}" -f $name,$s,$detail
}

$lines = @()

# 1) Domain / AD basic
$d = Get-ADDomain
$lines += Line "AD Domain detected" ($d.DNSRoot -eq $domain) "($($d.DNSRoot))"

# 2) DCs exist + services
$dcs = (Get-ADDomainController -Filter * | Select-Object -Expand HostName)
$lines += Line "DC1 registered in AD" ($dcs -contains "$dc1.$domain")
$lines += Line "DC2 registered in AD" ($dcs -contains "$dc2.$domain")

$svcOk = @( "NTDS","DNS" | ForEach-Object { (Get-Service $_).Status -eq "Running" } ) -notcontains $false
$lines += Line "Core services running (NTDS,DNS)" $svcOk

# 3) Replication summary (simple)
$repl = (repadmin /replsummary 2>$null)
$replOk = ($repl -notmatch "fails|failure|error") -and ($repl -match "Source DSA")
$lines += Line "Replication healthy (repadmin /replsummary)" $replOk

# 4) DNS checks
$dns1 = (Resolve-DnsName "$dc1.$domain" -Type A 2>$null)
$dns2 = (Resolve-DnsName "$dc2.$domain" -Type A 2>$null)
$lines += Line "DNS A record: DC1" ($dns1 -ne $null)
$lines += Line "DNS A record: DC2" ($dns2 -ne $null)

# OpenFiler record (optional)
$open = (Resolve-DnsName "openfiler.$domain" -Type A 2>$null)
$lines += Line "DNS A record: OpenFiler (optional)" ($open -ne $null)

# Reverse zone (optional)
$rev = (Get-DnsServerZone 2>$null | Where-Object { $_.IsReverseLookupZone -eq $true -and $_.ZoneName -like "*30.168.192.in-addr.arpa*" })
$lines += Line "Reverse Lookup Zone (192.168.30.0/24) (optional)" ($rev -ne $null)

# 5) DHCP on DC1
$dhcpSvc = (Get-Service DHCPServer).Status -eq "Running"
$lines += Line "DHCP service running on DC1" $dhcpSvc
$scope = (Get-DhcpServerv4Scope 2>$null | Where-Object { $_.StartRange -eq "192.168.30.100" -and $_.EndRange -eq "192.168.30.200" })
$lines += Line "DHCP Scope 192.168.30.100-200 exists" ($scope -ne $null)

# 6) OU structure
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

# 7) Users/Groups quick count
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

# 8) Password Policies
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

# 9) Shares / permissions (minimal)
$share1 = Get-SmbShare -Name "Jerusalem-Share" 2>$null
$share2 = Get-SmbShare -Name "TelAviv-Share" 2>$null
$share3 = Get-SmbShare -Name "Haifa-Share" 2>$null
$lines += Line "SMB Shares exist (Jerusalem/TelAviv/Haifa)" (($share1 -and $share2 -and $share3) -ne $null)

$homes = Get-SmbShare -Name "Homes$" 2>$null
$lines += Line "SMB Share exists: Homes$" ($homes -ne $null)

# 10) Backup + tasks
$wbs = Get-WindowsFeature Windows-Server-Backup
$lines += Line "Windows Server Backup installed" ($wbs.Installed -eq $true)

$bk = Test-Path $backupShare
$lines += Line "Backup target reachable: \\DC2\Backups$" $bk

$task1 = (schtasks /Query /TN "DailyBackup_1800" 2>$null) -match "DailyBackup_1800"
$task2 = (schtasks /Query /TN "ShadowCopy_0800" 2>$null) -match "ShadowCopy_0800"
$task3 = (schtasks /Query /TN "ShadowCopy_1700" 2>$null) -match "ShadowCopy_1700"
$lines += Line "Scheduled tasks exist (Backup+ShadowCopy)" ($task1 -and $task2 -and $task3)

# 11) Export CSVs (optional)
$lines += Line "Exports exist: C:\Temp\DC_Services.csv" (Test-Path "C:\Temp\DC_Services.csv")
$lines += Line "Exports exist: C:\Temp\AD_Users.csv"    (Test-Path "C:\Temp\AD_Users.csv")

# Print
"`n================ LABKERNEL - STATUS SUMMARY ================`n"
$lines | ForEach-Object { $_ }
"`n============================================================`n"

```