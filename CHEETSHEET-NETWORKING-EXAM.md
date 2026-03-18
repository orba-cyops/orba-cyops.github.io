תיעוד מבחן - 25.12.2025 - Networking
# 🟩מבנה
![[Screenshot 2025-12-24 at 21.17.59.png]]
# 🟩 Project Summary — What Was Implemented

### ✅ Router-on-a-Stick

ניתוב בין רשתות VLAN באמצעות תתי-ממשקים בראוטר

### ✅ VLAN Segmentation (3 VLAN per site)

הפרדה לוגית של רשתות בכל אתר לצורך ארגון ואבטחה

### ✅ ISP Network on R4 (/24)

רשת ייעודית המדמה ספק אינטרנט עם שער גישה מרכזי

### ✅ Dynamic Routing — OSPF

ניתוב דינמי בין הראוטרים לצורך קישוריות מלאה ויציבה

### ✅ NAT (PAT Overload on R4)

תרגום כתובות מאפשר גישה מהרשתות הפנימיות אל רשת חיצונית

### ✅ DHCP with Helper-Address

שרת כתובות מרכזי עם הפניות לכל רשת משנה

### ✅ STP Root per VLAN

בחירת מתג ראשי לכל רשת למניעת לולאות בשכבה 2

### ✅ Port Security (Sticky + Limit 1)

הקשחת חיבורי קצה באמצעות הגבלת כתובות MAC

### ✅ Static Routes Support

נתיבים סטטיים משלימים לקישוריות ורציפות תקשורת

### ✅ ARP & Connectivity Testing

בדיקות תקינות ברמת שכבה 2 ושכבה 3 לאימות תפקוד הרשת

### ✅ Consistent Network Topology

מבנה רשת עקבי הכולל קישורי Trunk וקישורי WAN בין האתרים

---
# 🟩 מלל להצגת הפרויקט (בשפה מקצועית וברורה)

בפרויקט הזה תכננתי והקמתי תשתית רשת מבוזרת הכוללת מספר אתרים (R1-R3) המחוברים דרך קישורי WAN, כאשר לכל אתר מוגדרים שלושה VLAN-ים נפרדים לצורך סגמנטציה והפרדה לוגית בין סוגי משתמשים ורשתות.

בכל אחד מהראוטרים יישמתי מודל **Router-on-a-Stick**, שבו הניתוב בין ה-VLAN-ים מתבצע באמצעות תתי-ממשקים על גבי ממשק פיזי אחד, תוך שימוש ב-dot1Q Trunk מול הסוויצ’ים.
עבור חלוקת הכתובות ברשת, הגדרתי **תכנון IP מפורט** הכולל Subnetting מדויק, רשתות Management ורשתות WAN בפורמט ‎/30.

לצורך ניהול הכתובות בצד התחנות, הקמתי **שרת DHCP מרכזי** והגדרתי **DHCP Relay (ip-helper)** בכל VLAN, כך שהתחנות מקבלות כתובת באופן אוטומטי גם כשהשרת לא נמצא באותו סגמנט. בנוסף, הוגדרו נתיבי ברירת מחדל ו-Static Routes משלימות, במקביל לפרוטוקול **OSPF** שמספק ניתוב דינמי וקישוריות מלאה בין כלל האתרים.

בצד יציאה לאינטרנט, יישמתי ב-R4 מנגנון **NAT מסוג PAT Overload**, המאפשר לכל התחנות ברשת הפנימית לצאת דרך כתובת ציבורית אחת. ברמת שכבה 2 יושמו מנגנוני יציבות והקשחה — **Spanning-Tree Root-Per-VLAN** למניעת לולאות ברשת, וכן **Port-Security** להגבלת מספר תחנות ואכיפת Sticky-MAC בקצוות.

לבסוף ביצעתי סדרת בדיקות תקינות: בדיקות **ARP, Ping, DHCP Bindings, OSPF Neighbors ו-NAT Translations**, כדי לוודא שכל רכיבי הרשת פועלים בצורה יציבה, עקבית ותואמת לארכיטקטורה שהוגדרה בתרשים הטופולוגיה.

---

## 🟢 משפט סיכום קצר לפרזנטציה

> “בפרויקט יצרתי רשת ארגונית מלאה עם סגמנטציה, ניתוב דינמי, NAT, DHCP מרכזי והקשחות רשת — תוך תכנון Top-Down והבטחת קישוריות ויציבות מקצה לקצה.”

--- 

# 🟩 1) מלא ומדויק - VLAN & IP PLAN 

## R1
```
VLAN 10
/27
192.168.1.0 - 31
sbnm 224
netid 0
f id 1
dg 30
bc 31
```

```
VLAN 20
/27
192.168.1.32 - 63
sbnm 224
netid 32
f id 33
dg 62
bc 63

```

```
VLAN 30
/28
192.168.1.64 - 79
sbnm 240
netid 64
f id 65
dg 78
bc 79
```
## R2

```
VLAN 40
/25
192.168.2.0 - 127
sbnm 128
netid 0
f id 1
dg 126
bc 127
```

```
VLAN 50
/27
192.168.2.128 - 159
sbnm 224
netid 128
f id 129
dg 158
bc 159
```

```
VLAN 60
/28
192.168.2.160 - 175
sbnm 240
netid 160
f id 161
dg 174
bc 175
```
## R3

```
VLAN 70
/27
192.168.3.0 - 31
sbnm 224
netid 0
f id 1
dg 30
bc 31

```

```
VLAN 80
/28
192.168.3.32 - 47
sbnm 240
netid 32
f id 33
dg 46
bc 47

```

```
VLAN 90
/29
192.168.3.48 - 55
sbnm 248
netid 48
f id 49
dg 54
bc 55

```
## R4 - ISP 

```
/24
192.168.10.0-255 
sb 0
netid 0
f id 1
dg 254
bc 255
```
## R5 - DHCP 

```
/24
192.168.100.0-255 
sb 0
netid 0
f id 1
dg 254
bc 255
```

## R6 - WIFI

```
/24
192.168.200.0-255 
sb 0
netid 0
f id 1
dg 254
bc 255
```
## קישורים בין ראוטרים- WAN :

| LINK  | NET       | MASK |
| ----- | --------- | ---- |
| R1-R2 | 10.0.0.0  | /30  |
| R2-R3 | 10.0.0.4  | /30  |
| R3-R1 | 10.0.0.8  | /30  |
| R3-R4 | 10.0.0.12 | /30  |
| R3-R5 | 10.0.0.16 | /30  |
| R3-R6 | 10.0.0.20 | /30  |

---

# 🟩 2) קונפיגורציית ראוטרים

## 🎛️ R1 (FULL)
```
conf t 
hostname R1  
interface g8/0  
no shut  

interface g8/0.10
 encapsulation dot1q 10
 ip address 192.168.1.30 255.255.255.224
 ip helper-address 192.168.100.1

interface g8/0.20
 encapsulation dot1q 20
 ip address 192.168.1.62 255.255.255.224
 ip helper-address 192.168.100.1

interface g8/0.30
 encapsulation dot1q 30
 ip address 192.168.1.78 255.255.255.240
 ip helper-address 192.168.100.1

interface g9/0  
ip address 10.0.0.1 255.255.255.252  
no shut  

interface g7/0  
ip address 10.0.0.10 255.255.255.252  
no shut  
ex

router ospf 1  
router-id 1.1.1.1  
network 192.168.1.0 0.0.0.255 area 0  
network 10.0.0.0 0.0.0.3 area 0
network 10.0.0.8 0.0.0.3 area 0
ex

en
conf t
ip route 0.0.0.0 0.0.0.0 10.0.0.2
ip route 0.0.0.0 0.0.0.0 10.0.0.9
ip route 198.168.2.0 255.255.255.0 10.0.0.3
ip route 198.168.3.0 255.255.255.0 10.0.0.9

```

```
  ! DHCP - NON SERVER OPTION 
  
ip dhcp excluded-address 192.168.1.1 192.168.1.20
ip dhcp excluded-address 192.168.1.126 192.168.1.126
ip dhcp excluded-address 192.168.1.190 192.168.1.190
ip dhcp excluded-address 192.168.1.206 192.168.1.206

ip dhcp pool VLAN10    
network 192.168.1.0 255.255.255.128  
default-router 192.168.1.126 

ip dhcp pool VLAN20  
network 192.168.1.128 255.255.255.192  
default-router 192.168.1.190 

ip dhcp pool VLAN30  
network 192.168.1.192 255.255.255.240  
default-router 192.168.1.206 

end
wr
```

## 🎛️ R2 (FULL)

```
conf t 
hostname R2  

interface g4/0  
no shut  

interface g4/0.40
 encapsulation dot1q 40
 ip address 192.168.2.126 255.255.255.128
 ip helper-address 192.168.100.1

interface g4/0.50
 encapsulation dot1q 50
	 ip address 192.168.2.158 255.255.255.224
 ip helper-address 192.168.100.1

interface g4/0.60
 encapsulation dot1q 60
 ip address 192.168.2.174 255.255.255.240
 ip helper-address 192.168.100.1
 
interface g8/0  
ip address 10.0.0.5 255.255.255.252  
no shut  

interface g9/0  
ip address 10.0.0.2 255.255.255.252  
no shut  
ex

router ospf 1  
router-id 2.2.2.2  
network 192.168.2.0 0.0.0.255 area 0  
network 10.0.0.0 0.0.0.3 area 0
network 10.0.0.4 0.0.0.3 area 0
ex

en
conf t
ip route 198.168.1.0 255.255.255.0 10.0.0.0
ip route 198.168.3.0 255.255.255.0 10.0.0.7
ip route 0.0.0.0 0.0.0.0 10.0.0.6
ip route 0.0.0.0 0.0.0.0 10.0.0.1

```

```
  ! DHCP - NON SERVER OPTION 
  
ip dhcp excluded-address 192.168.2.1 192.168.2.20  
ip dhcp excluded-address 192.168.2.126 192.168.2.126
ip dhcp excluded-address 192.168.2.190 192.168.2.190
ip dhcp excluded-address 192.168.2.206 192.168.2.206
  
ip dhcp pool VLAN40  
network 192.168.2.0 255.255.255.128  
default-router 192.168.2.126 

ip dhcp pool VLAN50  
network 192.168.2.128 255.255.255.192  
default-router 192.168.2.190 

ip dhcp pool VLAN60  
network 192.168.2.192 255.255.255.240  
default-router 192.168.2.206 

end
wr
```

## 🎛️ R3 (FULL)

```
conf t 
hostname R3  
interface g9/0  
no shut  

interface g9/0.70
 encapsulation dot1q 70
 ip address 192.168.3.30 255.255.255.224
 ip helper-address 192.168.100.1

interface g9/0.80
 encapsulation dot1q 80
 ip address 192.168.3.46 255.255.255.240
ip helper-address 192.168.100.1

interface g9/0.90
 encapsulation dot1q 90
 ip address 192.168.3.54 255.255.255.248
 ip helper-address 192.168.100.1
 
interface g7/0  
ip address 10.0.0.9 255.255.255.252  
no shut  

interface g8/0  
ip address 10.0.0.6 255.255.255.252  
no shut  

interface g6/0
ip address 10.0.0.13 255.255.255.252  
no shut

interface g5/0 
ip address 10.0.0.17 255.255.255.252  
no shut  

interface g4/0 
ip address 10.0.0.21 255.255.255.252  
no shut  
ex

router ospf 1  
router-id 3.3.3.3  
network 192.168.3.0 0.0.0.255 area 0  
network 10.0.0.8 0.0.0.3 area 0  
network 10.0.0.12 0.0.0.3 area 0  
network 10.0.0.4 0.0.0.3 area 0  
network 10.0.0.20 0.0.0.3 area 0
ex

en
conf t
ip route 0.0.0.0 0.0.0.0 10.0.0.18
ip route 0.0.0.0 0.0.0.0 10.0.0.22
ip route 0.0.0.0 0.0.0.0 10.0.0.10
ip route 0.0.0.0 0.0.0.0 10.0.0.5
ip route 198.168.10.0 255.255.255.0 10.0.0.14
ip route 198.168.100.0 255.255.255.0 10.0.0.18
ip route 198.168.1.0 255.255.255.0 10.0.0.10
ip route 198.168.2.0 255.255.255.0 10.0.0.5

```

```
  ! DHCP - NON SERVER OPTION 

ip dhcp excluded-address 192.168.3.1 192.168.3.20  
ip dhcp excluded-address 192.168.3.126 192.168.3.126
ip dhcp excluded-address 192.168.3.190 192.168.3.190
ip dhcp excluded-address 192.168.3.206 192.168.3.206

ip dhcp pool VLAN70  
network 192.168.3.0 255.255.255.128  
default-router 192.168.3.126 

ip dhcp pool VLAN80  
network 192.168.3.128 255.255.255.192  
default-router 192.168.3.190 

ip dhcp pool VLAN90  
network 192.168.3.192 255.255.255.240  
default-router 192.168.3.206 

end
wr
```

## 🎛️ R4 - ISP

```
conf t 
hostname R4  

interface g6/0
ip address 10.0.0.14 255.255.255.252  
no shut  
ip nat outside 
 
interface g4/0  
ip address 192.168.10.254 255.255.255.0  
no shut  
ip nat inside 
ex   

ip route 0.0.0.0 0.0.0.0 10.0.0.13
 

router ospf 1
router-id 10.10.10.10
network 10.0.0.14 0.0.0.3 area 0
network 192.168.10.0 0.0.0.255 area 0
ex


  
access-list 1 permit 192.168.1.0 0.0.0.255 
access-list 1 permit 192.168.2.0 0.0.0.255 
access-list 1 permit 192.168.3.0 0.0.0.255 
ip nat inside source list 1 interface gigabitEthernet4/0 overload


ip route 0.0.0.0 0.0.0.0 10.0.0.13

end
wr
```

---
## 🎛️ R5 - SERVER
```
conf t 
hostname R-SERVER 

int g5/0
ip address 10.0.0.18 255.255.255.252
no sh 

int g4/0
ip address 192.168.100.254 255.255.255.0
 no sh
 
ip route 0.0.0.0 0.0.0.0 10.0.0.17
 
router ospf 1
router-id 100.100.100.100
network 10.0.0.16 0.0.0.3 area 0
network 192.168.100.0 0.0.0.255 area 0
ex

```

ip config
![[Screenshot 2025-12-23 at 19.29.09.png]]
![[Screenshot 2025-12-24 at 12.04.12.png]]
להוסיף את ה מחשב של הwifi 192.168.200.1
## 🎛️ R6 - WIFI
```
conf t 
hostname R-SERVER 

int g4/0
ip address 10.0.0.22 255.255.255.252
no sh 

int g5/0
ip address 192.168.200.254 255.255.255.0
 no sh
 
 int g5/0.200
 ip helper address 192.168.100.1 
 ex
 
 ip route 0.0.0.0 0.0.0.0 10.0.0.21
 
 
 router ospf 1
router-id 200.200.200.200
network 10.0.0.20 0.0.0.3 area 0
network 192.168.100.0 0.0.0.255 area 0
ex

```
![[Screenshot 2025-12-24 at 10.47.57.png]]
# 🟩 3) קונפיגורציית סוויצ’ים

## SW-1
### SW-1-1
```
en
conf t 
hostname SW-1-1

interface range g9/1,g7/1
switchport mode trunk
switchport trunk allowed vlan 10,20,30

interface range g8/1
switchport mode trunk
switchport trunk allowed vlan 10,20,30

interface fa5/1  
switchport mode access  
switchport access vlan 10  
spanning-tree portfast 
ex
spanning-tree vlan 10 root primary 
vlan 20
ex
vlan 30
ex

end
wr
```

```PORT-SEC-OPTIONSAL 
en
conf t

int f5/1
switchport port-security  
switchport port-security maximum 1  
switchport port-security mac-address sticky  
switchport port-security violation shutdown
end
wr
```

### SW-1-2
```
en
conf t 
hostname SW-1-2

interface range g8/1
switchport mode trunk
switchport trunk allowed vlan 10,20,30

interface fa5/1  
switchport mode access  
switchport access vlan 20  
spanning-tree portfast 
spanning-tree vlan 20 root primary 
vlan 10
ex
vlan 30
ex

end
wr
```

```PORT-SEC-OPTIONSAL 
en
conf t

int f5/1
switchport port-security  
switchport port-security maximum 1  
switchport port-security mac-address sticky  
switchport port-security violation shutdown
end
wr
```

### SW-1-3
```
en
conf t 
hostname SW-1-3

interface range g8/1,g9/1
switchport mode trunk
switchport trunk allowed vlan 10,20,30

interface fa5/1  
switchport mode access  
switchport access vlan 30  
spanning-tree portfast 
ex
spanning-tree vlan 30 root primary 
vlan 10
ex
vlan 20
ex

end
wr
```

```PORT-SEC-OPTIONSAL 
en
conf t

int f5/1
switchport port-security  
switchport port-security maximum 1  
switchport port-security mac-address sticky  
switchport port-security violation shutdown
end
wr
```

---

## SW-2
### SW-2-1
```
en
conf t 
hostname SW-2-1

interface range g8/1,g7/1
switchport mode trunk
switchport trunk allowed vlan 40,50,60

interface range g6/1
switchport mode trunk
switchport trunk allowed vlan 40,50,60

interface fa5/1  
switchport mode access  
switchport access vlan 40  
spanning-tree portfast 
ex
spanning-tree vlan 40 root primary 
vlan 50
ex
vlan 60
ex

end
wr
```

```PORT-SEC-OPTIONSAL
en
conf t

int f5/1
switchport port-security  
switchport port-security maximum 1  
switchport port-security mac-address sticky  
switchport port-security violation shutdown
end
wr
```

### SW-2-2
```
en
conf t 
hostname SW-2-2

interface range g6/1
switchport mode trunk
switchport trunk allowed vlan 40,50,60

interface fa5/1  
switchport mode access  
switchport access vlan 50  
spanning-tree portfast 
spanning-tree vlan 50 root primary 
vlan 40
ex
vlan 60
ex

end
wr
```

```PORT-SEC-OPTIONSAL
en
conf t

int f5/1
switchport port-security  
switchport port-security maximum 1  
switchport port-security mac-address sticky  
switchport port-security violation shutdown
end
wr
```

### SW-2-3
```
en
conf t 
hostname SW-2-3

interface range g7/1,g6/1
switchport mode trunk
switchport trunk allowed vlan 40,50,60

interface fa5/1  
switchport mode access  
switchport access vlan 60  
spanning-tree portfast 
ex
spanning-tree vlan 60 root primary 
vlan 40
ex
vlan 50
ex

end
wr
```

```PORT-SEC-OPTIONSAL
en
conf t

int f5/1
switchport port-security  
switchport port-security maximum 1  
switchport port-security mac-address sticky  
switchport port-security violation shutdown
end
wr
```

## SW-3
### SW-3-1
```
en
conf t 
hostname SW-3-1

interface range g8/1,g6/1
switchport mode trunk
switchport trunk allowed vlan 70,80,90

interface range g9/1
switchport mode trunk
switchport trunk allowed vlan 70,80,90

interface fa5/1  
switchport mode access  
switchport access vlan 70  
spanning-tree portfast 
ex
spanning-tree vlan 70 root primary 
vlan 80
ex
vlan 90
ex

end
wr
```

```PORT-SEC-OPTIONSAL
en
conf t

int f5/1
switchport port-security  
switchport port-security maximum 1  
switchport port-security mac-address sticky  
switchport port-security violation shutdown
end
wr
```

### SW-3-2
```
en
conf t 
hostname SW-3-2

interface range g7/1
switchport mode trunk
switchport trunk allowed vlan 70,80,90

interface fa5/1  
switchport mode access  
switchport access vlan 80  
spanning-tree portfast 
ex
spanning-tree vlan 80 root primary 
vlan 70
ex
vlan 90
ex

end
wr
```

```PORT-SEC-OPTIONSAL
en
conf t

int f5/1
switchport port-security  
switchport port-security maximum 1  
switchport port-security mac-address sticky  
switchport port-security violation shutdown
end
wr
```

### SW-1-3
```
en
conf t 
hostname SW-3-3

interface range g7/1,g6/1
switchport mode trunk
switchport trunk allowed vlan 70,80,90

interface fa5/1  
switchport mode access  
switchport access vlan 90  
spanning-tree portfast 
spanning-tree vlan 90 root primary 
vlan 70
ex
vlan 80
ex

end
wr
```

```PORT-SEC-OPTIONSAL
en
conf t

int f5/1
switchport port-security  
switchport port-security maximum 1  
switchport port-security mac-address sticky  
switchport port-security violation shutdown
end
wr
```
# 🟩  בדיקות (מהירות)

```
show ip int brief 
show ip route 
show ip ospf neighbor 
show ip dhcp binding 
show ip nat translations 
show spanning-tree 
show port-security
```

---

# 🟩 בקירת ARP

```
show ip arp 
ping gateway 
ping other 
VLAN ping DNS server
```

---

# 🟩 מה קיבלת?

✔ **Router-on-Stick** — ניתוב בין VLANים דרך תתי־ממשקים  
✔ **IP Design מדויק** — חלוקת Subnets, Gateways ו-WAN /30  
✔ **DHCP Relay + Pools** — הפניות Helper ושרתי כתובות לפי VLAN  
✔ **OSPF Multi-Area Core** — פרסום רשתות ודינמיות בין ראוטרים  
✔ **Static Routes משלימות** — נתיבי ברירת מחדל וקישוריות קצה  
✔ **NAT ב-R4 (ISP)** — גישה ל-WAN באמצעות PAT Overload  
✔ **STP Root Per-VLAN** — יציבות טופולוגיה ומניעת Loops  
✔ **Port-Security** — הגבלת מכשירים ו-Sticky MAC להגנה בשוליים  
✔ **Trunk Topology עקבית** — Trunks בין סוויצ’ים לכל קבוצת VLAN  
✔ **(Server / WiFi / ISP)**  בידוד לוגי וסגמנטציה לרשתות יעודיות 

```Role-for-Route's
conf t
banner motd #
************************************************************
*  🤖 Welcome to the Lab — Also known as:                  *
*     "Fuck It… Tracer!" Edition 😎🔥                      *
*                                                          *
*  Special thanks to Moshe — the mentor who taught us      *
*  that when the topology breaks… we don't panic —         *
*  we just say:                                            *
*         >> fuck it tracer, let's troubleshoot 💻⚡        *
*                                                          *
*  Built with: VLANs, OSPF vibes, coffee injections ☕,    *
*  and at least 3 moments of “why is this not working?” 😂 *
*                                                          *
*  Security mindset? ✔  Persistence? ✔                     *
*  Turning chaos into configs? ✔🎯                        *
*                                                          *
*  Dedicated to the teacher who made learning fun,         *
*  breaking networks educational — and fixing them heroic. *
*                                                          *
*  — Student Project | Humor • Learning • Resilience 💪    *
************************************************************
#
end
wr
```


``` Roles-for-switch's
conf t
banner motd #
************************************************************
*  WELCOME TO THE CHAOS SWITCH - LAYER 2 MADNESS           *
*  This is not a network                                   *
*  This is a battlefield of VLANs, MAC tables and STP 😱   *
*  RULE 1                                                 *
*  If something breaks                                     *
*  don't panic                                             *
*  just say:                                               *
*      "FUCK IT... TRACER!" 💥😎                            *
*  Broadcast storms may cry                                *
*  trunks may collapse                                     *
*  and port-security will still bully someone 😂           *
*  Here we debug                                           *
*  we suffer                                               *
*  we learn                                                *
*  we conquer 💪                                           *
*                                                          *
*  — SWITCH LAB CREW | Chaos • Coffee • Config            *
************************************************************
#
end
wr

```

