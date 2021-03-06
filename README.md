# Custom Builder Image for OpenShift and OKD

### General

OKD and OpenShift offer pre-built and already-available builder containers named `origin-docker-builder` (OKD) and `ose-docker-builder` (OpenShift).  For most use cases, these images are sufficient.  However from time-to-time it
may be necessary to create your own.  Each builder image must adhere to specific requirements.

This repository is meant to act as a starting point for you to create your own custom builder images.

------

### Variable Descriptions

The `env` option allows you to specify additional environment variables that will be passed to the builder container environment. By default, these environment variables are passed to the build container:

**Table 1. Custom Builder Environment Variables**

| ENVIRONMENT VARIABLE  | DESCRIPTION |
| --------------------- | ----------- |
| `$BUILD` | The entire serialized JSON of the `Build` object definition. If you need to use a specific API version for serialization, you can set the `buildAPIVersion` parameter in the custom strategy specification of the build configuration. |
| `$SOURCE_REPOSITORY` | The URL of a Git repository with source to be built. |
| `$SOURCE_URI` | Uses the same value as `$SOURCE_REPOSITORY`. Either can be used. |
| `$SOURCE_CONTEXT_DIR` | Specifies the subdirectory of the Git repository to be used when building. Only present if defined. |
| `$SOURCE_REF` | The Git reference to be built. |
| `$ORIGIN_VERSION` | The version of the OKD or OpenShift Container Platform master that created this build object. |
| `$OUTPUT_REGISTRY` | The Docker registry to push the image to. |
| `$OUTPUT_IMAGE` | The Docker tag name for the image being built. |
| `$PUSH_DOCKERCFG_PATH` | The path to the Docker credentials for running a `docker push` or `podman push` operation. |
| `$DOCKER_SOCKET` | Specifies the path to the Docker socket, if exposing the Docker socket was enabled in the build configuration (if `exposeDockerSocket` was set to `true`.) |
| `$DOCKERFILE_PATH` | Specifies the Dockerfile, similar to `spec.strategy.dockerStrategy.dockerfilePath` when using `dockerStrategy`.  Only available only when using the `build.sh` file included in this repository, or if you write a similar custom script. |


### Custom Builder Workflow


Although Custom builder image authors have great flexibility in defining the build process, your builder image must still adhere to the following required steps necessary for seamlessly running a build inside of OKD or OpenShift Container Platform:

1.    The `Build` object definition contains all the necessary information about input parameters for the build.

2.    Run the build process.

3.    If your build produces an image, push it to the build’s output location if it is defined. Other output locations can be passed with environment variables.


### Custom Build Strategy in `BuildConfig`

The custom build strategy is very similar to *Docker build* strategy, but users might customize the builder image that will be used for build execution. The *Docker build* uses [openshift/origin-docker-builder](https://hub.docker.com/r/openshift/origin-docker-builder/) image by default. Using your own builder image allows you to customize your build process.

**An example JSON of a custom build strategy:**

```json
"strategy": {
  "type": "Custom",
    "customStrategy": {
      "image": "my-custom-builder-image",
      "exposeDockerSocket": true,
      "env": [
        { "name": "EXPOSE_PORT", "value": "8080" }
      ]
    }
}
```

**An example YAML of a custom build strategy:**

In this example we're using `DOCKERFILE_PATH` to specify the name of the Dockerfile (similar to `dockerfilePath` when using `dockerStrategy`).  `DOCKERFILE_PATH` is available only when using the `build.sh` file included in this repository.

```yaml
  strategy:
    customStrategy:
      env:
        - name: EXPOSE_PORT
          value: '8080'
        - name: DOCKERFILE_PATH
          value: 
      exposeDockerSocket: true
      from:
        kind: DockerImage
        name: my-custom-builder-image
    type: Custom
```

The `exposeDockerSocket` option will mount the Docker socket from host into your
builder container and allows you to execute the `docker build` and `docker push` commands.
Note that this might be restricted by the administrator in future.


