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

Welcome to the Ongair API! You can use our API to create awesome Bots and IM applications.

This API allows you to send and receive:

* Send and receive texts, images, video, audio, locations from supported Instant Messaging platforms
* Administer your Ongair account  

If you have any questions feel free to contact us at [dev@ongair.im](mailto:dev@ongair.im)

## Enable the API

Go to settings > Account and tick enable.

[[images/enable_api.png]]

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
  https://app.ongair.im/api/v1/base/status?token=<enter_token_here>
```

## Authentication Error

In the event of an authentication error .e.g. no token is provided or an invalid token is provided then the result is a 401 error code with the response:

```
  {"error":"Invalid API Token"}
```

# Status

Status Method returns a health check of the system.

> URL

```
  https://ongair.im/api/v1/base/status?token=<enter_token_here>
```

## Method

GET


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

# Create a Contact

Sets up a contact in the contacts database. This acts as an opt-in before messages can be sent.

> URL

```
  https://ongair.im/api/v1/base/create_contact
```

## Method

POST

## Contact Params

Field | Type | Mandatory | Format | Meaning 
------|------|-----------|--------|-------------
phone_number | String | Yes | (country code)(phone number) e.g. 254722200200 | The contact phone number
name | String | Yes | Utf8 only encoded strings | The contact name

## Response

> The server responds with a JSON message:

```
  {"created":true,"id":2067}
```

# Create a Group

Creates a group. This call is asynchronous. This means that the group will not be ready until your client receives a ‘GroupCreated’ notification from the API.

> URL

```
  https://ongair.im/api/v1/base/create_group
```

## Method

POST

## Group Params

Field | Type | Mandatory | Format | Meaning 
------|------|-----------|--------|-------------
name | String | Yes | Utf8 only encoded strings | The group subject name
Members | Array | Yes | Utf8 only encoded strings | The phone number of the members to be added to the group

> Example

```shell
curl "https://app.ongair.im/api/v1/base/create_group" \
  -d name=GroupName \
  -d members[]=254722200200 \
  -d members[]=254722201201 \
  -d token=YOUR_API_TOKEN”
```

## Response

> The server responds with a JSON message

```
  {"created":true,"id":2067}
```

## Error

> If there is an error or the group name already exists, the server returns an error message and response code of 422.

```
  {"error":"Group with the name ‘test’ already exists"}
```

## Callback

This is a web hook posted to the client when the group is ready. This callback is usually a second or two after the original call.

Field | Type | Value | Meaning  
------|------|-------|----------------
notification_type | String | GroupCreated | The type of the notification
name | String | Cooking Class | The name of the group
id | Integer | 2067 | The identifier of the group. This is the same id returned when the create group was sent.



# Add Participants to an existing Group

This adds participants to an existing group.

> URL

```
  https://ongair.im/api/v1/base/groups/{id}/add_participants
```

## Method

POST

## Params

Field | Type | Mandatory | Format | Meaning
------|------|-----------|--------|-----------------
id | Integer | Yes | Utf8 only encoded strings | The group id as returned from the group created call
Members | Array | Yes | Utf8 only encoded strings | The phone number of the members to be added to the group

## Response

> The server responds with a JSON message

```
  {"added": true }
```

## Error

> If there is an error, the server returns an error message and response code of 422.

```
  {"error":"Group with the name ‘test’ already exists"}
```

# Get Group Members

This function returns the contacts that belong to a specific group.

> URL

```
  https://ongair.im/api/v1/base/groups/{id}/members
```

## Method

GET

## Params

Field | Type | Mandatory | Format | Meaning 
------|------|-----------|--------|------------
Id | Integer | Yes | Utf8 only encoded strings | The group id as returned from the group created call

## Response

> The server responds with a JSON message:

```json
  {
    "id": 19,
    "name": "test to add",
    "contacts": [
        {
            "id": 1596,
            "name": "254733171036",
            "phone_number": "254733171036"
        },
        {
            "id": 1597,
            "name": "Kimenye Trevor",
            "phone_number": "254705866564"
        }
    ]
  }
```

Field | Type | Meaning  
------|------|----------------
id | Integer | The id of the group
name | String | The name of the group
Contacts | Array | An array of the members of the group. For each member, the id, name and phone_number are provided

## Error

> If there is an error, the server returns an error message and response code of 422

```
  {"error": "No group with id ‘2’ exists"}
```

# Remove Participant from a Group

This removes a participant from a group from which the Ongair hosted account is an administrator.

> URL

```
  https://ongair.im/api/v1/base/groups/{id}/remove_participant
```

## Method

POST

## Params

Field | Type | Mandatory | Format | Meaning 
------|------|-----------|--------|-------------
id | Integer | Yes | Utf8 only encoded strings | The group id as returned from the group created call
phone_number | String | Yes | (country code)(phone number) e.g. 254722200200 | The contact phone number

## Response

> The server responds with a JSON message:

```json
  {
    "removed": true 
  }
```

## Error

> If there is an error, the server returns an error message and response code of 422.

```json
  {
    "error":"Contact with the phone number ‘254722200200’ is not a member of the group"
  }
```

# Send a Message

Method sends a text message to a single recipient. Please note that there is a slight difference in how you send messages to WhatsApp compared to other channels like WeChat, Facebook Messenger and Telegram.

## WhatsApp

> URL

```
  https://ongair.im/api/v1/base/send
```

### Method

POST

### Params

Field | Type | Mandatory | Format | Meaning 
------|------|-----------|--------|--------------
phone_number | String | Yes | (country code)(phone number) e.g. 254722200200 | Phone number to send to
text | String | Yes | Utf8 only encoded strings | The message to be sent
thread | Boolean | No | true/false | Whether the response should be in a conversation thread with the original message


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

> At this stage, the message has been sent to the WhatsApp server for delivery.

### Error

> If there is an error or the phone number does not exist, the server returns an error message and response code of 422.

```json
  {
    "error":"Contact with phone number 25470586564 does not exists"
  }
```

## Other channels (WeChat, Facebook Messenger and Telegram)

> url

```
  https://ongair.im/api/v1/base/send
```

### Method

POST

### Message Params

Field | Type | Mandatory | Format | Meaning 
------|------|-----------|--------|-------------
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

> If there is an error or the phone number does not exist, the server returns an error message and response code of 422.

```json
  {
    "error":"Contact with phone number 123455677 does not exists"
  }
```

# Send an Image

Method sends a image message to a single recipient

> URL

```
  https://ongair.im/api/v1/base/send_image
```

## Method

POST

## Params

Field | Type | Mandatory | Format | Meaning 
------|------|-----------|--------|------------
phone_number | String | Yes | (country code)(phone number) e.g. 254722200200 | Phone number to send to
image | String/File | Yes | Either the URL of the image (must be publicly accessible) or the image uploaded | The image to be sent
Caption | String | No | The caption for the image | A brief text to accompany the image
content_type | String | Yes | image/file_extension_here eg image/jpg or /image/png etc. | The content type of the image file you want to send

## Response

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


## Error

> If there is an error or the phone number does not exist, the server returns an error message and response code of 422.

```json
  {
    "error":"Contact with phone number 25470586564 does not exists"
  }
```

# Send a Group Message

Method sends a text message to a group
> URL

```
  https://ongair.im/api/v1/base/groups/{id}/send_message
```

## Method

POST

## Params

Field | Type | Mandatory | Format | Meaning 
------|------|-----------|--------|-------------
text | String | Yes | Utf8 only encoded strings | The message to be sent


## Response

> The server responds with a JSON message:

```json
  {
    "sent":true,
    "id":2067
  }
```

Field | Type | Meaning
------|------|-----------------
sent | Boolean | True or False if the image
id | Integer | An Identifier for the message. No receipts are provided for group messages

# Receipts

This is a notification from the API when the message has been delivered to the client’s device. It takes the form of a HTTP POST to the specified URL with the following parameters.

Field | Type | Value | Meaning  
------|------|-------|------------------
notification_type | String |  DeliveryReceipt | The type of the notification
phone_number | String | (country code)(phone number) e.g. 254722200200 | Phone number to send to
Id | Integer | 2067 | The identifier of the message. This is the same id returned when the message was sent.

The client should respond with a HTTP status code of 200. The response body is ignored.

# Send Contact

Method sends a VCard to a single recipient

> URL

```
  https://ongair.im/api/v1/base/send_contact
```

## Method

POST

## Params

Field | Type | Mandatory | Format | Meaning 
------|------|-----------|--------|------------
phone_number | String | Yes | (country code)(phone number) e.g. 254722200200 | Phone number to send to
first_name | String | Yes | Utf8 only encoded strings | The first name of the contact
last_name | String | No | Utf8 only encoded strings | The last name of the contact
email | String | No | Utf8 only encoded strings | The email address of the contact
contact_number[0] | String | Yes | (country code)(phone number) e.g. 254722200200 | The primary contact to be sent
contact_number[x] | String | No | (country code)(phone number) e.g. 254722200200 | You can send as many phone numbers as you like by creating an array of contact numbers. e.g. contact_number[1] = 254722123456, contact_number[2] = 254722098765

## Response

> The server responds with a JSON message:

```json
  {
    "sent":true,
    "id":2067
  }
```

Field | Type | Meaning  
------|------|-----------------
sent | Boolean | True or False if the contact was sent
id | Integer | An Identifier for the message. This is used when delivering receipts


## Error

> If there is an error or the phone number does not exist, the server returns an error message and response code of 422.

```json
  {
    "error":"Contact with phone number 25470586564 does not exists"
  }
```

# Contact Received

This is a notification from the API when a contact is received at the Ongair hosted account. It takes the form of a HTTP POST to the specified URL with the following parameters:

Field | Type | Value | Meaning
------|------|-------|-------------------
notification_type | String | ContactReceived | The type of the notification
phone_number | String | (country code)(phone number) e.g. 254722200200 | Phone number that sent the message
contact_phone_number | String | 0706866564 | The phone number as was parsed from the vcard
account | String | 254722123456 | Your WhatsApp account number

# Location Received

This is a notification from the API when a location is received at the Ongair hosted account. It takes the form of a HTTP POST to the specified URL with the following parameters:

Field | Type | Value | Meaning  
------|------|-------|----------------
notification_type | String | LocationReceived | The type of the notification
phone_number | String | (country code)(phone number) e.g. 254722200200 | Phone number that sent the message
address | String | Location name e.g. Mombasa Road | Name of the location where the message was sent from
latitude | Float -1.3292233 for example for Mombasa Road | Latitude of the location where the message was sent from
longitude | Float 36.8772923 for example for Mombasa Road | Longitude of the location where the message was sent from

The client should respond with a HTTP status code of 200. The response body is ignored.

# Message Received

This is a notification from the API when a message is received at the Ongair hosted account. It takes the form of a HTTP POST to the specified URL with the following parameters:

Field | Type | Value | Meaning  
------|------|-------|-----------------
notification_type | String | MessageReceived | The type of the notification
phone_number | String | (country code)(phone number) e.g. 254722200200 | Phone number that sent the message
text | String | Hello world | The message text that was received
account | String | 254722123456 | Your WhatsApp account number

The client should respond with a HTTP status code of 200. The response body is ignored.

# Group Message Received

This is a notification from the API when a message is received at a group to which the Ongair hosted account belongs. It takes the form of a HTTP POST to the specified URL with the following parameters:

Field | Type | Value | Meaning  
------|------|-------|---------------
notification_type | String | GroupMessageReceived | The type of the notification
phone_number | String | (country code)(phone number) e.g. 254722200200 | Phone number that sent the message
group_id | Integer | 18 | The id of the group
text | String | Hello world | The message text that was received
account | String | 254722123456 | Your WhatsApp account number

The client should respond with a HTTP status code of 200. The response body is ignored.

# Image Received

This is a notification from the API when an image is received at the Ongair hosted account. It takes the form of a HTTP POST to the specified URL with the following parameters:

Field | Type | Value | Meaning  
------|------|-------|------------------
notification_type | String | ImageReceived | The type of the notification
phone_number | String | (country code)(phone number) e.g. 254722200200 | Phone number that sent the message
image | String | http://…/ | The url to the image that was sent.
account | String | 254722123456 | Your WhatsApp account number

The client should respond with a HTTP status code of 200. The response body is ignored.

# Added a Group Notification

This is a notification from the API when an Ongair hosted account is added to a group. It takes the form of a HTTP POST to the specified URL with the following parameters:

Field | Type | Value | Meaning  
------|------|-------|--------------
group_id | Integer | 27 | The id of the group
notification_type | String | AddedToGroup | The type of the notification
account | String | 254722123456 | Your WhatsApp account number

The client should respond with a HTTP status code of 200. The response body is ignored.

# Removed a Group Notification

This is a notification from the API when an Ongair hosted account is removed from a group. It takes the form of a HTTP POST to the specified URL with the following parameters:

Field | Type | Value | Meaning  
------|------|-------|-------------
group_id | Integer | 27 | The id of the group
notification_type | String | RemovedFromGroup | The type of the notification
account | String | 254722123456 | Your WhatsApp account number

The client should respond with a HTTP status code of 200. The response body is ignored.

# Contact Added to Group

This is a notification from the API when a contact is added to a group, in which the Ongair hosted account is a member. In this case the group administrator is not the Ongair account.

Field | Type | Value | Meaning  
------|------|-------|------------
notification_type | String | ContactAddedToGroup | The type of the notification
group_id | Integer | 12 | The id of the group
phone_number | String | (country code)(phone number) e.g. 254722200200 | The phone number of the contact that was added to the group
account | String | 254722123456 | Your WhatsApp account number

The client should respond with a HTTP status code of 200. The response body is ignored.

# Contact Removed from Group

This is a notification from the API when a contact is removed from a group, in which the Ongair hosted account is a member. In this case the group administrator is not the Ongair account.

Field | Type | Value | Meaning  
------|------|-------|------------
notification_type | String | ContactRemovedFromGroup | The type of the notification
group_id | Integer | 12 | The id of the group
phone_number | String | (country code)(phone number) e.g. 254722200200 | The phone number of the contact that was removed from the group
account | String | 254722123456 | Your WhatsApp account number

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

```
XML

https://ongair.im/api/v1/search/messages.xml
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

> This API is available in both JSON and XML formats

> JSON

```
  https://app.ongair.im/api/v1/search/contacts.json
```

> XML

```
  https://app.ongair.im/api/v1/search/contacts.xml
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

## Params

Field | Type | Mandatory | Format | Meaning 
------|------|-----------|--------|----------------
id | Integer | Yes | 5 | List to send to
text | String | Yes | Utf8 only encoded strings | The message to be sent
thread | Boolean | No | true/false | Whether the response should be in a conversation thread with the original message

# Sync

This lets you look up whether a phone number is register on WhatsApp or not. This is handled asynchronously and a response of type SyncResult is sent to your server.

> URL

```
  https://ongair.im/api/v1/search/sync
```

## Method

POST

## Params

Field | Type | Mandatory | Format | Meaning 
------|------|-----------|--------|------------
phone_number | String | Yes | (country code)(phone number) e.g. 254722200200 | The contact phone number
name | String | Yes | Utf8 only encoded strings | The contact name

## Batch

You may also send this method in batch by providing an array of phone numbers (upto 10 per call).

Field | Type | Mandatory | Format | Meaning
------|------|-----------|--------|-------------------
phone_numbers[0] | String | Yes | (country code)(phone number) e.g. 254722200201 | The contact phone number
phone_numbers[1] | String | Yes | (country code)(phone number) | The contact phone number

## Response

> The server responds with a JSON message:

```json
  { 
    "requested": true 
  }
```

## Error

> This occurs when the phone_number parameteter is not included in the request\

```json
  {
    "error": "No phone number was provided" 
  }
```

## Callback

This is a web hook posted to the client when the phone number look up returns a response. This callback is usually a second or two after the original call.

Field | Type | Value | Meaning
------|------|-------|-------------------
notification_type | String | SyncResult | The type of the notification
registered | Array | ["254722123456", "254722123457"] | An array of numbers that were found to be registered on WhatsApp.
unregistered | Array | ["254722123458", "254722123459"] | An array of numbers that were found not to be registered on WhatsApp.



