<div align="center">

# Moodle Plugin CI Action

<a href="#description">Description</a>
•
<a href="#params">Params</a>
•
<a href="#usage">Usage</a>
•
<a href="#important-note">Important Note</a>
•
<a href="#license">License</a>
</div>

## Description

This GitHub Action facilitates the execution of comprehensive code quality checks and tests for Moodle plugins. It is designed to be reusable and is based on the official [Moodle Plugin CI workflow](https://github.com/moodlehq/moodle-plugin-ci/blob/main/.github/workflows/test.yml) , enabling seamless integration into other Moodle plugin repositories.

### Features

Executes a comprehensive suite of checks:

- PHP Linting
- PHP Mess Detector
- Moodle Code Checker
- PHPDoc Validation
- Plugin Validation
- PHPUnit Tests
- Behat Tests

> [!IMPORTANT]
> This Action does not start database services. If your tests require a database (PostgreSQL or MariaDB), you must define the services in your workflow.

## Params 

| Param | Description | Required | Default |
| --- | --- | --- | --- |
| php-version | Version of PHP to use | `false` | 8.3 |
| moodle-branch | Moodle branch to test against | `false` | MOODLE_500_STABLE |
| database | Database to use: pgsql or mariadb | `false` | pgsql |
| plugin-path | Path to the Moodle plugin to test | `false` | ./ |

## Usage

### Basic usage (without a database)

```yml
name: Test for Moodle plugin in version 5.0 to php 8.3

jobs:
  test:
    runs-on: ubuntu-22.04
    steps:
      - name: Moodle CI PHP 8.3 Moodle 5.0 DB pgsql
        uses: gfrancv/moodle-plugin-ci-action@v1
```

### Basic usage (with database)

```yml
name: Test for Moodle plugin in version 5.0 to php 8.3

jobs:
  test:
    runs-on: ubuntu-22.04
    services:
      postgres:
        image: postgres:14
        env:
          POSTGRES_USER: postgres
          POSTGRES_HOST_AUTH_METHOD: trust
        ports:
          - 5432:5432
      # Or .
      mariadb:
        image: mariadb:10.11
        env:
          MYSQL_ALLOW_EMPTY_PASSWORD: "true"
        ports:
          - 3306:3306
    steps:
      - name: Moodle CI PHP 8.3 Moodle 5.0 DB pgsql
        uses: gfrancv/moodle-plugin-ci-action@v1
```

### Advanced use for multiple versions
```yml
name: Test for Moodle plugin

jobs:
  test:
    runs-on: ubuntu-22.04

    strategy:
      matrix:
        include:
          - php: '8.2'
            moodle: 'MOODLE_500_STABLE'
            db: pgsql
          - php: '8.3'
            moodle: 'MOODLE_500_STABLE'
            db: mariadb
    steps:
      - name: Moodle CI PHP ${{ matrix.php }} Moodle ${{ matrix.moodle }} DB ${{ matrix.db }}
        uses: gfrancv/moodle-plugin-ci-action@v1
        with:
          php-version: ${{ matrix.php }}
          moodle-branch: ${{ matrix.moodle }}
          database: ${{ matrix.db }}
```

## Important Note:

Ensure that your plugin repository includes a `version.php` file with the appropriate `$plugin->supported` range to align with the Moodle versions being tested.

This action is intended to be used within a GitHub Actions workflow in your plugin repository.

## License

The scripts and documentation in this project are released under the MIT License