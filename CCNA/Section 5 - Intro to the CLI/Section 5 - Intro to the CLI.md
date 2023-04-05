## Cisco iOS CLI
- iOS is the OS that runs on cisco hardware
- This is not related to iOS that runs Apple Products

## What is a CLI?
- Command Line interface
- the interface you use to configure Cisco devices, like routers, switches, and firewalls
- there is also a GUI, which is a graphical method instead of a CLI
- Most network engineers prefer to use CLI
### Connecting via console port
- When you first configure a device, you will need to use a console port. These ports can be usb, rj45, or db-9.
- an RJ45 to DB-9 port is called a **Rollover Cable**
- to access the CLI, you will need to use a terminal emulator and open a **serial** connection
	- speed: 9600
	- data bits: 8
	- stop bits: 1
	- parity: none
	- Flow control: none
- Once you connect, you will be greeted with a CLI
### User EXEC Mode
- you will start in user exec mode. It is denoted by a > symbol
```
Router>
```
- User exec mode is limited. 
- Users can look at some things, but can't make config changes
- sometimes called user mode
### Priveleged EXEC mode
```
Router>enable
Router#
```
- type "enable" to access privileged EXEC mode
- complete access to view the devices config, restart the device, etc
- this is not the mode to change the config, but you can change the time, save the config file, etc
### Global Configuration Mode
```
Router>enable
Router#configure terminal
Enter configuration commands, one per line. End with CNTL/Z.
Router(config)#
```
- The mode to change configurations is global configuration mode
- type configure terminal to enter this mode
- you can also simply enter conf t to enter global configuration mode as a shortcut
#### Enable Password
```
Router(config)#enable password?
password
Router(config)#enable password ?
	7    Specifies a HIDDEN password will follow
	LINE   The UNENCRYPTED (cleartext) 'enable' password
	level   Set exec level password
Router(config)#enable password CCNA ?
	<cr>
Router(config)#enable password CCNA
Router(config)#
```
- we dont want just anyone to be able to change configurations on our routers and switches, so we enable a **password**
- We protect privileged exec mode with **enable password**
- NOTE: if you use a question mark with no space, it shows completions. If you use a question mark with a space, it gives info for the command
##### Service password-encryption
- encrypts passwords witha  jumble of numbers, so they cannot be read from the config.
- there is a number in front of the encryption. In the case of 7, it uses Cisco proprietary encryption
- current and future passwords will be encrypted
- no effect on enable secret
##### disabling service password-encryption
- future passwords will **not** be encrypted
- current password will be unaffected
### enable secret
- instead of using cisco proprietary encryption, it uses MD5 encryption
- secrets are **always** encrypted
## running-config/startup-config
- There are two seperate configuration files kept on the device at once
### Running-config
	the current, active config file on the device. As you enter commands in the CLI, you edit the active configuration.
### Startup-config
	The config that is loaded on restart. If you restart the device, this is the config that will load
### Saving the config
These commands are run in privileged exec mode. All of these commands perform the sane function
- Write
- write memory
- copy running-config startup-config
## Important Commands

### ?
```
Router>?
```
use a question mark to view available commands

### Enable Password
- enables password for privileged modes

### enable secret
- create MD5 pass

### Conf t
- enter config mode

### show running-config
- view current running config
- sh run

### show startup-config
- view current startup config

### write
- save running-config as startup-config

### do
- allows running commands in other configuration levels

### no
- undo command

### Tab Completion
use tab completion to ensure speed and accuracy when typing commands

