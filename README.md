# sequelize/cli [![Build Status](https://travis-ci.org/sequelize/cli.svg?branch=master)](https://travis-ci.org/sequelize/cli) [![Greenkeeper badge](https://badges.greenkeeper.io/sequelize/cli.svg)](https://greenkeeper.io/)

The Sequelize Command Line Interface (CLI)

## Sequelize Support

In current v3 release CLI generate migration/models which follow Sequelize v3 format, CLI will work with Sequelize v4 in most cases but migration/model skeleton is still generated to support v3.

Full support for Sequelize v4 will be coming soon with [Sequelize CLI v4](https://github.com/sequelize/cli/issues/441)

## Installation

Install this globally and you'll have access to the `sequelize` command anywhere on your system.

```bash
$ npm install -g sequelize-cli
```

or install it locally to your `node_modules` folder

```bash
$ npm install --save sequelize-cli
```
## Global Install Usage

```bash
$ sequelize
```

```
Sequelize CLI [Node: 6.11.2, CLI: 3.0.0, ORM: 4.8.0]

Commands:
  db:migrate                        Run pending migrations
  db:migrate:schema:timestamps:add  Update migration table to have timestamps
  db:migrate:status                 List the status of all migrations
  db:migrate:undo                   Reverts a migration
  db:migrate:undo:all               Revert all migrations ran
  db:seed                           Run specified seeder
  db:seed:undo                      Deletes data from the database
  db:seed:all                       Run every seeder
  db:seed:undo:all                  Deletes data from the database
  init                              Initializes project
  init:config                       Initializes configuration
  init:migrations                   Initializes migrations
  init:models                       Initializes models
  init:seeders                      Initializes seeders
  migration:generate                Generates a new migration file       [aliases: migration:create]
  model:generate                    Generates a model and its migration  [aliases: model:create]
  seed:generate                     Generates a new seed file            [aliases: seed:create]

Options:
  --version  Show version number                                         [boolean]
  --help     Show help                                                   [boolean]
```

## Local Install Usage

```
$ node_modules/.bin/sequelize [--HARMONY-FLAGS]
```

## Options

The manuals will show all the flags and options which are available for the respective tasks.
If you find yourself in a situation where you always define certain flags in order to
make the CLI compliant to your project, you can move those definitions also into a file called
`.sequelizerc`. The file will get `require`d if available and can therefore be either a JSON file
or a Node.JS script that exports a hash.

### Example for a Node.JS script

```js
var path = require('path')

module.exports = {
  'config':          path.resolve('config', 'database.json'),
  'migrations-path': path.resolve('db', 'migrate')
}
```

This will configure the CLI to always treat `config/database.json` as config file and
`db/migrate` as the directory for migrations.

### Configuration file

By default the CLI will try to use the file `config/config.js` and `config/config.json`. You can modify that path either via the `--config` flag or via the option mentioned earlier. Here is how a configuration file might look like (this is the one that `sequelize init` generates):

```json
{
  "development": {
    "username": "root",
    "password": null,
    "database": "database_development",
    "host": "127.0.0.1",
    "dialect": "mysql"
  },
  "test": {
    "username": "root",
    "password": null,
    "database": "database_test",
    "host": "127.0.0.1",
    "dialect": "mysql"
  },
  "production": {
    "username": "root",
    "password": null,
    "database": "database_production",
    "host": "127.0.0.1",
    "dialect": "mysql"
  }
}
```

The properties can also be combined to a `url`:

```json
{
  "development":  {
    "url": "mysql://root:password@mysql_host.com/database_name",
    "dialect": "mysql"
  }
}
```

In case of a JS file it obviously needs to `module.exports` the object.
Optionally, it's possible to put all the configuration to the `url` option. The format is explained in the section below.

### Configuration Connection String

As an alternative to the `--config` option with configuration files defining your database, you can
use the `--url` option to pass in a connection string. For example:

`sequelize db:migrate --url 'mysql://root:password@mysql_host.com/database_name'`

### Configuration Connection Environment Variable

Another possibility is to store the URL in an environment variable and to tell
the CLI to lookup a certain variable during runtime. Let's assume you have an
environment variable called `DB_CONNECTION_STRING` which stores the value
`mysql://root:password@mysql_host.com/database_name`. In order to make the CLI
use it, you have to use declare it in your config file:

```json
{
    "production": {
        "use_env_variable": "DB_CONNECTION_STRING"
    }
}
```

With v2.0.0 of the CLI you can also just directly access the environment variables inside the `config/config.js`:

```js
module.exports = {
    "production": {
        "hostname": process.env.DB_HOSTNAME
    }
}
```

### Storage

There are three types of storage that you can use: `sequelize`, `json`, and `none`.

- `sequelize` : stores migrations and seeds in a table on the sequelize database
- `json` : stores migrations and seeds on a json file
- `none` : does not store any migration/seed


#### Migration

By default the CLI will create a table in your database called `SequelizeMeta` containing an entry
for each executed migration. To change this behavior, there are three options you can add to the
configuration file. Using `migrationStorage`, you can choose the type of storage to be used for
migrations. If you choose `json`, you can specify the path of the file using `migrationStoragePath`
or the CLI will write to the file `sequelize-meta.json`. If you want to keep the information in the
database, using `sequelize`, but want to use a different table, you can change the table name using
`migrationStorageTableName`.

```json
{
  "development": {
    "username": "root",
    "password": null,
    "database": "database_development",
    "host": "127.0.0.1",
    "dialect": "mysql",

    // Use a different storage type. Default: sequelize
    "migrationStorage": "json",

    // Use a different file name. Default: sequelize-meta.json
    "migrationStoragePath": "sequelizeMeta.json",

    // Use a different table name. Default: SequelizeMeta
    "migrationStorageTableName": "sequelize_meta"
  }
}
```

NOTE: The `none` storage is not recommended as a migration storage. If you decide to use it, be
aware of the implications of having no record of what migrations did or didn't run.


#### Seed

By default the CLI will not save any seed that is executed. If you choose to change this behavior (!),
you can use `seederStorage` in the configuration file to change the storage type. If you choose `json`,
you can specify the path of the file using `seederStoragePath` or the CLI will write to the file
`sequelize-data.json`. If you want to keep the information in the database, using `sequelize`, you can
specify the table name using `seederStorageTableName`, or it will default to `SequelizeData`.

```json
{
  "development": {
    "username": "root",
    "password": null,
    "database": "database_development",
    "host": "127.0.0.1",
    "dialect": "mysql",
    // Use a different storage. Default: none
    "seederStorage": "json",
    // Use a different file name. Default: sequelize-data.json
    "seederStoragePath": "sequelizeData.json",
    // Use a different table name. Default: SequelizeData
    "seederStorageTableName": "sequelize_data"
  }
}
```


### Dialect specific options

In order to pass options to the underlying database connectors, you can add the property `dialectOptions`
to your configuration like this:

```js
var fs = require('fs');

module.exports = {
  development: {
    dialect: 'mysql',
    dialectOptions: {
      ssl: {
        ca: fs.readFileSync(__dirname + '/mysql-ca.crt')
      }
    }
  }
};
```

### Schema migration

Sequelize CLI continue to use schema from `v2` and fully compatible with `v2`. If you are still using old schema from pre `v2`, use `v2` to upgrade to current schema with `db:migrate:old_schema`

#### Timestamps

Since v2.8.0 the CLI supports a adding timestamps to the schema for saving the executed migrations. You can opt-in for timestamps by running the following command:

```bash
$ sequelize db:migrate:schema:timestamps:add
```

### The migration schema

The CLI uses [umzug](https://github.com/sequelize/umzug) and its migration schema. This means a migration has to look like this:

```js
"use strict";

module.exports = {
  up: function(queryInterface, Sequelize, done) {
    done();
  },

  down: function(queryInterface) {
    return new Promise(function (resolve, reject) {
      resolve();
    });
  }
};
```

Please note that you can either return a Promise or call the third argument of the function once your asynchronous logic was executed. If you pass something to the callback function (the `done` function) it will be treated as erroneous execution.

Additional note: If you need to access the sequelize instance, you can easily do that via `queryInterface.sequelize`. For example `queryInterface.sequelize.query('CREATE TABLE mytable();')`.

## Documentation and FAQ

More documentation around migrations [here](http://docs.sequelizejs.com/manual/tutorial/migrations.html). You can find FAQ section [here](https://github.com/sequelize/cli/blob/master/FAQ.md)
