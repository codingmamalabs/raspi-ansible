# raspi-ansible
This is a simple Ansible setup to configure a Raspberry Pi Zero W as web server.

This repository contains two roles, one to configure the SSH access for easier access and another role to set up a PHP 7 web server running Nginx + PHP7-FPM.

## Step 1: Set Up Connectivity
The first thing we need to do is to test connectivity and prepare for the first Ansible run. Since this is a new Raspbian install, 
it has the default user and password ( u:**pi** / p:**raspberry** ) that comes with all fresh installs. We will use these for the SSH setup in the next step. 

But first of all, you'll need to edit the *inventory file* (a file named `hosts` in the project's root directory) and change the IP address there to reflect your
Raspberry Pi Zero W IP address. The *inventory file* contains, as the name suggests, the inventory of servers to be controlled by Ansible. This is how our inventory file looks like:

```ini
[raspi01]
192.168.0.27

[webservers:children]
raspi01
```

Change `192.168.0.27` to the IP address of your Raspberry Pi Zero W.
After changing that, let's just run a command to test connectivity and show information about your Raspberry Pi Zero:

```
ansible webservers -k -m setup
```
This command will ask for the SSH password - use **raspberry**. After a few seconds it should print a big json containing all the facts collected by Ansible. Example output (excerpt):

```json
SSH password: 
192.168.0.27 | SUCCESS => {
    "ansible_facts": {
        "ansible_all_ipv4_addresses": [
            "192.168.0.27"
        ], 
        "ansible_all_ipv6_addresses": [
            "fd00:f0f2:4990:a2:3d6e:857e:cafe:681", 
            "fe80::f745:81d2:91a6:55a2"
        ], 
        "ansible_architecture": "armv6l", 
        "ansible_bios_date": "NA", 
        "ansible_bios_version": "NA", 
        "ansible_cmdline": {
            "8250.nr_uarts": "0", 
            "bcm2708_fb.fbheight": "984", 
            "bcm2708_fb.fbswap": "1", 
            "bcm2708_fb.fbwidth": "1824", 
            "console": "tty1", 
            "dwc_otg.lpm_enable": "0", 
            "elevator": "deadline", 
            "fsck.repair": "yes", 
            "plymouth.ignore-serial-consoles": true, 
            "quiet": true, 
            "root": "PARTUUID=5ffcb831-02", 
            "rootfstype": "ext4", 
            "rootwait": true, 
            "smsc95xx.macaddr": "B8:27:EB:7F:A1:46", 
            "splash": true, 
            "vc_mem.mem_base": "0x1ec00000", 
            "vc_mem.mem_size": "0x20000000"
```

If you got an error, maybe you need to install `sshpass` in order to be able to provide a password for Ansible. Check the error message to see if that's the case. 
If you already have `sshpass` installed and the error is something else, try adding -vvvv to make the command extra verbose.

If you got the output, it means connectivity is fine, and Ansible is capable of running commands on your Raspberry system. You can go ahead to the next step.

## Step 2: Set Up SSH Access

We are going to run a playbook this time. It has a few tasks to configure the server and it will copy your current user key to the user **pi** inside the Raspberry, so you can log in simply 
by running `ssh pi@your-pi-address` , no need for passwords. This will also facilitate running Ansible in the future, to avoid having to provide the password all the time!

```
ansible-playbook -k setup.yml
```

After this playbook is executed, you should be able to connect via SSH to the Raspberry Pi without the need to provide a password.

## Step 3: Run the webserver Playbook
Now, finally for the web server playbook.

```
ansible-playbook webserver.yml
```

After this playbook run is finished, you should have a working PHP 7 web server running on your Raspberry Pi Zero W. Go to your browser and point it to the Rasp Pi address, you should see something like this:


### Troubleshooting

If you get any Ansible errors, add `-vvvv` to the command to increase verbosity. This will help a lot!
