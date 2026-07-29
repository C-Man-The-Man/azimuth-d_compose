# Azimuth Docker Compose

This repository originally started as a community Docker deployment while the official Azimuth Docker documentation was still under development.

Since then, the Azimuth team has released their official Docker guide and kindly listed this repository as a community resource on the official website.

This project now complements the official documentation with community-tested configurations, examples and deployment tips while continuing to use the **official Azimuth Docker image** without modifications.

Official Docker documentation: https://azimuth.day/docs/docker

---

## Support my work

If this guide helped you get your node running, consider joining Azimuth using my referral:

### https://azimuth.day/login?ref=cmantheman

After registering:

- Login to your dashboard
- Go to **Settings**
- **Generate new API Key**

You'll need that API key later when editing the `docker-compose.yml`.
Note: The API key can only be copied until the webpage is navigated away.

---

# Requirements

- Docker Engine
- Docker Compose
- RTL-SDR Blog V3/V4 (or compatible RTL2832U SDR)
- (Optional) USB GPS receiver
- Raspberry Pi OS / Debian / Ubuntu (tested)

---

# Install RTL-SDR

```bash
sudo apt update
sudo apt install rtl-sdr
```

Verify your SDR is detected:

```bash
rtl_test -t
```

You should see something similar to:

```
Found Rafael Micro R828D tuner
```

If your SDR is **not** detected here, Docker will not be able to use it either.

---

# Optional: Add udev rules

If your RTL-SDR already works correctly, you can skip this step.

Create:

```bash
sudo nano /etc/udev/rules.d/99-rtl-sdr.rules
```

Paste:

```udev
# RTL-SDR USB device permissions

SUBSYSTEMS=="usb", ATTRS{idVendor}=="0bda", ATTRS{idProduct}=="2832", MODE="0666"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="0bda", ATTRS{idProduct}=="2838", MODE="0666"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="0bda", ATTRS{idProduct}=="2830", MODE="0666"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="0bda", ATTRS{idProduct}=="2836", MODE="0666"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="0bda", ATTRS{idProduct}=="283c", MODE="0666"

# Optional u-blox GPS receiver

SUBSYSTEMS=="usb", ATTRS{idVendor}=="1546", ATTRS{idProduct}=="01a7", MODE="0666"
```

Reload the rules:

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

Download and import the official Docker image.

## ARM64 (Raspberry Pi 4/5)

```bash
curl -LO https://azimuth.day/downloads/azimuth-node-docker-arm64.tar.gz
docker load < azimuth-node-docker-arm64.tar.gz
```

## AMD64

```bash
curl -LO https://azimuth.day/downloads/azimuth-node-docker-amd64.tar.gz
docker load < azimuth-node-docker-amd64.tar.gz
```

Confirm the image was imported:

```bash
docker images
```

---

# Docker Compose

Copy the `docker-compose.yml` from this repository.

The compose file intentionally stays close to the official release while adding additional explanations gathered from community testing.

Edit at least the following values before starting the container:

- AZIMUTH_API_KEY
- AZIMUTH_NODE_LAT
- AZIMUTH_NODE_LON
- AZIMUTH_NODE_ALT
- AZIMUTH_NODE_LABEL (optional)

If you are **not** using a USB GPS receiver, simply remove:

```yaml
- /dev/ttyACM0:/dev/ttyACM0
```

---

# Start the node

```bash
docker compose up -d
```

View the logs:

```bash
docker compose logs -f
```

A successful startup should look similar to:

```
azimuth  | [entrypoint] Generating /var/lib/azimuth/config.toml from AZIMUTH_* environment
azimuth  | 2026-07-24T16:46:40.648735Z  INFO azimuth_daemon: Config loaded from /var/lib/azimuth/config.toml
azimuth  | 2026-07-24T16:46:40.650318Z  INFO azimuth_daemon::status_server: Status server listening addr="0.0.0.0:8420"
azimuth  | 2026-07-24T16:46:40.693410Z  INFO azimuth_daemon: No node_id — attempting registration via API
azimuth  | 2026-07-24T16:46:42.364037Z  INFO azimuth_daemon::api_client: Node registered node_id=YOUR_API_KEY
azimuth  | 2026-07-24T16:46:42.365095Z  INFO azimuth_daemon: Node registered node_id=YOUR_API_KEY
azimuth  | 2026-07-24T16:46:42.391490Z  INFO azimuth_daemon::sdr: RTL-SDR devices found count=1
azimuth  | 2026-07-24T16:46:42.421200Z  INFO rtl_sdr_rs::device::device_handle: Opening device at index 0
azimuth  | 2026-07-24T16:46:42.641298Z  INFO rtl_sdr_rs::rtlsdr: Probing I2C address 0x34 checking address 0x0
azimuth  | 2026-07-24T16:46:42.641344Z ERROR rtl_sdr_rs::rtlsdr: Reading failed, continuing. Err: Pipe error
azimuth  | 2026-07-24T16:46:42.645265Z  INFO rtl_sdr_rs::rtlsdr: Probing I2C address 0x74 checking address 0x0
azimuth  | 2026-07-24T16:46:42.645291Z  INFO rtl_sdr_rs::rtlsdr: Got tuner ID r828d
azimuth  | 2026-07-24T16:46:43.175749Z  INFO rtl_sdr_rs::rtlsdr: Init tuner
azimuth  | 2026-07-24T16:46:43.198909Z  INFO rtl_sdr_rs::tuners::r82xx: freq (kHz): 56000
azimuth  | 2026-07-24T16:46:43.211569Z  INFO rtl_sdr_rs::tuners::r82xx: vco_freq: 1792000000
azimuth  | 2026-07-24T16:46:43.211600Z  INFO rtl_sdr_rs::tuners::r82xx: nint: 31
azimuth  | 2026-07-24T16:46:43.211672Z  INFO rtl_sdr_rs::tuners::r82xx: ni: 4, si: 2, reg: 132
azimuth  | 2026-07-24T16:46:43.294938Z  INFO rtl_sdr_rs::rtlsdr: Init complete
azimuth  | 2026-07-24T16:46:43.294981Z  INFO rtl_sdr_rs::rtlsdr: set_sample_rate: rate: 2048000, xtal: 28800000, rsamp_ratio: 58982400
azimuth  | 2026-07-24T16:46:43.294995Z  INFO rtl_sdr_rs::rtlsdr: real_resamp_ratio: 58982400
azimuth  | 2026-07-24T16:46:43.330166Z  INFO rtl_sdr_rs::tuners::r82xx: set_freq - freq: 0
azimuth  | 2026-07-24T16:46:43.330199Z  INFO rtl_sdr_rs::tuners::r82xx: set_freq - lo_freq: 30425000
azimuth  | 2026-07-24T16:46:43.342461Z  INFO rtl_sdr_rs::tuners::r82xx: freq (kHz): 30425
azimuth  | 2026-07-24T16:46:43.354999Z  INFO rtl_sdr_rs::tuners::r82xx: vco_freq: 1947200000
azimuth  | 2026-07-24T16:46:43.355030Z  INFO rtl_sdr_rs::tuners::r82xx: nint: 33
azimuth  | 2026-07-24T16:46:43.355041Z  INFO rtl_sdr_rs::tuners::r82xx: ni: 5, si: 0, reg: 5
```

---

# Notes

- The API key and node configuration are only used during the **first startup**.
- They are automatically stored inside the Docker volume named `azimuth-data`.
- If you later change your API key or coordinates, delete the Docker volume and start the container again.

---

# Observation modes

## Legacy mode (default)

As of **Azimuth v0.2.3**, the default Docker deployment continuously samples **100 MHz every 30 seconds** while running the **Legacy observation loop**.

This is the intended behavior of the official daemon and is documented by the Azimuth team.

## Multi-signal scanner

The official daemon already contains the experimental multi-signal scanner capable of detecting:

- FM + RDS
- LTE
- DTV

The scanner is disabled by default.

An example `config.toml` is included in this repository to make enabling the scanner easier.

---

# Scanner configuration

This repository also includes an example `config.toml` that enables the built-in scanner mode.

The scanner configuration is intended as a starting point for experimentation.

LTE frequencies, television standards and RF allocations differ between countries, therefore you should adapt the configuration for your own region whenever possible.

---

# Removing everything

Stop and remove the container:

```bash
docker compose down
```

Remove the persistent volume:

```bash
docker volume rm azimuth-data
```

Remove the Docker image:

```bash
docker rmi azimuth-node:0.2.3-arm64
```

(or replace it with the AMD64 image if applicable)

Remove the directory:

```bash
cd ..
rm -rf azimuth
```

---

Tested on:

- Raspberry Pi 4B (ARM64)
- Raspberry Pi OS
- RTL-SDR Blog V4
- u-blox USB GPS receiver
- Azimuth Docker image v0.2.3

Happy scanning!📡🚀

For more detailed project breakdown, join the **Finacial Freedom** Discord server, we share and discuss new projects there https://discord.gg/wY3N2hCT3u.

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
