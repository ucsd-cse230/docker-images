# workspace-vscode-lean

Docker image for [PrairieLearn workspace](https://prairielearn.readthedocs.io/en/latest/workspaces/) based on <https://github.com/PrairieLearn/PrairieLearn/tree/master/workspaces/vscode-python>

The image comes with `leanprover/lean4:4.15.0`

## Build and Push Image

```bash
docker build --pull --rm -f "Dockerfile" -t <username>/workspace-vscode-lean:latest "."
docker image push <username>/workspace-vscode-lean:latest
```
where `<username>` is the username in <https://hub.docker.com/> where you want to upload the image.

## PrairieLearn config

To use as a PrairieLearn workspace the `info.json` should contain at least the following:

```json
{
  "workspaceOptions": {
    "image": "<username>/workspace-vscode-lean",
    "home": "/home/coder/workspace",
    "port": 8080
  }
}
