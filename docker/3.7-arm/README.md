# Docker
Docker images creation for different purposes

## CRUX-ARM 3.7 hardfloat image
- Build the image with the Dockerfile
```shell
# docker build -t svc-carm37hf .
```

- Run the image
```shell
# docker run --cap-add all --privileged --name CARM37HF -it --entrypoint bash svc-carm37hf
```
