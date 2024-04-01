# AWX EE

The InfluxData Execution Environment for AWX.

## Build the image locally

First, [install ansible-builder](https://ansible-builder.readthedocs.io/en/stable/installation/).

Then run the following command from the root of this repo:

```bash
$ ansible-builder build -v3 -t quay.io/influxdb/awx-ee --container-runtime=docker # Uses podman by default
```

## Build the image via CI

The Github actions configuration in this repository should work for you as well, provided that you're using that platform. Just update the secrets to reflect your chosen container repository.

