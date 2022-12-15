# Doxygen GitHub Pages Deploy Action

GitHub Action for making and deploying Doxygen documentation to a GitHub pages branch

## Basic Usage

To deploy docs on every push to the `main` branch, create a new file in the `.github/workflows/` directory called `doxygen-gh-pages.yml` with the following contents:

```yml
name: Doxygen GitHub Pages Deploy Action

on:
  push:
    branches:
      - main
      - master

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: TaleSpire-Modding/doxygen-github-pages-action@v1.2.5
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
```

In the Doxyfile you can include the following Paramaters:
```
- <ProjectName>
- <Version>
- <Description>
```
and they will be updated from the `manifest.json`'s properties `[Name,version_number,description]` respectively.

## Options

- `github_token` (required): GitHub token for pushing to repo. See the [docs](https://git.io/passing-token) for more info.
- `branch` (optional): Branch to deploy to. Defaults to `gh-pages`.
- `folder` (optional): Folder where the docs are built. Defaults to `docs/html`.
- `config_file` (optional): Path of the Doxygen configuration file. Defaults to `Doxyfile`.
- ` manifest_file` (optional): Path of the thunderstore mods manifest file. Defaults to `manifest.json`

## Advanced Usage

Here is an example of a `.github/workflows/doxygen-gh-pages.yml` file with more advanced configuration:

```yml
name: Doxygen GitHub Pages Deploy Action

on:
  push:
    branches:
      - main
      - master
  workflow_dispatch:

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: TaleSpire-Modding/doxygen-github-pages-action@v1.2.5
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          branch: gh-pages
          folder: docs/html
          config_file: Doxyfile
          manifest_file: manifest.json
          readme_file: README.md
```

## About this Action

This action is a composite action containing the following steps:

### 1. Checkout repository

The [actions/checkout](https://github.com/actions/checkout) step is used to checkout the repository with any submodules.

### 2. Install Doxygen

Doxygen is installed by running the following command:

```bash
sudo apt-get install doxygen -y
```

### 3. Update Doxyfile

Doxyfile is updated from manifest.json

The [rgarcia-phi/json-to-variables](https://github.com/rgarcia-phi/json-to-variables) action is used to map manifest.json to environment variables and then [visiosto/replace-value](https://github.com/visiosto/replace-value) is used to map them to the Config File

### 4. Generate Doxygen Documentation

Doxygen documentation is generated by running the following command.

Set the `config_file` input option to change `Doxyfile` to a different filename.

```bash
doxygen Doxyfile
```

### 5. Create .nojekyll

Creating a .nojekyll file ensures pages with underscores work on GitHub Pages.

Set the `folder` input option to change `docs/html` to a different folder.

```bash
touch docs/html/.nojekyll
```

### 6. Deploy to GitHub Pages

The [JamesIves/github-pages-deploy-action](https://github.com/JamesIves/github-pages-deploy-action) action is used to deploy the documentation to GitHub Pages.

The `folder` option determines which folder to deploy. By default, it is `docs/html`.

The `branch` option determines which branch to deploy to. By default, it is `gh-pages`.

## License

This work is under an [MIT license](LICENSE)
