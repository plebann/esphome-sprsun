# GitHub Repository Setup Instructions

This document provides step-by-step instructions for setting up the GitHub repository for the SPRSUN ESPHome Package.

## Repository Creation Steps

### 1. Create New GitHub Repository

1. Go to [GitHub](https://github.com) and sign in to your account
2. Click the "+" icon in the top right corner
3. Select "New repository"
4. Configure the repository:
   - **Repository name**: `esphome-packages`
   - **Description**: "ESPHome package for SPRSUN heat pump integration with native Git support"
   - **Visibility**: Public (recommended for community access)
   - **Initialize repository**: Leave unchecked (we'll push existing content)
5. Click "Create repository"

### 2. Initialize Local Repository

Navigate to the created package directory and initialize Git:

```bash
cd esphome-packages
git init
git add .
git commit -m "Initial commit: SPRSUN ESPHome Package v1.0"
```

### 3. Connect to GitHub Repository

Add the GitHub repository as remote origin:

```bash
git remote add origin https://github.com/yourusername/esphome-packages.git
git branch -M main
git push -u origin main
```

### 4. Configure Repository Settings

1. **Enable Issues and Discussions**:
   - Go to repository Settings → General
   - Under "Features", enable:
     - Issues
     - Discussions
     - Wikis (optional)

2. **Set Up Branch Protection** (optional but recommended):
   - Go to Settings → Branches
   - Add rule for `main` branch:
     - Require pull request reviews
     - Require status checks (once Actions are set up)

3. **Configure GitHub Actions**:
   - Actions should automatically detect the `.github/workflows/validation.yml`
   - First run may require approval for new repository

### 5. Add Repository Topics

Add relevant topics to help users discover the package:
- `esphome`
- `heat-pump`
- `sprsun`
- `modbus`
- `home-automation`
- `home-assistant`
- `esp32`

### 6. Create First Release

1. Go to "Releases" section
2. Click "Create a new release"
3. Configure release:
   - **Tag**: `v1.0.0`
   - **Title**: `SPRSUN ESPHome Package v1.0.0`
   - **Description**: Include changelog and features
4. Publish release

## Repository Structure Verification

Ensure your repository contains:

```
esphome-packages/
├── .github/
│   └── workflows/
│       └── validation.yml
├── packages/
│   └── sprsun/
│       ├── complete.yaml
│       ├── binary_sensors.yaml
│       ├── sensors.yaml
│       ├── text_sensors.yaml
│       ├── controls.yaml
│       ├── globals.yaml
│       ├── modbus_controller.yaml
│       └── README.md
├── examples/
│   ├── basic-esp32.yaml
│   ├── boneio-integration.yaml
│   ├── advanced-features.yaml
│   └── selective-components.yaml
├── test-configurations/
│   ├── test-full-git.yaml
│   ├── test-selective.yaml
│   └── test-variables.yaml
├── README.md
├── LICENSE
├── MIGRATION.md
└── GITHUB-SETUP.md (this file)
```

## Testing Repository Access

Test that the repository works with ESPHome Git integration:

```yaml
# Test configuration
packages:
  sprsun_test:
    url: https://github.com/yourusername/esphome-packages
    files: [packages/sprsun/complete.yaml]
    ref: main
    vars:
      sprsun_device_prefix: "test"
      sprsun_device_name: "Test Heat Pump"
      # ... other required variables
```

Run `esphome config test-config.yaml` to verify Git access works.

## Post-Setup Tasks

1. **Update README.md**: Replace placeholder URLs with actual repository URL
2. **Test GitHub Actions**: Make a small change and verify workflows run
3. **Create Documentation**: Consider adding Wiki pages for advanced topics
4. **Community Setup**: Enable discussions and issue templates

## Maintenance

- **Regular Updates**: Keep package synchronized with any local changes
- **Version Tags**: Use semantic versioning for releases
- **Automated Testing**: Monitor GitHub Actions for validation failures
- **Community Engagement**: Respond to issues and discussions

## Security Considerations

- **Repository Access**: Keep repository public for community use
- **Secrets**: Never commit sensitive information (API keys, passwords)
- **Permissions**: Use branch protection to prevent direct commits to main
- **Dependencies**: Monitor for security updates in ESPHome