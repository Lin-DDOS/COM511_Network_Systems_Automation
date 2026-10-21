[Personal Learning Record](../../personal_journal/personal_journal.md) | [Session Notes](../sessions/README.md) 

# Session 4

## Topics covered
*What topics were covered in this session*
- Automated bare metal deployment
- PXE (Preboot Execution Envrionment)
- Network booting
- NFS (Network File System)
- Automated operating system installation
- Relationship between DHCP, PXE, TFTP and NFS
- Testing NFS shares from a linux client
- Using the Vagrant-pxe example environment


## Personal Notes and research following this session
*Which class sessions and personal research refers to technology in this proposal. Link to examples.*
### PXE boot
PXE (Preboot Execution Environment) allows a computer to boot from a network rather than a local hard drive, USB stick or DVD. this is useful in enterprise environment where many computers need to be deployed automatically.
The PXE boot process follows these steps:
1. Client machine powers on.
2. Client contacts a DHCP server.
3. DHCP assigns an IP address.
4. PCE server provides the location of boot files.
5. Client downloads boot files using TFTP.
6. operating system installation files are retrieved from an NFS or HTTP server.
7. Operating system installtion begins automatically.


## Exercises and results
*What exercises did you complete. What results. Screen shots and notes*



## Summary of learning
*What did you learn through these exercises*
