# AWX EE

An Execution Environment for AWX.

Main features:
- CentOS Stream 10
- Python 3.13
- Ansible 2.21

> An ansible-core **2.16** line is maintained on the [`legacy`](../../tree/legacy)
> branch (CentOS Stream 9 / Python 3.12), published as the `:legacy` image tag.

### Collections
- `amazon.aws`
- `ansible.posix`
- `ansible.receptor`
- `ansible.utils`
- `awx.awx`
- `community.aws`
- `community.docker`
- `community.general`
- `community.grafana`

### Python packages
- ara
- boto3
- mitogen
- netaddr
- paramiko
- pykerberos
- pywinrm
- receptorctl
- redis
- toml

View the full configuration in the [execution-environment.yaml](execution-environment.yaml) file.

## Build the image locally

First, [install ansible-builder](https://ansible-builder.readthedocs.io/en/stable/installation/).

Then run the following command from the root of this repo:

```bash
$ ansible-builder build -v3 -t quay.io/influxdb/awx-ee --container-runtime=docker # Uses podman by default
```

## Build the image via CI

The GitHub Actions workflow builds and publishes images automatically.

### Releasing

Releases are cut from labeled PRs across two lines:

| Line | Branch | Version scheme | Moving tag |
|------|--------|----------------|------------|
| Current | `main` | linear semver (e.g. `v1.3.0`) | `latest` |
| Legacy (ansible-core 2.16) | `legacy` | `v<base>-legacy.N` (e.g. `v1.2.0-legacy.1`) | `legacy` |

1. **Label the PR** with `release/patch`, `release/minor`, or `release/major` to set the
   version bump. On `legacy`, any `release/*` label cuts a maintenance release — the bump
   type is ignored and the `-legacy.N` counter simply increments.
2. **Merge the PR.** Two things happen:
   - the branch's **moving tag** is rebuilt and pushed automatically (`main` → `:latest`,
     `legacy` → `:legacy`, `devel` → `:devel`);
   - a matching **git tag** is created (`v1.3.0`, `v1.2.0-legacy.1`).
3. **Publish the immutable versioned image** (`:v1.3.0`, `:v1.2.0-legacy.1`): create a
   GitHub **Release** from that tag — this builds and pushes the version-tagged image.

### Automatic builds
- **Push to `main`/`legacy`/`devel`**: builds and pushes the branch's moving tag (`latest` / `legacy` / `devel`)
- **Releases**: creating a GitHub Release pushes an image tagged with the release name (e.g. `v1.3.0`)
- **Nightly**: weekly scheduled builds push a `nightly` tag

### Manual builds
To build and push an image for any branch on demand:
1. Go to Actions > "Build & Release" workflow
2. Click "Run workflow"
3. Select the target branch
4. Check "Push image to registry after build"
5. Click "Run workflow"

Image tags:
| Branch | Tag |
|--------|-----|
| `main` | `latest` |
| `legacy` | `legacy` |
| `devel` | `devel` |
| Branch with open PR | `DEV-PR-<number>` |
| Other branches | `DEV-<commit-sha>` |

### CI validation
Pull requests to `main`/`legacy`/`devel` trigger a CI build (Podman) to validate the image
builds successfully, but do not push to the registry.

### Configuration
To use this workflow in your own fork, configure these repository variables/secrets:
- `IMAGE_REGISTRY_URL`: Container registry URL (default: `ghcr.io`)
- `IMAGE_REPOSITORY`: Image repository path (default: `github.repository`)
- `IMAGE_REGISTRY_USER`: Registry username (default: `github.actor`)
- `IMAGE_REGISTRY_TOKEN` (secret): Registry auth token (default: `GITHUB_TOKEN`)
- `ALWAYS_PUSH_GHCR`: Set to `true` to also push to `ghcr.io` when using an alternate primary registry

