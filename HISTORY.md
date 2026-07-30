# Project History

This repository has evolved alongside Azimuth itself.

Rather than being a fork of the official project, its goal has always been to make running an Azimuth node easier for the community while using the official Docker images without modification.

## The Beginning (Azimuth v0.2.3)

When Azimuth v0.2.3 was released, official Docker support was still limited.

Many users wanted a simple Docker Compose deployment, especially for Raspberry Pi systems, but there was no community-maintained solution that explained the process from start to finish.

This repository was created to provide:

- Ready-to-use Docker Compose files
- Separate ARM64 and AMD64 deployments
- Raspberry Pi guidance
- RTL-SDR installation instructions
- USB permission troubleshooting
- Additional documentation for beginners

Everything was built around the official Docker image distributed by the Azimuth team.

---

## Regional Scanner Configurations

At the time, Azimuth relied on manually maintained scanner configurations.

To improve scanning performance in different parts of the world, this repository introduced regional `config.toml` files based on publicly available national spectrum allocation documentation whenever possible.

The goal was to provide users with better defaults while remaining technically accurate.

Although useful, maintaining these regional configurations required continuous research and updates as the project evolved.

---

## Working with the Azimuth Team

As the Azimuth project matured, the official Docker documentation improved significantly.

The Azimuth team later added this repository to the official website as a community Docker resource.

This marked an important milestone for the project and shifted its purpose.

Instead of filling gaps in the official documentation, the repository could now focus on complementing it.

---

## Azimuth v0.3.0

Version 0.3.0 introduced a major redesign of the node configuration.

The container now automatically generates its own `config.toml` during the first startup using the `AZIMUTH_*` environment variables defined in the Docker Compose file.

Regional frequency plans also became part of the official project through the new `AZIMUTH_REGION` setting.

These changes eliminated the need for manually maintained regional scanner configuration files.

---

## Repository Simplification

Following the release of Azimuth v0.3.0, this repository was redesigned to closely follow the official Docker deployment.

Several components that had previously been necessary were removed:

- Generic `config.toml`
- Regional scanner configuration files
- Scanner profile documentation

The repository now focuses on:

- Official Docker Compose deployments
- Additional explanations and comments
- Raspberry Pi friendly documentation
- Troubleshooting guides

The Docker Compose files remain functionally identical to the official versions while providing more detailed explanations for users who are new to Docker or SDR hardware.

---

## Project Philosophy

The goal of this repository is **not** to replace the official documentation.

Instead, it aims to remain as close as possible to the official Docker deployment while making it easier to understand and deploy.

Whenever Azimuth releases a new Docker Compose file, this repository is updated to match the official implementation, with additional comments and documentation where appropriate.

This approach keeps maintenance simple while ensuring users always benefit from the latest official improvements.

---

## Thank You

Thank you to the Azimuth team for building an exciting project and for recognizing this repository as a community resource.

And thank you to everyone who has tested the Docker Compose files, reported issues, suggested improvements, and helped make running an Azimuth node easier for the community.
