# Phone &middot; [![Build Status](https://travis-ci.org/AfterShip/phone.svg?branch=v2)](https://travis-ci.org/AfterShip/phone) [![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](http://makeapullrequest.com)
[![FOSSA Status](https://app.fossa.com/api/projects/git%2Bgithub.com%2FAfterShip%2Fphone.svg?type=shield)](https://app.fossa.com/projects/git%2Bgithub.com%2FAfterShip%2Fphone?ref=badge_shield)

## What is phone?
`phone` is used to normalize mobile phone numbers into E.164 format.

A common problem is that users normally input phone numbers in this way:

```
`(817) 569-8900` or
`817569-8900` or
`1(817) 569-8900` or
`+1(817) 569-8900` or ...
```

We always want:

```
+18175698900
```

## Install
```
npm install phone

// or

yarn add phone
```

## Usage

```javascript
const {phone} = require('phone');
```

### Simple usage

```javascript
phone('+852 6569-8900'); // { phoneNumber: '+85265698900',  countryIso2: 'HK', countryIso3: 'HKG' }
```

### With Country

```javascript
phone('+1(817) 569-8900', {country: ''}); // { phoneNumber: '+18175698900', countryIso2: 'US', countryIso3: 'USA'}
phone('(817) 569-8900', {country: 'USA'}); // { phoneNumber: '+18175698900', countryIso2: 'US', countryIso3: 'USA'}
phone('(817) 569-8900', {country: 'HKG'}); // { phoneNumber: null, countryIso2: null, countryIso3: null }, not a valid HKG mobile phone number
phone('+1(817) 569-8900', {country: 'HKG'}); // { phoneNumber: null, countryIso2: null, countryIso3: null }, not a valid HKG mobile phone number
phone('6123-6123', {country: 'HKG'}); // { phoneNumber: '+85261236123', countryIso2: 'HK', countryIso3: 'HKG' }
```

### Without country code and no phone prefix

If both country code and country phone prefix are not provided, the phone number will be treated as USA or Canada by default.

```javascript
phone('(817) 569-8900'); // { phoneNumber: '+18175698900', countryIso2: 'US', countryIso3: 'USA' }
phone('(817) 569-8900', {country: ''}); // { phoneNumber: '+18175698900', countryIso2: 'US', countryIso3: 'USA' }
phone('780-569-8900', {country: null}); // { phoneNumber: '+17805698900', countryIso2: 'CA', countryIso3: 'CAN' }, 780 is a Canada phone prefix
phone('6123-6123', {country: null}); // { phoneNumber: null, countryIso2: null, countryIso3: null }, as default country is USA / CAN and the phone number does not fit such countries' rules
```

### Without country code, with phone prefix, but no `+` sign

Even you input a valid phone number with a valid prefix, if there is no plus sign, it will not work as expected:

```javascript
phone('85291234567');
// or
phone('85291234567', {country: null});
```

`852` is a valid Hong Kong phone prefix, and `91234567` is a valid Hong Kong mobile phone number.
However, there is no plus sign provided, the module will assume the phone number is a USA or Canada phone number, 
hence no result will be found.   

If you know you have provided country phone prefix, make sure you also provide a plus sign:

```javascript
phone('+85291234567');
// or
phone('+85291234567', {country: null});

// { phoneNumber: '+85291234567', countryIso2: 'HK', countryIso3: 'HKG' }
```

or, if you know the country, and only want to reformat the phone number to E.164 format:

```javascript
phone('91234567',  {country: 'HKG'})
// { phoneNumber: '+85291234567', countryIso2: 'HK', countryIso3: 'HKG' }
```

### Skipping phone number initial digit checking

If you want to skip phone number initial digit checking, set `validateMobilePrefix` to false:

```javascript
phone('+(852) 2356-4902');
// { phoneNumber: null, countryIso2: null, countryIso3: null }
// '2' is not a Hong Kong landline phone number prefix, but not mobile phone number

phone('+(852) 2356-4902', {validateMobilePrefix: true});
// { phoneNumber: null, countryIso2: null, countryIso3: null }
// same as above, default value of validateMobilePrefix = true

phone('+(852) 2356-4902', {validateMobilePrefix: false});
// { phoneNumber: '+85223564902', countryIso2: 'HK', countryIso3: 'HKG' }
// skipping mobile prefix checking
```

With `validateMobilePrefix` set to `false, the initial digit checking logic will be disabled completely, even you enter a phone number start with a non-exist digit:

```javascript
phone('+(852) 0356-4902', {validateMobilePrefix: false});
// { phoneNumber: '+85203564902', countryIso2: 'HK', countryIso3: 'HKG' }
// even the phone number start with `0` is not a valid landline phone number
```
Note that the module does not have the capability to determine if the prefix is a valid `landline` prefix number.

### Trunk Code Removal Logic



## API

```javascript
const {phone} = require('phone');
```

```
phone(phoneNumber: string, { country, validateMobilePrefix, strictDetection }?: {
    country?: string;
    validateMobilePrefix?: boolean;
    strictDetection?: boolean;
}): {
    phoneNumber: string | null;
    countryIso2: string | null;
    countryIso3: string | null;
}
```

#### Input

Parameter | Type | Required | Default | Description
--- | --- | --- | --- | ---
phoneNumber | String | Yes | - | The phone number text you want to process
country | String | No | null | Provided country code in iso-3166 alpha 2 or 3 format
validateMobilePrefix | Boolean | No | true | Set to false if you want to skip phone number initial digit checking
strictDetection | Boolean | No | false | Set to true if you want to disable trunk code removal logic. 

#### Returns

```javascript
{
  phoneNumber: string | null;
  countryIso2: string | null;
  countryIso3: string | null;
}
```

Parameter | Type | Description
--- | --- | ---
phoneNumber | String or null | Normalized phone number in E.164 format
countryIso2 | String or null | Detected phone number country code in iso-3166 alpha 3 format
countryIso3 | String or null | Detected phone number country code in iso-3166 alpha 3 format

[comment]: <> (## Demo)

[comment]: <> ([Try it on CodeSandbox]&#40;https://codesandbox.io/s/phone-browser-example-react-o5vt5?file=/src/App.js&#41;)


## Test

```
yarn test
```

## Build

```
yarn build
```

[comment]: <> (## Supported Environment)

[comment]: <> (We currently transpile script to work on target environments for which the browser's global usage is >1%, and Node.js 6.10+.)

[comment]: <> (You can check browser usage statistics on the [browserlist]&#40;http://browserl.ist/?q=%3E1%25&#41;.)

[comment]: <> (You may need polyfills for some older browsers; for more details, please read the `example/README` file.)


## FAQ

1. Does `phone` do any logical validation?

	Yes. If you provide `country`, and the phone number does not start with `+` sign,

	the module will validate `phone_number_lengths` and `mobile_begin_with`

2. Why is `phone` returning null for a valid phone number?

	By default, the function will validate a mobile phone number only, to validate a landline phone number, please set `validateMobilePrefix` to `false`.

	If you find the result is still incorrect, please submit a ticket to improve our validation rules.


## Migrate from v2

## Help

We've tried to make sure that this package works for as many cases as possible, if you notice that we have an incorrect rule for a country or other case, please open an issue to let us know.

For creating new pull requests regarding add or modify phone number formats, please include the reference information such as PDFs, websites, etc. Thank you very much. 

## License

This project is licensed under the [MIT license](https://github.com/AfterShip/phone/blob/master/LICENSE).


[![FOSSA Status](https://app.fossa.com/api/projects/git%2Bgithub.com%2FAfterShip%2Fphone.svg?type=large)](https://app.fossa.com/projects/git%2Bgithub.com%2FAfterShip%2Fphone?ref=badge_large)
