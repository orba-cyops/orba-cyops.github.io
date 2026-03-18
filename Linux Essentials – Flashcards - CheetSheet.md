# **Linux Commands & Shell Basics**

### Q1: Which of the following are typical services offered by public cloud providers? (Choose three)

**A:**

- Infrastructure as a Service (IaaS)
    
- Platform as a Service (PaaS)
    
- Software as a Service (SaaS)
    

**Memory Trick (EN):** _“The three aaS of cloud: I, P, S.”_  
**Hebrew Explanation:** ספקי ענן מציעים משאבים כחבילות: IaaS (מכונות וירטואליות, רשת, אחסון), PaaS (פלטפורמה להרצת אפליקציות), SaaS (אפליקציה מוכנה כמו מייל או CRM). במבחן – כל תשובה שנשמעת כמו אחד מאלה היא הנכונה.

---

### Q2: What information can be displayed by `top`?

**A:** Running processes, ordered by CPU or RAM consumption.

**Memory Trick (EN):** _“top shows who is on top of CPU/RAM.”_  
**Hebrew Explanation:** `top` מציג רשימה חיה של תהליכים, יחד עם שימוש במשאבי CPU ו־RAM. הכי חשוב לזכור למבחן: הוא מדורג לפי צריכת משאבים, ולכן מזהים דרכו “מי אוכל את המערכת”.

---

### Q3: A directory contains `'texts 1.txt', 'texts 2.txt', 'texts 3.csv'`. Which command copies the two files ending in `.txt` to `/tmp/`?

**A:**

`cp *.txt /tmp/`

**Memory Trick (EN):** _“Star + .txt = all text files.”_  
**Hebrew Explanation:** ה־glob `*.txt` מתרחב לשמות כל הקבצים שמסתיימים ב־`.txt`, גם אם יש רווח בשם. `texts 3.csv` לא יכלל כי הסיומת שלו שונה. במבחן כל תשובה עם `cp *.txt /tmp/` היא הנכונה.

---

### Q4: Which operator in a regular expression matches the preceding character either zero or one time?

**A:** `?`

**Memory Trick (EN):** _“? means maybe – zero or one.”_  
**Hebrew Explanation:** ב־regex, סימן השאלה אומר: התו שלפני יכול להופיע פעם אחת או בכלל לא. לדוגמה, `colou?r` יתפוס גם `color` וגם `colour`. אסור להתבלבל עם `*` (אפס או יותר פעמים) או `+` (פעם אחת או יותר).

---

### Q5: The current directory contains `-rwxr-xr-x ... test.sh`. Given it is a valid shell script, how can this script be executed? (Choose two)

**A:**

`./test.sh bash test.sh`

**Memory Trick (EN):** _“Execute script: dot-slash or Bash.”_  
**Hebrew Explanation:** בגלל שיש ביט x כבר, אפשר להריץ ישירות מהנתיב (`./test.sh`) או להעביר ל־shell (כמו `bash test.sh`). במבחן תמיד לבחור שתי תשובות שמראות או `./` או הרצה דרך `bash` / `sh`.

---

### Q6: Members of a team know Red Hat Enterprise Linux and want a free similar server. Which distribution allows them to reuse most of their knowledge?

**A:** CentOS

**Memory Trick (EN):** _“CentOS = Community RHEL.”_  
**Hebrew Explanation:** CentOS (וגם היום היורשים שלה) היא הפצה מבוססת RHEL כמעט 1:1, בלי תמיכה בתשלום. פקודות, מנהל חבילות (`rpm`, `yum`/`dnf`), מבנה קבצים – הכל דומה מאוד.

---

### Q7: What are the differences between hard disk drives and solid state disks? (Choose two)

**A:**

- Hard disks have a motor and moving parts; solid state disks do not.
    
- Solid state disks provide faster access to stored data than hard disks.
    

**Memory Trick (EN):** _“Spinning = HDD, Solid = SSD, SSD is faster.”_  
**Hebrew Explanation:** HDD הוא דיסק מכני עם פלטות מסתובבות וראש קריאה, SSD מבוסס זיכרון פלאש בלי חלקים נעים. SSD מהיר יותר בגישה לנתונים, זמן תגובה ו־IOPS, אך לרוב יקר יותר ל־GB.

---

### Q8: What key should you press to exit the `less` command?

**A:** `q`

**Memory Trick (EN):** _“q for quit.”_  
**Hebrew Explanation:** גם ב־`less` וגם ב־`man`, לוחצים `q` בשביל לצאת. זה אחד הדברים שהמבחן אוהב לבדוק כטיפול ב־pager.

---

### Q9: Which command displays only file names without additional information?

**A:** `ls -a`

**Memory Trick (EN):** _“ls shows names; -a also shows hidden ones.”_  
**Hebrew Explanation:** `ls -a` מציג רק שמות קבצים (כולל מוסתרים), בלי פרטים כמו הרשאות, גודל ותאריכים. בגרסת המבחן שנתנו לך, זאת התשובה שסומנה כנכונה – חשוב לזכור לא לבחור `ls -l` שמוסיף המון מידע.

---

### Q10: What parameter of `ls` prints a recursive listing of a directory's content?

**A:** `-R`

**Memory Trick (EN):** _“R for Recursive.”_  
**Hebrew Explanation:** `ls -R` מציג את התוכן של התיקייה הנוכחית ושל כל תתי־התיקיות שלה. במבחן – כל תשובה שמזכירה `-R` היא זו שקשורה לרקורסיה.

---

### Q11: Which files are the source of the information in:

`uid=1000 (bob) gid=1000 (bob) groups=1000 (bob), 10 (wheel), ...`? (Choose two)

**A:** `/etc/passwd`, `/etc/group`

**Memory Trick (EN):** _“passwd = users, group = groups.”_  
**Hebrew Explanation:** הפקודה `id bob` למשל משתמשת ב־`/etc/passwd` למיפוי UID→שם משתמש, וב־`/etc/group` למיפוי GID→שם קבוצה. זה בדיוק מה שמופיע בשורה הזו.

---

### Q12: Which command extracts the contents of `file1.tar.gz`?

**A:**

`tar -xzf file1.tar.gz`

**Memory Trick (EN):** _“x = extract, z = gzip, f = file.”_  
**Hebrew Explanation:** בפורמטים `tar.gz` או `tgz`, תמיד השתמש ב־`tar -xzf`. שים לב לסדר האותיות – במבחן יחפשו אותך על זה.

---

### Q13: Directory has: `a.txt, b.txt, c.cav`. What is the output of:

`for file in *.txt do     echo $file done`

**A:**

`a.txt b.txt`

**Memory Trick (EN):** _“_.txt expands to matching files only.”*  
**Hebrew Explanation:** ה־shell מרחיב `*.txt` לשמות הקבצים שקיימים בפועל. אין שום `*.txt` בפלט, אלא רק שמות אמיתיים של הקבצים שהסתיימו ב־`.txt`.

---

### Q14: Script `script.sh` prints `$MYVAR`, but `MYVAR=value; ./script.sh` prints empty. How should MYVAR be set?

**A:**

`export MYVAR=value`

**Memory Trick (EN):** _“Export to pass variable to child processes.”_  
**Hebrew Explanation:** משתנים רגילים קיימים רק ב־shell הנוכחי. כדי שסקריפט שתרץ כש־child process יראה אותם, צריך `export` להפוך אותם ל־environment variables.

---

### Q15: Which example shows the general structure of a `for` loop in a shell script?

**A (concept):**

`for VAR in list do     commands done`

(Exam’s example uses `for file in *.txt; do ...; done`.)

**Memory Trick (EN):** _“for…in…do…done.”_  
**Hebrew Explanation:** כל לולאת for ב־bash בנויה כך: `for` + שם משתנה + `in` + רשימה, ואז `do` עד `done`. אם התשובה נראית בפורמט הזה – זו הנכונה.

---

### Q16: Which command can be used to resolve a DNS name to an IP address?

**A:** `host`

**Memory Trick (EN):** _“host looks up hosts.”_  
**Hebrew Explanation:** `host example.com` יבקש רשומת A/AAAA מה־DNS ויחזיר כתובת IP. במבחן לפעמים ינסו לבלבל עם `ping`, אבל התשובה הרשמית היא `host`.

---

### Q17: Which permissions are set on the `/tmp/` directory?

**A:** `rwxrwxrwt`

**Memory Trick (EN):** _“World-writable temp ends with t.”_  
**Hebrew Explanation:** `/tmp` פתוחה לקריאה/כתיבה לכל המשתמשים, אבל ה־sticky bit (`t`) אומר שרק היוצר של קובץ יכול למחוק אותו. זו הרשאת ברירת־המחדל ברוב המערכות.

---

### Q18: Which DNS record types hold an IP address? (Choose two)

**A:** `A`, `AAAA`

**Memory Trick (EN):** _“A = IPv4, AAAA = IPv6 (4 As, 4×4 bits).”_  
**Hebrew Explanation:** רשומת A מחזיקה IPv4, רשומת AAAA מחזיקה IPv6. זה בסיס לכל שאלה על DNS במבחן.

---

### Q19: What is true about a recursive directory listing?

**A:** It includes the content of sub-directories.

**Memory Trick (EN):** _“Recursive = goes down the tree.”_  
**Hebrew Explanation:** ברשימה רקורסיבית לא מסתפקים בתיקייה הנוכחית, אלא עוברים גם על כל תתי־התיקיות ומציגים את תוכנן (כמו `ls -R`).

---

### Q20: Which of the following outputs comes from the command `free`?

**A:** A line like:  
`total used free shared buff/cache available`  
Followed by `Mem: ...` rows.

**Memory Trick (EN):** _“free shows memory table.”_  
**Hebrew Explanation:** `free` מסכם את מצב זיכרון ה־ RAM וה־swap: סה״כ, בשימוש, פנוי, ועוד. בפלט שמתחיל ב־`total used free shared buff/cache` – זה `free`.

---
# **Users, Permissions & Filesystem**
## **Q21: Which command creates an archive file `work.tar` from the contents of the directory `./work`?**

**A:**

`tar -cf work.tar ./work/`

**Memory Trick (EN):** _“c = create, f = file.”_  
**Hebrew Explanation:** פקודת `tar` יוצרת ארכיון ללא דחיסה כשמשתמשים ב־`c` (create) ו־`f` (file output). הנתיב האחרון הוא המקור להכללה בארכיון. במבחן יחפשו בדיוק את הצירוף `-cf`.

---

## **Q22: Which command sets the variable USERNAME to the value bob?**

**A:**

`USERNAME=bob`

**Memory Trick (EN):** _“No spaces around = in shell.”_  
**Hebrew Explanation:** הגדרת משתנה בסביבת shell תמיד נכתבת ללא רווחים. רווח לפני/אחרי `=` יהפוך את הפקודה ללא תקינה. עדיין לא export — רק משתנה מקומי ב־shell.

---

## **Q23: Which Linux distributions provide long-term security updates? (Choose two)**

**A:**

- Ubuntu LTS
    
- Red Hat Enterprise Linux (RHEL)
    

**Memory Trick (EN):** _“LTS or Enterprise = long life.”_  
**Hebrew Explanation:** גרסאות LTS (Long Term Support) של אובונטו, ו־Enterprise של רד־האט מבטיחות שנים של עדכוני אבטחה. הפצות Rolling או קהילתיות לא מבטיחות זאת.

---

## **Q24: What can be found in the `/proc/` directory?**

**A:** One directory per running process.

**Memory Trick (EN):** _“proc = process mirror.”_  
**Hebrew Explanation:** `/proc` הוא filesystem וירטואלי המציג מידע חי על תהליכים, כשכל PID מקבל תיקייה משלו (`/proc/1234`). בנוסף, יש קבצים על מצב הקרנל.

---

## **Q25: What keyword is used in a shell script to begin a loop?**

**A:** `for`

**Memory Trick (EN):** _“Loops start with for.”_  
**Hebrew Explanation:** במבחן מדובר על לולאת for בלבד, ולא while/until. לכן התשובה היא רק `for` ללא פרמטרים.

---

## **Q26: You are in `/home/user/Downloads`. What directory content is shown by: `ls -l ../Documents`?**

**A:** `/home/user/Documents/`

**Memory Trick (EN):** _“.. = go up one level.”_  
**Hebrew Explanation:** `..` מחזיר תיקייה אחת אחורה → `/home/user/`. משם ממשיכים ל־`Documents`. הפקודה לא מציגה את השם עצמו אלא את התוכן שבתוכו.

---

## **Q27: Which command copies the contents of `/etc/`, including all sub-directories, to `/root/`?**

**A:**

`cp -r /etc/* /root`

**Memory Trick (EN):** _“r = recursive.”_  
**Hebrew Explanation:** בשביל להעתיק תיקיות ותתי־תיקיות, חייבים את האופציה `-r`. שימוש ב־`/etc/*` מבטיח העתקה של כל הקבצים בתיקייה (לא את התיקייה עצמה).

---

## **Q28: Which of the following outputs could stem from the command `last`?**

**A:**  
`root tty2 Wed May 17 21:11 - 21:11 (00:00)`

**Memory Trick (EN):** _“last = login history.”_  
**Hebrew Explanation:** `last` מציג היסטוריית התחברויות, כולל זמן, TTY, ומשך session. אם התשובה נראית כמו `ttyX`, שם משתמש וזמן — זה `last`.

---

## **Q29: Differences between private and regular browser windows (Choose three)**

**A:**

- Private windows do not store cookies persistently
    
- Private windows do not keep browsing history
    
- Private windows do not send previously stored cookies
    

**Memory Trick (EN):** _“Private = no memory.”_  
**Hebrew Explanation:** Incognito לא שומר היסטוריה, לא שומר cookies חדשים לאורך זמן, ולא שולח cookies ישנים — זה מה שמבדיל אותו ממצב רגיל.

---

## **Q30: Running `rm Downloads` shows: “Is a directory”. How to remove it if it’s empty? (Choose two)**

**A:**

- `rmdir Downloads`
    
- `rm -r Downloads`
    

**Memory Trick (EN):** _“rm needs -r for directories.”_  
**Hebrew Explanation:** `rm` ברירת מחדל מוחק קבצים, לא תיקיות. `rmdir` מתאים לתיקיות ריקות בלבד. `rm -r` מוחק תיקיות כולל תוכן.

---

## **Q31: What is the UID of the user root?**

**A:** `0`

**Memory Trick (EN):** _“Root starts at zero.”_  
**Hebrew Explanation:** Root מוגדר תמיד כ־UID 0 בכל הפצות לינוקס — זה מזהה סופר־משתמש.

---

## **Q32: What is true about the `su` command?**

**A:** It runs a shell or command as another user.

**Memory Trick (EN):** _“su = switch user.”_  
**Hebrew Explanation:** `su` לא בהכרח root — ניתן להפעיל `su username`. הוא פותח shell חדש עם הרשאות המשתמש שנבחר.

---

## **Q33: How is a new Linux instance provisioned in an IaaS cloud?**

**A:** By selecting a pre-prepared Linux image provided by the cloud provider.

**Memory Trick (EN):** _“Cloud = choose image → create instance.”_  
**Hebrew Explanation:** ספקי ענן מציעים תמונות מוכנות (AMI, ISO, template). אתה בוחר תמונה → המערכת יוצרת VM על בסיס זה.

---

## **Q34: Return value of a shell script after successful execution**

**A:** `0`

**Memory Trick (EN):** _“Zero = success.”_  
**Hebrew Explanation:** ב־shell, ערך חזרה 0 מציין הצלחה. כל ערך אחר מייצג שגיאה או כשל.

---

## **Q35: What character can be used to split a long command across multiple lines?**

**A:** `\`

**Memory Trick (EN):** _“Backslash continues lines.”_  
**Hebrew Explanation:** בסקריפטים ובפקודות ארוכות, `\` בסוף שורה אומר ל־shell שהפקודה תמשיך בשורה הבאה.

---

## **Q36: What is defined by a Free Software license?**

**A:** Conditions for modifying and distributing the licensed software.

**Memory Trick (EN):** _“Free = freedom, not price.”_  
**Hebrew Explanation:** רישיון קוד פתוח מגדיר מה מותר למשתמש: לשנות, להפיץ, ועוד. דגש על זכויות — לא על עלות.

---

## **Q37: Where is the operating system of a Raspberry Pi stored?**

**A:** On a removable SD card.

**Memory Trick (EN):** _“Pi boots from SD.”_  
**Hebrew Explanation:** בניגוד למחשבים רגילים, כל מערכת ההפעלה של Raspberry Pi נטענת מכרטיס SD — לא מכונן פנימי.

---

## **Q38: Permissions after `chmod 654 file.txt`**

**A:**  
`-rw-r-xr--`

**Memory Trick (EN):** _“6=rw, 5=r-x, 4=r.”_  
**Hebrew Explanation:** ההמרה מבוססת על בסיס 8:  
6 → rw-  
5 → r-x  
4 → r--

---

## **Q39: What is true about links in a Linux filesystem?**

**A:** A symbolic link can point to a file on another filesystem.

**Memory Trick (EN):** _“Soft link = flexible.”_  
**Hebrew Explanation:** Hard link חייב להיות באותו filesystem, symlink (קיצור) יכול להפנות לכל נתיב, גם לדיסק אחר לגמרי.

---

## **Q40: Script exists but gives “Permission denied” when running `./test.sh`. What should be done?**

**A:** Set the execute bit:

`chmod +x test.sh`

**Memory Trick (EN):** _“Need X to eXecute.”_  
**Hebrew Explanation:** גם אם הוא סקריפט תקין, הפעלת קובץ ישירה מחייבת ביט `x` בהרשאות.

---
# **Networking, DNS, Cloud & Internet**
## **Q41: Reverse DNS assigns hostnames to IP addresses. How is the name of 198.51.100.165 stored on a DNS server?**

**A:**  
`165.100.51.198.in-addr.arpa.` (PTR record)

**Memory Trick (EN):** _“Reverse = reverse the IP + in-addr.arpa.”_  
**Hebrew Explanation:** ב־Reverse DNS הופכים את סדר האוקטטים של ה־IP ואז מוסיפים `.in-addr.arpa` עבור IPv4. זה תמיד PTR (לא A/AAAA). חייבים לזכור שזה הופכי לחלוטין מה־IP המקורי.

---

## **Q42: What command displays manual pages?**

**A:** `man`

**Memory Trick (EN):** _“man = manual.”_  
**Hebrew Explanation:** `man` מציג את התיעוד הרשמי של פקודות. במבחן לפעמים מנסים לבלבל עם `info` אבל השאלה מבקשת **only the command**.

---

## **Q43: Which command adds the new user tux and creates the user’s home directory with default configuration files?**

**A:**

`useradd -m tux`

**Memory Trick (EN):** _“m = make home.”_  
**Hebrew Explanation:** `useradd -m` יוצר תיקיית בית ומעתיק לתוכה את קבצי `/etc/skel`. חשוב לא להתבלבל עם `adduser`, שלא בכל הפצות קיים אותו דבר.

---

## **Q44: What happens to a file outside the home directory when the owner's account is deleted? (Choose two)**

**A:**

- The UID of the former owner is shown
    
- Ownership and permissions remain unchanged
    

**Memory Trick (EN):** _“User disappears, files stay.”_  
**Hebrew Explanation:** מחיקת משתמש לא מוחקת קבצים ששייכים לו. במקום שם המשתמש יופיע ה־UID. ההרשאות נשארות בדיוק אותן הרשאות.

---

## **Q45: Which command creates the ZIP archive `poems.zip` containing all `.txt` files?**

**A:**

`zip poems.zip *.txt`

**Memory Trick (EN):** _“zip archive files.”_  
**Hebrew Explanation:** בפורמט zip — קודם שם הארכיון, ואז רשימת הקבצים. `*.txt` יאסוף את כל קבצי הטקסט.

---

## **Q46: What is true about the `dmesg` command? (Choose two)**

**A:**

- It displays the kernel’s ring buffer
    
- Old messages may be overwritten by newer information
    

**Memory Trick (EN):** _“Kernel messages roll over.”_  
**Hebrew Explanation:** `dmesg` מציג לוגים מהקרנל בזיכרון זמני. בגלל שזה buffer מוגבל, מידע ישן נדחק החוצה כשנוצר מידע חדש.

---

## **Q47: Which tasks can the command `passwd` accomplish? (Choose two)**

**A:**

- Change a user’s password
    
- Lock a user account
    

**Memory Trick (EN):** _“passwd = password + lock.”_  
**Hebrew Explanation:** `passwd username` משנה סיסמה, ו־`passwd -l` נועל חשבון על ידי השמת `!` בשדה ההאש שלו.

---

## **Q48: What information is stored in `/etc/passwd`? (Choose three)**

**A:**

- Username
    
- Numerical user ID (UID)
    
- User’s default shell
    

**Memory Trick (EN):** _“passwd = user map.”_  
**Hebrew Explanation:** `/etc/passwd` מכיל מידע בסיסי על כל משתמש — ללא הסיסמה (שנמצאת ב־/etc/shadow). השדות כוללים: שם, UID, GID, shell, ותיקיית בית.

---

## **Q49: How to change the owner of `doku.odt` to tux?**

**A:**

`chown tux doku.odt`

**Memory Trick (EN):** _“chown = change owner.”_  
**Hebrew Explanation:** `chown` משנה בעלות בלבד. לא משנה הרשאות ולא משנה קבוצה (אלא אם מוסיפים סימון `:`).

---

## **Q50: Which program is a graphical editor for vector graphics?**

**A:** Inkscape

**Memory Trick (EN):** _“Ink = drawing shapes.”_  
**Hebrew Explanation:** Inkscape היא תוכנה לעריכת גרפיקה וקטורית, כמו SVG. אינה עריכת תמונות (כמו GIMP).

---

## **Q51: Which directory contains documentation and example configuration files for installed software packages?**

**A:** `/usr/share/doc/`

**Memory Trick (EN):** _“share/doc = documentation.”_  
**Hebrew Explanation:** כל חבילה שמותקנת דרך המערכת יוצרת תיקייה משלה בתוך `/usr/share/doc`, ושם יש README, license, קבצי דוגמה, ותיעוד.

---

## **Q52: Which statement is true about Free Software?**

**A:** It may be modified by anyone using it.

**Memory Trick (EN):** _“Free = freedom, not price.”_  
**Hebrew Explanation:** עקרון בסיסי של תוכנה חופשית: המשתמש רשאי לשנות את הקוד ולהפיץ אותו מחדש תחת תנאי רישיון חופשי.

---

## **Q53: What is the preferred source for installing new applications on Linux?**

**A:** The distribution’s package repository.

**Memory Trick (EN):** _“Repo = safe updates.”_  
**Hebrew Explanation:** מקורות רשמיים מבטיחים אמינות, אבטחה, ופתירת תלות בין חבילות — בניגוד להתקנות ידניות.

---

## **Q54: What is the purpose of the PATH environment variable?**

**A:** It allows execution of commands without specifying their full path.

**Memory Trick (EN):** _“PATH = where the shell looks.”_  
**Hebrew Explanation:** אם תוכנה נמצאת בנתיב שמופיע ב־PATH, אפשר להריץ אותה רק בשם הפקודה — בלי נתיב מוחלט.

---

## **Q55: Why are web browser cookies considered dangerous?**

**A:** Because they support identification and tracking of users.

**Memory Trick (EN):** _“Cookies leave crumbs → track you.”_  
**Hebrew Explanation:** cookies מאפשרות לעקוב בין אתרים, לבנות פרופיל משתמש, לבצע מעקב פרסומי, ולעיתים גם גניבת session.

---

## **Q56: Which statement concerning Linux passwords is true?**

**A:** Passwords are stored only in hashed form.

**Memory Trick (EN):** _“Never plaintext.”_  
**Hebrew Explanation:** לינוקס לא שומרת סיסמאות ברורות. במקום זה יש hash בקובץ `/etc/shadow`.

---

## **Q57: Which command sorts the output of `export-logs`?**

**A:**

`export-logs | sort`

**Memory Trick (EN):** _“Pipe → sort.”_  
**Hebrew Explanation:** הכלל: רוצים למיין פלט? מפעילים pipe ל־`sort`. במבחן ישימו תשובות מבלבלות כמו redirect.

---

## **Q58: Which tar options handle compression? (Choose two)**

**A:** `-z` (gzip), `-j` (bzip2)

**Memory Trick (EN):** _“z = gzip, j = bzip2.”_  
**Hebrew Explanation:** שני הדגלים האלו מטפלים בדחיסה. שאר האפשרויות (כמו `c`, `x`, `f`) לא קשורות לדחיסה עצמה.

---

## **Q59: Which symbol in a shell prompt indicates root privileges?**

**A:** `#`

**Memory Trick (EN):** _“# = high privilege.”_  
**Hebrew Explanation:** root תמיד מוצג כ־`#` ב־prompt. משתמש רגיל הוא `$`.

---

## **Q60: Which commands display the content of `Texts 2.txt`? (Choose two)**

**A:**

- `cat 'Texts 2.txt'`
    
- `cat Texts\ 2.txt`
    

**Memory Trick (EN):** _“Quotes or escape spaces.”_  
**Hebrew Explanation:** שמות קבצים עם רווחים דורשים או מרכאות או backslash.
# **Hardware, Distros, Packages & System Fundamentals**
## **Q61: Which Linux distribution is derived from Red Hat Enterprise Linux?**

**A:** CentOS  
**Memory Trick (EN):** _“CentOS = Community RHEL.”_  
**Hebrew Explanation:** CentOS מבוססת ישירות על קוד המקור של RHEL, ולכן כל הכלים, הפקודות, וההתנהגות שלה זהים כמעט לחלוטין ל־Red Hat.

---

## **Q62: Which command puts the lines of `data.csv` into alphabetical order?**

**A:**

`sort data.csv`

**Memory Trick (EN):** _“sort sorts.”_  
**Hebrew Explanation:** הפקודה sort ממיינת קלט alfabetically כברירת מחדל. אין צורך בשום דגל מיוחד במקרה הזה.

---

## **Q63: What is true about the owner of a file?**

**A:** Each file is owned by exactly one user and one group.  
**Memory Trick (EN):** _“One file = one owner + one group.”_  
**Hebrew Explanation:** מודל הבעלות של לינוקס פשוט: כל קובץ שייך למשתמש אחד ולקבוצה אחת בלבד. ניתן ליצור ACLs אבל זה לא חלק מהמבחן.

---

## **Q64: Which device represents a hard disk partition?**

**A:** `/dev/sda2`  
**Memory Trick (EN):** _“Number = partition.”_  
**Hebrew Explanation:** שם דיסק ללא מספר (כמו /dev/sda) הוא הדיסק כולו. ברגע שיש מספר בסוף — זו מחיצה (partition).

---

## **Q65: Which commands show usage info for `ls`? (Choose two)**

**A:**

- `man ls`
    
- `info ls`
    

**Memory Trick (EN):** _“man/info = official help.”_  
**Hebrew Explanation:** man ו־info הם שני מקורות התיעוד התקניים בלינוקס. --help היה יכול להיות תשובה, אך המבחן ביקש “Which of the following”.

---

## **Q66: Which command shows the absolute path of your current working directory?**

**A:** `pwd`  
**Memory Trick (EN):** _“Print Working Directory.”_  
**Hebrew Explanation:** פקודה בסיסית המציגה את הנתיב המלא לתיקייה שאתה נמצא בה.

---

## **Q67: What is a Linux distribution?**

**A:** A bundle of the Linux kernel, system utilities, and additional software.  
**Memory Trick (EN):** _“Kernel + tools + apps = distro.”_  
**Hebrew Explanation:** הפצה היא שילוב של ליבה, תוכנות מערכת, מנהלי חבילות, ותוכנות נוספות שיוצרות מערכת שלמה אחת.

---

## **Q68: Which package management tool is used in Red Hat-based systems?**

**A:** `rpm`  
**Memory Trick (EN):** _“Red Hat Packages Manager.”_  
**Hebrew Explanation:** rpm הוא מנגנון החבילות הבסיסי ב־RHEL, CentOS, Fedora. מנהל החבילות ברמת CLI הוא yum/dnf אך rpm הוא הכלי הבסיסי.

---

## **Q69: Which statement about Linux hardware drivers is correct?**

**A:** Drivers are either compiled into the Linux kernel or loaded as kernel modules.  
**Memory Trick (EN):** _“Built-in or module.”_  
**Hebrew Explanation:** בלינוקס דרייברים הם חלק מהקרנל עצמו או נטענים בזמן ריצה כמודולים (`lsmod`). לא מגיעים מהיצרן כספרייה DLL.

---

## **Q70: Which statements about `/etc/skel` are correct? (Choose two)**

**A:**

- Files are copied to the new user’s home directory when account is created
    
- Contains default configuration files used by `useradd`
    

**Memory Trick (EN):** _“skel = skeleton home.”_  
**Hebrew Explanation:** כשמשתמש חדש נוצר עם `useradd -m`, קבצי `/etc/skel` מועתקים לתיקיית הבית שלו — כמו שלד לתיקייה חדשה.

---

## **Q71: Which command adds `/new/dir/` to the PATH environment variable?**

**A:**

`export PATH=/new/dir:$PATH`

**Memory Trick (EN):** _“New path goes first.”_  
**Hebrew Explanation:** PATH מוגדר כרשימת נתיבים מופרדים בנקודתיים. שמים את הנתיב החדש לפני הישן כדי שיקבל עדיפות בהרצת פקודות.

---

## **Q72: Which command finds all lines in `operating-systems.txt` containing the term “linux”, regardless of case?**

**A:**

`grep -i linux operating-systems.txt`

**Memory Trick (EN):** _“i = ignore case.”_  
**Hebrew Explanation:** -i הופך את החיפוש ללא תלוי אותיות (uppercase / lowercase). פעולה נפוצה בבדיקות טקסט.

---

## **Q73: Which programs are web servers? (Choose two)**

**A:** Apache HTTPD, NGINX  
**Memory Trick (EN):** _“Apache + NGINX = the kings of the web.”_  
**Hebrew Explanation:** שתי התוכנות המובילות לאירוח אתרי אינטרנט — מוכרות בכל מערכת לינוקס.

---

## **Q74: Which of the following values could be a process ID on Linux?**

**A:** `21398`  
**Memory Trick (EN):** _“PID = integer only.”_  
**Hebrew Explanation:** PIDs הם מספרים חיוביים. כל ערך שאינו מספר — אינו PID תקין.

---

## **Q75: Which types of bus can connect hard drives to the motherboard?**

**A:** SATA  
**Memory Trick (EN):** _“SATA = storage bus.”_  
**Hebrew Explanation:** SATA הוא התקן החיבור הנפוץ ביותר לכוננים בחומרה מודרנית, כולל SSD/HDD (לא כולל NVMe שזה PCIe, אבל זה מעבר להיקף המבחן).

---

## **Q76: Which directory must be mounted with read/write access if it resides on its own filesystem?**

**A:** `/var`  
**Memory Trick (EN):** _“var = variable data → needs write.”_  
**Hebrew Explanation:** `/var` מכיל לוגים, spool, cache — מערכת חייבת יכולת לכתוב לשם. אם `/var` read-only → המערכת תקרוס.

---

## **Q77: How do most Linux commands display their usage information?**

**A:** Using the options `-h` or `--help`  
**Memory Trick (EN):** _“Help always helps.”_  
**Hebrew Explanation:** כמעט כל פקודה תומכת בדגל עזרה בסיסי שמציג את כל האופציות שלה.

---

## **Q78: Which protocol is used for automatic IP address configuration?**

**A:** DHCP  
**Memory Trick (EN):** _“Dynamic Host Configuration Protocol = auto IP.”_  
**Hebrew Explanation:** DHCP מחלק IP, DNS, gateway אוטומטית בתחילת התחברות לרשת.

---

## **Q79: Which statements are true regarding a typical shell script? (Choose two)**

**A:**

- It has the executable permission bit set
    
- It starts with the two characters `#!`
    

**Memory Trick (EN):** _“Shebang + execute bit.”_  
**Hebrew Explanation:** סקריפט מתחיל ב־shebang כדי לקבוע interpreter (כמו bash), וחייב הרשאת `x` להרצה.

---

## **Q80: Which command searches for the file `foo.txt` under `/home`?**

**A:**

`find /home -name foo.txt`

**Memory Trick (EN):** _“find path -name file.”_  
**Hebrew Explanation:** פקודת find תמיד נכתבת: find <path> -name <pattern>. על זה אוהבים לשים מלכודת במבחן.

---
