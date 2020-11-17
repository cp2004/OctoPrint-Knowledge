## Automatic workflow to use latest release, via URL `/releases/latest/download/release.zip`

Useful for when using versioneer in a plugin, since the plugin manager installs from the branch URL by default
- which does not contain the correct metadata to generate version numbers.

Add this workflow to the plugin's repository, be sure to change the archive name to the correct repo name!:
```yaml
name: Release
on:
  release:
    types: [published, prereleased]

jobs:
  build:
    name: 🔨 Build distribution
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
          fetch-depth: 0
      - name: 🏗 Set up Python 3.8
        uses: actions/setup-python@v1
        with:
          python-version: 3.8
      - name: 🏗 Install build dependencies
        run: |
          python -m pip install wheel octoprint --user
      - name: 🔨 Build a source zip
        run: |
          python setup.py sdist --formats=zip
      - name: ⬆ Upload build result
        uses: actions/upload-artifact@v1
        with:
          name: dist
          path: dist

  test-install:
    name: 🧪 Installation tests
    needs: build
    strategy:
      matrix:
        python: ["2.7", "3.7", "3.8"]
    runs-on: ubuntu-latest
    steps:
      - name: 🏗 Set up Python ${{ matrix.python }}
        uses: actions/setup-python@v1
        with:
          python-version: ${{ matrix.python }}
      - name: ⬇ Download build result
        uses: actions/download-artifact@v1
        with:
          name: dist
          path: dist
      - name: 🏗 Install dependencies
        run: |
          python -m pip install --upgrade wheel setuptools pip
          python -m pip install octoprint
      - name: 🧪 Test install of package
        run: |
          python -m pip install dist/OctoPrint-AutoLoginConfig-*.zip  # CHANGE THIS BIT TO THE CORRECT PLUGIN NAME

  upload-asset:
    name: 📦 Upload asset to release
    runs-on: ubuntu-latest
    needs:
      - build
      - test-install
    steps:
      - name: ⬇ Download build result
        uses: actions/download-artifact@v1
        with:
          name: dist
          path: dist
      - name: 🚚 Rename to release.zip
        run: |
          cp dist/OctoPrint-AutoLoginConfig-*.zip release.zip  # CHANGE THIS BIT TO THE CORRECT PLUGIN NAME
      - name: 🥅 Catch release ID
        id: get_release
        uses: bruceadams/get-release@v1.2.2
        env:
          GITHUB_TOKEN: ${{ github.token }}
      - name: 📦 Attach release artifact
        uses: actions/upload-release-asset@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          upload_url: ${{ steps.get_release.outputs.upload_url }}
          asset_path: release.zip
          asset_name: release.zip
          asset_content_type: application/zip
```
