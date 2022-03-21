# DCSync Lab Setup

⇒ So for an user to perform DCSync Attack he needs the following permissions

- The “**[DS-Replication-Get-Changes](https://msdn.microsoft.com/en-us/library/ms684354(v=vs.85).aspx)**” extended right
    - **CN:** DS-Replication-Get-Changes
    - **GUID:** 1131f6aa-9c07-11d1-f79f-00c04fc2dcd2
- The “**[Replicating Directory Changes All](https://msdn.microsoft.com/en-us/library/ms684355(v=vs.85).aspx)**” extended right
    - **CN:** DS-Replication-Get-Changes-All
    - **GUID:** 1131f6ad-9c07-11d1-f79f-00c04fc2dcd2
- The “**[Replicating Directory Changes In Filtered Set](https://msdn.microsoft.com/en-us/library/hh338663(v=vs.85).aspx)**” extended right (this one isn’t always needed but we can add it just in case)
    - **CN:** DS-Replication-Get-Changes-In-Filtered-Set
    - **GUID:** 89e95b76-444d-4c62-991a-0facbeda640c

⇒ So we created an user named Joe Mama ( j.mama ) and then gave the permissions required to perform DCSync Attack

![DCSync%20Lab%20Setup/Untitled.png](DCSync%20Lab%20Setup/Untitled.png)

---

References [https://adsecurity.org/?p=1729](https://adsecurity.org/?p=1729)