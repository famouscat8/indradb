# IndraDB [![Build Status](https://travis-ci.org/indradb/indradb.svg?branch=master)](https://travis-ci.org/indradb/indradb) [![Coverage Status](https://coveralls.io/repos/github/indradb/indradb/badge.svg?branch=master)](https://coveralls.io/github/indradb/indradb?branch=master)

A graph database written in rust. [Graph databases](https://en.wikipedia.org/wiki/Graph_database) facilitate all sorts of use cases, such as:

* Recommendation engines: graph databases make collaborative filtering and other recommendation algorithms easy to build and easy to understand.
* Fraud and threat detection: You can use graph databases to find strange/atypical user behavior.
* Social networking: Graphs provide a natural representation of people and the relationships between them.
* Bayesian networks, Markov networks, and similar data science systems.

This software is in the alpha state. Do not use this as your single source of truth, and do not expect peak performance.

## Features at a glance

* Support for directed, weighted, and typed graphs.
* An advanced query DSL.
* Multiple ways to work with the database:
    * Via HTTP API, and the clients that build off of it.
    * Via lua-based scripting.
    * By embedding IndraDB directly as a library.
* Multitenancy / support for multiple accounts.
* Support for metadata.
* Pluggable underlying datastores, with built-in support for in-memory-only, [postgres](https://www.postgresql.org/) and [rocksdb](https://github.com/facebook/rocksdb).
* Written in rust!

For more details, see:

* [An overview of the concepts in IndraDB.](https://indradb.github.io/concepts.html)
* [The HTTP API.](https://indradb.github.io/http-api.html)
* [The scripting API.](https://indradb.github.io/scripting.html)
* [The library.](https://github.com/indradb/indradb)
* [The python client.](https://github.com/indradb/python-client)

## Getting started

* [Download the latest release for your platform.](https://github.com/indradb/indradb/releases)
* Add the binaries to your `PATH`.
* Start the app: `indradb-server`

This should start an in-memory-only datastore, where all work will be wiped
out when the server is shutdown. You can persist your work with one of the
alternative datastores.

### In-memory

By default, IndraDB starts an in-memory datastore that does not persist to
disk, and does not support multitenancy. This is useful for kicking the tires,
or if your graph does not need to be persisted.

If you want to use the in-memory datastore, follow these steps:

* Start the server: `PORT=8000 indradb-server`
* Make a sample HTTP request to `http://localhost:8000`. Use a UUID with all
0's (i.e. `00000000-0000-0000-0000-000000000000`) for the username, and no
password.

### Postgres

If you want to use the postgres-backed datastore, follow these steps:

* Create a database: `createdb indradb`
* Initialize the database schema: `DATABASE_URL=postgres://localhost:5432/indradb indradb-admin init`
* Create a new account: `DATABASE_URL=postgres://localhost:5432/indradb indradb-account add`.
* Start the server: `DATABASE_URL=postgres://localhost:5432/indradb PORT=8000 indradb-server`.
* Make a sample HTTP request to `http://localhost:8000`, with the credentials supplied when you created the account.

### RocksDB

If you want to use the rocksdb-backed datastore, follow these steps:

* Create a new account: `DATABASE_URL=rocksdb://database.rdb indradb-account add`.
* Start the server: `DATABASE_URL=rocksdb://database.rdb PORT=8000 indradb-server`.
* Make a sample HTTP request to `http://localhost:8000`, with the credentials supplied when you created the account.

## Applications

This exposes three applications:

* `indradb-server`: For running the HTTP server.
* `indradb-account`: Manages the creation and deletion of accounts.
* `indradb-admin`: For managing the databases underlying IndraDB datastores. At the moment, this only has one function: to create the database schema for postgres-backed datastores, via `indradb-admin init`.

## Environment variables

Applications are configured via environment variables:

* `DATABASE_URL` - The connection string to the underlying database. Examples:
    * For a postgres datastore: `postgres://user:pass@localhost:5432/database-name`.
    * For a rocksdb datastore: `rocksdb://indradb.rdb`. This will store data in the directory `./indradb.rdb`.
* `PORT` - The port to run the server on. Defaults to `8000`.
* `SECRET` - The postgres implementation uses this as a [pepper](https://en.wikipedia.org/wiki/Pepper_%28cryptography%29) for increased security. Defaults to an empty string.
* `INDRADB_SCRIPT_ROOT` - The directory housing the lua scripts. Defaults to `./scripts`.

## Install from source

If you don't want to use the pre-built releases, you can build/install from source:

* Install [rust](https://www.rust-lang.org/en-US/install.html) 1.16+ stable or nightly.
* Make sure you have gcc 5+ and postgres 9.5+ installed.
* Clone the repo: `git clone git@github.com:indradb/indradb.git`.
* Build/install it: `cargo install`.

## Running tests

Use `./test.sh` to run the test suite. Note that this will run the full test suite across the entire workspace, including tests for all datastore implementations. You'll need to have postgres running, since the postgres datastore depends on it. Alternatively, you can filter which tests run via the `TEST_NAME` environment variable, e.g. `TEST_NAME=create_vertex ./test.sh`
