<div align="center">

# Moodle Plugin CI Action

[![Test](https://img.shields.io/github/actions/workflow/status/GFrancV/moodle-plugin-ci-action/test.yml?style=for-the-badge&label=Test&logo=github)](https://GitHub.com/GFrancV/moodle-plugin-ci-action/tags/)
[![GitHub tag](https://img.shields.io/github/v/tag/GFrancV/moodle-plugin-ci-action?style=for-the-badge&logo=github)](https://GitHub.com/GFrancV/moodle-plugin-ci-action/tags)
![Moodle](https://img.shields.io/badge/^4.4-1000?style=for-the-badge&logo=Moodle&logoColor=ffffff&labelColor=gray&color=f98012&label=Moodle)

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
| database | Database to use: pgsql or mariadb | `false` | null |
| plugin-path | Path to the Moodle plugin to test | `false` | ./ |

## Usage

### Basic usage (without a database)

This assumes that the plugin code is located in the root directory. For this, the action [checkout@v4](https://github.com/marketplace/actions/checkout) is used to clone the current repository.

```yml
name: Test Moodle plugin (no database)

jobs:
  test:
    runs-on: ubuntu-22.04
    steps:
      - name: Checkout action code
        uses: actions/checkout@v4

      - name: Moodle CI PHP 8.3 Moodle 5.0
        uses: gfrancv/moodle-plugin-ci-action@v1.1.0
        with:
          php-version: 8.3
          moodle-branch: MOODLE_500_STABLE
```
> Note: It is important to checkout your plugin repository before running the action. The action needs access to the plugin files, so make sure to use actions/checkout@v4 (or a similar checkout step) to clone your repository into the workflow workspace.

### Basic usage (with database)

This assumes that the plugin code is located in the root directory.

```yml
name: Test Moodle plugin (with database)

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
      # Or 
      mariadb:
        image: mariadb:10.11
        env:
          MYSQL_ALLOW_EMPTY_PASSWORD: "true"
        ports:
          - 3306:3306
    steps:
      - name: Checkout action code
        uses: actions/checkout@v4

      - name: Moodle CI PHP 8.3 Moodle 5.0 DB mariadb
        uses: gfrancv/moodle-plugin-ci-action@v1.1.0
        with: 
          php-version: 8.3
          moodle-branch: MOODLE_500_STABLE
          database: mariadb
```

### Advanced use for multiple versions

```yml
name: Test Moodle plugin (matrix)

jobs:
  test:
    runs-on: ubuntu-22.04
    services: 
      mariadb:
        image: mariadb:10.11
        env:
          MYSQL_ALLOW_EMPTY_PASSWORD: "true"
        ports:
          - 3306:3306
          
    strategy:
      matrix:
        include:
          - php: '8.2'
            moodle: 'MOODLE_500_STABLE'
            db: mariadb
          - php: '8.3'
            moodle: 'MOODLE_500_STABLE'
            db: mariadb

    steps:
      - name: Checkout action code
        uses: actions/checkout@v4

      - name: Moodle CI PHP ${{ matrix.php }} Moodle ${{ matrix.moodle }} DB ${{ matrix.db }}
        uses: gfrancv/moodle-plugin-ci-action@v1.1.0
        with:
          php-version: ${{ matrix.php }}
          moodle-branch: ${{ matrix.moodle }}
          database: ${{ matrix.db }}
```

## Important Note:

Ensure that your plugin repository includes a `version.php` file with the appropriate `$plugin->supported` range to align with the Moodle versions being tested.

This action is intended to be used within a GitHub Actions workflow in your plugin repository.

It is important to checkout your plugin repository before running the action. The action needs access to the plugin files, so make sure to use actions/checkout@v4 (or a similar checkout step) to clone your repository into the workflow workspace.

## License

The scripts and documentation in this project are released under the [MIT License](LICENSE)