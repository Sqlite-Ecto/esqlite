[![CircleCI](https://circleci.com/gh/Sqlite-Ecto/elixir_sqlite.svg?style=svg)](https://circleci.com/gh/Sqlite-Ecto/elixir_sqlite)
[![Coverage Status](https://coveralls.io/repos/github/Sqlite-Ecto/elixir_sqlite/badge.svg?branch=master)](https://coveralls.io/github/Sqlite-Ecto/elixir_sqlite?branch=master)

# Sqlite
Elixir API for interacting with SQLite databases.
This library allows you to use the accelent sqlite engine from
erlang. The library is implemented as a nif library, which allows for
the fastest access to a sqlite database. This can be risky, as a bug
in the nif library or the sqlite database can crash the entire Erlang
VM. If you do not want to take this risk, it is always possible to
access the sqlite nif from a separate erlang node.

Special care has been taken not to block the scheduler of the calling
process. This is done by handling all commands from erlang within a
lightweight thread. The erlang scheduler will get control back when
the command has been added to the command-queue of the thread.

# Usage
```elixir
alias Sqlite.Connection
{:ok, database} = Connection.open(database: "/tmp/database.sqlite3")
{:ok, statement} = Connection.prepare(database, "CREATE TABLE data (id int, data text)")
{:ok, _} = Connection.execute(database, statement, [])
{:ok, statement} = Connection.prepare(database, "INSERT INTO data (data) VALUES ($1)")
{:ok, _} = Connection.execute(database, statement, ["neat!"])
{:ok, %Sqlite.Result{columns: [:data], num_rows: 1, rows: [["neat!"]]}} = Connection.query(database, "SELECT data FROM data", [])
```

# Tests
Since this project was originally an Erlang package, I chose to maintain the
original module name (as an alias) and it's tests to try to maintain
backwards compatibility. By default these tests get ran by default.

```bash
# All the tests without the bench marks.
mix test
```

# Benchmarks
There is also a benchmark suite located in the `bench` directory.
It does not get ran with the test suite since it can take quite a while.

```bash
# run all the tests and the benchmarks.
mix test --include bench
```

# Thanks and License
This project is originally a fork of [esqlite](https://github.com/mmzeeman/esqlite)
Which was originally an Erlang implementation. The underlying NIF code (in `c_src`),
and the test file in `erl_test` both retain the original Apache v2 license.
