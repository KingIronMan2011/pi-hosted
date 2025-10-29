# GitHub Copilot Instructions for pi-hosted

## Project Overview

This repository provides Docker and Portainer templates for hosting applications on Raspberry Pi and other ARM/AMD64 systems. The project generates Portainer application templates in JSON format for different architectures (ARM32, ARM64, AMD64).

## Architecture Support

The project supports three main architectures:
- **ARM32**: Raspberry Pi OS (legacy, being phased out)
- **ARM64**: Raspberry Pi OS, Ubuntu, DietPi (recommended)
- **AMD64**: Ubuntu, Debian

Generate separate template files for each architecture:
- Portainer v2: `portainer-v2-{arm32,arm64,amd64}.json`
- Portainer v3: `portainer-v3-{arm32,arm64,amd64}.json`

## Repository Structure

- `/template/apps/`: Individual JSON files for each application template
- `/template/`: Generated combined Portainer template files
- `/stack/`: Docker compose stack files for complex applications
- `/build/`: Build scripts and generators
- `/docs/`: Documentation files
- `/tools/`: Installation and utility scripts
- `/configs/`: Configuration examples

## Code Standards and Conventions

### JSON Templates

When creating or modifying app templates in `/template/apps/`:

1. **File naming**: Use lowercase with hyphens (e.g., `nginx-proxy-manager.json`)
2. **Required fields**: Each template must include:
   - `type`: Usually `1` for container
   - `title`: Human-readable application name
   - `name`: Container name (lowercase, hyphens)
   - `logo`: URL to application logo
   - `image`: Docker image name with tag
   - `description`: Clear description of the application
   - `categories`: Array of relevant categories
   - `platform`: Target architecture (`linux/arm/v7`, `linux/arm64`, `linux/amd64`)
   - `ports`: Array of port mappings
   - `volumes`: Array of volume mappings
   - `env`: Array of environment variables

3. **Volume paths**: Use `/portainer/Files/AppData/Config/{appname}` as the standard base path for persistent data
4. **Port format**: Use strings like `"8080:8080/tcp"`
5. **Environment variables**: Include sensible defaults and clear labels

### Shell Scripts

1. **Shebang**: Always use `#!/bin/bash`
2. **Error handling**: Include error checking and informative error messages
3. **Internet check**: Scripts that download should check for internet connectivity
4. **Idempotency**: Scripts should be safe to run multiple times
5. **User feedback**: Provide clear output messages for user actions

### Documentation

1. **Format**: Use Markdown (.md)
2. **Images**: Store in `/docs/images/` or `/images/`
3. **Links**: Use relative paths for internal repository links
4. **Structure**: Include clear headings, code blocks with language specification

## Build Process

The repository uses GitHub Actions to automatically:
1. Join individual app JSON files into combined templates (`build/joinApps.sh`)
2. Generate documentation (`build/generators/`)
3. Commit and push changes automatically

### Key Files

- `build/joinApps.sh`: Combines individual app JSONs into architecture-specific templates
- `build/generators/generateAppList.sh`: Creates the app list documentation
- `build/generators/generateREADME.sh`: Updates main README
- `build/info.json`: Metadata about apps and architecture support

## Testing and Validation

### JSON Validation

- All JSON files must be valid JSON syntax
- The GitHub Actions workflow includes JSON linting via super-linter
- Test locally before committing: `cat file.json | jq .`

### Manual Testing

For new app templates:
1. Deploy using Portainer from the generated template
2. Verify the container starts successfully
3. Test basic functionality of the application
4. Check volume persistence after container restart
5. Verify environment variables are correctly set

## Adding New Applications

When adding a new application template:

1. Create a new JSON file in `/template/apps/{appname}.json`
2. Follow the existing template structure from similar apps
3. Ensure logo URL is accessible and uses HTTPS
4. Add appropriate categories for discoverability
5. Include all necessary environment variables with defaults
6. Document any special setup in `/docs/` if needed
7. The build process will automatically integrate it into combined templates

## Common Patterns

### Volume Mounts
```json
"volumes": [
  {
    "container": "/config",
    "bind": "/portainer/Files/AppData/Config/appname"
  }
]
```

### Port Mappings
```json
"ports": [
  "8080:8080/tcp",
  "443:443/tcp"
]
```

### Environment Variables with UI
```json
"env": [
  {
    "name": "PUID",
    "label": "PUID",
    "default": "1000"
  },
  {
    "name": "PGID",
    "label": "PGID",
    "default": "1000"
  }
]
```

## Stack Files

For complex multi-container setups:
- Create YAML compose files in `/stack/`
- Include clear comments explaining dependencies
- Document setup steps in `/docs/`
- Reference the docs in the stack file header

## Deprecation and Retirement

- ARM32 support is being phased out
- Retired templates go in `/template/retired/`
- Update documentation to reflect deprecations
- Keep templates available but mark as deprecated

## Best Practices

1. **Security**: Never include hardcoded passwords or secrets
2. **Defaults**: Provide sensible defaults that work out of the box
3. **Documentation**: If an app needs special configuration, document it
4. **Testing**: Test on the target architecture when possible
5. **Updates**: Prefer `latest` tags for community templates, specific versions for production
6. **Compatibility**: Note any OS-specific requirements in descriptions
7. **Resources**: Include resource requirements in descriptions for resource-constrained devices

## External Resources

- [Portainer Documentation](https://docs.portainer.io/)
- [Docker Documentation](https://docs.docker.com/)
- [Pi-hosted YouTube Series](https://www.youtube.com/playlist?list=PL846hFPMqg3jwkxcScD1xw2bKXrJVvarc)
- [Pi-hosted Discord](https://discord.com/invite/v8dAnFV)
