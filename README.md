angular-credit-cards
====================

[![Build Status](https://travis-ci.org/bendrucker/angular-credit-cards.svg?branch=master)](https://travis-ci.org/bendrucker/angular-credit-cards) [![Code Climate](https://codeclimate.com/github/bendrucker/angular-credit-cards/badges/gpa.svg)](https://codeclimate.com/github/bendrucker/angular-credit-cards) [![Test Coverage](https://codeclimate.com/github/bendrucker/angular-credit-cards/badges/coverage.svg)](https://codeclimate.com/github/bendrucker/angular-credit-cards) [![NPM version](https://badge.fury.io/js/angular-credit-cards.svg)](http://badge.fury.io/js/angular-credit-cards)

A set of Angular directives for constructing credit card payment forms. Uses [creditcards](https://www.npmjs.org/package/creditcards) to parse and validate inputs. Pairs well with [angular-stripe](https://www.npmjs.org/package/angular-stripe) or any other payments backend.

## Installation
```bash
# use npm
$ npm install angular-credit-cards
# or bower
$ bower install angular-credit-cards
```

## Getting Started

Include `'angular-credit-cards'` in your module's dependencies:

```js
// node module exports the string 'angular-credit-cards' for convenience
angular.module('myApp', [
  require('angular-credit-cards')
]);
// otherwise, include the code first then the module name
angular.module('myApp', [
  'credit-cards'
]);
```

If you'd like to use the [creditcards](https://www.npmjs.org/package/creditcards) API directly, you can inject the service as `creditcards`.

## Usage and API

With the exception of `ccExp`, all directives require `ngModel` on their elements. While designed to be used together, all directives except `ccExp` can be used completely independently. 

All directives apply a [numeric input pattern](http://bradfrostweb.com/blog/mobile/better-numerical-inputs-for-mobile-forms/) so that mobile browsers use a modified version of the enlarged telephone keypad. You can optionally set `type="number"` for the month (`cc-exp-month`) and year (`cc-exp-year`) inputs. You should use `type="text"` for `cc-number` and `cc-cvc`.

### Card Number (`cc-number`)

```html
<input type="text" ng-model="card.number" cc-number cc-type="cardType" />
```

* Strips all punctuation and spaces
* Validates the card against the [Luhn algorithm](http://en.wikipedia.org/wiki/Luhn_algorithm)
* Checks whether the card is the type specified in scope property in `cc-type` (optional)
* Otherwise, checks whether the card matches any valid card type
* Exposes the [card type](https://github.com/bendrucker/creditcards/blob/master/README.md#cardtypenumber---string) as `$type` on the model controller

The `cc-type` property is optional. If its value is defined on the scope, the card number will be checked against that type in addition to the Luhh algorithm. A special validity key—`ccNumberType`—indicates whether the card matched the specified type. If no type is provided, `ccNumberType` will always be valid for any card that passes Luhn. `$type` will mirror `ccType` for convenience. 

```html
<form name="paymentForm">
  <input type="text" ng-model="card.number" name="cardNumber" cc-number />
</form>
Paying with {{cardNumber.$type}}
```

```html
<form name="paymentForm">
  <select ng-model="cardType" ng-options="type in ['Visa', 'American Express', 'MasterCard']"></select>
  <input type="text" ng-model="card.number" name="cardNumber" cc-number cc-type="cardType" />
  <p ng-show="paymentForm.cardNumber.$error.ccNumberType">That's not a valid {{cardType}}</p>
</form>
```

### CVC (`cc-cvc`)

```html
<input type="text" ng-model="card.cvc" cc-cvc />
<input type="text" ng-model="card.cvc" cc-type="cardNumber.$type" />
```

* Sets `maxlength="4"`
* Validates the CVC

You can optionally specify a scope property that stores the card type as `cc-type`.

### Expiration (`cc-exp`, `cc-exp-month`, `cc-exp-year`)

```html
<div cc-exp>
  <input type="number" ng-model="card.exp_month" cc-exp-month />
  <input type="number" ng-model="card.exp_year" cc-exp-year />
</div>
```

* `cc-exp-month`
  * Sets `maxlength="2"`
  * Validates the month
  * Converts it to a number
* `cc-exp-year`
  * Sets `maxlength="2"`
  * Converts the year to a 4 digit number (`'14'` -> `2014`)
  * Validates the year
  * Validates that the expiration year has not passed
* `cc-exp`
  * Validates that the month/year pair has not passed

`cc-exp` must be placed on a parent element of `cc-exp-month` and `cc-exp-year`. Using the [full 4 digit year](https://github.com/bendrucker/angular-credit-cards/issues/9) is not supported.

## Form Validation

Background Reading: 
* [Angular Documentation: Forms](https://docs.angularjs.org/guide/forms)
* [Angular Form Validation (Scotch.io)](http://scotch.io/tutorials/javascript/angularjs-form-validation)
* [Form validation with AngularJS (ng-newsletter)](http://www.ng-newsletter.com/posts/validations.html)

angular-credit-cards sets validity keys that match the directive names (`ccNumber`, `ccCvc`, `ccExp`, `ccExpMonth`, `ccExpYear`). You can use these keys or the form css classes in order to display error messages.

## License

[MIT](LICENSE)
