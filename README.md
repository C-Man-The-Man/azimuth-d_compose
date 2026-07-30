# Azimuth Docker Compose Community Edition

This repository originally started as a community Docker deployment while the official Azimuth Docker documentation was still under development.

Since then, the Azimuth team has released their official Docker guide and kindly listed this repository as a community resource on the official website.

Today, this repository focuses on providing community-tested Docker deployments, scanner configurations and regional scanner profiles while continuing to use the **official Azimuth Docker images** without modifications.

Official Docker documentation:

https://azimuth.day/docs/docker

---

# Support my work

If this repository helped you get your node running, consider joining Azimuth using my referral:

## https://azimuth.day/login?ref=cmantheman

After registering:

- Login to your Dashboard
- Go to **Settings**
- Generate a new **API Key**

The API key will be required when editing the Docker Compose file.

---

# Repository Structure

```
.
├── README.md
├── docker-compose.arm64.yml
├── docker-compose.amd64.yml
├── config.toml
│
└── regional-scanner-configs/
    ├── SCANNER-README.md
    ├── europe-generic.toml
    └── usa-canada.toml
```

This repository intentionally separates:

- Docker deployment
- Generic scanner configuration
- Region-specific scanner configurations

making it easier to maintain and expand.

---

# Requirements

- Docker Engine
- Docker Compose
- RTL-SDR Blog V3 / V4 (or compatible RTL2832U SDR)
- Raspberry Pi OS / Debian / Ubuntu
- (Optional) USB GPS receiver

---

# Install RTL-SDR

```bash
sudo apt update
sudo apt install rtl-sdr
```

Verify your SDR:

```bash
rtl_test -t
```

Expected output:

```
Found Rafael Micro R828D tuner
```

If the SDR is not detected here, Docker will not be able to access it either.

---

# Optional: Install udev Rules

If your SDR already works correctly, this step can be skipped.

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

Reload:

```bash
sudo udevadm control --reload-rules
sudo udevadm trigger
```

Reconnect the SDR if necessary and verify again:

```bash
rtl_test -t
```

---

# Installation

Create a working directory:

```bash
mkdir azimuth && cd azimuth
```

---

# Download the Official Docker Image

## Raspberry Pi 4 / Raspberry Pi 5 or similar (ARM64)

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

---

# Download the Generic Scanner Configuration

Download the generic scanner configuration:

```bash
curl -LO https://raw.githubusercontent.com/C-Man-The-Man/azimuth-d_compose/main/config.toml
```

This generic configuration works in most locations and serves as the default scanner profile.

Regional scanner configurations are available inside the `regional-scanner-configs` directory.

---

# Docker Compose

Open the compose file:

```bash
nano docker-compose.yml
```

Edit:

- AZIMUTH_API_KEY
- AZIMUTH_NODE_LABEL (optional)
- AZIMUTH_NODE_LAT
- AZIMUTH_NODE_LON
- AZIMUTH_NODE_ALT

Save and exit.

---

# Regional Scanner Configurations

The included `config.toml` is a generic scanner configuration.

This repository also contains region-specific scanner configurations based on publicly available national spectrum allocation documentation.

For example, to use the Spain scanner profile:

```bash
curl -LO https://raw.githubusercontent.com/C-Man-The-Man/azimuth-d_compose/main/regional-scanner-configs/spain.toml
mv spain.toml config.toml
```

Likewise, any other regional profile can be used by replacing the filename in the download command.

See:

```
regional-scanner-configs/
```

and

```
regional-scanner-configs/SCANNER-README.md
```

for additional information.

---

# Start the Node

```bash
docker compose up -d
```

View the logs:

```bash
docker compose logs -f
```

---

# Notes

- Docker volumes preserve your node identity.
- Replacing `config.toml` does **not** register a new node.
- To regenerate the node configuration from the environment variables:

```bash
docker compose down
docker volume rm azimuth-data
docker compose up -d
```

---

# Scanner Configurations

The scanner configuration included with this repository is intentionally generic.

Regional scanner configurations are maintained separately.

Whenever possible, regional profiles are based on official spectrum allocation documentation published by each country's telecommunications authority instead of estimated or community-guessed frequencies.

This ensures the repository remains technically accurate and easy to maintain.

Community contributions are welcome once verified on real hardware.

---

# Remove Everything

```bash
docker compose down
```

```bash
docker volume rm azimuth-data
```

ARM64

```bash
docker rmi azimuth-node:latest-arm64
```

AMD64

```bash
docker rmi azimuth-node:latest-amd64
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

For more technical discussions, Docker updates and new DePIN projects, join the **Financial Freedom** Discord community:

https://discord.gg/wY3N2hCT3u

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

**Thank you!**
