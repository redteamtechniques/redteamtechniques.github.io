# Abusing Parent Child Domain Trusts for Privilege Escalation from DA to EA

⇒ So we will be learning about how to abuse **`Child ↔ Parent`** domain trusts relationship for privilege escalation from **`Domain Admin`** ( in child domain ) to **`Enterprise Admin`** in the forest.

- Refer to [Child Domain lab Setup](../Lab%20Creation/Child%20Domain%20Lab%20Setup.md) guide to setup the environment required for this attack

⇒ So if we compromise a **`child domain`** and get the **`krbtgt`** hash of a **`child domain`** and the **`Enterpise Admin`** SID that is **`[ParentDomainSID]-519`** . We can then craft a golden ticket using the info with mimikatz and get **`Enterprise Admin`** on the **`parent domain`**.

> If you compromise the domain controller of a child domain in a forest, you can compromise its entire parent domain.
~harmj0y

---

## Performing the Attack

⇒ Tool Required for this attack:

- mimikatz
- PowerView

⇒ Okay so after compromising the child domain [ **`child.endark.local`** ] we check the **`trusts`** relationship between the parent and child domain :

```python
Get-ADTrust -Filter *
```

![Abusing%20Parent%20Child%20Domain%20Trusts%20for%20Privilege%20Escalation%20from%20DA%20to%20EA/Untitled.png](Abusing%20Parent%20Child%20Domain%20Trusts%20for%20Privilege%20Escalation%20from%20DA%20to%20EA/Untitled.png)

- We see that the trust relationship is **`BiDirectional`** which basically means that members can authenticate from one domain to another when they want to access shared resources.

⇒ Now to craft a Golden Ticket we require

- krbtgt hash in child domain

![Untitled%201.png](Abusing%20Parent%20Child%20Domain%20Trusts%20for%20Privilege%20Escalation%20from%20DA%20to%20EA/Untitled%201.png)

- child domain sid [ baby.endark.local ]

![Untitled%202.png](Abusing%20Parent%20Child%20Domain%20Trusts%20for%20Privilege%20Escalation%20from%20DA%20to%20EA/Untitled%202.png)

- Enterprise Admin SID

![Untitled%203.png](Abusing%20Parent%20Child%20Domain%20Trusts%20for%20Privilege%20Escalation%20from%20DA%20to%20EA/Untitled%203.png)

⇒ Now lets start crafting the Golden Ticket using mimikatz

```python
.\mimikatz.exe "kerberos::golden /user:Administrator /domain:baby.endark.local /sid:<Child Domain SID> /krbtgt:<krbtgt hash> /sids:<EnterpriseAdmin SID>" "exit"
```

![Untitled%204.png](Abusing%20Parent%20Child%20Domain%20Trusts%20for%20Privilege%20Escalation%20from%20DA%20to%20EA/Untitled%204.png)

⇒ Next we will load the ticket and get a shell on vDC01 in the root domain :

![Untitled%205.png](Abusing%20Parent%20Child%20Domain%20Trusts%20for%20Privilege%20Escalation%20from%20DA%20to%20EA/Untitled%205.png)

- Using **`Enter-PSSession`**:

![Untitled%206.png](Abusing%20Parent%20Child%20Domain%20Trusts%20for%20Privilege%20Escalation%20from%20DA%20to%20EA/Untitled%206.png)

![Untitled%207.png](Abusing%20Parent%20Child%20Domain%20Trusts%20for%20Privilege%20Escalation%20from%20DA%20to%20EA/Untitled%207.png)

- Using **`Invoke-Command`**:

![Untitled%208.png](Abusing%20Parent%20Child%20Domain%20Trusts%20for%20Privilege%20Escalation%20from%20DA%20to%20EA/Untitled%208.png)

**⇒ We have successfully compromised vDC01 through vDC02 by abusing the trusts relationship between child and parent domains**

---

References : [http://www.harmj0y.net/blog/redteaming/the-trustpocalypse/](http://www.harmj0y.net/blog/redteaming/the-trustpocalypse/)

## Author: d4rckh and enox