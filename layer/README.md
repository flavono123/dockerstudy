### Lab(WIP)

```sh
❯ docker build -t layer .
❯ docker inspect layer | jq -r '.[0].RootFS.Layers[]'
❯ docker history layer
❯ dive layer
```

### Questions and Arguments
- https://stackoverflow.com/questions/71853912/docker-workdir-create-a-layer/71888818#71888818
