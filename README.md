# Linux Lab – Full Disk Encryption
## Objective

The objective of this script is to block incoming network traffic from a list of specified IP addresses using the iptables firewall in Linux. It automates the process of applying IP-based traffic restrictions for network security purposes.

### Job Skills Learned

- Linux System Administration
- Managing firewall rules using iptables.
- Bash Shell Scripting
- Using loops and conditional commands.
- Network Security and Access Control
- Automation and Efficiency


### Tools Used

- Linux Terminal Command Line Interface (CLI)
- Bash Shell
- iptables (Firewall)
- Networking Commands: ping, ifconfig
- VMware Tools: Linux VM

*Ref: Diagram:



### STEPS


FULL DISK ENCRYPTION
 
# 1. installing cryptsetup & dm-crypt
`apt install cryptsetup`
![image](https://github.com/user-attachments/assets/baa69155-f77b-4474-a660-3eed21ef056e)
![image](https://github.com/user-attachments/assets/b28b2665-975c-481c-8c3d-ba9f86cb5573)
 
 
 
# 2. Idenfity the name of the disk or partition to encrypt:
`fdisk -l  # for this example it will be /dev/sdc`
or 
dmesg  # for usb sticks
![image](https://github.com/user-attachments/assets/e87d44a4-4a16-4fb5-a180-61bedb8d8c3d)
 
 
# 3. Filling the disk or partition to encrypt with random data (optional)
`dd if=/dev/urandom of=/dev/sdc status=progress`
![image](https://github.com/user-attachments/assets/04515dba-5315-43f1-bab4-2fb888b55e84)
 
 
# 4. Initialize the LUKS partition and set the initial passphrase
`cryptsetup -y -v luksFormat /dev/sdc`
![image](https://github.com/user-attachments/assets/178daf9b-1a60-4024-9b3d-c1575e3219f0)
 
 
 
#5.  Open the encrypted device and set up a mapping name
`cryptsetup luksOpen /dev/sdc secretdata`
`ls -l /dev/mapper`
![image](https://github.com/user-attachments/assets/67f4b98a-6906-44c6-b4cf-fdb85263342b)
  


# Display the status of the mapping file
`cryptsetup status secretdata`
![image](https://github.com/user-attachments/assets/ac03b503-6416-4a7c-9041-5162142f8b99)
 

 
# 6. Format the filesystem
`mkfs.ext4 /dev/mapper/secretdata`
![image](https://github.com/user-attachments/assets/a51660b3-b430-4148-bded-1521d03d0c73)
 

 
# 7. Mount the encrypted file system into the main file tree.
`mount /dev/mapper/secretdata /mnn  # -> you can mount it to any existing directory like /mnt`
![image](https://github.com/user-attachments/assets/9af275cf-c3e6-4fbe-9203-10359e9b464a)
  


# 8. Unmount the encrypted disk
`umount /mnt`
`cryptsetup luksClose secretdata`
![image](https://github.com/user-attachments/assets/2778df51-117e-4da6-accb-13fe820bbce0)
 

 
# 9. Accesing the encrypted disk after a restart or unmounting:
`cryptsetup luksOpen /dev/hdc secretdata`
`mount /dev/mapper/secretdata /root/secret`
![image](https://github.com/user-attachments/assets/52060ed7-a201-4743-b77b-0390cb6d619e)
 
 


#10. Unlocking LUKS Encrypted Drives With A Keyfile
 
# Generating a random keyfile
`dd if=/dev/urandom of=/root/keyfile bs=1024 count=4`
![image](https://github.com/user-attachments/assets/003fd542-3c86-4b23-b817-065ce269f313)
 
 
# Set the permissions (only root can read it)
`chmod 400 /root/keyfile`
![image](https://github.com/user-attachments/assets/db90b2c9-e1ef-48d6-9725-681f34c105e1)
 

 
# Add the keyfile as an additional authorization method
`cryptsetup luksAddKey /dev/sdc /root/keyfile`
![image](https://github.com/user-attachments/assets/e6609069-b5b5-4e25-aca0-ebc0e79d624f)
 
 
# Unlock the drive using the keyfile
`cryptsetup luksOpen /dev/hdc secret --key-file /root/keyfile`
