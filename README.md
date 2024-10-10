# pipelines
Common pipelines collection for the Federated Node related repositories

## Trigger Task
Upon the action trigger, i.e. a PR merged on `branch_trigger`
```yaml
on:
  push:
    branches:
      - branch_trigger
```

it will:
- checkout the contents of a k8s template in the `TEMPLATE_PATH` variable and save it in the action context
- checkout the `DEST_BRANCH` branch via the github cli, using `SA_TOKEN` as service account token
- Replaces the file `TEMPLATE_PATH`, and appends with `jq` some dynamic information such as the repository name with the organization name and the user that opened the PR involving the `branch_trigger`
- commits and pushes the changes to `DEST_BRANCH`

_Note that the `TEMPLATE_PATH` has to be a json file since it uses jq for file manipulation_

### Example
```yaml
jobs:
  update:
    uses: Aridhia-Open-Source/pipelines/.github/workflows/trigger-task.yml@main
    with:
      DEST_BRANCH: new_changes
      TEMPLATE_PATH: k8s/template-custom-resource-definition.json
    secrets:
      SA_TOKEN: ${{ secrets.SA_TOKEN }}
```

## Build Docker
Just standardises the build docker image process, mostly to set the image tag. This is done following the pattern below, assuming the `TAG` variable provided has value 0.0.1
|branch|`HASH_SUFFIX`|tag used|
|---|---|---|
|main||0.0.1|
|not main|true|0.0.1-ae12df|
|not main|false|0.0.1-dev|

_note that `ae12df` would be the first 6 characters of that commit_

### Example
```yaml
jobs:
  build:
    uses: Aridhia-Open-Source/pipelines/.github/workflows/build-docker.yml@main
    with:
      TAG: 1.2.3
      IMAGE: beautiful_image
      BUILD_PATH: build/docker
      HASH_SUFFIX: false
      DOCKER_ACR: ghcr.io
    secrets:
      # Or whichever GitHub secret the docker registry token is save as
      DOCKER_TOKEN: ${{ secrets.DOCKER_TOKEN }}
```
