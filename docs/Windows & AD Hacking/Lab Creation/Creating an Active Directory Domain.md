# Creating an Active Directory domain

# Setting up DC

### Preqrequisite :

Windows server 2019 : [https://www.microsoft.com/en-in/evalcenter/evaluate-windows-server-2019](https://www.microsoft.com/en-in/evalcenter/evaluate-windows-server-2019)

---

### Setting up DNS :

DNS is an important prerequisite of Active Directory. Without it, Active Directory will not function, or should we say, you can't install or promote a server to a domain controller. Active Directory heavily relies on DNS.

- So first we will open up our network adapter settings in control panel

![Creating%20an%20Active%20Directory%20Domain/Untitled.png](Creating%20an%20Active%20Directory%20Domain/Untitled.png)

- Next will go to its properties and setup IPv4 with our DNS server address :

![Creating%20an%20Active%20Directory%20Domain/Untitled%201.png](Creating%20an%20Active%20Directory%20Domain/Untitled%201.png)

![Creating%20an%20Active%20Directory%20Domain/Untitled%202.png](Creating%20an%20Active%20Directory%20Domain/Untitled%202.png)

![Creating%20an%20Active%20Directory%20Domain/Untitled%203.png](Creating%20an%20Active%20Directory%20Domain/Untitled%203.png)

Thats all we have to do to setup DNS on our Windows Server 2019.

---

### Changing Machine Name

- Settings → System → About → Rename this PC

![Creating%20an%20Active%20Directory%20Domain/Untitled%204.png](Creating%20an%20Active%20Directory%20Domain/Untitled%204.png)

- We named it vDC-01 :

```
v - Virtual
DC - Domain Controller
01 - First Domain Controller
```

---

### Installing Active Directory

- First we will go to Manage → Add Roles and Features

![Creating%20an%20Active%20Directory%20Domain/Untitled%205.png](Creating%20an%20Active%20Directory%20Domain/Untitled%205.png)

- Now just follow the screenshots :

![Creating%20an%20Active%20Directory%20Domain/Untitled%206.png](Creating%20an%20Active%20Directory%20Domain/Untitled%206.png)

![Creating%20an%20Active%20Directory%20Domain/Untitled%207.png](Creating%20an%20Active%20Directory%20Domain/Untitled%207.png)

![Creating%20an%20Active%20Directory%20Domain/Untitled%208.png](Creating%20an%20Active%20Directory%20Domain/Untitled%208.png)

![Creating%20an%20Active%20Directory%20Domain/Untitled%209.png](Creating%20an%20Active%20Directory%20Domain/Untitled%209.png)

---

### Promoting Server to Domain Controller

⇒ So when we startup Server Manager we see the following Alert saying that we need to configure some thing for AD Domain Services to promote it to a DC.

![Creating%20an%20Active%20Directory%20Domain/Untitled%2010.png](Creating%20an%20Active%20Directory%20Domain/Untitled%2010.png)

- So first we will have to create a new forest with whatever root domain name you like.

![Creating%20an%20Active%20Directory%20Domain/Untitled%2011.png](Creating%20an%20Active%20Directory%20Domain/Untitled%2011.png)

- Next we just set DSRM Password

![Creating%20an%20Active%20Directory%20Domain/Untitled%2012.png](Creating%20an%20Active%20Directory%20Domain/Untitled%2012.png)

- We dont need to create DNS delegation.

![Creating%20an%20Active%20Directory%20Domain/Untitled%2013.png](Creating%20an%20Active%20Directory%20Domain/Untitled%2013.png)

- Next we have the option to set our NETBIOS Name

![Creating%20an%20Active%20Directory%20Domain/Untitled%2014.png](Creating%20an%20Active%20Directory%20Domain/Untitled%2014.png)

- Finally we finish and install

![Creating%20an%20Active%20Directory%20Domain/Untitled%2015.png](Creating%20an%20Active%20Directory%20Domain/Untitled%2015.png)

---

So now our Domain Controller is ready to be used. When it reboots we get the following login :

![Creating%20an%20Active%20Directory%20Domain/Untitled%2016.png](Creating%20an%20Active%20Directory%20Domain/Untitled%2016.png)

---

## Adding Users

⇒ We will go to our windows start menu and go into Windows Administrative Tools and open up Active Directory Users and Computers

![Creating%20an%20Active%20Directory%20Domain/Untitled%2017.png](Creating%20an%20Active%20Directory%20Domain/Untitled%2017.png)

![Creating%20an%20Active%20Directory%20Domain/Untitled%2018.png](Creating%20an%20Active%20Directory%20Domain/Untitled%2018.png)

- First we will be creating OU ( Organizational Unit ) for Groups and Users under IN ( India - Country ) OU.

![Creating%20an%20Active%20Directory%20Domain/Untitled%2019.png](Creating%20an%20Active%20Directory%20Domain/Untitled%2019.png)

- Next we moved the groups from Users to the OU we created for Groups :

![Creating%20an%20Active%20Directory%20Domain/Untitled%2020.png](Creating%20an%20Active%20Directory%20Domain/Untitled%2020.png)

- Next we will be creating a user in Users OU

![Creating%20an%20Active%20Directory%20Domain/Untitled%2021.png](Creating%20an%20Active%20Directory%20Domain/Untitled%2021.png)

![Creating%20an%20Active%20Directory%20Domain/Untitled%2022.png](Creating%20an%20Active%20Directory%20Domain/Untitled%2022.png)

- We set samname as **rkumar** which is the username the person will use to login.

- Next we set password : P@ssw0rd

![Creating%20an%20Active%20Directory%20Domain/Untitled%2023.png](Creating%20an%20Active%20Directory%20Domain/Untitled%2023.png)

![Creating%20an%20Active%20Directory%20Domain/Untitled%2024.png](Creating%20an%20Active%20Directory%20Domain/Untitled%2024.png)

---

# Setting up Client

### Prerequisites

Windows 10 Enterprise : [https://www.microsoft.com/en-in/evalcenter/evaluate-windows-10-enterprise](https://www.microsoft.com/en-in/evalcenter/evaluate-windows-10-enterprise)

---

### Connecting our client to DC01

- First lets open up network adapter properties and change the ipv4 settings to use DC01 dns address : `192.168.51.133`

![Creating%20an%20Active%20Directory%20Domain/Untitled%2025.png](Creating%20an%20Active%20Directory%20Domain/Untitled%2025.png)

![Creating%20an%20Active%20Directory%20Domain/Untitled%2026.png](Creating%20an%20Active%20Directory%20Domain/Untitled%2026.png)

![Creating%20an%20Active%20Directory%20Domain/Untitled%2027.png](Creating%20an%20Active%20Directory%20Domain/Untitled%2027.png)

- Next we will connect to the domain **lexi.local** :
    - First we open up System Properties

    ![Creating%20an%20Active%20Directory%20Domain/Untitled%2028.png](Creating%20an%20Active%20Directory%20Domain/Untitled%2028.png)

    - Next we will set the Domain to lexi.local

    ![Creating%20an%20Active%20Directory%20Domain/Untitled%2029.png](Creating%20an%20Active%20Directory%20Domain/Untitled%2029.png)

    ![Creating%20an%20Active%20Directory%20Domain/Untitled%2030.png](Creating%20an%20Active%20Directory%20Domain/Untitled%2030.png)

    ![Creating%20an%20Active%20Directory%20Domain/Untitled%2031.png](Creating%20an%20Active%20Directory%20Domain/Untitled%2031.png)

- Next we just restart our client and we can login as user rkumar ( raj kumar ) on this machine

![Creating%20an%20Active%20Directory%20Domain/Untitled%2032.png](Creating%20an%20Active%20Directory%20Domain/Untitled%2032.png)

⇒ Going back to our DC01 and opening up Active Directory Users and Computers settings and we see that our CLIENT01 machine was sucessfully connected to domain.

![Creating%20an%20Active%20Directory%20Domain/Untitled%2033.png](Creating%20an%20Active%20Directory%20Domain/Untitled%2033.png)

---

⇒ The lab is now ready and you can practise any attacks you want.