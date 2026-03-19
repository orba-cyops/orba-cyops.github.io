# Linux Commands & Shell Basics — Flashcard Cheatsheet

> All questions translated to English with examples added for clarity.
> Format: Question Answer Memory Trick Explanation + Example

---

# Section 1: Linux Commands & Shell Basics

---

### Q1: Which of the following are typical services offered by public cloud providers? (Choose three)

**A:**
- Infrastructure as a Service (IaaS)
- Platform as a Service (PaaS)
- Software as a Service (SaaS)

**Memory Trick:** _"The three aaS of cloud: I, P, S."_

**Explanation:** Cloud providers package their resources in three main layers:
- **IaaS** — Virtual machines, networking, and storage. You manage the OS and up. _(Example: AWS EC2, Azure VM)_
- **PaaS** — A platform to run your application without managing the OS. _(Example: Google App Engine, Heroku)_
- **SaaS** — A ready-to-use application. You just log in and use it. _(Example: Gmail, Salesforce, Office 365)_

On the exam — any answer that sounds like one of these three is correct.

---

### Q2: What information can be displayed by `top`?

**A:** Running processes, ordered by CPU or RAM consumption.

**Memory Trick:** _"top shows who is on top of CPU/RAM."_

**Explanation:** `top` shows a live, auto-refreshing list of processes ranked by resource consumption. It's the go-to tool to identify a process that is overloading the system.

**Example output:**
```
PID USER %CPU %MEM COMMAND
1234 root 89.0 2.1 python3
5678 alice 3.2 0.4 bash
```
Press `q` to exit.

---

### Q3: A directory contains `texts 1.txt`, `texts 2.txt`, `texts 3.csv`. Which command copies the two `.txt` files to `/tmp/`?

**A:** `cp *.txt /tmp/`

**Memory Trick:** _"Star + .txt = all text files."_

**Explanation:** The shell glob `*.txt` expands to all filenames ending in `.txt`, even those with spaces. `texts 3.csv` is excluded because it has a different extension.

**Example:**
```bash
ls
# texts 1.txt texts 2.txt texts 3.csv

cp *.txt /tmp/

ls /tmp/
# texts 1.txt texts 2.txt
```

---

### Q4: Which operator in a regular expression matches the preceding character either zero or one time?

**A:** `?`

**Memory Trick:** _"? means maybe — zero or one."_

**Explanation:** In regex, `?` makes the preceding character optional — it matches if the character appears once or not at all. Don't confuse it with `*` (zero or more) or `+` (one or more).

**Example:**
```
colou?r matches "color" and "colour"
https? matches "http" and "https"
```

---

### Q5: The current directory contains `-rwxr-xr-x ... test.sh`. How can this script be executed? (Choose two)

**A:** `./test.sh` and `bash test.sh`

**Memory Trick:** _"Execute script: dot-slash or Bash."_

**Explanation:** Since the execute (`x`) bit is already set, you can run the script two ways:
1. **Direct path:** `./test.sh` — runs it using the shebang interpreter
2. **Explicitly via shell:** `bash test.sh` — passes the file to bash directly

**Example:**
```bash
./test.sh # Works — x bit is set
bash test.sh # Works — passes to bash regardless of permissions
sh test.sh # Also works — similar to bash
```

---

### Q6: Members of a team know Red Hat Enterprise Linux and want a free similar server. Which distribution allows them to reuse most of their knowledge?

**A:** CentOS

**Memory Trick:** _"CentOS = Community RHEL."_

**Explanation:** CentOS is built almost 1:1 from RHEL source code without the paid support. Commands, package manager (`rpm`, `yum`/`dnf`), file structure — nearly identical to Red Hat.

**Example:** A sysadmin who knows `yum install httpd` on RHEL can run the exact same command on CentOS.

---

### Q7: What are the differences between hard disk drives (HDD) and solid state disks (SSD)? (Choose two)

**A:**
- Hard disks have a motor and moving parts; solid state disks do not.
- Solid state disks provide faster access to stored data.

**Memory Trick:** _"Spinning = HDD, Solid = SSD. SSD is faster."_

**Explanation:**
| Feature | HDD | SSD |
|---------|-----|-----|
| Technology | Spinning magnetic platters + read head | Flash memory, no moving parts |
| Speed | ~100 MB/s | ~500 MB/s+ (NVMe: 3000+ MB/s) |
| Noise | Audible | Silent |
| Failure mode | Mechanical wear | Flash cell degradation |

---

### Q8: What key should you press to exit the `less` command?

**A:** `q`

**Memory Trick:** _"q for quit."_

**Explanation:** Both `less` and `man` use `q` to quit. This is one of the most commonly tested "pager" interactions on the exam.

**Other useful `less` keys:**
- `Space` — scroll down one page
- `b` — scroll back one page
- `/search` — search within the file
- `G` — jump to end of file

---

### Q9: Which command displays only file names without additional information?

**A:** `ls -a`

**Memory Trick:** _"ls shows names; -a also shows hidden ones."_

**Explanation:** `ls -a` lists filenames including hidden files (those starting with `.`), without extra details like permissions or size.

**Example:**
```bash
ls -a
# . .. .bashrc myfile.txt documents/
```
Compare with `ls -l` which adds permissions, ownership, size, and date to each entry.

---

### Q10: What parameter of `ls` prints a recursive listing of a directory's content?

**A:** `-R`

**Memory Trick:** _"R for Recursive."_

**Explanation:** `ls -R` lists the current directory and descends into every sub-directory, showing all contents.

**Example:**
```bash
ls -R /home/user/
# /home/user/:
# docs/ scripts/

# /home/user/docs/:
# report.pdf notes.txt

# /home/user/scripts/:
# backup.sh
```

---

### Q11: Which files are the source of the information shown by the `id` command? (Choose two)

Output: `uid=1000(bob) gid=1000(bob) groups=1000(bob),10(wheel),...`

**A:** `/etc/passwd`, `/etc/group`

**Memory Trick:** _"passwd = users, group = groups."_

**Explanation:**
- `/etc/passwd` maps UID numbers to usernames (e.g., `1000 bob`)
- `/etc/group` maps GID numbers to group names (e.g., `10 wheel`)

**Example:**
```bash
grep bob /etc/passwd
# bob:x:1000:1000::/home/bob:/bin/bash

grep wheel /etc/group
# wheel:x:10:bob,alice
```

---

### Q12: Which command extracts the contents of `file1.tar.gz`?

**A:** `tar -xzf file1.tar.gz`

**Memory Trick:** _"x = extract, z = gzip, f = file."_

**Explanation:** For `.tar.gz` or `.tgz` archives, always use `tar -xzf`. The letter order is important — the exam may test this specifically.

**Flags breakdown:**
| Flag | Meaning |
|------|---------|
| `-x` | Extract |
| `-z` | Filter through gzip (decompress) |
| `-f` | Followed by the filename |
| `-v` | Verbose (optional — shows progress) |

**Example:**
```bash
tar -xzf project.tar.gz # Extract to current directory
tar -xzf project.tar.gz -C /tmp # Extract to /tmp/
```

---

### Q13: Directory has `a.txt`, `b.txt`, `c.csv`. What is the output of this loop?

```bash
for file in *.txt
do
 echo $file
done
```

**A:**
```
a.txt
b.txt
```

**Memory Trick:** _"\*.txt expands to matching files only."_

**Explanation:** The shell expands `*.txt` into the actual filenames that match. `c.csv` doesn't match `.txt`, so it's skipped. The output is exactly the two filenames, one per line.

---

### Q14: Script `script.sh` prints `$MYVAR`, but running `MYVAR=value; ./script.sh` prints empty. How should `MYVAR` be set?

**A:** `export MYVAR=value`

**Memory Trick:** _"Export to pass variable to child processes."_

**Explanation:** Regular variables only exist in the current shell. Scripts run as separate (child) processes and don't inherit local variables. `export` promotes a variable to an **environment variable** so child processes can see it.

**Example:**
```bash
# Without export — script sees nothing:
MYVAR=hello
./script.sh # prints empty

# With export — script sees the value:
export MYVAR=hello
./script.sh # prints "hello"
```

---

### Q15: Which example shows the general structure of a `for` loop in a shell script?

**A:**
```bash
for VAR in list
do
 commands
done
```

**Memory Trick:** _"for…in…do…done."_

**Explanation:** Every bash `for` loop follows this structure. If an answer on the exam looks like this pattern, it's correct.

**Practical example:**
```bash
for color in red green blue
do
 echo "Color: $color"
done
# Output:
# Color: red
# Color: green
# Color: blue
```

---

### Q16: Which command can be used to resolve a DNS name to an IP address?

**A:** `host`

**Memory Trick:** _"host looks up hosts."_

**Explanation:** `host example.com` queries the DNS server for an A/AAAA record and returns the IP. The exam may try to confuse you with `ping`, but the official DNS lookup tool is `host`.

**Example:**
```bash
host google.com
# google.com has address 142.250.185.46
# google.com has IPv6 address 2607:f8b0:4004:c1b::64

host 8.8.8.8
# 8.8.8.8.in-addr.arpa domain name pointer dns.google.
```

---

### Q17: Which permissions are set on the `/tmp/` directory?

**A:** `rwxrwxrwt`

**Memory Trick:** _"World-writable temp ends with t."_

**Explanation:** `/tmp` is open for read/write/execute by everyone, but the **sticky bit** (`t`) means only the file's owner can delete their own files. This prevents users from deleting each other's temp files.

**Example:**
```bash
ls -ld /tmp
# drwxrwxrwt 12 root root 4096 Mar 19 10:00 /tmp/
```
The `t` at the end is the sticky bit in action.

---

### Q18: Which DNS record types hold an IP address? (Choose two)

**A:** `A`, `AAAA`

**Memory Trick:** _"A = IPv4, AAAA = IPv6 (four A's, four 4-bit groups)."_

**Explanation:**
| Record | Purpose | Example |
|--------|---------|---------|
| `A` | Maps hostname IPv4 | `example.com 93.184.216.34` |
| `AAAA` | Maps hostname IPv6 | `example.com 2606:2800::1` |
| `MX` | Mail server | (not an IP record) |
| `CNAME` | Alias | (not an IP record) |

---

### Q19: What is true about a recursive directory listing?

**A:** It includes the content of sub-directories.

**Memory Trick:** _"Recursive = goes down the tree."_

**Explanation:** A recursive listing doesn't stop at the top-level directory — it descends into every sub-directory and shows their contents too (like `ls -R` or `find`).

---

### Q20: Which of the following outputs comes from the command `free`?

**A:**
```
 total used free shared buff/cache available
Mem: 16384000 8200000 3000000 512000 5184000 7600000
Swap: 2097152 0 2097152
```

**Memory Trick:** _"free shows the memory table."_

**Explanation:** `free` summarizes RAM and swap usage: total, used, free, and available. If output starts with `total used free shared buff/cache` — it's from `free`.

```bash
free -h # Human-readable (shows GB/MB instead of bytes)
```

---

# Section 2: Users, Permissions & Filesystem

---

### Q21: Which command creates an archive file `work.tar` from the contents of `./work`?

**A:** `tar -cf work.tar ./work/`

**Memory Trick:** _"c = create, f = file."_

**Explanation:** `tar` creates an uncompressed archive when using `-c` (create) and `-f` (output file). The last argument is the source directory to include.

**Example:**
```bash
tar -cf backup.tar ./work/ # Create archive
tar -czf backup.tar.gz ./work/ # Create + gzip compress
tar -tf backup.tar # List archive contents without extracting
```

---

### Q22: Which command sets the variable USERNAME to the value `bob`?

**A:** `USERNAME=bob`

**Memory Trick:** _"No spaces around = in shell."_

**Explanation:** Shell variable assignment requires no spaces around `=`. A space before or after makes the shell interpret it as a command instead.

**Example:**
```bash
USERNAME=bob # Correct
echo $USERNAME # Outputs: bob

USERNAME = bob # ERROR: "USERNAME: command not found"
```

---

### Q23: Which Linux distributions provide long-term security updates? (Choose two)

**A:**
- Ubuntu LTS
- Red Hat Enterprise Linux (RHEL)

**Memory Trick:** _"LTS or Enterprise = long life."_

**Explanation:** Ubuntu LTS versions receive 5 years of support, RHEL provides 10+ years. Rolling-release or community distros (like Arch, Fedora) don't guarantee this.

**Example:** Ubuntu 22.04 LTS is supported until April 2027 standard, extended to 2032 with ESM.

---

### Q24: What can be found in the `/proc/` directory?

**A:** One directory per running process.

**Memory Trick:** _"proc = process mirror."_

**Explanation:** `/proc` is a virtual filesystem — it doesn't exist on disk. The kernel generates it in real-time. Each PID gets its own folder (e.g., `/proc/1234/`), containing info like memory maps, file descriptors, and command-line arguments.

**Example:**
```bash
ls /proc/
# 1 2 3 ... 1234 ... cpuinfo meminfo uptime

cat /proc/cpuinfo # CPU details
cat /proc/meminfo # RAM details
cat /proc/1/cmdline # Command that started PID 1
```

---

### Q25: What keyword is used in a shell script to begin a loop?

**A:** `for`

**Memory Trick:** _"Loops start with for."_

**Explanation:** On this exam, loops refer specifically to `for` loops (not `while`/`until`). The answer is simply the keyword `for`.

---

### Q26: You are in `/home/user/Downloads`. What directory content is shown by `ls -l ../Documents`?

**A:** The contents of `/home/user/Documents/`

**Memory Trick:** _".. = go up one level."_

**Explanation:** `..` moves one directory up from your current location. From `/home/user/Downloads`, `..` takes you to `/home/user/`. Then `/Documents` navigates into that folder.

**Example:**
```bash
pwd # /home/user/Downloads
ls -l ../Documents # Lists /home/user/Documents/
```

---

### Q27: Which command copies the contents of `/etc/`, including all sub-directories, to `/root/`?

**A:** `cp -r /etc/* /root`

**Memory Trick:** _"r = recursive."_

**Explanation:** To copy directories and their sub-directories, you must use `-r`. Using `/etc/*` copies the contents of `/etc/` (not the directory itself).

**Example:**
```bash
cp -r /etc/nginx /root/nginx-backup # Copy a single subdirectory
cp -r /etc/* /root/etc-backup/ # Copy everything in /etc/
```

---

### Q28: Which of the following outputs could come from the command `last`?

**A:** `root tty2 Wed May 17 21:11 - 21:11 (00:00)`

**Memory Trick:** _"last = login history."_

**Explanation:** `last` displays a log of who logged in, what terminal they used (TTY), what time, and how long the session lasted. If the output shows a username, a ttyX, and a timestamp — it's `last`.

**Example:**
```bash
last
# alice pts/0 192.168.1.10 Wed Mar 19 09:00 - 10:00 (01:00)
# root tty1 Tue Mar 18 22:00 - 22:05 (00:05)
```

---

### Q29: Differences between private and regular browser windows (Choose three)

**A:**
- Private windows do not store cookies persistently
- Private windows do not keep browsing history
- Private windows do not send previously stored cookies

**Memory Trick:** _"Private = no memory."_

**Explanation:** Incognito/private mode starts fresh every session — no previous cookies are sent, no new cookies are saved after close, and no history is recorded. It does NOT make you anonymous online.

---

### Q30: Running `rm Downloads` shows "Is a directory". How to remove it if it's empty? (Choose two)

**A:**
- `rmdir Downloads`
- `rm -r Downloads`

**Memory Trick:** _"rm needs -r for directories."_

**Explanation:**
- `rmdir` — removes an **empty** directory only
- `rm -r` — removes a directory and all its contents (use with caution!)

**Example:**
```bash
rmdir emptydir/ # Works only if empty
rm -r Downloads/ # Works even if it has files
rm -rf Downloads/ # Same, but suppresses confirmation prompts (dangerous!)
```

---

### Q31: What is the UID of the user root?

**A:** `0`

**Memory Trick:** _"Root starts at zero."_

**Explanation:** Root is always UID 0 on every Linux distribution. This is the superuser identifier — the kernel grants unlimited privileges to processes running as UID 0.

**Example:**
```bash
id root
# uid=0(root) gid=0(root) groups=0(root)
```

---

### Q32: What is true about the `su` command?

**A:** It runs a shell or command as another user.

**Memory Trick:** _"su = switch user."_

**Explanation:** `su` doesn't just switch to root — you can switch to any user. It opens a new shell with that user's identity.

**Example:**
```bash
su alice # Switch to alice (prompts for alice's password)
su - # Switch to root with a full login shell
su -c "id" alice # Run one command as alice, then return
```

---

### Q33: How is a new Linux instance provisioned in an IaaS cloud?

**A:** By selecting a pre-prepared Linux image provided by the cloud provider.

**Memory Trick:** _"Cloud = choose image create instance."_

**Explanation:** Cloud providers offer ready-made images (AMI on AWS, images on Azure/GCP). You pick one the platform creates a VM from it. No installation needed.

---

### Q34: What is the return value of a shell script after successful execution?

**A:** `0`

**Memory Trick:** _"Zero = success."_

**Explanation:** In shell scripting, exit code `0` always means success. Any non-zero value indicates an error.

**Example:**
```bash
ls /home
echo $? # 0 — command succeeded

ls /nonexistent
echo $? # 2 — command failed (file not found)
```

---

### Q35: What character can be used to split a long command across multiple lines?

**A:** `\`

**Memory Trick:** _"Backslash continues lines."_

**Explanation:** Placing `\` at the end of a line tells the shell the command continues on the next line.

**Example:**
```bash
tar -czf backup.tar.gz \
 --exclude='*.log' \
 --exclude='*.tmp' \
 /home/user/project/
```

---

### Q36: What is defined by a Free Software license?

**A:** Conditions for modifying and distributing the licensed software.

**Memory Trick:** _"Free = freedom, not price."_

**Explanation:** Free Software (as defined by the FSF) grants users 4 freedoms: run, study, modify, and distribute the software. It's about rights, not cost.

---

### Q37: Where is the operating system of a Raspberry Pi stored?

**A:** On a removable SD card.

**Memory Trick:** _"Pi boots from SD."_

**Explanation:** Unlike regular computers, the Raspberry Pi loads its entire OS from an SD card — no internal storage. You simply flash a Linux image to the SD card and it boots.

---

### Q38: What are the permissions after `chmod 654 file.txt`?

**A:** `-rw-r-xr--`

**Memory Trick:** _"6=rw, 5=r-x, 4=r."_

**Explanation:** Convert each octal digit to binary permission bits:

| Digit | Binary | Permissions |
|-------|--------|-------------|
| 6 | 110 | `rw-` (owner: read + write) |
| 5 | 101 | `r-x` (group: read + execute) |
| 4 | 100 | `r--` (others: read only) |

**Example:**
```bash
chmod 654 file.txt
ls -l file.txt
# -rw-r-xr-- 1 alice alice 1024 Mar 19 10:00 file.txt
```

---

### Q39: What is true about links in a Linux filesystem?

**A:** A symbolic link can point to a file on another filesystem.

**Memory Trick:** _"Soft link = flexible."_

**Explanation:**
- **Hard link** — must be on the same filesystem. Points to the same inode.
- **Symbolic (soft) link** — can point anywhere, including other filesystems or network paths. Like a Windows shortcut.

**Example:**
```bash
ln file.txt hardlink.txt # Hard link — same filesystem only
ln -s /mnt/usb/file.txt link.txt # Symlink — can cross filesystems
```

---

### Q40: Script exists but gives "Permission denied" when running `./test.sh`. What should be done?

**A:** `chmod +x test.sh`

**Memory Trick:** _"Need X to eXecute."_

**Explanation:** Even a perfectly valid script cannot be run directly without the execute (`x`) bit set on its permissions.

**Example:**
```bash
ls -l test.sh
# -rw-r--r-- 1 alice alice 120 test.sh no x bit

chmod +x test.sh

ls -l test.sh
# -rwxr-xr-x 1 alice alice 120 test.sh x bit added

./test.sh # Now works
```

---

# Section 3: Networking, DNS, Cloud & Internet

---

### Q41: Reverse DNS assigns hostnames to IP addresses. How is the name of `198.51.100.165` stored on a DNS server?

**A:** `165.100.51.198.in-addr.arpa.` (PTR record)

**Memory Trick:** _"Reverse = reverse the IP + in-addr.arpa."_

**Explanation:** For reverse DNS (IPv4), octets are reversed and `.in-addr.arpa` is appended. The result is a PTR record (not A/AAAA). You must reverse the IP completely.

**Example:**
```
IP: 198.51.100.165
Reverse: 165.100.51.198.in-addr.arpa.
```

---

### Q42: What command displays manual pages?

**A:** `man`

**Memory Trick:** _"man = manual."_

**Explanation:** `man` displays the official documentation for commands. The exam might offer `info` as an alternative, but the question asks for the manual page command specifically.

**Example:**
```bash
man ls # Manual for ls
man 5 passwd # Section 5 — file format for /etc/passwd
man -k copy # Search man pages for "copy" keyword
```

---

### Q43: Which command adds the new user `tux` and creates their home directory?

**A:** `useradd -m tux`

**Memory Trick:** _"m = make home."_

**Explanation:** `useradd -m` creates the user AND makes their home directory, copying default files from `/etc/skel`. Without `-m`, no home directory is created.

**Example:**
```bash
useradd -m tux # Create user + home directory
useradd -m -s /bin/bash tux # Also set default shell to bash
ls /home/tux # Home directory now exists
```

---

### Q44: What happens to a file outside the home directory when the owner's account is deleted? (Choose two)

**A:**
- The UID of the former owner is shown
- Ownership and permissions remain unchanged

**Memory Trick:** _"User disappears, files stay."_

**Explanation:** Deleting a user account does NOT delete their files outside their home directory. The files remain with the old UID number displayed (since the username no longer resolves). Permissions are untouched.

**Example:**
```bash
# Before deletion:
ls -l /shared/report.txt
# -rw-r--r-- 1 alice alice 512 report.txt

userdel alice

# After deletion:
ls -l /shared/report.txt
# -rw-r--r-- 1 1001 1001 512 report.txt UID shows, not name
```

---

### Q45: Which command creates the ZIP archive `poems.zip` containing all `.txt` files?

**A:** `zip poems.zip *.txt`

**Memory Trick:** _"zip archive files."_

**Explanation:** With `zip`, the archive name comes first, then the files to include. `*.txt` expands to all matching text files.

**Example:**
```bash
zip poems.zip *.txt # Zip all .txt files
zip -r archive.zip ./project/ # Recursively zip a directory
unzip poems.zip # Extract
```

---

### Q46: What is true about the `dmesg` command? (Choose two)

**A:**
- It displays the kernel's ring buffer
- Old messages may be overwritten by newer information

**Memory Trick:** _"Kernel messages roll over."_

**Explanation:** `dmesg` reads the kernel's ring buffer — a fixed-size memory buffer of kernel log messages. Since it's fixed-size, older entries are overwritten as the system runs longer.

**Example:**
```bash
dmesg | tail -20 # Show the most recent 20 kernel messages
dmesg | grep "USB" # Filter for USB-related events
dmesg | grep "error" # Find hardware errors
```

---

### Q47: Which tasks can the command `passwd` accomplish? (Choose two)

**A:**
- Change a user's password
- Lock a user account

**Memory Trick:** _"passwd = password + lock."_

**Example:**
```bash
passwd alice # Change alice's password (as root)
passwd -l alice # Lock alice's account (prepends ! to hash in /etc/shadow)
passwd -u alice # Unlock alice's account
passwd # Change your own password
```

---

### Q48: What information is stored in `/etc/passwd`? (Choose three)

**A:**
- Username
- Numerical user ID (UID)
- User's default shell

**Memory Trick:** _"passwd = user map."_

**Explanation:** `/etc/passwd` holds basic user info but NOT the password (that's in `/etc/shadow`). Each line has 7 colon-separated fields.

**Example:**
```
alice:x:1001:1001:Alice Smith:/home/alice:/bin/bash
 Default shell
 Home directory
 Comment (GECOS)
 GID
 UID
 Password placeholder (x = see /etc/shadow)
 Username
```

---

### Q49: How to change the owner of `doku.odt` to `tux`?

**A:** `chown tux doku.odt`

**Memory Trick:** _"chown = change owner."_

**Explanation:** `chown` changes ownership only. It does not change permissions or group unless you use `:group` syntax.

**Example:**
```bash
chown tux doku.odt # Change owner to tux
chown tux:staff doku.odt # Change owner to tux AND group to staff
chown :staff doku.odt # Change group only
chown -R tux /home/tux/ # Recursive ownership change
```

---

### Q50: Which program is a graphical editor for vector graphics?

**A:** Inkscape

**Memory Trick:** _"Ink = drawing shapes."_

**Explanation:** Inkscape is the main Linux tool for editing vector graphics (SVG files). It's the Linux equivalent of Adobe Illustrator. Don't confuse it with GIMP, which edits raster/pixel-based images.

---

### Q51: Which directory contains documentation for installed software packages?

**A:** `/usr/share/doc/`

**Memory Trick:** _"share/doc = documentation."_

**Explanation:** Every package installed via the system package manager creates a subdirectory in `/usr/share/doc/` containing README, license files, examples, and changelogs.

**Example:**
```bash
ls /usr/share/doc/
# bash/ openssl/ nginx/ python3/

cat /usr/share/doc/nginx/README
```

---

### Q52: Which statement is true about Free Software?

**A:** It may be modified by anyone using it.

**Memory Trick:** _"Free = freedom, not price."_

**Explanation:** Free Software guarantees the right to study, modify, and redistribute the source code under a free license (like GPL). Cost is irrelevant.

---

### Q53: What is the preferred source for installing new applications on Linux?

**A:** The distribution's package repository.

**Memory Trick:** _"Repo = safe updates."_

**Explanation:** Official repositories guarantee security patches, dependency resolution, and verified integrity. Manual installation from random sources bypasses all of these protections.

**Example:**
```bash
apt install nginx # Ubuntu/Debian — installs from official repo
dnf install nginx # RHEL/CentOS/Fedora
```

---

### Q54: What is the purpose of the PATH environment variable?

**A:** It allows execution of commands without specifying their full path.

**Memory Trick:** _"PATH = where the shell looks."_

**Explanation:** When you type a command, the shell searches each directory in PATH in order. If found, it runs it. If not found — "command not found".

**Example:**
```bash
echo $PATH
# /usr/local/bin:/usr/bin:/bin:/home/alice/scripts

# Without PATH, you'd have to type:
/usr/bin/ls

# With PATH, you just type:
ls
```

---

### Q55: Why are web browser cookies considered dangerous?

**A:** Because they support identification and tracking of users.

**Memory Trick:** _"Cookies leave crumbs — they track you."_

**Explanation:** Cookies can be used to track browsing behavior across sites, build user profiles, enable targeted ads, and in some cases, steal session tokens.

---

### Q56: Which statement concerning Linux passwords is true?

**A:** Passwords are stored only in hashed form.

**Memory Trick:** _"Never plaintext."_

**Explanation:** Linux never stores plaintext passwords. Instead, a one-way cryptographic hash is stored in `/etc/shadow`. The hash cannot be reversed — login works by hashing what you type and comparing it.

**Example:**
```bash
sudo grep alice /etc/shadow
# alice:$6$xyz...(sha-512 hash)...:19000:0:99999:7:::
```

---

### Q57: Which command sorts the output of `export-logs`?

**A:** `export-logs | sort`

**Memory Trick:** _"Pipe sort."_

**Explanation:** Use a pipe (`|`) to feed the output of one command into another. `sort` alphabetically sorts lines by default.

**Example:**
```bash
export-logs | sort # Sort alphabetically
export-logs | sort -n # Sort numerically
export-logs | sort -r # Reverse sort
export-logs | sort | uniq # Sort + remove duplicates
```

---

### Q58: Which tar options handle compression? (Choose two)

**A:** `-z` (gzip), `-j` (bzip2)

**Memory Trick:** _"z = gzip, j = bzip2."_

**Explanation:**
| Flag | Compression | Extension |
|------|-------------|-----------|
| `-z` | gzip | `.tar.gz` or `.tgz` |
| `-j` | bzip2 | `.tar.bz2` |
| `-J` | xz | `.tar.xz` |

Other flags like `-c`, `-x`, `-f` are not compression — they're create/extract/file.

---

### Q59: Which symbol in a shell prompt indicates root privileges?

**A:** `#`

**Memory Trick:** _"# = high privilege."_

**Explanation:** The shell prompt visually tells you your privilege level. Regular users see `$`, root sees `#`. Never confuse the two.

**Example:**
```bash
alice@server:~$ # Regular user
root@server:~# # Root / superuser
```

---

### Q60: Which commands display the content of `Texts 2.txt`? (Choose two)

**A:**
- `cat 'Texts 2.txt'`
- `cat Texts\ 2.txt`

**Memory Trick:** _"Quotes or escape spaces."_

**Explanation:** Filenames with spaces must be handled with either single/double quotes or by escaping the space with `\`.

**Example:**
```bash
cat 'Texts 2.txt' # Single quotes — exact literal name
cat "Texts 2.txt" # Double quotes — also works
cat Texts\ 2.txt # Backslash-escaped space
```

---

# Section 4: Hardware, Distros, Packages & System Fundamentals

---

### Q61: Which Linux distribution is derived from Red Hat Enterprise Linux?

**A:** CentOS

**Memory Trick:** _"CentOS = Community RHEL."_

**Explanation:** CentOS is built directly from RHEL source code. Commands, file structure, and package manager (`rpm`, `dnf`) are virtually identical.

---

### Q62: Which command puts the lines of `data.csv` into alphabetical order?

**A:** `sort data.csv`

**Memory Trick:** _"sort sorts."_

**Explanation:** `sort` sorts input alphabetically by default. No special flags needed for basic alphabetical ordering.

**Example:**
```bash
sort data.csv # Sort alphabetically
sort -n data.csv # Sort numerically
sort -r data.csv # Reverse order
sort -k2 data.csv # Sort by the 2nd column
```

---

### Q63: What is true about the owner of a file?

**A:** Each file is owned by exactly one user and one group.

**Memory Trick:** _"One file = one owner + one group."_

**Explanation:** Linux's ownership model is simple: every file has exactly one user owner and one group owner. ACLs (Access Control Lists) can extend this, but are beyond this exam's scope.

---

### Q64: Which device represents a hard disk partition?

**A:** `/dev/sda2`

**Memory Trick:** _"Number = partition."_

**Explanation:** Device naming in Linux:
- `/dev/sda` — the entire first disk
- `/dev/sda1` — first partition on that disk
- `/dev/sda2` — second partition on that disk

```bash
lsblk
# sda 8:0 0 500G 0 disk
# sda1 8:1 0 499G 0 part /
# sda2 8:2 0 1G 0 part [SWAP]
```

---

### Q65: Which commands show usage info for `ls`? (Choose two)

**A:**
- `man ls`
- `info ls`

**Memory Trick:** _"man/info = official help."_

**Explanation:** `man` and `info` are the two standard documentation systems in Linux. `--help` is convenient but wasn't listed as a valid option in this exam question's context.

---

### Q66: Which command shows the absolute path of your current working directory?

**A:** `pwd`

**Memory Trick:** _"Print Working Directory."_

**Example:**
```bash
cd /home/alice/projects
pwd
# /home/alice/projects
```

---

### Q67: What is a Linux distribution?

**A:** A bundle of the Linux kernel, system utilities, and additional software.

**Memory Trick:** _"Kernel + tools + apps = distro."_

**Explanation:** A distribution (distro) combines the Linux kernel with a package manager, system utilities, desktop environment (optionally), and pre-configured software — all packaged into a coherent installable system.

**Examples:** Ubuntu, Fedora, Debian, Arch Linux, CentOS.

---

### Q68: Which package management tool is used in Red Hat-based systems?

**A:** `rpm`

**Memory Trick:** _"Red Hat Package Manager."_

**Explanation:** `rpm` is the low-level package tool for RHEL/CentOS/Fedora. Higher-level tools like `yum` and `dnf` use `rpm` underneath.

**Example:**
```bash
rpm -qa # List all installed packages
rpm -qi nginx # Info about installed nginx
rpm -ivh package.rpm # Install a .rpm file
dnf install nginx # Preferred — resolves dependencies automatically
```

---

### Q69: Which statement about Linux hardware drivers is correct?

**A:** Drivers are either compiled into the Linux kernel or loaded as kernel modules.

**Memory Trick:** _"Built-in or module."_

**Explanation:** Linux doesn't use external driver packages (like Windows .dll files). Drivers are either baked into the kernel binary, or loaded on-demand as kernel modules (`.ko` files).

**Example:**
```bash
lsmod # List currently loaded kernel modules
modprobe usbhid # Load a kernel module
lsmod | grep usb # Find USB-related modules
```

---

### Q70: Which statements about `/etc/skel` are correct? (Choose two)

**A:**
- Files are copied to the new user's home directory when account is created
- Contains default configuration files used by `useradd`

**Memory Trick:** _"skel = skeleton home."_

**Explanation:** When `useradd -m` creates a new user, the contents of `/etc/skel/` are copied to their new home directory — providing default config files like `.bashrc`, `.profile`, etc.

**Example:**
```bash
ls -a /etc/skel/
# . .. .bash_logout .bashrc .profile

useradd -m newuser
ls -a /home/newuser/
# . .. .bash_logout .bashrc .profile copied from skel
```

---

### Q71: Which command adds `/new/dir/` to the PATH environment variable?

**A:** `export PATH=/new/dir:$PATH`

**Memory Trick:** _"New path goes first."_

**Explanation:** PATH is a colon-separated list of directories. Placing the new path at the beginning gives it priority when the shell looks for commands.

**Example:**
```bash
export PATH=/home/alice/scripts:$PATH
echo $PATH
# /home/alice/scripts:/usr/local/bin:/usr/bin:/bin
```

To make it permanent, add the `export` line to `~/.bashrc` or `~/.bash_profile`.

---

### Q72: Which command finds all lines in `operating-systems.txt` containing "linux", regardless of case?

**A:** `grep -i linux operating-systems.txt`

**Memory Trick:** _"i = ignore case."_

**Example:**
```bash
grep -i linux operating-systems.txt
# Matches: "linux", "Linux", "LINUX", "GNU/Linux"

grep -i -n linux operating-systems.txt # Also show line numbers
grep -i -c linux operating-systems.txt # Count matching lines
```

---

### Q73: Which programs are web servers? (Choose two)

**A:** Apache HTTPD, NGINX

**Memory Trick:** _"Apache + NGINX = the kings of the web."_

**Explanation:** Both are the dominant open-source web servers on Linux. Apache is older and more flexible with `.htaccess`; NGINX is known for high performance and efficient static file serving.

---

### Q74: Which of the following values could be a process ID on Linux?

**A:** `21398`

**Memory Trick:** _"PID = positive integer only."_

**Explanation:** PIDs are always positive integers starting from 1 (PID 1 is always `init` or `systemd`). Strings, negative numbers, or floats are never valid PIDs.

**Example:**
```bash
ps aux | head -5 # See PIDs of running processes
echo $$ # PID of the current shell
kill 21398 # Send SIGTERM to process 21398
```

---

### Q75: Which types of bus can connect hard drives to the motherboard?

**A:** SATA

**Memory Trick:** _"SATA = storage bus."_

**Explanation:** SATA (Serial ATA) is the most common interface for HDDs and SSDs. NVMe drives use PCIe instead, but that's beyond this exam's scope.

---

### Q76: Which directory must be mounted with read/write access if it resides on its own filesystem?

**A:** `/var`

**Memory Trick:** _"var = variable data needs write."_

**Explanation:** `/var` holds dynamic data: system logs (`/var/log`), mail spools (`/var/spool`), package manager caches, and more. The system constantly writes to it — if `/var` is read-only, the OS will crash or behave erratically.

---

### Q77: How do most Linux commands display their usage information?

**A:** Using the options `-h` or `--help`

**Memory Trick:** _"Help always helps."_

**Example:**
```bash
ls --help # Full help text
tar --help # List all tar options
grep -h # Short help (some tools use -h)
```

---

### Q78: Which protocol is used for automatic IP address configuration?

**A:** DHCP

**Memory Trick:** _"Dynamic Host Configuration Protocol = auto IP."_

**Explanation:** DHCP automatically assigns IP address, subnet mask, default gateway, and DNS server to clients when they connect to a network.

**Example:**
```bash
dhclient eth0 # Request an IP via DHCP on eth0
ip addr show eth0 # See the assigned IP
```

---

### Q79: Which statements are true regarding a typical shell script? (Choose two)

**A:**
- It has the executable permission bit set
- It starts with the two characters `#!`

**Memory Trick:** _"Shebang + execute bit."_

**Explanation:** A shell script needs two things to be directly executable:
1. A **shebang** (`#!`) on the first line to declare the interpreter
2. The execute (`x`) permission bit

**Example:**
```bash
#!/bin/bash
# ^ shebang — tells the OS to run this script with bash

echo "Hello World"
```

```bash
chmod +x script.sh # Add execute bit
./script.sh # Run directly
```

---

### Q80: Which command searches for the file `foo.txt` under `/home`?

**A:** `find /home -name foo.txt`

**Memory Trick:** _"find path -name file."_

**Explanation:** `find` always follows the pattern: `find <where to look> <what to look for>`. This is a common exam trap — the path comes before the criteria.

**Example:**
```bash
find /home -name foo.txt # Find exact filename
find /home -name "*.txt" # Find all .txt files
find /home -name "*.log" -mtime -7 # Find .log files modified in the last 7 days
find / -user alice # Find all files owned by alice
```

---

*End of Linux Essentials Flashcard Cheatsheet*
