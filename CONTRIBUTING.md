# Contributing to Postgres Message Queue (PGMQ)

## Installation

The fastest way to get started is by running the Tembo docker image, where PGMQ comes pre-installed.

```bash
docker run -d --name postgres -e POSTGRES_PASSWORD=postgres -p 5432:5432 quay.io/tembo/pgmq-pg:latest
```

## Building from source

PGMQ is written as a Rust extension and requires [pgrx](https://github.com/pgcentralfoundation/pgrx).

To build pgmq from source, you need
* A toolchain capable of building Postgres
* Rust toolchain
* [pg_partman](https://github.com/pgpartman/pg_partman).

Once you have those pre-requisites, you need to setup `pgrx`.

```bash
cargo install --locked cargo-pgrx --version 0.11.3
```

Clone the repo and change into the directory.

```bash
git clone https://github.com/tembo-io/pgmq.git
cd pgmq
```

After this point, the steps differ slightly based on if you'd like to build
and install against an existing Postgres setup or develop against pgrx managed
development environment (which installs and allows you to test against multiple
Postgres versions).

### Install to a pre-existing Postgres

Initialize `cargo-pgrx`, and tell it the path to the your `pg_config`. For example,
if `pg_config` is on your `$PATH` and you have Postgres 15, you can run:

```bash
cargo pgrx init --pg16=`which pg_config`
```
Then, to install the release build, you can simply run:
```
cargo pgrx install --release
```

### Install against pgrx managed Postgres (Recommended for Development)

Initialize `cargo-pgrx` development environment:

```bash
cargo pgrx init
```

**Note**: Make sure you build and install `pg_partman` against the postgres installation
you want to build against (`PG_CONFIG` in `~/.pgrx/PG_VERSION/pgrx-install/bin/pg_config`
and `PGDATA` in `~/.pgrx/data-PG_MAJOR_VERSION`)

Example steps using `pg_partman` 4.7.3 and `PostgreSQL` 15.5:

```bash
wget https://github.com/pgpartman/pg_partman/archive/refs/tags/v4.7.3.tar.gz
tar xvfz v4.7.3.tar.gz
cd pg_partman-4.7.3
make install PG_CONFIG=~/.pgrx/15.5/pgrx-install/bin/pg_config PG_DATA=~/.pgrx/data-15
```

Then, you can use the run command, which will build and install the extension
and drop you into psql:

```bash
cargo pgrx run pg16
```

Finally, you can create the extension and get started with the example in the [README.md](README.md).

```psql
CREATE EXTENSION pgmq cascade;
```

# Packaging

Run this script to package into a `.deb` file, which can be installed on Ubuntu.

```
/bin/bash build-extension.sh
```

# Releases

PGMQ Postgres Extension releases are automated through a [Github workflow](https://github.com/tembo-io/pgmq/blob/main/.github/workflows/extension_ci.yml). The compiled binaries are publish to and hosted at [pgt.dev](https://pgt.dev). To create a release, create a new tag follow a valid [semver](https://semver.org/), then create a release with the same name. Auto-generate the release notes and/or add more relevant details as needed. See subdirectories for the [Rust](https://github.com/tembo-io/pgmq/tree/main/core) and [Python](https://github.com/tembo-io/pgmq/tree/main/tembo-pgmq-python) SDK release processes.
