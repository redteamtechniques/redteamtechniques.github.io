# LLMNR Poisoning

Microsoft systems use LLMNR and the NetBIOS Name Service (NBT-NS) for [localhost](http://localhost) resolution when DNS lookups file.

You can impersonate services that are searched by hosts using Responder to send fake responses.

# What are we doing?

LLMNR Works like this:

1. User tries to access `\\DC1`
2. DC1 **does not** have a **DNS** record
3. User's computer asks on the entire network who is `\\DC1`
4. `\\DC1` answers
5. User's computer connects to `\\DC1`, **sending his hash**

But **LLMNR** can also go like: 

1. User tries to access `\\DC2`
2. `\\DC2` does not have a **DNS** record
3. User's computer asks on the entire network who is `\\DC2`
4. Nobody answers

We can listen to every LLMNR request on the network and answer to the unaswered one's

So it will work like:

1. User tries to access `\\DC2`
2. `\\DC2` does not have a **DNS** record
3. User's computer asks on the entire network who is `\\DC2`
4. **Nobody** answers
5. Our **attacker machine** answers to **request**
6. User's computer connects to `\\DC2` (our attack machine), **sending his hash to us** 

This way, if the user makes a mistake in the hostname, responder can yoink user's NTLMv2 hash😉

# Firing the attack

# Setting up responder

We will need to setup responder answer to unanswered LLMNR request using the following syntax

```jsx
python Responder.py -I <interface> -r -d -w
```

![LLMNR%20Poisoning/Untitled.png](LLMNR%20Poisoning/Untitled.png)

## A user makes a mistake.....

A user tries to access `\\client6` which does not exist

![LLMNR%20Poisoning/Untitled%201.png](LLMNR%20Poisoning/Untitled%201.png)

And we capture his hash!

![LLMNR%20Poisoning/Untitled%202.png](LLMNR%20Poisoning/Untitled%202.png)

# Defending

1. Disable LLMNR, select `Turn OFF Multicast Name Resolution` under `Local Computer Policy > Computer Configuration > Administrative Templates > Network > DNS Client` in **GPO Editor**
2. Disable NBT-NS, navigate to `Network Connections > Network Adapter Properties > TCP/IPv4 Properties > Advanced Tab > WINS tab` and select `"Disable NetBIOS over TCP/IP"`

**If a company must use LLMNR, best is to require strong user passwords.**

# Cracking the NTLMv2 challenge response

You can use hashcat

```jsx
hashcat -m 5600 ntlmv2 /usr/share/wordlists/rockyou.txt  --force
```