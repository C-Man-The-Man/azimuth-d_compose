# Regional Scanner Configuration Examples

The official Azimuth Docker image includes an experimental multi-signal scanner that can be enabled by mounting a custom `config.toml`.

The scanner supports multiple signal types, including:

- FM + RDS
- LTE
- Digital Television

Different parts of the world use different RF spectrum allocations.

FM frequencies, LTE bands and digital television standards vary between countries, meaning that a scanner configuration that performs well in one region may not be optimal somewhere else.

This directory contains community-maintained scanner configurations intended as starting points for different regions.

---

# Why do the filenames include a version?

Example:

```
europe-generic-v0.2.3.toml
```

The scanner is still under active development.

Future Azimuth releases may:

- add new signal types
- change scanner behavior
- introduce new configuration options
- deprecate existing parameters

Including the daemon version in the filename makes it immediately obvious which version of the software the configuration was designed and tested for.

When a future Azimuth release changes the scanner, new configuration files can simply be added without breaking compatibility with older releases.

---

# How to use

1. Choose the configuration matching your region.

2. Copy its contents into your own `config.toml`.

3. Place `config.toml` next to `docker-compose.yml`.

4. Uncomment (or add) the bind mount inside `docker-compose.yml`:

```yaml
volumes:
  - azimuth-data:/var/lib/azimuth
  - ./config.toml:/var/lib/azimuth/config.toml
```

5. Restart the container:

```bash
docker compose up -d --force-recreate
```

Your node identity is stored inside the Docker volume, so replacing `config.toml` does **not** create a new node.

---

# Current regional configurations

## europe-generic-v0.2.3.toml

A generic European scanner configuration.

Uses:

- European FM broadcast band
- DVB-T / DVB-T2 television sweep

No LTE frequencies are included because LTE allocations differ significantly across Europe.

---

## usa-canada-v0.2.3.toml

Based on the official Azimuth scanner example.

Uses:

- North American FM band
- ATSC television sweep
- Example LTE frequencies provided by the official documentation

---

# Community Contributions

Regional configurations should only be submitted after they have been tested on real hardware.

If you would like to contribute a new regional configuration, please include:

- Country or region
- Azimuth daemon version
- Hardware used
- SDR model
- Television standard
- LTE frequencies tested
- Any observations or improvements

The goal of this repository is to build a reliable collection of community-tested scanner configurations rather than speculative examples.
