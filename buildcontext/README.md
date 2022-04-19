### Labs

```sh
# Preparation: build context
❯ dd if=/dev/urandom of=contexts/small/file bs=1K count=1
❯ dd if=/dev/urandom of=contexts/small/dummy bs=1M count=1024
❯ dd if=/dev/urandom of=contexts/large/file bs=1M count=10

# Measurement (also check logs)
❯ time docker build -t small -f Dockerfile contexts/small
❯ time docker build -t large -f Dockerfile contexts/large

# As a legacy method
❯ time sh -c "DOCKER_BUILDKIT=0 docker build -t small -f Dockerfile contexts/small"
❯ time sh -c "DOCKER_BUILDKIT=0 docker build -t large -f Dockerfile contexts/large"

```

### Questions and Arguments
- https://stackoverflow.com/questions/71838211/docker-build-context-when-it-transferred-and-does-it-cached-by-the-runtime/71885289?noredirect=1#comment127084326_71885289
