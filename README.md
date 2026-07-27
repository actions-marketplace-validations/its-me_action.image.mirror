# Cross-registry image mirror

A GitHub Action that logs in to a source and destination registry, then
copies each of a set of tags from one image repository to another via
`docker buildx imagetools create`.

It replaces the `push-hub` / `push-quay` job pattern duplicated across this
account's image repositories (e.g.
[its-me/image-workflow](https://github.com/its-me/image-workflow)'s
`simple.yaml`) with a single reusable step.

## Usage

```yaml
push-hub:
  name: Deploy to Docker Hub
  needs: build
  runs-on: ubuntu-slim
  permissions:
    packages: read
  steps:
    - uses: its-me/action.image.mirror@v0
      with:
        source: ghcr.io/its-me/workflow
        source-username: ${{ github.actor }}
        source-password: ${{ secrets.GITHUB_TOKEN }}
        destination: ${{ secrets.HUB_USERNAME }}/workflow
        destination-username: ${{ secrets.HUB_USERNAME }}
        destination-password: ${{ secrets.HUB_TOKEN }}
        tags: ${{ needs.build.outputs.tags }}
```

For a Quay.io destination, point `destination` at
`quay.io/<namespace>/<repository>` — the registry to log in to is derived
from `source`/`destination` automatically (see `Inputs` below).

## Inputs

| Name                    | Description                                                                                 | Default  |
| ------------------------ | ------------------------------------------------------------------------------------------------ | -------- |
| `source`                 | Source image repository, e.g. `ghcr.io/its-me/workflow` or `1tsme/workflow`. The registry to log in to is derived from the first path segment: it's treated as a hostname (and logged in to) when it contains a `.` or `:` or is `localhost`, otherwise the image is assumed to be on Docker Hub. | _(none)_ |
| `source-username`        | Username for logging in to the source registry.                                                   | _(none)_ |
| `source-password`        | Password or token for logging in to the source registry.                                          | _(none)_ |
| `destination`             | Destination image repository, e.g. `1tsme/workflow` or `quay.io/itsme/workflow`. The registry to log in to is derived the same way as for `source`. | _(none)_ |
| `destination-username`   | Username for logging in to the destination registry.                                              | _(none)_ |
| `destination-password`   | Password or token for logging in to the destination registry.                                     | _(none)_ |
| `tags`                    | Whitespace-separated list of tag names to copy from source to destination.                        | _(none)_ |

## License

[MIT](LICENSE)
