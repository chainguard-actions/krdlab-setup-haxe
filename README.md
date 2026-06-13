# setup-haxe

[![Build Status](https://github.com/krdlab/setup-haxe/actions/workflows/test.yml/badge.svg "GitHub Actions")](https://github.com/krdlab/setup-haxe/actions/workflows/test.yml)
[![License](https://img.shields.io/github/license/krdlab/setup-haxe.svg?label=license)](#license)

This action sets up a Haxe environment for use in your workflows.

## Usage

See [action.yml](action.yml) and [.github/workflows/](.github/workflows/).

Basic:
```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: krdlab/setup-haxe@v1
        with:
          haxe-version: 4.3.1
      - run: |
          haxe -version
          haxelib install hxnodejs
```

For nigthly versions:
```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: krdlab/setup-haxe@v1
        with:
          haxe-version: latest  # Install 'haxe_latest.tar.gz' from https://build.haxe.org/builds/haxe/linux64/
      - run: haxe -version
```

Caching global packages data:
```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: krdlab/setup-haxe@v1
        with:
          haxe-version: 4.3.1
          cache-dependency-path: 'lib.hxml'
      - run: |
          haxe -version
          haxelib install lib.hxml --always
```

## Privacy

This Action contacts Chainguard's licensing server to verify authorization. Connection metadata (IP address, GitHub repository identifier, timestamp, and any metadata encoded in the auth token) is transmitted to Chainguard, Inc. even if authorization is denied in accordance with our [Privacy Notice](https://www.chainguard.dev/legal/privacy-notice)
