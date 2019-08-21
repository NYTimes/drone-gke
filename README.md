# drone-gke

Drone plugin to deploy container images to Kubernetes on Google Container Engine.
For the usage information and a listing of the available options please take a look at [the docs](DOCS.md).

Simplify deploying to Google Kubernetes Engine.
Derive the API endpoints and credentials from the Google credentials and open the yaml file to templatization and customization with each Drone build.

## Links

- Usage [documentation](DOCS.md)
- Docker Hub [release tags](https://hub.docker.com/r/nytimes/drone-gke/tags)
- Drone.io [builds](https://beta.drone.io/nytimes/drone-gke)
- Contributing [documentation](CONTRIBUTING.md)

## Releases and versioning

### Tool

This tool follows [semantic versioning](https://semver.org/).

Use the `x.X` releases for stable use cases (eg 0.8).
Breaking changes may occur between `x.X` releases (eg 0.7 and 0.8), and will be documented in the [release notes](https://github.com/nytimes/drone-gke/releases).

### Kubernetes API

Since the [237.0.0 (2019-03-05) Google Cloud SDK][sdk], the container image contains multiple versions of `kubectl`.
The corresponding client version that matches the cluster version will be used automatically.
This follows the minor release support that ([GKE offers](https://cloud.google.com/kubernetes-engine/versioning-and-upgrades)).

If you want to use a different version, you can specify the version of `kubectl` used with the [`kubectl_version` parameter][version-parameter].

[sdk]: https://cloud.google.com/sdk/docs/release-notes#23700_2019-03-05
[version-parameter]: DOCS.md#kubectl_version

### Container

- Pushes to the [`develop`](https://github.com/nytimes/drone-gke/tree/develop) branch will update the image tagged `develop`.
- Pushes to the [`master`](https://github.com/nytimes/drone-gke/tree/master) branch will update the image tagged `latest`.
- Tags to the [`master`](https://github.com/nytimes/drone-gke/tree/master) branch will create the images with the patch and minor tag values (eg `0.7.1` and `0.7`).

## Usage

> :warning: For usage within in a `.drone.yml` pipeline, please take a look at [the docs](DOCS.md)

Executing locally from the working directory:

```sh
# Deploy the manifest templates in local-example/
cd local-example/

# Set to the path of your GCP service account JSON-formatted key file
export TOKEN=xxx

# Set to your cluster
export PLUGIN_CLUSTER=yyy

# Set to your cluster's zone
export PLUGIN_ZONE=zzz

# Set to a namespace within your cluster's
export PLUGIN_NAMESPACE=drone-gke

# Example variables referenced within .kube.yml
export PLUGIN_VARS="$(cat vars.json)"
# {
#   "app": "echo",
#   "env": "dev",
#   "image": "gcr.io/google_containers/echoserver:1.4"
# }

# Example secrets referenced within .kube.sec.yml
export SECRET_APP_API_KEY=123
export SECRET_BASE64_P12_CERT="cDEyCg=="

# Execute the plugin
docker run --rm \
  -e PLUGIN_CLUSTER \
  -e PLUGIN_NAMESPACE \
  -e PLUGIN_VARS \
  -e PLUGIN_ZONE  \
  -e SECRET_APP_API_KEY \
  -e SECRET_BASE64_P12_CERT \
  -e TOKEN="$(cat $TOKEN)" \
  -v $(pwd):$(pwd) \
  -w $(pwd) \
  nytimes/drone-gke --dry-run --verbose

# Remove --dry-run to deploy
```
