# Child Domain Lab Setup

### Preqrequisite:

Windows server 2019: [https://www.microsoft.com/en-in/evalcenter/evaluate-windows-server-2019](https://www.microsoft.com/en-in/evalcenter/evaluate-windows-server-2019)

- Setup One Domain Controller [ Refer to this guide to learn how to setup a DC [https://csenox.github.io/active-directory/2020/10/07/AD-Lab/](https://csenox.github.io/active-directory/2020/10/07/AD-Lab/) ]

---

## Setting up DC02 [ Child Domain ]

⇒ Okay so after setting up our first domain controller ( vDC01 ) in the domain **`endark.local`** we will try and ping it from our fresh install of windows server 2019: 

![Child%20Domain%20Lab%20Setup/Untitled.png](Child%20Domain%20Lab%20Setup/Untitled.png)

- As we can see we are able to ping the machine.

---

### Setting up Static IP and DNS Server

⇒ Now we will setup static ip and DNS server ( i.e vDC01 )

![Child%20Domain%20Lab%20Setup/Untitled%201.png](Child%20Domain%20Lab%20Setup/Untitled%201.png)

---

### Installing AD

⇒ Now we will be installing Active Directory Domain Services and DNS Server

![Child%20Domain%20Lab%20Setup/Untitled%202.png](Child%20Domain%20Lab%20Setup/Untitled%202.png)

![Child%20Domain%20Lab%20Setup/Untitled%203.png](Child%20Domain%20Lab%20Setup/Untitled%203.png)

![Child%20Domain%20Lab%20Setup/Untitled%204.png](Child%20Domain%20Lab%20Setup/Untitled%204.png)

---

### Promoting Server to DC

⇒ Now we will promote this server to a domain controller

![Child%20Domain%20Lab%20Setup/Untitled%205.png](Child%20Domain%20Lab%20Setup/Untitled%205.png)

⇒ So we want this to be a child domain we will set the following settings 

![Child%20Domain%20Lab%20Setup/Untitled%206.png](Child%20Domain%20Lab%20Setup/Untitled%206.png)

- Using the vDC01 administrator credentials:

![Child%20Domain%20Lab%20Setup/Untitled%207.png](Child%20Domain%20Lab%20Setup/Untitled%207.png)

![Child%20Domain%20Lab%20Setup/Untitled%208.png](Child%20Domain%20Lab%20Setup/Untitled%208.png)

- Press Next and Install!

⇒ After reboot we are presented with the following login:

![Child%20Domain%20Lab%20Setup/Untitled%209.png](Child%20Domain%20Lab%20Setup/Untitled%209.png)

---

### Checking Trusts b/w Parent and Child Domain

⇒ Now lets head over to vDC01 [ [endark.local](http://endark.local) ] and check the trusts b/w these domains:

![Child%20Domain%20Lab%20Setup/Untitled%2010.png](Child%20Domain%20Lab%20Setup/Untitled%2010.png)

![Child%20Domain%20Lab%20Setup/Untitled%2011.png](Child%20Domain%20Lab%20Setup/Untitled%2011.png)

![Child%20Domain%20Lab%20Setup/Untitled%2012.png](Child%20Domain%20Lab%20Setup/Untitled%2012.png)

![Child%20Domain%20Lab%20Setup/Untitled%2013.png](Child%20Domain%20Lab%20Setup/Untitled%2013.png)

- We see that **baby.endark.local** is child of **endark.local** domain!

⇒ Running **`Get-ADTrust -Filter *`** we see that the trust direction is **`bidirectional`** which means that members can authenticate from one domain to another when they want to access shared resources.

![Child%20Domain%20Lab%20Setup/Untitled%2014.png](Child%20Domain%20Lab%20Setup/Untitled%2014.png)

---

⇒ Now our lab is ready to practise Parent Child Domain Trust Abuse [ Domain Admin to Enterprise Admin ].

## Author: **d4rckh** & **enox**