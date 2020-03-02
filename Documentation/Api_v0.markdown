# Documentation for the Basement API v0

## Connecting

The API is found at https://api.basement.yoga.unicus.com/v0, and can only be accessed through HTTPS. The individual end points are usually either only available to POST or GET requests, and the specific method required is outlined for each endpoint below.

### Module: User

The user module has the base URI `/user`.

#### `POST /v0/user/register`

Registers (and logs in) a new user. Logged in users cannot register new users.

##### Parameters

```
username: A username. Must be unique.
password_one: A password.
password_two: The same password repeated, must match password_one.
email_address: An email address. This is subjected to basic sanity checks, and must be valid.
```

##### Response Codes

- 201 Created: The user was successfully created and logged in.
- 400 Bad Request: A form field is missing or malformed.
- 403 Forbidden: A user is already logged in, or the username is taken.

#### `POST /v0/user/login`

Logs in a returning user.

##### Parameters

```
username: The username.
password: The password for the user given by the username.
```

##### Response Codes

- 204 No Content: The user was successfully logged in.
- 400 Bad Request: A form field is missing or malformed.
- 403 Forbidden: The user does not exist, or the password is incorrect.

#### `GET /v0/user/profile`

Lists the profile of the currently logged in user.

##### Response Body

A JSON object describing the current user, with the following fields:

```
id: The internal user ID, an UUID.
username: The username.
password: The user's hashed password.
email_address: The user's email address.
```

##### Response Codes

- 200 OK: The response body represents the user.
- 403 Forbidden: There is no user logged in.

#### `GET /v0/user/cards`

Lists the cards authored by the currently logged in user.

##### Response Body

A JSON list containing the cards created by the current user (for a complete description of the format, see [Module: Cards](#module-card).

##### Response Codes

- 200 OK: The response body represents the user.
- 403 Forbidden: There is no user logged in.

### Module: Card

The card module has the base URI `/card`.

#### `POST /v0/card/create`

Creates a new card, tied to the currently logged in user.

##### Post Body

A JSON object containing the following fields must be provided:

```
id: The internal card ID, an UUID (optional, can be used to re-create a deleted card to emulate editing cards).
title: The title of the card.
genre: A freetext field containing the genre of the card.
description: A description of the event.
date: The date of the event, in ISO8601 format.
location: The location of the event.
image_url: An URL poitining to an image associated with the event.
```

##### Response Body

A JSON object representing the completed card object. Any info the user has entered should be updated with the info received in this way.

##### Response Codes

- 200 OK: The body contains the updated information about the body.
- 201 Created: The updated information can be found at a location indicated by a Location header.
- 400 Bad Request: The post body is missing or malformed.
- 403 Forbidden: No user is logged in, or the logged in user does not have permission to create cards.

#### `GET /v0/card/list`

##### Parameters

The card list requires two parameters:

```
page: The number of pages of cards to skip.
limit: The number of cards per page.
```

##### Response Body

A JSON list containing the cards matching the parameters, sorted by `date` (newer first).

##### Response Codes

- 200 OK: The list was fetched successfully.
- 400 Bad Request: Parameter `page` or `limit` is missing or malformed.

#### `GET /v0/card/{card_id}/read`

Lists the contents of the card identified by the `card_id` URL field (an UUID).

##### Response Body

A JSON object describing the card, with the following fields:

```
id: The internal card ID, an UUID.
author_id: The ID corresponding to the user who created the card.
title: The title of the card.
genre: A freetext field containing the genre of the card.
description: A description of the event.
date: The date of the event, in ISO8601 format.
location: The location of the event.
image_url: An URL poitining to an image associated with the event.
```

##### Response Codes

- 200 OK: The body describes the card.
- 400 Bad Request: The `card_id` URL field is malformed.
- 404 Not Found: No card corresponding to the `card_id` URL field could be located.

#### `POST /v0/card/{card_id}/update`

Updates a card.

##### Response Codes

- 501 Not Implemented: Always.

#### `GET /v0/card/{card_id}/delete`

Deletes the card identified by the `card_id` URL field (an UUID).

##### Response Codes

- 204 No Content: The card is deleted.
- 400 Bad Request: The `card_id` URL field is malformed.
- 403 Forbidden: No user is logged in, or the logged in user does not have permission to delete the card.
- 404 Not Found: No card corresponding to the `card_id` URL field could be located.
