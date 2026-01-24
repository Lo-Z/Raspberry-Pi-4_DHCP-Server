# A Raspberry Pi 4 Image for a quick on-the-spot DHCP server 
A **plug-and-play, headless Raspberry Pi DHCP server** designed for **temporary onboarding, commissioning, and lab environments**.

Power it via PoE or USB, plug it into a switch, and it immediately begins handing out IP addresses.  
Unplug it when you’re done and move to the next site.

### Useful Materials:

Hardware:

- [Raspberry Pi 4 2Gb](https://www.microcenter.com/product/621439/raspberry-pi-4-model-b)

- [Raspberry Pi 4 PoE Hat](https://www.microcenter.com/product/688720/raspberry-pi-poe-for-pi-3b-and-4b)

Downloads:

- [Raspberry Pi Imager](https://www.raspberrypi.com/software/)

---

## 🔧 Use Cases

- Network onboarding / commissioning  
- WAP and switch configuration  
- Isolated lab environments  
- Field troubleshooting  
- AV / IT pre-staging  
- Temporary DHCP where no router exists  

---

## ✅ Features

- Headless (SSH only)
- Starts DHCP automatically on boot
- No gateway or DNS required
- Designed for isolated networks
- Stable static IP (no NetworkManager flapping)
- Disposable / portable by design

---

## ⚙ Default Configuration

### Credentials
- **User:** `pi`
- **Password:** `admin`
- **Hostname:** `dhcpserver`

### Network
- **IP Address:** `192.168.3.254`
- **Subnet:** `255.255.255.0 (/24)`
- **Gateway:** None
- **DNS:** None

### DHCP Pool
- **Range:** `192.168.3.100 – 192.168.3.250`
- **Lease Time:** `12 hours`

---

Download the ``` RPI4_DHCP_Server.img.xz ``` from the Release section or click [Here](https://github.com/Lo-Z/Raspberry-Pi-4_DHCP-Server/releases/download/RPI4_Image/RPI4_DHCP_Server.img.xz)

---

## 🚀 Quick Start

### - Firstly, unzip the image.

On Windows you can use Winrar or 7zip to extract the file.

for Linux or Mac use the following CLI command inside the directory which the "RPI4_DHCP_Server.img.xz" file is stored
```bash
xz -d RPI4_DHCP_Server.img.xz
```

### - Next flash the .img

1. Flash the image using **Raspberry Pi Imager**
   - Choose **“Use custom”**
   - Select the provided `RPI4_DHCP_Server.img` file
   - Select the SD card (image originally written on a 32Gb SD)
   - Write the SD 
2. Insert the SD card into a Raspberry Pi 4
3. Connect Ethernet to a switch
4. Apply power (PoE or USB)
5. Devices on the network will immediately receive IP addresses

### Note: Do not set up the Wi-Fi or change custom configurations within the Raspberry Pi Imager
---

# Changing The Default Network Settings

## 1. Connect via SSH

```bash
ssh pi@192.168.3.254
```
If hostname resolution is available:
```bash
ssh pi@dhcpserver
```
---


## 2. Update the IP address

Change the Pi's IP / Subnet on ipv4.addresses line

Example: set the Pi to 192.168.10.254/24

```bash
sudo nmcli connection modify netplan-eth0 \
  ipv4.method manual \
  ipv4.addresses 192.168.10.254/24 \
  ipv4.gateway "" \
  ipv4.dns "" \
  ipv4.never-default yes
```


Apply the changes with a reboot:

```bash
sudo reboot now
```

Now Reconnect your ssh connection using the new IP address.
---


## 3. Change the DHCP Pool
Edit the dnsmasq configuration file:


```bash
sudo nano /etc/dnsmasq.d/dhcp-only.conf
```

Edit the dhcp-range line "dhcp-range=start_IP, end_IP, Subnet, Lease time in hours":
```bash
dhcp-range=192.168.10.100,192.168.10.250,255.255.255.0,12h
```

Restart DHCP:

```bash
sudo systemctl restart dnsmasq
```
---

## 4. Verification
Check the Pi’s IP address:

```bash
ip -4 addr show eth0
```

Check DHCP service status:

```bash
sudo systemctl is-active dnsmasq
```

Expected result:
```bash
active
```

## 5. View Current Network Settings
```bash
nmcli -f ipv4.method,ipv4.addresses,ipv4.gateway,ipv4.dns,ipv4.never-default connection show netplan-eth0
```

Example output:
```bash
text
Copy code
ipv4.method:            manual
ipv4.addresses:         192.168.3.254/24
ipv4.gateway:           --
ipv4.dns:               --
ipv4.never-default:     yes
```

⚠️ Notes & Warnings
- Only one DHCP server per network

- Designed for temporary use

- No routing, NAT, or DNS provided

- Change the default password if security matters!
---

🧠 Design Philosophy
- This image is intentionally minimal:

- No web UI

- No routing

- No persistence assumptions

- It exists to solve one problem cleanly: “I need DHCP right now.”
