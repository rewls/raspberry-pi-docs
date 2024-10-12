# Remote access

## Introduction to remote access

- Sometimes you need to access a Raspberry Pi without connecting it to a monitor, keyboard, and mouse.

### Remote control over the local network

- To remotely control your Raspberry Pi from another device on your local network, use one of the following services:

    - SSH

    - VNC

    - Raspberry Pi Connect

- SSH (Secure SHell) provides secure access to a terminal session on your Raspberry Pi.

- VNC (Virtual Network Computing) provides secure access to a desktop screen share on your Raspberry Pi.

- All you need is another computer, a local network, and the local IP address of your Raspberry Pi.

### Share files between devices over the local network

- Services like NFS (Network File System), SCP (Secure Copy Protocol), Samba, and rsync enable you to share files between devices on the local network without directly controlling the remote device.

### Remote control over the Internet

- To remotely control your Raspberry Pi from any device connected to the Internet, you can:

    - Expose SSH or VNC on your Raspberry Pi over the open internet, within a VPN, or using an external service like RealVNC’s cloud VNC Viewer.

    - Use Raspberry Pi Connect, a free screen sharing service provided by Raspberry Pi.

## Find the IP address of your Raspberry Pi

### Desktop

- Hover over the network icon in the system tray, and a tooltip will appear.

- This tooltip displays the name of the network you're currently connected to and your IP address.

### Boot output

- If you use a display with your Raspberry Pi and you boot to the command line instead of the desktop, the boot sequence includes your IP address as one of the last few output messages before your login prompt.

### Resolve `raspberry.local` with mDNS

- Raspberry Pi OS supports multicast DNS as part of the Avahi service.

<br>

- If your device supports mDNS, you can reach your Raspberry Pi by using its hostname and the `.local` suffix.

- The default hostname on a fresh Raspberry Pi OS install is `raspberrypi`, so by default any Raspberry Pi running Raspberry Pi OS responds to:

    ```sh
    $ ping raspberrypi.local
    ```

- If the Raspberry Pi is reachable, `ping` will show its IP address:

## Access a remote terminal with SSH

- You can access the terminal of a Raspberry Pi remotely from another computer on the same network using the Secure SHell (SSH) protocol.

### Enable the SSH server

- By default, Raspberry Pi OS disables the SSH server.

- Enable SSH in one of the following ways:

    - On the desktop

        1. From the Preferences menu, launch Raspberry Pi Configuration.

        2. Navigate to the Interfaces tab.

        3. Select Enabled next to SSH.

        4. Click OK.

    - While flashing a fresh OS image

        1. Follow the instruction in the Install with Imager guide.

        2. During the OS Customisation step, navigate to the Services tab.

        3. Tick the checkbox to Enable SSH.

        4. Select password authentication to log in using the same username and password you use while physically using your Raspberry Pi.

### Connect to an SSH server

- Open a terminal window on your computer and enter the following command, replacing the `<ip address>` placeholder with the IP address of the Raspberry Pi you're trying to connect to and `<username>` with your username:

    ```sh
    $ ssh <username>@<ip address>
    ```

#### Forward X11 over SSH

> ##### Note
>
> - On Raspberry Pi 4 and 5, Raspberry Pi OS bookworm uses the Wayland window server by default.
>
> - You can only forward X11 if you use the X window server.
>
> - To enable window forwarding over X11, switch your desktop to the X window server in Raspberry Pi Configuration.

> ##### Note
>
> - X11 is no longer installed by default on many desktop environments.
>
> - Install a third-party X server such as XQuartz to use X11 forwarding.

- X11 enables graphical applications over SSH.

- Pass the `-Y` flag to forward an X session over SSH.

- Once authenticated, you will see the command line as usual.

- However, you can also open graphical windows that an X server can render for you.

### Configure SSH without a password

- To remotely access your Raspberry Pi without providing a password each time you connect, use an SSH key pair.

#### Preconfigure an OS image with Raspberry Pi Imager

- When configuring boot image with Raspberry Pi Imager, you can preconfigure SSH keys.

- You can generate a new SSH keypair or an existing SSH key.

    1. Follow the install using Imager guide to configure your boot image.

    2. During the OS Customisation step, navigate to the Services tab and tick the Enable SSH checkbox.

    3. Select the Allow public-key authentication only radio button.

        - If you already have an SSH public key stored in `~/.ssh/id_rsa.pub`, Imager automatically uses that public key to prefill the text box.

        - If Imager doesn't find an SSH public key, you can click the RUN SSH-KEYGEN button to generate a new keypair.

#### Manually configure an SSH key

- If you already have an installation of Raspberry Pi OS, you can update your existing configuration to use SSH key authentication.

#### Check for existing SSH public keys

- To check for an existing SSH public key on the computer you use to remotely connect to the RAspberry Pi, run the following command:

    ```sh
    $ ls ~/.ssh
    ```

- If you see file named `id_ed25519.pub`, `id_rsa.pub`, or `id_dsa.pub`, you already have an SSH key.

##### Generate new SSH keypair

> ##### Tip
>
> - This guide provides instructions to generate a new RSA key.
>
> - For additional security, you can instead generate a Ed25519 key.
>
> - Pass `-t ed25519` to `ssh-keygen` and replace `rsa` with `ed25519` when referencing your public and private key file names to use an Ed25519 key.

- To generate a new SSH keypair, enter the following command:

    ```sh
    $ ssh-keygen
    ```

- When asked where to save the key, press Enter to use the default location, `~/.ssh/id_rsa`.

- When asked for an optional keyphrase, press Enter to use no keyphrase.

- Run the following command to check the contents of the `.ssh` directory:

    ```sh
    $ ls ~/.ssh
    ```

- You should see the files `id_rsa` and `id_rsa.pub`:

- The `id_rsa` file contains your private key.

- Keep this secure on the computer you use to remotely connect to the Raspberry Pi.

- The `id_rsa.pub` file contains your public key.

- You will share this key with your RaspberryPi.

- When you connect with the Raspberry Pi remotely, it will use this key to verify your idneity.

#### Add the SSH key to your list of SSH identities

- Start the SSH agent:

    ```sh
    $ eval "$(ssh-agent -s)"
    ```

- Next, add your key identities to `ssh-agent` with the following command:

    ```sh
    $ ssh-add ~/.ssh/id_rsa
    ```

##### Copy a public key to your Raspberry Pi

- On the computer you use to remotely connect to the Raspberry Pi, use the following command to securely copy your public key to the Raspberry Pi:

    ```sh
    $ ssh-copy-id <username>@<ip address>
    ```

- When prompted, enter the password for your user account on the Raspberry Pi.

- You can now connect to your Raspberry Pi without entering a password.

## Share files with SCP

- Secure Copy Protocol (`scp`) sends files over SSH.

- You can use `scp` to copy files between your Raspberry Pi and another computer.

- To use `scp`, find your Raspberry Pi's IP address.

### Copy files to your Raspberry Pi

- To copy a file named `myfile.txt` from your personal computer to a user's home folder on your Raspberry Pi, run the following command from the direct containing `myfile.txt`, replacing the `<username>` placeholder with the username you use to log in to your Raspberry Pi and the `<pi_ip_address>` placeholder with your Raspberry Pi's IP address:

    ```sh
    $ scp myfile.txt <username>@<pi_ip_address>:
    ```

- To copy a file to a specific directory, append the directory path after the `:` in the `scp` command.

- Create the folder before you run `scp`, since `scp` won't create folders automatically.

### Copy files from your Raspberry Pi

- To copy a file named myfile.txt from a user's home directory on a Raspberry Pi to the current directory on another computer, run the following command:

    ```sh
    $ scp <username>@<pi_ip_address>:myfile.txt .
    ```

### Copy multiple files with one command

- To copy multiple files, list the file names in a single command, separated by spaces.

## Synchronise folders between computers with `rsync`

- You can use `rsync` to synchronise folders between computers.

- For example, you could use `rsync` to transfer new pictures taken by your Raspberry Pi to your personal computer automatically.

- Before you can configure `rsync`, determine values for the following:

    - `<pi_ip_address>`: your Raspberry Pi's local IP address

    - `<pi_username>`: the username you use to log into your Raspberry Pi

    - `<pi_folder_name>`: the name of the folder you want to copy files from on your Raspberry Pi

    - `<pc_folder_name>`: the name of the folder you would like to synchronise on your personal computer.

- To configure `rsync` to synchronise files, complete the following steps on your personal computer, replacing placeholders in the commands with the values you determined above:

    1. Create the folder you would like to synchronise to:

        ```sh
        $ mkdir <pc_folder_name>
        ```

    2. Synchronise files to the folder with `rsync`:

        ```sh
        $ rsync -avz -e ssh <pi_username>@<pi_ip_address>:<pi_folder_name>/ <pc_folder_name>/
        ```

- This command copies all files from the selected folder on your Raspberry Pi to the selected folder on your personal computer.

- If you run the command multiple times, `rsync` keeps track of the files you have already downloaded and skips them.

- If you delete or modify an already synchronised file on the Raspberry Pi, `rsync` updates the files on your personal computer accordingly.

## Network File System (NFS)

- Network File System (NFS) allows you to share a directory located on one networked computer with other computers or devices on the same network.

- The computer where the directory is located is called the server, and computers or devices connecting to that server are called clients.

- Clients usually `mount` the shared directory to make it a part of their own directory structure.

- The shared directory is an example of a shared resource or network share.

<br>

- NFS is a popular way to create a simple NAS (Network-attached storage) in a Linux/Unix environment.

<br>

- An NFS is perhaps best suited to more permanent network-mounted directories, such as `/home` directories or regularly-accessed shared resources.

- If you want a network share that guest users can easily connect to, Samba is better suited to the task.

- Tools to temporarily mount and detach from Samba shares are more readily available across operating systems.

- Before deploying an NFS, you should be familiar with:

    - Linux file and directory permissions

    - mounting and unmounting filesystems

### Set up a basic NFS server

- Install the packages required using the command below:

    ```sh
    $ sudo apt install nfs-kernel-server
    ```

- For easier maintenance, we will isolate all NFS exports in single directory, into which the real directories will be mounted with the `--bind` option.

- Suppose we want to export our our users' home directories, which are in `/home/users`.

- First we create the export filesystem:

    ```sh
    $ sudo mkdir -p /export/users
    ```

- Grant `/export` and `/export/users` read, write, and execute permissions so you can access the NFS share from the client without LDAP/NIS authentication:

    ```sh
    $ chmod -R 777 /export
    ```

- Next, mount the real `users` directory with:

    ```sh
    $ sudo mount --bind /home/users /export/users
    ```

- To save us from retyping this after every reboot, we add the following line to `/etc/fstab`:

    ```
    /home/users    /export/users   none    bind  0  0
    ```

- There are three configuration fiels that relate to an NFS server:

    1. `/etc/default/nfs-kernel-server`

    2. `/etc/default/nfs-common`

    3. `/etc/exports`

- The only important option in `/etc/default/nfs-kernel-server` for now is `NEED_SVCGSSD`.

- It is set to `"no"` by default, which is fine, because we are not activating NFSv4 security this time.

<br>

- In order for the ID names to be automatically mapped, the file `/etc/idmapd.conf` must exist on both the client and the server with the same contents and with the correct domain names.

- Furthermore, this file should have the following lines in the `Mapping` section:

    ```
    [Mapping]

    Nobody-User = nobody
    Nobody-Group = nogroup
    ```

- However, note that the client may have different requirements for the Nobody-User and Nobody-Group.

- For example, on RedHat variants, it is `nfsnobody` for both.

- If you're not sure, check via the following commands to see if `nobody` and `nogroup` are there:

    ```sh
    $ cat /etc/passwd
    $ cat /etc/group
    ```

- This way, server and client do not need the users to share same UID/GUID.

- To export our directories to a local network `192.168.1.0/24`, add the following two lines to `/etc/exports`:

    ```
    /export       192.168.1.0/24(rw,fsid=0,insecure,no_subtree_check,async)
    /export/users 192.168.1.0/24(rw,nohide,insecure,no_subtree_check,async)
    ```

### Configure an NFS client

- Now that your server is running, you need to set up any clients to be able to access it.

- To start, install the required packages:

    ```sh
    $ sudo apt install nfs-common
    ```

- On the client, we can mount the complete export tree with one command:

    ```sh
    $ mount -t nfs -o proto=tcp,port=2049 <nfs-server-IP>:/ /mnt
    ```

- You can also specify the NFS server hostname instead of its IP address, but in this case you need to ensure that the hostname can be resolved to an IP on the client side.

- A robust way of ensuring that this will always resolve is to use the `/etc/hosts` file.

- Note that `<nfs-server-IP>:/export` is not necessary in NFSv4, as it was in NFSv3.

- The root export `:/` defaults to export with `fsid=0`.

- We can also mount an exported subtree with:

    ```sh
    $ mount -t nfs -o proto=tcp,port=2049 <nfs-server-IP>:/users /home/users
    ```

- To ensure this is mounted on every reboot, add the following line to `/etc/fstab`:

    ```
    <nfs-server-IP>:/   /mnt   nfs    auto  0  0
    ```

- If, after mounting, the entry in `/proc/mounts` appears as `<nfs-server-IP>://`, then you might need to specify two slashes in `/etc/fstab`, or else `umount` might complain that it cannot find the mount.
