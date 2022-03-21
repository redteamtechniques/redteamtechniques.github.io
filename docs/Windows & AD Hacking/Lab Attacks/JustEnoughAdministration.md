# Just Enough Administration

![Just%20Enough%20Administration/Untitled.png](Just%20Enough%20Administration/Untitled.png)

⇒ This my friend, is JEA ( Just Enough Administration ) which is a security technology that enables delegated administration for anything managed by powershell. 

- It limits what users can do by specifying which cmdlets, functions, and external commands they can run.
- It can only constrain powershell access.

---

## Lab Setup

### 1) Setting up Group

![Just%20Enough%20Administration/Untitled%201.png](Just%20Enough%20Administration/Untitled%201.png)

![Just%20Enough%20Administration/Untitled%202.png](Just%20Enough%20Administration/Untitled%202.png)

### 2) Setting up JEA

i) Creating Configuration file

```powershell
New-PSSessionConfigurationFile -Path 'C:\Program Files\WindowsPowerShell\endark_conf.pssc'

notepad C:\Program Files\WindowsPowerShell\endark_conf.pssc
```

![Just%20Enough%20Administration/Untitled%203.png](Just%20Enough%20Administration/Untitled%203.png)

ii) Creating folder for JEA

```powershell
New-Item -Path 'C:\Program Files\WindowsPowerShell\Modules\JEA\RoleCapabilities' -ItemType Directory
```

iii) Creating the PS Role Capability File for the Endark Admins

```powershell
New-PSRoleCapabilityFile -Path 'C:\Program Files\WindowsPowerShell\Modules\JEA\RoleCapabilities\endark_admins.psrc'
notepad C:\Program Files\WindowsPowerShell\Modules\JEA\RoleCapabilities\endark_admins.psrc
```

![Just%20Enough%20Administration/Untitled%204.png](Just%20Enough%20Administration/Untitled%204.png)

iv) Registering the Configuration

```powershell
Register-PSSessionConfiguration -Name Endark_Admins -Path 'C:\Program Files\WindowsPowerShell\endark_conf.pssc'
Restart-Service WinRM
```

v) Testing it

```powershell
Enter-PSSession -ComputerName vDC01 -credential Andrei -ConfigurationName endark_admins
```

![Just%20Enough%20Administration/Untitled%205.png](Just%20Enough%20Administration/Untitled%205.png)

- So right now its running in no-language mode which is the safest language mode.

---

## JEA Bypasses

⇒ So there are couple of ways to bypass JEA and i will be showing a few of them

- Constrained Language Mode
- Command Injection
- Script Block Injection

---

### 1) Constrained Language Mode

- In ConstrainedLanguage you are allowed to create new functions and all you gotta do is create a new function that could run anything you want. That is no longer restricted and hence jea can be bypassed.
- NoLanguageMode is the only safe language mode

![Just%20Enough%20Administration/Untitled%206.png](Just%20Enough%20Administration/Untitled%206.png)

- **`function CommandName { whoami | out-host }`**

![Just%20Enough%20Administration/Untitled%207.png](Just%20Enough%20Administration/Untitled%207.png)

![Just%20Enough%20Administration/Untitled%208.png](Just%20Enough%20Administration/Untitled%208.png)

---

### 2) Command Injection

⇒ So the following function takes user input and run Invoke-Expression with Get-Process on it. This function can be easily exploited by escaping the double quotes .

![Just%20Enough%20Administration/Untitled%209.png](Just%20Enough%20Administration/Untitled%209.png)

- **`Check-Process " ; <command> "`**

![Just%20Enough%20Administration/Untitled%2010.png](Just%20Enough%20Administration/Untitled%2010.png)

![Just%20Enough%20Administration/Untitled%2011.png](Just%20Enough%20Administration/Untitled%2011.png)

---

### 3) Script Block Injection

⇒ So this script creates a script block which has Invoke-Expression command that includes the input we provided. It runs it on remote system vDC01 as Administrator.

![Just%20Enough%20Administration/Untitled%2012.png](Just%20Enough%20Administration/Untitled%2012.png)

- **`Check-Process " ; <command> "`**

![Just%20Enough%20Administration/Untitled%2013.png](Just%20Enough%20Administration/Untitled%2013.png)

---

- Reference : [https://www.youtube.com/watch?v=ahxMOAAani8](https://www.youtube.com/watch?v=ahxMOAAani8) [ Also a lot more attacks covered in the video and mitigation ]