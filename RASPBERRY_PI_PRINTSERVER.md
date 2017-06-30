## Setup a Raspberry Pi print server

These are the steps to setup a *HP JetDirect* print server on your *Raspberry Pi*

1. Update the software on your *Raspberry Pi*
```bash
sudo apt-get update
sudo apt-get upgrade
```
2. Install **CUPS**
```bash
sudo apt-get install cups
```
3. Let the `pi` user be part of the `lpadmin` group
```bash
sudo usermod -a -G lpadmin pi
```
4. Let the CUPS webinterface be accessible by any remote client (but have a second thought on this setting, if the Raspberry Pi is accessible from the internet)
```bash
sudo cupsctl --remote-any
```
5. Restart CUPS
```bash
sudo /etc/init.d/cups restart
```
6. In the browser open: `http://<raspberry pi's ip-address>:631` and
    - Add new printer
    - Choose "Other Manufacturer" > Raw"
    - Choose "none" and "none"
7. Install xinetd if necessary
```bash
sudo apt-get install xinetd
```
8. Make sure `/etc/services` has the following line:
```bash
jetdirect 9100/tcp laserjet hplj
```
9. Create the file `/etc/xinetd.d/jetdirect` with the following contents:
```bash
service jetdirect
{
	socket_type = stream
	protocol = tcp
	wait = no
	user = lp
	server = /usr/bin/lp
	server_args = -d <CUPS_PRINTER_NAME> -o raw
	groups = yes
	disable = no
}
```
10. Restart xinetd and cups
```bash
service xinetd restart
service cups restart
```