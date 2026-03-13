---
name: ha-addon-maintenance
description: "Maintain Home Assistant addons. Use for: updating dependencies, building Docker images, managing configuration schemas, documenting features, security updates, and testing addon functionality."
argument-hint: "What maintenance task are you working on?"
---

# Home Assistant Addon Maintenance

Structured workflow for maintaining Home Assistant Community Add-ons with proper versioning, documentation, and Docker image management.

## When to Use

- **Dependency Updates**: Updating Python packages, base images, or system dependencies
- **Building & Testing**: Creating Docker images and verifying addon functionality
- **Security Updates**: Addressing vulnerabilities in dependencies or base images
- **Configuration Changes**: Modifying addon schema, options, or service definitions
- **Documentation**: Updating DOCS.md, README, or changelog entries
- **Version Management**: Bumping version numbers and managing releases

## Quick Maintenance Checklist

### 1. Dependency Management
- [ ] Review `requirements.txt` for outdated Python packages
- [ ] Check `build.yaml` for outdated base image versions (e.g., `debian-base:X.Y.Z`)
- [ ] Review `Dockerfile` for system packages (`apt-get install`) that need updates
- [ ] Check `vscode.extensions` for VSCode extensions needing updates
- [ ] Run security scanner on dependencies (e.g., `pip-audit` for Python)

### 2. Docker Build & Testing
- [ ] Verify `Dockerfile` structure is correct (FROM, COPY, RUN statements)
- [ ] Check `build.yaml` has architecture definitions (aarch64, amd64)
- [ ] Build locally: `docker build -t test-addon .`
- [ ] Test addon boots and responds to requests
- [ ] Verify mounted volumes work correctly (config, ssl, media, etc.)

### 3. Configuration & Schema
- [ ] Review `config.yaml` for missing or outdated options
- [ ] Validate schema definitions in `config.yaml` (log_level, packages, etc.)
- [ ] Test configuration parsing with sample config
- [ ] Document all options in `DOCS.md` with examples
- [ ] Check `init` flag (usually false unless services must start early)

### 4. Documentation
- [ ] Update `README.md` with changes if external-facing
- [ ] Update `DOCS.md` with configuration instructions
- [ ] Add installation steps if adding new integrations
- [ ] Document any breaking changes
- [ ] Include examples for new options

### 5. Version & Release
- [ ] Update `version: X.Y.Z` in `config.yaml` (follow semantic versioning)
- [ ] Add changelog entry documenting what changed
- [ ] Create git tag matching version (e.g., `git tag vX.Y.Z`)
- [ ] Push changes: `git push origin main --tags`

### 6. Verification Checklist
- [ ] Does `config.yaml` have valid YAML syntax?
- [ ] Are all referenced files present (Dockerfile, requirements.txt, etc.)?
- [ ] Do environment variables in Dockerfile match intended behavior?
- [ ] Are permissions correct on startup scripts (`run` files)?
- [ ] Does addon pass Home Assistant schema validation?

## Key Files Structure

```
addon-vscode/
├── vscode/
│   ├── config.yaml          # Addon metadata & schema
│   ├── DOCS.md              # User-facing documentation
│   ├── build.yaml           # Docker build configuration
│   ├── Dockerfile           # Container image definition
│   ├── requirements.txt      # Python dependencies
│   ├── vscode.extensions    # VSCode extensions list
│   └── rootfs/              # Addon filesystem (copied into image)
│       └── etc/s6-overlay/  # Service startup scripts
└── README.md                # Project readme

```

## Common Patterns

### Adding Python Dependencies
1. Add package to `requirements.txt` with pinned version (e.g., `flask==2.3.0`)
2. Run `pip freeze` to verify resolved versions
3. Test import in Dockerfile or startup
4. Document any breaking changes in `DOCS.md`

### Updating Base Image
1. Update both architectures in `build.yaml`:
   ```yaml
   build_from:
     aarch64: ghcr.io/hassio-addons/debian-base:9.2.0
     amd64: ghcr.io/hassio-addons/debian-base:9.2.0
   ```
2. Update ARG in Dockerfile: `ARG BUILD_FROM=ghcr.io/hassio-addons/debian-base:9.2.0`
3. Build and test both architectures
4. Document base image version bump in changelog

### Adding Configuration Option
1. Add option to `schema` section in `config.yaml`:
   ```yaml
   schema:
     my_new_option: str?
   ```
2. Add to `options` defaults:
   ```yaml
   options:
     my_new_option: "default_value"
   ```
3. Document option in `DOCS.md` with examples
4. Reference in startup scripts if needed

### Managing VSCode Extensions
1. Edit `vscode.extensions` (one per line):
   ```
   ms-python.python
   esbenp.prettier-vscode
   ```
2. Extensions auto-install on container startup
3. Update `DOCS.md` to list available extensions
4. Test that extensions load correctly

## References

- Home Assistant Add-ons Development: [Developer Documentation](https://developers.home-assistant.io/docs/add-ons/tutorial/)
- Add-on Configuration: See [Home Assistant Add-on metadata](https://developers.home-assistant.io/docs/add-ons/configuration/)
- Docker Multi-Architecture: [Build for ARM & x86](https://docs.docker.com/build/building/multi-platform/)
