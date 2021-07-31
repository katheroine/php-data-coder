# DataCoder

[![Build Status](https://travis-ci.org/ExOrg/php-data-coder.svg?branch=master)](https://travis-ci.org/ExOrg/php-data-coder)

Extendable set of data and datafile encoders and decoders. It allows to transfer PHP arrays into data strings and datafiles and vice versa with chosen format like YAML and JSON. It provides encapsulation of various decoding and encoding mechanisms, unifies interface and exceptions handling.

There are various groups of decoders and encoders

* With predefined data format - e.g. *JsonDataDecoder*, *YamlDatafileEncoder*
* With configurable data format - e.g. *DataDecoder*, *DatafileEncoder*
* For raw data - e.g. *JsonDataDecoder*, *DataEncoder*
* For data in the file - e.g. *YamlDatafileEncoder*, *DatafileDecoder*

## Getting Started

### Prerequisities

* PHP 5.5.9+
* [YAML PHP extension](http://php.net/manual/en/book.yaml.php) 1.2.0
* [Composer](https://getcomposer.org/) 1.0

### Installation

The recommended way to install DataCoder into the source code of the project is to handle it as code dependency by [Composer](http://getcomposer.org).

#### YAML PHP extension

YAML Coders uses [YAML PHP extension](http://php.net/manual/en/book.yaml.php) so it needs to be installed before any YAML Coder will be run.

##### PHP 5

```bash
sudo aptitude install php-pear libyaml-dev
pecl install yaml
```

##### PHP 7

```bash
sudo aptitude install php-pear libyaml-dev
pecl install yaml-beta
```

##### PHP 8

```bash
sudo aptitude install php-pear libyaml-dev
sudo aptitude install php8.0-dev
pecl install yaml
```

Don't forget to add `"extension=yaml.so"` line to your *php.ini* file.

#### cURL, php5-cli and Git

The command line tool cURL will be used to to download *Composer* installer and *php5-cli* for and running it to install Composer. Git is needed by to downloading dependencies by the Composer.

#### Composer

The common way to intall Composer is downloading installer via *curl* tool and call the PHP interpreter to run it. 

```bash
curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer
```
Now composer is available as the command line tool and can be run by typing `composer` with appropriate parameters.

#### DataCoder

Add to your **composer.json** file following entry in the *require* section
```
{
    "require": {
        "exorg/data-coder": "^1.0.0"
    }
}
```
You can do it manually or by running the following command
```bash
composer require exorg/data-coder
```
If it's project set-up stage and no one dependency have been installed yet, run
```bash
composer install
```

If another dependencies have been intalled previously, run
```bash
composer update
```

## Usage

The simplest way to autoload all needed files in executable file is attaching *autoload.php* file generated by Composer (after running `composer install` or `composer update` command) like in following example

```php
require_once (__DIR__ . '/vendor/autoload.php');

```

### Data Encoders with predefined format

```php

use Exorg\DataCoder\JsonDataEncoder;

$data = array (
    "firstName" => "John",
    "lastName" => "Smith",
    "address" => array (
        "streetAddress" => "21 2nd Street",
        "city" => "New York",
        "state" => "NY",
        "postalCode" => "10021-3100",
    ),
);

$encoder = new JsonDataEncoder();
$result = $encoder->encodeData($data);

print($result);
```
---
```
{
    "firstName": "John",
    "lastName": "Smith",
    "address": {
        "streetAddress": "21 2nd Street",
        "city": "New York",
        "state": "NY",
        "postalCode": "10021-3100"
    }
}
```

### Data Decoders with predefined format

```php

use Exorg\DataCoder\YamlDataDecoder;

$data = '
---
firstName: John
lastName: Smith
address:
  streetAddress: 21 2nd Street
  city: New York
  state: NY
  postalCode: 10021-3100
...
';

$decoder = new YamlDataDecoder();
$result = $decoder->decodeData($data);

print_r($result);
```
---
```
Array
(
    [firstName] => John
    [lastName] => Smith
    [address] => Array
        (
            [streetAddress] => 21 2nd Street
            [city] => New York
            [state] => NY
            [postalCode] => 10021-3100
        )

)
```

### Data Encoder with configurable format

```php
use Exorg\DataCoder\DataEncoder;

$data = array (
    "firstName" => "John",
    "lastName" => "Smith",
    "address" => array (
        "streetAddress" => "21 2nd Street",
        "city" => "New York",
        "state" => "NY",
        "postalCode" => "10021-3100",
    ),
);

$encoder = new DataEncoder();
$encoder->setDataFormat('yaml');
$result = $encoder->encodeData($data);

print($result);
```
---
```
---
firstName: John
lastName: Smith
address:
  streetAddress: 21 2nd Street
  city: New York
  state: NY
  postalCode: 10021-3100
...

```

### Data Decoder with configurable format

```php
use Exorg\DataCoder\DataDecoder;

$data = '
{
    "firstName": "John",
    "lastName": "Smith",
    "isAlive": true,
    "address": {
        "streetAddress": "21 2nd Street",
        "city": "New York",
        "state": "NY",
        "postalCode": "10021-3100"
    }
}
';

$decoder = new DataDecoder();
$decoder->setDataFormat('json');
$result = $decoder->decodeData($data);

print_r($result);
```
---
```
Array
(
    [firstName] => John
    [lastName] => Smith
    [isAlive] => 1
    [address] => Array
        (
            [streetAddress] => 21 2nd Street
            [city] => New York
            [state] => NY
            [postalCode] => 10021-3100
        )

)
```

### Datafile Encoders and Decoders

Datafile Encoders and Decoders usage is similar to the Data Encoders and Decoders. There are coders with predefined data format like **JsonDatafileEncoder** or **YamlDatafileDecoder** and those, where data format can be chosen by function *setDataFormat*, just like in examples above - **DatafileEncoder** and **DatafileDecoder**.

#### Data format recognizing by file extension

Datafile coders with configurable data format - **DatafileEncoder** and **DatafileDecoder** - can recognize data format by file extension. In that case, there is no need to set data format manually.

##### DatafileEncoder

```php
use Exorg\DataCoder\DatafileEncoder;

$data = array (
    "firstName" => "John",
    "lastName" => "Smith",
    "address" => array (
        "streetAddress" => "21 2nd Street",
        "city" => "New York",
        "state" => "NY",
        "postalCode" => "10021-3100",
    ),
);

$datafilePath = 'data.json';

$encoder = new DatafileEncoder();
$encoder->encodeFile($data, $datafilePath);

print file_get_contents($datafilePath);
```
---
```
{
    "firstName": "John",
    "lastName": "Smith",
    "address": {
        "streetAddress": "21 2nd Street",
        "city": "New York",
        "state": "NY",
        "postalCode": "10021-3100"
    }
}
```

##### DatafileDecoder

```php
use Exorg\DataCoder\DatafileDecoder;

$datafilePath = 'data.yaml';

print file_get_contents($datafilePath);

$decoder = new DatafileDecoder();
$data = $decoder->decodeFile($datafilePath);

print_r($data);
```
---
```
---
firstName: John
lastName: Smith
address:
  streetAddress: 21 2nd Street
  city: New York
  state: NY
  postalCode: 10021-3100
...
Array
(
    [firstName] => John
    [lastName] => Smith
    [address] => Array
        (
            [streetAddress] => 21 2nd Street
            [city] => New York
            [state] => NY
            [postalCode] => 10021-3100
        )

)
```

## Tests

### Unit tests

This project has unit tests, which has been built with [PHPUnit](https://phpunit.de/) framework and run on Linux operating system.

To run tests, write the following command in your command line inside the main DataCoder project directory

```bash
vendor/bin/phpunit tests/
```

### Code style tests

This code follows [PSR-1](http://www.php-fig.org/psr/psr-1/) and [PSR-2](http://www.php-fig.org/psr/psr-2/) standards.

To run tests for code style  write the following command in your command line inside the main DataCoder project directory

```bash
vendor/bin/phpcs tests/ src/
```

## Built with

* [Linux Mint](https://www.linuxmint.com/)
* [Eclipse](https://eclipse.org/)
* [Remarkable](https://remarkableapp.github.io/)
* [PHPUnit](https://phpunit.de/)
* [PHPCodeSniffer](https://www.squizlabs.com/php-codesniffer)
* [Git](https://git-scm.com/)
* [GitHub](https://github.com/)
* [Travis](https://travis-ci.org/)

## Versioning

This project is versioning according to [SemVer](http://semver.org/)  versioning standars. All available [releases](https://github.com/ExOrg/php-data-coder/releases) are [tagged](https://github.com/ExOrg/php-data-coder/tags). 

## Contributing

Please read [CONTRIBUTING.md](https://github.com/ExOrg/php-data-coder/blob/master/CONTRIBUTING.md) for details on the code of conduct, and the process for submitting pull requests.

## Author

* **Katarzyna Krasińska** - [katheroine](https://github.com/katheroine), [ExOrg](https://github.com/ExOrg)


## License

This project is licensed under the MIT License - see the [LICENSE.md](https://github.com/ExOrg/php-data-coder/blob/master/LICENSE.md) file for details.
