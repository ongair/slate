---
title: Ongair for Developers

language_tabs:
  - shell: cURL  

toc_footers:
  - <a href='https://ongair.im/users/sign_up'>Sign Up for an Account</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the [Ongair](https://ongair.im) API! You can use our API to create awesome Bots and IM applications.

This API allows you to send and receive:

* Send and receive texts, images, video, audio, locations from supported Instant Messaging platforms
* Administer your Ongair account  

If you have any questions feel free to contact us at [dev@ongair.im](mailto:dev@ongair.im)

## Enable the API

Go to **settings** > **Account** and tick enable.

# Security

## URL

Depending on the environment where you account has been set up, use the correct prefix.

* [https://ongair.im](https://ongair.im)
* [https://beta.ongair.im](https://beta.ongair.im)

All requests must have the authentication token in the header or within the URL parameters. The first option is preferred.

## Option 1

Header | Values
-------|--------
Accept | application/json
Authorization | Token token="enter_token_here”

## Option 2

> URL

```
  https://ongair.im/api/v1/base/status?token=<enter_token_here>
```

```shell
  curl "https://ongair.im/api/v1/base/status" \
  -h "Content-Type' => 'application/json" \
  -d token=<enter_token_here>
```

## Authentication Error

In the event of an authentication error .e.g. no token is provided or an invalid token is provided then the result is a 401 error code with the response:

```json
  {
    "error":"Invalid API Token"
  }
```

# Status

Status Method returns a health check of the system.

> URL

```
  https://ongair.im/api/v1/base/status?token=<enter_token_here>
```

## Method

GET

```shell
  curl -X GET "https://ongair.im/api/v1/base/status" \
    -d token=<enter_token_here>
```

## Response

> It returns the following json:

```json
  {
    "success":true,
    "phone_number":"254727550098",
    "status_message":"Hey",
    "online":false
  }
```

Field | Type | Meaning 
------|------|----------
phone_number | String | Phone number on which the account is registered
status_message | String | The current whatsapp status message
online | boolean | True or false as to whether the whatsapp account is currently online



# Sending References

## Send Message
Method sends a text message to a single recipient. Channels supported WeChat, Facebook Messenger and Telegram.

> URL

```
  https://ongair.im/api/v1/base/send
```

### Method

POST

```shell
curl -X POST "https://ongair.im/api/v1/base/send" \
  -d external_id=12345678 \
  -d text="Hey" \
  -d thread=true // optional \
  -d token=<enter_token_here>
```

### Params

Field | Type | Mandatory | Format | Meaning 
------|------|-----------|--------|--------------
external_id | String | Yes | 123455677 | User ID of the recipient*
text | String | Yes | Utf8 only encoded strings | The message to be sent
thread | Boolean | No | true/false | Whether the response should be in a conversation thread with the original message

<aside class="success">
  *external_id is the ID of the user on their respective messaging app. This is sent to your webhook as external_contact_id when a new message is sent to it.
</aside>

### Response

> The server responds with a JSON message:

```json
  {
    "sent":true,
    "id":2067
  }
```

Field | Type | Meaning  
------|------|------------
sent | Boolean | True or False if the image
id | Integer | An Identifier for the message. This is used when delivering receipts

### Error

<aside class="warning">
  If there is an error or the external id does not exist, the server returns an error message and response code of 422.
</aside>

```json
  {
    "error":"Contact with external_id 123455677 does not exists"
  }
```


## Send Image

Method sends a image message to a single recipient. Channels supported WeChat, Facebook Messenger and Telegram.

> URL

```
  https://ongair.im/api/v1/base/send_image
```

### Method

POST

```shell
curl -X POST "https://ongair.im/api/v1/base/send_image" \
  -d external_id=12345678 \
  -d image="http://127.0.0.1:4567/images/ongair_logo.png" \
  -d caption="image for api" // optional \
  -d content_type="image/jpg" \
  -d token=<enter_token_here>
```

### Params

Field | Type | Mandatory | Format | Meaning 
------|------|-----------|--------|------------
external_id | String | Yes | 123455677 | User ID of the recipient*
image | String/File | Yes | Either the URL of the image (must be publicly accessible) or the image uploaded | The image to be sent
Caption | String | No | The caption for the image | A brief text to accompany the image
content_type | String | Yes | image/file_extension_here eg image/jpg or /image/png etc. | The content type of the image file you want to send

### Response

> The server responds with a JSON message:

```json
  {
    "sent":true,"id":2067
  }
```

Field | Type | Meaning  
------|------|-------------
sent | Boolean | True or False if the image
id | Integer | An Identifier for the message. This is used when delivering receipts


### Error
<aside class="warning">
  If there is an error or the external id does not exist, the server returns an error message and response code of 422.
</aside>

```json
  {
    "error":"Contact with external_id 25470586564 does not exists"
  }
```



## Send Audio

Method sends a audio message to a single recipient. Channels supported WeChat, Facebook Messenger and Telegram.

> URL

```
  https://ongair.im/api/v1/base/send_audio
```

### Method

POST

```shell
curl -X POST "https://ongair.im/api/v1/base/send_image" \
  -d external_id=12345678 \
  -d image="http://127.0.0.1:4567/images/ongair.mp3" \
  -d caption="audio for api" // optional \
  -d content_type="audio/mp3" \
  -d token=<enter_token_here>
```

### Params

Field | Type | Mandatory | Format | Meaning 
------|------|-----------|--------|------------
external_id | String | Yes | 123455677 | User ID of the recipient*
audio | String/File | Yes | Either the URL of the audio (must be publicly accessible) or the audio uploaded | The audio to be sent
Caption | String | No | The caption for the audio | A brief text to accompany the audio
content_type | String | Yes | audio/file_extension_here eg audio/mp3 or audio/ogg etc. | The content type of the audio file you want to send


### Response

> The server responds with a JSON message:

```json
  {
    "sent":true,
    "id":2067
  }
```

Field | Type | Meaning  
------|------|-------------
sent | Boolean | True or False if the audio
id | Integer | An Identifier for the message. This is used when delivering receipts


### Error
<aside class="warning">
  If there is an error or the external id does not exist, the server returns an error message and response code of 422.
</aside>

```json
  {
    "error":"Contact with external_id 25470586564 does not exists"
  }
```



## Send Video

Method sends a video message to a single recipient. Channels supported Facebook Messenger.

> URL

```
  https://ongair.im/api/v1/base/send_video
```

### Method

POST

```shell
curl -X POST "https://ongair.im/api/v1/base/send_image" \
  -d external_id=12345678 \
  -d video="http://127.0.0.1:4567/images/ongair.mp4" \
  -d caption="video for api" // optional \
  -d content_type="video/mp4" \
  -d token=<enter_token_here>
```

### Params

Field | Type | Mandatory | Format | Meaning 
------|------|-----------|--------|------------
external_id | String | Yes | 123455677 | User ID of the recipient*
video | String/File | Yes | Either the URL of the video (must be publicly accessible) or the video uploaded | The video to be sent
Caption | String | No | The caption for the video | A brief text to accompany the video
content_type | String | Yes | video/file_extension_here eg video/mp4 etc. | The content type of the video file you want to send


### Response

> The server responds with a JSON message:

```json
  {
    "sent":true,
    "id":2067
  }
```

Field | Type | Meaning  
------|------|-------------
sent | Boolean | True or False if the video
id | Integer | An Identifier for the message. This is used when delivering receipts


### Error
<aside class="warning">
  If there is an error or the external id does not exist, the server returns an error message and response code of 422.
</aside>

```json
  {
    "error":"Contact with external_id 25470586564 does not exists"
  }
```



## Sending Broadcast

Method sends a broadcast text message to recipients in a list. Use this to send the same message to multiple recipients.

> URL

```
  https://ongair.im/api/v1/base/lists/:id/send_message
```

### Method

You need to have created a list in advance.

```shell
  curl "https://ongair.im/api/v1/base/lists/{id}/send_message" \
    -d id=5 \
    -d text="Hello List" \
    -d thread=false // optional \
    -d token=<enter_token_here>
```

### Params

Field | Type | Mandatory | Format | Meaning 
------|------|-----------|--------|----------------
id | Integer | Yes | 5 | List to send to
text | String | Yes | Utf8 only encoded strings | The message to be sent
thread | Boolean | No | true/false | Whether the response should be in a conversation thread with the original message


















# Received References

## Message Received

This is a notification from the API when a message is received at the Ongair hosted account. It takes the form of a HTTP POST to the specified URL with the following parameters:

Field | Type | Value | Meaning  
------|------|-------|-----------------
notification_type | String | MessageReceived | The type of the notification
phone_number | String | (country code)(phone number) e.g. 254722200200 | Phone number that sent the message
text | String | Hello world | The message text that was received
account | String | 254722123456 | Your WhatsApp account number

The client should respond with a HTTP status code of 200. The response body is ignored.

## Image Received

This is a notification from the API when an image is received at the Ongair hosted account. It takes the form of a HTTP POST to the specified URL with the following parameters:

Field | Type | Value | Meaning  
------|------|-------|------------------
notification_type | String | ImageReceived | The type of the notification
phone_number | String | (country code)(phone number) e.g. 254722200200 | Phone number that sent the message
image | String | http://…/ | The url to the image that was sent.
account | String | 254722123456 | Your WhatsApp account number

The client should respond with a HTTP status code of 200. The response body is ignored.

## Contact Received

This is a notification from the API when a contact is received at the Ongair hosted account. It takes the form of a HTTP POST to the specified URL with the following parameters:

Field | Type | Value | Meaning
------|------|-------|-------------------
notification_type | String | ContactReceived | The type of the notification
phone_number | String | (country code)(phone number) e.g. 254722200200 | Phone number that sent the message
contact_phone_number | String | 0706866564 | The phone number as was parsed from the vcard
account | String | 254722123456 | Your WhatsApp account number

## Location Received

This is a notification from the API when a location is received at the Ongair hosted account. It takes the form of a HTTP POST to the specified URL with the following parameters:

Field | Type | Value | Meaning  
------|------|-------|----------------
notification_type | String | LocationReceived | The type of the notification
phone_number | String | (country code)(phone number) e.g. 254722200200 | Phone number that sent the message
address | String | Location name e.g. Mombasa Road | Name of the location where the message was sent from
latitude | Float -1.3292233 for example for Mombasa Road | Latitude of the location where the message was sent from
longitude | Float 36.8772923 for example for Mombasa Road | Longitude of the location where the message was sent from

The client should respond with a HTTP status code of 200. The response body is ignored.



















# Search

This returns a list of records for whatever resource you are querying for e.g. messages or contacts.

## Messages

> URL

<aside class="notice">
This API is available in both JSON and XML formats
</aside>

```
JSON

https://ongair.im/api/v1/search/messages.json
```

```shell
curl -X GET -H "accept: application/json" "https://ongair.im/api/v1/search/messages.json" \
  -d token=<enter_token_here>
```

```
XML

https://ongair.im/api/v1/search/messages.xml
```

```shell
curl -X GET -H "accept: application/xml" "https://ongair.im/api/v1/search/messages.xml" \
  -d token=<enter_token_here>
```

### Method

GET

### Response

> JSON

```json
[
    {
        "id": 1,
        "text": "hey",
        "created_at": "2015-04-17T14:49:35.000Z",
        "contact": {
            "name": "John",
            "id": 1,
            "phone_number": "254703336224"
        }
    },
    {
        "id": 2,
        "text": "Hi",
        "created_at": "2015-05-05T03:40:22.000Z",
        "contact": {
            "name": "Jane",
            "id": 2,
            "phone_number": "254704755453"
        }
    }
]
```

> XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<messages>
    <message>
        <id>1</id>
        <text>hey</text>
        <created_at>2015-04-17 14:49:35 UTC</created_at>
        <contact>
            <id>1</id>
            <phone_number>254703336224</phone_number>
            <name>John</name>
        </contact>
    </message>
    <message>
        <id>2</id>
        <text>Hi</text>
        <created_at>2015-05-05 03:40:22 UTC</created_at>
        <contact>
            <id>2</id>
            <phone_number>254704755453</phone_number>
            <name>Jane</name>
        </contact>
    </message>
</messages>
```

## Contacts

<aside class="notice">
This API is available in both JSON and XML formats
</aside>

```
  JSON

  https://app.ongair.im/api/v1/search/contacts.json
```

```shell
curl -X GET "accept: application/json" "https://app.ongair.im/api/v1/search/contacts.json" \
  -d token=<enter_token_here>
```


```
  XML

  https://app.ongair.im/api/v1/search/contacts.xml
```


```shell
curl -X GET "accept: application/xml" "https://app.ongair.im/api/v1/search/contacts.xml" \
  -d token=<enter_token_here>
```

### Method

GET

### Response

> JSON

```json
[
    {
        "id": 1,
        "phone_number": "254703336224",
        "name": "John",
        "country": "Kenya",
        "created_at": "2015-04-17T14:49:35.000Z"
    },
    {
        "id": 2,
        "phone_number": "254704755453",
        "name": "Jane",
        "country": "Kenya",
        "created_at": "2015-05-11T13:41:49.000Z"
    }
]
```

> XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<contacts>
    <contact>
        <id>1</id>
        <name>John</name>
        <phone_number>254703336224</phone_number>
        <country>Kenya</country>
        <created_at>2015-04-17 14:49:35 UTC</created_at>
    </contact>
    <contact>
        <id>2</id>
        <name>Jane</name>
        <phone_number>254704755453</phone_number>
        <country>Kenya</country>
        <created_at>2015-05-11 13:41:49 UTC</created_at>
    </contact>
</contacts>
```

















# Sending Broadcast

Method sends a broadcast text message to recipients in a list. Use this to send the same message to multiple recipients.

> URL

```
  https://ongair.im/api/v1/base/lists/:id/send_message
```

## Method

You need to have created a list in advance.

```shell
  curl "https://ongair.im/api/v1/base/lists/{id}/send_message" \
    -d id=5 \
    -d text="Hello List" \
    -d thread=false // optional \
    -d token=<enter_token_here>
```

## Params

Field | Type | Mandatory | Format | Meaning 
------|------|-----------|--------|----------------
id | Integer | Yes | 5 | List to send to
text | String | Yes | Utf8 only encoded strings | The message to be sent
thread | Boolean | No | true/false | Whether the response should be in a conversation thread with the original message




