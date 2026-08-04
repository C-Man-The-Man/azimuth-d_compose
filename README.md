# Azimuth Docker Compose Community Edition

This repository provides a community-maintained Docker Compose deployment for the official **Azimuth Tier 1 SDR Node**.

Since the release of the official Docker image and documentation, this repository closely follows the upstream deployment while providing:

- Raspberry Pi tested Docker Compose files
- Separate ARM64 and AMD64 deployments
- Additional installation and troubleshooting notes
- Community support

The Docker image itself is **not modified** and is always downloaded directly from the official Azimuth website.

Official Docker documentation:

https://azimuth.day/docs/docker

---

# Support my work

If this repository helped you get your node running, consider joining Azimuth using my referral:

## https://azimuth.day/login?ref=cmantheman

After registering:

- Login to your Dashboard
- Open **Settings**
- Generate a new **API Key**

Your API key is required before starting the node.

---

# Repository Structure

```
.
├── HISTORY.md
├── LICENSE
├── README.md
├── docker-compose.arm64.yml
└── docker-compose.amd64.yml
```

Both Docker Compose files closely follow the official deployment while remaining easy to understand and modify.

---

# Requirements

- Docker Engine
- Docker Compose
- RTL-SDR Blog V3 or V4 (RTL2832U compatible)
- Raspberry Pi OS / Debian / Ubuntu
- Optional USB GPS receiver

---

# Install RTL-SDR Tools

```bash
sudo apt update
sudo apt install rtl-sdr
```

Verify that your SDR is detected:

```bash
rtl_test -t
```

Expected output:

```
Found Rafael Micro R828D tuner
```

If your SDR is not detected here, Docker will not be able to access it either.

Stop `rtl_test` before starting the container, since only one process can access the SDR at a time.

---

# Blacklist the DVB Driver

Most Linux distributions automatically load the DVB television driver, preventing the RTL-SDR from being used.

Blacklist it once:

```bash
echo "blacklist dvb_usb_rtl28xxu" | sudo tee /etc/modprobe.d/blacklist-rtlsdr.conf
```

Unload the driver immediately:

```bash
sudo modprobe -r dvb_usb_rtl28xxu
```

or simply reboot the system.

---

# Optional udev Rules

Most Raspberry Pi installations work without this step.

Only configure udev rules if your SDR has permission issues.

Create:

```bash
sudo nano /etc/udev/rules.d/99-rtl-sdr.rules
```

Paste:

```udev
# RTL-SDR USB permissions

SUBSYSTEMS=="usb", ATTRS{idVendor}=="0bda", ATTRS{idProduct}=="2832", MODE="0666"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="0bda", ATTRS{idProduct}=="2838", MODE="0666"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="0bda", ATTRS{idProduct}=="2830", MODE="0666"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="0bda", ATTRS{idProduct}=="2836", MODE="0666"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="0bda", ATTRS{idProduct}=="283c", MODE="0666"

# Optional u-blox USB GPS receiver

SUBSYSTEMS=="usb", ATTRS{idVendor}=="1546", ATTRS{idProduct}=="01a7", MODE="0666"
```

Reload the rules:

```bash
sudo udevadm control --reload-rules
sudo udevadm trigger
```

Reconnect the SDR if necessary and verify it again:

```bash
rtl_test -t
```

---

# Installation

Create a working directory:

```bash
mkdir azimuth
cd azimuth
```

---

# Download the Official Docker Image

## ARM64 (Raspberry Pi 4 / Raspberry Pi 5)

```bash
curl -LO https://azimuth.day/downloads/azimuth-node-docker-arm64.tar.gz
docker load < azimuth-node-docker-arm64.tar.gz
```

Download the ARM64 Docker Compose file:

```bash
curl -LO https://raw.githubusercontent.com/C-Man-The-Man/azimuth-d_compose/main/docker-compose.arm64.yml
mv docker-compose.arm64.yml docker-compose.yml
```

---

## AMD64

```bash
curl -LO https://azimuth.day/downloads/azimuth-node-docker-amd64.tar.gz
docker load < azimuth-node-docker-amd64.tar.gz
```

Download the AMD64 Docker Compose file:

```bash
curl -LO https://raw.githubusercontent.com/C-Man-The-Man/azimuth-d_compose/main/docker-compose.amd64.yml
mv docker-compose.amd64.yml docker-compose.yml
```

---

Verify the image:

```bash
docker images
```

You should see:

```
azimuth-node    latest
```

---

# Configure Docker Compose

Open the compose file:

```bash
nano docker-compose.yml
```

At minimum configure:

- `AZIMUTH_API_KEY`
- `AZIMUTH_NODE_LABEL`
- `AZIMUTH_NODE_LAT`
- `AZIMUTH_NODE_LON`
- `AZIMUTH_NODE_ALT`

Optionally set your region:

```
AZIMUTH_REGION=ES
```

Replace `ES` with your own ISO country code if necessary.

---

# Start the Node

```bash
docker compose up -d
```

Follow the logs:

```bash
docker compose logs -f
```

---

# First Boot

During the first startup the container automatically generates:

```
/var/lib/azimuth/config.toml
```

using the `AZIMUTH_*` environment variables defined in the compose file.

The generated configuration and node identity are stored inside the Docker volume.

After the first startup, the environment variables are ignored and the stored configuration is reused.

---

# Regenerating the Configuration

If you need to regenerate `config.toml` from the environment variables:

```bash
docker compose down -v
docker compose up -d
```

⚠️ **Warning**

Removing the Docker volume also removes your node identity.

Your node will register again as a new node.

---

# Updating the Node

When a new Docker image is released:

```bash
docker load < azimuth-node-docker-xxxx.tar.gz
docker compose up -d --force-recreate
```

Your configuration and node identity remain stored inside the Docker volume.

## Known Issues

If you download a new Docker image but the node still appears to be running an older version, verify the loaded image tags:

```bash
docker images
```
or inspect the version the latest tagged image

```bash
docker inspect azimuth-node:latest-arm64 | grep -i version
```

Some Azimuth releases have shipped with an incorrect `latest-arm64` tag. If this happens, either:

- wait for the corrected image, or
- temporarily change the image name in `docker-compose.arm64.yml` to the specific version tag (for example `azimuth-node:0.3.2-arm64`), or
- change the tag locally and re-run the container (recommended)
- 
```bash
docker tag azimuth-node:0.3.2-arm64 azimuth-node:latest-arm64
docker compose down
docker compuse up -d --force-recreate
```
(replace `azimuth-node:0.3.2-arm64` with the actual latest image version that is present on your system)

---

# Troubleshooting

### SDR not detected

Verify:

```bash
rtl_test -t
```

### USB permission issues

Verify that the correct USB group is configured inside `docker-compose.yml`.

### Container keeps restarting

Inspect the logs:

```bash
docker compose logs -f
```

Most startup issues are caused by:

- Missing API key
- Incorrect SDR permissions
- Invalid configuration
- USB driver conflicts

---

# Remove Everything

Stop the node:

```bash
docker compose down -v
```

Remove the Docker image:

```bash
docker rmi azimuth-node:latest
```

Remove the working directory:

```bash
cd ..
rm -rf azimuth
```

---

# Tested Hardware

- Raspberry Pi 4B
- Raspberry Pi OS
- RTL-SDR Blog V4
- u-blox USB GPS receiver

---

Happy scanning! 📡🚀

For Docker discussions, DePIN projects and technical support, join the **Financial Freedom** Discord community:

https://discord.gg/wY3N2hCT3u

---

## Project History

Interested in how this repository evolved?

See **[HISTORY.md](HISTORY.md)** for the background, design decisions, and how the repository changed from Azimuth v0.2.3 to the current release.

---

# Donations

**Bitcoin wallet address**
```text
bc1qpcfex53u7mqx4dc25gw7j7446amw9vn6743cn5
```

**EVM / Metamask  (ETH, ETC, OCTA, POL, PEAQ, MONAD, BASE etc.)**
```text
0xbE4879888d95B02B2FCaed2FcAeBbcf36829BDC9
```

**Solana wallet address**
```text
7EHWvShXfjLJ2HhzTf4CsHgjKckivfMQMjnEoUAEqau
```

**Sui wallet address**
```text
0x421a5a462f99c2d675d035d0c741ba5765a47c1e28f95d33ad770cd34a36a6ea
```

**Thank you for your support! ❤️**
