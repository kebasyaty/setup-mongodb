# setup-mongodb

The missing action for MongoDB :tada:

- Faster (with the default version) and simpler than containers
- Works on Linux, Mac, and Windows
- Supports different versions

[![Build Status](https://github.com/ankane/setup-mongodb/workflows/build/badge.svg?branch=v1)](https://github.com/ankane/setup-mongodb/actions)

## Getting Started

Add it as a step to your workflow

```yml
      - uses: kebasyaty/setup-mongodb@v1
```

## Versions

Specify a version

```yml
      - uses: kebasyaty/setup-mongodb@v1
        with:
          mongodb-version: 7.0
```

Currently supports

Version | `7.0` | `6.0` | `5.0` | `4.4`
--- | --- | --- | --- | ---
`ubuntu-22.04` | ✓ | default | |
`ubuntu-20.04` | ✓ | ✓ | default |
`macos-13` | ✓ | ✓ | default | ✓ |
`macos-12` | ✓ | ✓ | default | ✓ |
`macos-11` | ✓ | ✓ | default | ✓ |
`windows-2022` | | | default |
`windows-2019` | | | default |

Full working example for Crystal language

```yml
name: Specs

on:
  push:
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ${{ matrix.os }}

    strategy:
      matrix:
        os: [ubuntu-22.04]
        mongodb-version: [7.0, 6.0]

    container:
      image: crystallang/crystal:latest
      options: --user root

    steps:
      - name: Install sudo, wget, gnupg2 and systemctl
        run: |
          apt update
          apt install -y sudo
          sudo apt install -y wget gnupg2
          sudo apt -y install systemctl
      - name: Download source
        uses: actions/checkout@v4
      - name: Install dependencies
        run: shards install
      - name: Check formatting
        run: crystal tool format --check
      - name: Start MongoDB
        uses: kebasyaty/setup-mongodb@v1
        with:
          mongodb-version: ${{ matrix.mongodb-version }}
      - name: Run tests
        run: crystal spec
```

Alternative option without setup-mongodb@v1
<br>
Ubuntu 22.04, MongoDB 6.0 and Crystal language

```yml
name: Specs

on:
  push:
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-22.04

    container:
      image: crystallang/crystal:latest
      options: --user root

    steps:
      - name: Install MongoDB 6.0 Server
        run: |
          apt update
          apt install -y sudo
          sudo apt install -y gnupg curl systemctl
          curl -fsSL https://pgp.mongodb.com/server-6.0.asc | \sudo gpg -o /etc/apt/trusted.gpg.d//mongodb-server-6.0.gpg \--dearmor
          echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
          sudo apt update
          sudo apt install -y mongodb-org
          sudo systemctl enable --now mongod
      - name: Download source
        uses: actions/checkout@v4
      - name: Install dependencies
        run: shards install
      - name: Check formatting
        run: crystal tool format --check
      - name: Run tests
        run: crystal spec
```

## Extra Steps

Run queries

```yml
      - run: mongosh --eval "db.version()"
```

Use `mongo` for MongoDB < 6

## Related Actions

- [setup-postgres](https://github.com/ankane/setup-postgres)
- [setup-mysql](https://github.com/ankane/setup-mysql)
- [setup-mariadb](https://github.com/ankane/setup-mariadb)
- [setup-elasticsearch](https://github.com/ankane/setup-elasticsearch)
- [setup-opensearch](https://github.com/ankane/setup-opensearch)
- [setup-sqlserver](https://github.com/ankane/setup-sqlserver)

## Contributing

Everyone is encouraged to help improve this project. Here are a few ways you can help:

- [Report bugs](https://github.com/ankane/setup-mongodb/issues)
- Fix bugs and [submit pull requests](https://github.com/ankane/setup-mongodb/pulls)
- Write, clarify, or fix documentation
- Suggest or add new features
