# Home File-Sharing Server

Overview
Converted an old laptop into a home file sharing server using Ubuntu. The server ensures secure acccess to share files across devices on the local network using Samba.

## Hardware & OS
| Component                     | Details                                                                                                                             |
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Laptop Model                  | Lenovo Ideapad                                                                                                                      |
| CPU & RAM                     | Intel(R) Core(TM) i3-8130U CPU, 4GB RAM                                                                                             |
| Storage                       | 915GB HDD                                                                                                                           |
| OS                            | Ubuntu 24.04.2 LTS                                                                                                                  |
| Power Setup                   | This server requires manual power-on by pressing the power button. Laptop is constantly plugged in to avoid accidental power loss.  |


## Services Configured
| Service                       | Purpose
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Samba                         | Local network file sharing (SMB)                                                                                                    |
| Wireguard                     | Encrypted VPN to securely connect to the home server from anywhere outside of the local network. Chosen for its simplicity and lightweight.                                                                    |
| Duckdns                       | Used to attain a static domanin name to my home's dynamic IP.                        |

##Installation Steps
1. Ubuntu installation
   -  Installed Ubuntu Server from USB on old laptop
   -  Ensure that OpenSSH Server is checked to ensure remote access
  
2. Samba Installation
   -  On server:
     *  sudo apt install samba
     *  sudo mkdir /media/myfiles (Creates a folder called /myfiles in the /media/ directory.)
     *  sudo chown $USER: /media/myfiles (Changes the ownership of the directory to the current user and the user's primary group. Ensures that the user has all permissions regarding this directory)
       
     *  Samba Configuration:
         > access samba configuration: sudo vi /etc/samba/smb.conf
         > change the line "map to guest = bad user" to "map to guest = never"
         > add folder we created:
           [myfiles] (share name and will be used when connecting to the server)
             path = /media/myfiles (folder shared from server)
             writeable=yes (can create or edit files
             public=no (only accessible to authorized users)
         > run the following to set up a password for samba: sudo smbpasswd -a youruser
         > restart samba: sudo systemctl restart smbd

3. Duckdns setup:
  - Go to duckdns.org and sign in. Create the domain name and click install
  - Select Linux Cron and follow listed instruction

4. Wireguard Installation & Setup
  - Run curl -L https://install.pivpn.io | bash and follow the prompts
  - To give a name to the client, run pivpn add
  - Next run pivpn -qr to display a QR code
  - Open Wireguard from your phone and scan the qr code
  - Toggle switch to route all traffic through there

###Lessons learnt
-  Auto Boot Requires Power Cord
    *  The server doesn’t support true headless autoboot without a connected power source.
    *  A reliable power supply is essential — even when running headless via SSH or VPN.

-  Port Forwarding Is Crucial
    *  Port forwarding is a router setting that allows traffic from the internet to reach specific devices on one's home network.
    *  Services like WireGuard and Samba (via VPN) require port forwarding on the home router:
        >  Forward UDP 51820 for WireGuard
