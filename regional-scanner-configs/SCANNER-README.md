# Regional Scanner Configurations

The scanner configurations in this directory are intended to optimize Azimuth's multi-signal scanner for different countries and regions.

Instead of relying on estimated or randomly selected frequencies, these profiles aim to follow each country's officially published spectrum allocations whenever possible.

---

# Why Regional Profiles?

Although many radio services occupy similar frequency ranges worldwide, there are important regional differences.

Examples include:

- LTE center frequencies
- Digital television standards and channel spacing
- Licensed broadcast spectrum
- National frequency allocations

Using frequencies that actually exist in your region allows the scanner to spend more time observing active signals instead of scanning unused spectrum.

---

# Generic Configuration

The repository root contains:

```
config.toml
```

This is a generic scanner profile intended to work reasonably well in most locations.

If no regional profile exists for your country, the generic configuration is recommended.

---

# Available Regional Profiles

Current profiles include:

| Profile | Region | Status |
|----------|--------|--------|
| europe-generic.toml | Europe | Generic |
| usa-canada.toml | North America | Generic |
| spain.toml | Spain | Official spectrum allocation |

Additional countries will be added over time.

---

# Installing a Regional Profile

Each regional profile can be downloaded directly from GitHub.

For example, to install the Spain profile:

```bash
curl -LO https://raw.githubusercontent.com/C-Man-The-Man/azimuth-d_compose/main/regional-scanner-configs/spain.toml
```

Rename it:

```bash
mv spain.toml config.toml
```

The provided `docker-compose.yml` always mounts:

```yaml
./config.toml:/var/lib/azimuth/config.toml
```

No changes to the Docker Compose file are required when switching between scanner profiles.

Simply replace `config.toml` with another regional profile and restart the container.

---

# Updating the Scanner Configuration

After replacing the scanner configuration:

```bash
docker compose restart
```

or

```bash
docker compose down
docker compose up -d
```

The node identity stored inside the Docker volume is preserved.

Changing scanner configurations does **not** create a new node or require re-registration.

---

# How Regional Profiles Are Built

Whenever possible, regional scanner profiles are based on official documentation published by the corresponding national telecommunications authority.

Examples include:

- National Frequency Allocation Tables
- Digital Television channel plans
- LTE spectrum allocations
- Licensed FM broadcast bands

The objective is to produce practical scanner configurations that reflect the radio spectrum actually used within each country.

---

# Contributing

Community contributions are welcome.

When submitting a new regional profile, please include references to the official documentation used whenever possible.

Profiles based solely on assumptions or estimated frequencies are discouraged.

---

Happy scanning! 📡
