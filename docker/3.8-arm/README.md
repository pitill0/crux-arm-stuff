# Docker
Docker images creation for different purposes

## CRUX-ARM 3.8 hardfloat image
- Build the image with the Dockerfile
```shell
# docker build -t svc-carm38hf .
```

- Run the image
```shell
# docker run --cap-add all --privileged --name CARM38HF -it --entrypoint bash svc-carm38hf
```
