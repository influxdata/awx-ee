# AWX EE

An Execution Environment for AWX.

Main features:
- Centos Stream 9
- Python 3.12
  - ara
  - boto3
  - mitogen
  - redis
  - toml
- Ansible 2.16
- Minimal base collections installed

View the full configuration in the [execution-environment.yaml](execution-environment.yaml) file.

## Build the image locally

First, [install ansible-builder](https://ansible-builder.readthedocs.io/en/stable/installation/).

Then run the following command from the root of this repo:

```bash
$ ansible-builder build -v3 -t quay.io/influxdb/awx-ee --container-runtime=docker # Uses podman by default
```

## Build the image via CI

The GitHub Actions workflow builds and publishes images automatically.

### Automatic builds
- **Releases**: Creating a GitHub release pushes an image tagged with the release name (e.g., `v1.0.0`)
- **Nightly**: Weekly scheduled builds push a `nightly` tag

### Manual builds
To push an image for a specific branch:
1. Go to Actions > "Build & Release" workflow
2. Click "Run workflow"
3. Select the target branch
4. Check "Push image to registry after build"
5. Click "Run workflow"

Image tags for manual builds:
| Branch | Tag |
|--------|-----|
| `main` | `latest` |
| `devel` | `devel` |
| Branch with open PR | `DEV-PR-<number>` |
| Other branches | `DEV-<commit-sha>` |

### CI validation
Pull requests and pushes to `main`/`devel` trigger a CI build (Podman) to validate the image builds successfully, but do not push to the registry.

### Configuration
To use this workflow in your own fork, configure these repository variables/secrets:
- `IMAGE_REGISTRY_URL`: Container registry URL (default: `ghcr.io`)
- `IMAGE_REPOSITORY`: Image repository path (default: `github.repository`)
- `IMAGE_REGISTRY_USER`: Registry username (default: `github.actor`)
- `IMAGE_REGISTRY_TOKEN` (secret): Registry auth token (default: `GITHUB_TOKEN`)
- `ALWAYS_PUSH_GHCR`: Set to `true` to also push to `ghcr.io` when using an alternate primary registry

