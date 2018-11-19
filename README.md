# MaxMind GeoIP2 Node.js API (BETA)

[![Build Status](https://api.travis-ci.com/maxmind/GeoIP2-node.svg?branch=master)](https://travis-ci.com/maxmind/GeoIP2-node)
[![Known Vulnerabilities](https://snyk.io/test/github/maxmind/GeoIP2-node/badge.svg)](https://snyk.io/test/github/maxmind/GeoIP2-node)

## Description

This package provides an API for the [GeoIP2
databases](https://dev.maxmind.com/geoip/geoip2/downloadable), and will
provide an API for the [GeoIP2 Precision web
services](https://dev.maxmind.com/geoip/geoip2/web-services).  This API also
works with MaxMind's free [GeoLite2
databases](https://dev.maxmind.com/geoip/geoip2/geolite2/).

## Installation

```
yarn add @maxmind/geoip2-node
```

If you are not able to use `yarn`, you may also use `npm`:

```
npm install @maxmind/geoip2-node
```


## IP Geolocation Usage

IP geolocation is inherently imprecise. Locations are often near the center of
the population. Any location provided by a GeoIP2 database or web service
should not be used to identify a particular address or household.

## Database Usage

The database reader returns a promise that resolves with a reader instance.
You may then call the function corresponding to the request type (e.g.
`city` or `country`), passing it the IP address you want to look up.

If the request succeeds, the function call will return an object for the GeoIP2
lookup. The object in turn contains multiple record objects, each of which
represents part of the data returned by the database.

### Options

We use the [node-maxmind](https://github.com/runk/node-maxmind) library as the
database reader. As such, you have access to the same
[options](https://github.com/runk/node-maxmind#options) found in that library
and can be used like this:

```
const Reader = require('@maxmind/geoip2-node').Reader;
// Typescript:
// import { Reader } from '@maxmind/geoip2-node';

const options = {
  // you can use options like `cache` or `watchForUpdates`
};

Reader.open('/usr/local/database.mmdb', options).then(reader => {
  console.log(reader.country('1.1.1.1'));
});
```
## Database Examples

### Anonymous IP Database Example

```
const Reader = require('@maxmind/geoip2-node').Reader;
// Typescript:
// import { Reader } from '@maxmind/geoip2-node';

Reader.open('/usr/local/share/GeoIP/GeoIP2-Anonymous-IP.mmdb').then(reader => {
  const response = reader.anonymousIP('85.25.43.84');

  console.log(response.isAnonymous); // true
  console.log(response.isAnonymousVpn); // false
  console.log(response.isHostingProvider); // true
  console.log(response.isPublicProxy); // false
  console.log(response.isTorExitNode); // false
  console.log(response.ipAddress); // '85.25.43.84'
});
```

### ASN Example

```
const Reader = require('@maxmind/geoip2-node').Reader;
// Typescript:
// import { Reader } from '@maxmind/geoip2-node';

Reader.open('/usr/local/share/GeoIP/GeoLite2-ASN.mmdb').then(reader => {
  const response = reader.asn('128.101.101.101');

  console.log(response.autonomousSystemNumber); // 217
  console.log(response.autonomousSystemOrganization); // 'University of Minnesota'
});
```

### City Example

```
const Reader = require('@maxmind/geoip2-node').Reader;
// Typescript:
// import { Reader } from '@maxmind/geoip2-node';

Reader.open('/usr/local/share/GeoIP/GeoIP2-City.mmdb').then(reader => {
  const response = reader.city('128.101.101.101');

  console.log(response.country.isoCode); // 'US'
});
```

### Connection-Type Example

```
const Reader = require('@maxmind/geoip2-node').Reader;
// Typescript:
// import { Reader } from '@maxmind/geoip2-node';

Reader.open('/usr/local/share/GeoIP/GeoIP2-Connection-Type.mmdb').then(reader => {
  const response = reader.connectionType('128.101.101.101');

  console.log(response.connectionType) // 'Cable/DSL'
  console.log(response.ipAddress) // '128.101.101.101'
});
```

### Country Example

```
const Reader = require('@maxmind/geoip2-node').Reader;
// Typescript:
// import { Reader } from '@maxmind/geoip2-node';

Reader.open('/usr/local/share/GeoIP/GeoIP2-Country.mmdb').then(reader => {
  const response = reader.country('128.101.101.101');

  console.log(response.country.isoCode); // 'US'
});
```

### Domain Example

```
const Reader = require('@maxmind/geoip2-node').Reader;
// Typescript:
// import { Reader } from '@maxmind/geoip2-node';

Reader.open('/usr/local/share/GeoIP/GeoIP2-Domain.mmdb').then(reader => {
  const response = reader.domain('128.101.101.101');

  console.log(response.domain) // 'umn.edu'
  console.log(response.ipAddress) // '128.101.101.101'
});
```

### Enterprise Example

```
const Reader = require('@maxmind/geoip2-node').Reader;
// Typescript:
// import { Reader } from '@maxmind/geoip2-node';

Reader.open('/usr/local/share/GeoIP/GeoIP2-Enterprise.mmdb').then(reader => {
  const response = reader.enterprise('128.101.101.101');

  console.log(response.country.isoCode) // 'US'
});
```

### ISP Example

```
const Reader = require('@maxmind/geoip2-node').Reader;
// Typescript:
// import { Reader } from '@maxmind/geoip2-node';

Reader.open('/usr/local/share/GeoIP/GeoIP2-ISP.mmdb').then(reader => {
  const response = reader.isp('128.101.101.101');

  console.log(response.autonomousSystemNumber); // 217
  console.log(response.autonomousSystemOrganization); // 'University of Minnesota'
  console.log(response.isp); // 'University of Minnesota'
  console.log(response.organization); // 'University of Minnesota'

  console.log(response.ipAddress); // '128.101.101.101'
});
```

## Database Exceptions

If the database file does not exist, is not readable, is invalid, or there is a bug
in the reader, the promise will be rejected with an `Error` with a message
explaining the issue.

If the database file and the reader method do not match (e.g.
`reader.city` is used with a Country database), a `BadMethodCalledError` will
be thrown.

If the IP address is not found in the database, an `AddressNotFoundError` will
be thrown.

If the IP address is not valid, a `ValueError` will be thrown.

## Values to use for Database or Object Keys

We strongly discourage you from using a value from any `names` property as a
key in a database or object.

These names may change between releases. Instead we recommend using one of the
following:

* geoip2-node.CityRecord - `city.geonameId`
* geoip2-node.ContinentRecord - `continent.code` or `continent.geonameId`
* geoip2-node.CountryRecord and geoip2.records.RepresentedCountry - `country.isoCode` or `country.geonameId`
* geoip2-node.SubdivisionsRecord - `subdivision.isoCode` or `subdivision.geonameId`

## What data is returned?

While many of the models contain the same basic records, the attributes which
can be populated vary between web service end points or databases. In addition,
while a model may offer a particular piece of data, MaxMind does not always
have every piece of data for any given IP address.

Because of these factors, it is possible for any request to return a record
where some or all of the attributes are unpopulated.

The only piece of data which is always returned is the `ipAddress` attribute in
the `geoip2-node.TraitsRecord` record.

## Integration with GeoNames

[GeoNames](http://www.geonames.org/) offers web services and downloadable databases with data on
geographical features around the world, including populated places. They offer
both free and paid premium data. Each feature is uniquely identified by a
`geonameId`, which is an integer.

Many of the records returned by the GeoIP web services and databases include a
`geonameId` field. This is the ID of a geographical feature (city, region,
country, etc.) in the GeoNames database.

Some of the data that MaxMind provides is also sourced from GeoNames. We source
things like place names, ISO codes, and other similar data from the GeoNames
premium data set.

## Reporting Data Problems

If the problem you find is that an IP address is incorrectly mapped, please
[submit your correction to MaxMind](https://support.maxmind.com/geoip-data-correction-request/).

If you find some other sort of mistake, like an incorrect spelling,
please check [the GeoNames site](http://www.geonames.org/) first. Once
you've searched for a place and found it on the GeoNames map view, there
are a number of links you can use to correct data ("move", "edit",
"alternate names", etc.). Once the correction is part of the GeoNames
data set, it will be automatically incorporated into future MaxMind
releases.

If you are a paying MaxMind customer and you're not sure where to submit a
correction, please [contact MaxMind support for help](https://support.maxmind.com/contact-us/).

## Requirements

MaxMind has tested this API with Node.js versions 6, 8, 10.  We aim to support
active LTS versions of Node.js, as well as the latest stable release.

## Contributing

Patches and pull requests are encouraged. Please include unit tests
whenever possible, as we strive to maintain 100% code coverage.

## Versioning

The GeoIP2 Node.js API uses [Semantic Versioning](http://semver.org/).

## Support

Please report all issues with this code using the [GitHub issue
tracker](https://github.com/maxmind/GeoIP2-node/issues)

If you are having an issue with a MaxMind service that is not specific to the
client API, please contact [MaxMind support for assistance](https://support.maxmind.com/contact-us/).

## Copyright and License ##

This software is Copyright (c) 2018 by MaxMind, Inc.

This is free software, licensed under the Apache License, Version 2.0.