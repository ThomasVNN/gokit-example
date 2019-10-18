# gokit-example

This is a demo service for accounts with balances and transfers between those accounts.

<!-- MarkdownTOC levels="1,2" -->

- [Setup](#setup)
- [Running](#running)
- [API Docs](#api-docs)
- [Usage](#usage)
- [Development](#development)

<!-- /MarkdownTOC -->

## Setup

### Setup postgres

You can use docker-compose:

```sh
# start postgres docker container
docker-compose up -d
```

Otherwise, install and run postgres as you wish, and create a database.
The default database name is `wallet`.

### Install golang-migrate

[migrate](https://github.com/golang-migrate/migrate) is used to manage DB migrations and setup the initial tables.

Follow the installation instructions from https://github.com/golang-migrate/migrate/tree/master/cmd/migrate to install.

### Install mockery

Mockery is used to generate mocks for testing. This is only needed if developing this software. You can skip it if you are only running the software.

```sh
go get github.com/vektra/mockery/.../
```

### Setup database

If using docker-compose:

```sh
# create database
docker exec -it my_postgres psql -U postgres -c "create database wallet" 
```

Otherwise, create a database in your postgres setup as you wish. The default
database name is `wallet`.

Run the migration tool to initialize the [database schema](./migrations/1_init.up.sql):

```sh
make update-db
```

## Running

By default, the application runs on `localhost:8888` and connects to the local postgres database run by docker-compose, with url `postgresql://postgres@localhost:54320/wallet?sslmode=disable`.

```sh
go run cmd/wallet/
```

## API Docs

See the [API Docs](./API.md)

## Usage

### Add test data for manual experimentation

```sh
go run cmd/testdata
```

This adds 6 accounts and credits each account with 100 units of their currency.

### Run the server

TODO -- docker-compose?

```sh
go run cmd/wallet
```

### List accounts

```sh
curl 'http://localhost:8888/v1/accounts'
```

### Make a transfer

Copy two IDs from the previous response, for accounts with the same currency type, and fill them in the response below.

```sh
curl -X POST 'http://localhost:8888/v1/transfer' -d '{"to":"...","from":"...","amount":"1.23"}'
```

### List payments

See all payments. It will include the original credits to the accounts created by the `testdata` tool, and the new transfer payments.

```
curl 'http://localhost:8888/v1/payments'
```

## Development

### Running tests

```sh
make test
```

### Linting

```sh
# Install linters if you don't have them
make install-linters
# Run the linters (uses gometalinter)
make lint
```

### Generate mocks

First, [install mockery](#install-mockery)

```sh
make generate-mocks
```
