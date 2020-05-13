# How to pass in secrect with the new Docker secrets mount

the following instruction show how to use the new buildkit functionality to pass in mounted docker secrets to a container
without having to rely on copy plaintext file or expose env variables as part of the development process.
https://docs.docker.com/develop/develop-images/build_enhancements/#new-docker-build-secret-information

### Requirements:

- Docker Engine CE Version 19.03.8
- Ubuntu 18.04 Bionic as base image

### Steps to build an image with experimental features

- create a secrets file
  ```
  echo "YOUR SECRET" > ./mysecret.txt
  ```
- enable experimetal features for the docker daemon
  ```
  # /etc/docker/daemon.json
  { "features": { "buildkit": true } }
  # on docker desktop for mac
  # install edge version and enable experimental features
  # add following Docker engine to confiuration file
  "features": { "buildkit": true }
  ```
- create Dockerfile with the frontend syntax flag enabled as a comment

  ```
  # syntax = docker/dockerfile:1.0-experimental
  FROM alpine

  # shows secret from default secret location, visible in the build stdout:
  RUN --mount=type=secret,id=mysecret cat /run/secrets/mysecret

  # shows secret from custom secret location, creates a file with the secret:
  RUN --mount=type=secret,id=mysecret,dst=/foobar echo "$(cat /foobar)" > test
  ```

- run build
  ```
  docker build --no-cache --progress=plain --secret id=mysecret,src=mysecret.txt -t yourusername/secrets:tag .
  ```
- run container
  ```
  docker run -it --rm secrets
  ```

### Conclusion

We can now use pass in any secret in any Docker file command with a simple cat or echo
in order to read that secret into another command without having to pass in the actual secret as plaintext. T
in the above example which is taken from the docker documentation. a file named foobar is created during build time.
It is only during build that this file actually contain any values and after if has been built
the secret value of mysecret is no longer present. This enables us to pass in a secrets file to an application that
we call in the RUN command.
