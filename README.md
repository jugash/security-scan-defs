# Image Import Pipeline Organization Configuration Repository

This repository contains **organization definitions** for the Image Import Pipeline security scanning system. Global settings (registry configuration, security policies, scan settings) are maintained in the main application repository, while organization and team definitions are externalized here.

## 📁 Repository Structure

```
config-repo/
├── configs/
│   ├── organizations.yaml                    # Main organization definitions
│   ├── web-team-organizations.yaml          # Web team managed organizations
│   ├── backend-team-organizations.yaml      # Backend team managed organizations
│   ├── infrastructure-organizations.yaml    # Infrastructure team managed organizations
│   └── [other-team-organizations.yaml]     # Additional team-specific files
└── README.md                                 # This file
```

## 🔗 How It Works

The Image Import Pipeline uses a **split configuration approach**:

1. **Global Configuration** (in main repository):

   - Registry settings
   - Security scan policies
   - Vulnerability thresholds
   - Pipeline configuration

2. **Organization Definitions** (in this repository):
   - Team definitions
   - Repository specifications
   - Image sources and versions
   - Organization-specific settings

The system automatically merges both configurations at runtime.

## 📄 Multiple File Support

The system scans **all YAML files** in the `configs/` directory and merges organization definitions from each file. This allows:

- **Team-based organization**: Different teams manage their own organization files
- **Separation of concerns**: Logical grouping of related organizations
- **Flexible structure**: Add new organization files as needed
- **Automatic merging**: All organizations are combined into a single configuration

### File Loading Process

1. Scans all `*.yaml` files in `configs/` directory
2. Extracts `organizations` section from each file
3. Merges all organizations into a single configuration
4. Reports count of organizations loaded from each file

### Example File Organization

```
configs/
├── organizations.yaml                    # Main/shared organizations
├── web-team-organizations.yaml          # Web team specific
├── backend-team-organizations.yaml      # Backend team specific
├── infrastructure-organizations.yaml    # Infrastructure team specific
└── qa-organizations.yaml                # QA team specific
```

Each file can contain its own `organizations` section, and they will all be merged together.

## 🚀 Quick Start

### Using with Image Import Pipeline

1. **Clone this repository:**

   ```bash
   git clone https://github.com/your-org/image-import-configs.git
   cd image-import-configs
   ```

2. **Deploy with Helm:**

   ```bash
   helm upgrade image-import-pipeline ./helm/image-import-pipeline \
     --set scanner.gitConfig.enabled=true \
     --set scanner.gitConfig.repository="https://github.com/your-org/image-import-configs.git" \
     --set scanner.gitConfig.branch="main"
   ```

3. **Or use with Docker:**
   ```bash
   docker run -e GIT_REPO="https://github.com/your-org/image-import-configs.git" \
              -e GIT_BRANCH="main" \
              your-scanner-image
   ```

## 📋 Configuration Files

### Organization Definition Files

The system loads organization definitions from **all YAML files** in the `configs/` directory:

- **`organizations.yaml`** - Main organization definitions (optional)
- **`web-team-organizations.yaml`** - Web team managed organizations
- **`backend-team-organizations.yaml`** - Backend team managed organizations
- **`infrastructure-organizations.yaml`** - Infrastructure team managed organizations
- **`[custom]-organizations.yaml`** - Custom organization files

Each file can contain an `organizations` section with organization definitions.

## 🔧 Organization Configuration Format

This repository contains only organization definitions. Global settings are in the main application repository.

```yaml
# Schema version
schema_version: "1.0"

# Organization definitions only (global settings are in main repository)
organizations:
  my-team:
    description: "My team's container images"
    repositories:
      web-app:
        description: "Web application containers"
        type: "container"
        source:
          registry: "docker.io"
          repository: "myorg/web-app"
        versions:
          pattern: "^v[0-9]+\\.[0-9]+\\.[0-9]+$"
        tags:
          - "latest"
          - "stable"

  infrastructure:
    description: "Infrastructure and base images"
    repositories:
      ubuntu-base:
        description: "Ubuntu base images"
        type: "container"
        source:
          registry: "docker.io"
          repository: "library/ubuntu"
        versions:
          pattern: "^22\\.04.*$"
        tags:
          - "latest"
          - "22.04"
```

## 🏗️ Organization Structure

Organize your organization definitions by teams, projects, or environments:

```
configs/
├── organizations.yaml                 # Main organization definitions
├── teams/
│   ├── frontend-team.yaml
│   ├── backend-team.yaml
│   └── platform-team.yaml
├── environments/
│   ├── development-orgs.yaml
│   ├── staging-orgs.yaml
│   └── production-orgs.yaml
└── projects/
    ├── web-portal-orgs.yaml
    ├── api-gateway-orgs.yaml
    └── data-processor-orgs.yaml
```

## 🔄 CI/CD Integration

### GitHub Actions Example

```yaml
name: Security Scan
on: [push, pull_request]

jobs:
  security-scan:
    runs-on: ubuntu-latest
    steps:
      - name: Trigger Security Scan
        run: |
          curl -X POST https://your-pipeline.example.com/api/trigger-scan \
            -H "Content-Type: application/json" \
            -d '{
              "git_repo": "https://github.com/your-org/image-import-configs.git",
              "git_branch": "main"
            }'
```

### GitLab CI Example

```yaml
security_scan:
  script:
    - curl -X POST https://your-pipeline.example.com/api/trigger-scan
          -H "Content-Type: application/json"
          -d "{\"git_repo\": \"https://github.com/your-org/image-import-configs.git\", \"git_branch\": \"main\"}"
```

## 🔒 Security Considerations

### Repository Access

- Use HTTPS URLs for public repositories
- Consider using deploy keys or personal access tokens for private repositories
- Implement proper access controls on the repository

### Organization Data

- This repository contains only organization and repository definitions
- No sensitive credentials or global policies are stored here
- Global security policies are maintained in the main application repository

## 🐛 Troubleshooting

### Common Issues

1. **Repository not found**

   - Verify the repository URL is correct and accessible
   - Check if the repository is public or if credentials are required

2. **Branch not found**

   - Ensure the branch name exists in the repository
   - Check for typos in the branch name

3. **Organizations not loaded**

   - Verify YAML files exist in the `configs/` directory
   - Check that files contain valid `organizations` sections
   - Check YAML syntax in organization definitions
   - Ensure files have `.yaml` extension

4. **Authentication failed**
   - For private repositories, ensure proper authentication is configured
   - Use deploy keys or personal access tokens as needed

### Debug Mode

Enable verbose logging to troubleshoot issues:

```bash
python security-scan.py --git-repo https://github.com/your-org/image-import-configs.git --git-branch main -v
```

## 📝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/new-org`
3. Add or modify organization definitions in the appropriate file:
   - Use existing team files (e.g., `web-team-organizations.yaml`)
   - Create new files for new teams (e.g., `data-team-organizations.yaml`)
   - Add to `organizations.yaml` for shared organizations
4. Test your changes
5. Commit your changes: `git commit -am 'Add new organization definition'`
6. Push to the branch: `git push origin feature/new-org`
7. Submit a pull request

## 📄 License

This configuration repository is licensed under the MIT License.

## 🆘 Support

For support and questions:

- Create an issue in this repository
- Check the main Image Import Pipeline documentation
- Review the troubleshooting section above

---

**Note**: This repository contains only organization definitions. Global configuration and security policies are maintained in the main Image Import Pipeline repository.
