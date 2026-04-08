# Docker
Docker images creation for different purposes

## CRUX-ARM 3.8 multiabi image
- Build the image with the Dockerfile
```shell
# docker build -t svc-carm38mabi .
```

- Run the image
```shell
# docker run --cap-add all --privileged --name CARM38MABI -it --entrypoint bash svc-carm38mabi
```
