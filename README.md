# Custom Builder Image

Upon invocation, a custom builder image will receive the following environment variables with the information needed to proceed with the build:
Table 1. Custom Builder Environment Variables

### Variable Descriptions

`BUILD`                The entire serialized JSON of the Build object definition. If you need to use a specific API version for serialization, you can set the buildAPIVersion parameter in the custom strategy specification of the build configuration.

`SOURCE_REPOSITORY`    The URL of a Git repository with source to be built.

`SOURCE_URI`           Uses the same value as SOURCE_REPOSITORY. Either can be used.

`SOURCE_CONTEXT_DIR`   Specifies the subdirectory of the Git repository to be used when building. Only present if defined.

`SOURCE_REF`           The Git reference to be built.

`ORIGIN_VERSION`       The version of the OpenShift Container Platform master that created this build object.

`OUTPUT_REGISTRY`      The Docker registry to push the image to.

`OUTPUT_IMAGE`         The Docker tag name for the image being built.

`PUSH_DOCKERCFG_PATH`  The path to the Docker credentials for running a docker push operation.

`DOCKER_SOCKET`        Specifies the path to the Docker socket, if exposing the Docker socket was enabled in the build configuration (if exposeDockerSocket was set to true.)
Custom Builder Workflow


Although Custom builder image authors have great flexibility in defining the build process, your builder image must still adhere to the following required steps necessary for seamlessly running a build inside of OpenShift Container Platform:

1.    The Build object definition contains all the necessary information about input parameters for the build.

2.    Run the build process.

3.    If your build produces an image, push it to the build’s output location if it is defined. Other output locations can be passed with environment variables.




