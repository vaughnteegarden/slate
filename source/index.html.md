---
title: Rezolve SDK Documentation

language_tabs:
  - objective_c: IOS
  - java: Android

toc_footers:
  - <a href='#'>Footer link 1</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

The **Rezolve SDK** is a software development kit that enables the app developer to integrate Rezolve's unique abilities within their app. 

## Capabilities

The Rezolve SDK is a full-featured application suite. Capabilities that can be integrated into your application include:

* Consumer registration and login
* Consumer profile and wallet management
* Scanning of Rezolve Encoded visual media
* Listening to Rezolve Encoded audio media
* Purchasing of scanned products
* Purchase history

## Intended audience

This document is intended for experienced IOS and Android developer. It is expected that you have built apps before, and know your way around an IDE.

## For more information

For more information on Rezolve, see http://rezolve.com.

# Getting Started

## Get an API key

Using the Rezolve API requires a contract. Please visit https://www.rezolve.com/#sectionForm and supply your contact info. We will respond ASAP, and provied an API key upon contract completion.

## Download the SDK

The Rezolve SDK for **IOS** can be downloaded here: <a href="#">TBD</a>

The Rezolve SDK for **Android** can be downloaded here: <a href="#">TBD</a>

## Set up the SDK - IOS

Need setup steps and screenshots.

## Set up the SDK - Android

Need setup steps and screenshots.

# App Flows

## Login flow

Image test.
<img src="https://www.google.com/images/branding/googlelogo/1x/googlelogo_color_272x92dp.png"/>

## Scan to buy flow

## Profile flow

## etc...

# Modules

## Customer

### Register customer method

### Log in customer

### Log out customer

## Shop



# Errors

# Kitten API Examples

> To authorize, use this code:

``` objective_c
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
```

```java
import kittn

api = kittn.authorize('meowmeowmeow')
```


> Make sure to replace `meowmeowmeow` with your API key.

Kittn uses API keys to allow access to the API. You can register a new Kittn API key at our [developer portal](http://example.com/developers).

Kittn expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: meowmeowmeow`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>

# Kittens

## Get All Kittens

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

