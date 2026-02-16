[![Serverpod banner](https://github.com/serverpod/serverpod/raw/main/misc/images/github-header.webp)](https://github.com/serverpod/serverpod)

# serverpod-cloud-deploy

[serverpod-cloud-deploy][] deploys a project to [Serverpod Cloud](https://serverpod.dev/cloud).

[serverpod-cloud-deploy]: https://github.com/marketplace/actions/serverpod-cloud-deploy


## Usage

This is a simple workflow example that uses this action.

Prerequisites:
  * `serverpod generate` has been run and the generated files are committed

  * `scloud.yaml` is committed in the server package
    > (create this with `scloud launch` or `scloud link`)

  * A GitHub secret has been set with the access token
    > (create this with `scloud auth create-token`)

```yml
name: Automated Serverpod Cloud deploy

on:
  push:
    branches: [main]

permissions:
  contents: read

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: dart-lang/setup-dart@v1

      - uses: serverpod/serverpod-cloud-deploy@v0.1
        with:
          token: ${{ secrets.MY_SERVERPOD_CLOUD_ACCESS_TOKEN }}
```


## Inputs

The action takes the following inputs:

- Required:

  * `token`: Serverpod Cloud access token.

- Optional:
  * `working_directory`: Path to the action's working directory.
    Default value is `.`

  * `project_id`: Tenant project ID.

  * `project_dir`: Path to the project's server directory.

  * `project_config_file`: Path to the project's configuration file.


## Combine with serverpod generate

The following example does not requires the generated serverpod files to be
committed nor the scloud.yaml file to be available.

```yml
name: Automated Serverpod Cloud generate & deploy
description: >
  Checks out the repository, sets up Flutter, runs serverpod generate,
  and deploys to Serverpod Cloud.

on:
  push:
    branches: ["main"]
    paths:
      # Only redeploy on changes in the server directory
      - 'packages/my_server/**'

permissions:
  contents: read

jobs:
  generate:
    runs-on: ubuntu-latest
    steps:
        - uses: actions/checkout@v4

        - name: Setup Flutter SDK
          uses: subosito/flutter-action@v2
          with:
            # Picks the latest stable version. Some may want to pin the version instead.
            channel: stable
            cache: true

        - name: Run serverpod generate
          working-directory: ./packages/my_server
          # Automatically picks the used Serverpod version from dependencies
          run: dart run serverpod_cli generate

  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: serverpod/serverpod-cloud-deploy@v0.1
        with:
          token: ${{ secrets.MY_SERVERPOD_CLOUD_ACCESS_TOKEN }}
          project_id: my-project-id
          project_dir: ./packages/my_server
```


## License

See the [LICENSE](LICENSE) file.

## Contributing

Contributions are welcome! Please see [CONTRIBUTING.md](CONTRIBUTING.md).

## Version history

Please see our [CHANGELOG.md](CHANGELOG.md) file.
