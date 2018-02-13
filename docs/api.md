# Reimagined-Fortnight API

## Root URL

`api/v0/`

## Listings

### GET `listings`

> Return all listings in the database

### GET `listings?offset=<offset>&limit=<limit>`

> Return a page of `<limit>` listings starting from `<offset>` pages

### GET `listings/<id>`

> Return a single listing with the matching ID

### GET `listings/<id>/sellers`

> Return the seller for a listing

### GET `listings/<id>/manufacturers`

> Return the manufacturer for a listing

### GET `listings/<id>/models`

> Return the model for a listing

### GET `listings/<id>/photos`

> Return all photos for the listing

### POST `listings`

> Create a new listing

### POST `listings/<id>/photos`

> Add a new photo to the listing

### PUT `listings/<id>`

> Update a single listing

### PUT `listings/<id>/photos/<id>`

> Update a listing photo

### DELETE `listings/<id>`

> Delete a single listing

### DELETE `listings/<id>/photos/<id>`

> Delete a listing photo

## Sellers

### GET `sellers`

> Return all sellers

### GET `sellers?offset=<offset>&limit=<limit>`

> Return `<limit>` sellers starting `<offset>` pages in

### GET `sellers/<id>`

> Return a specific Seller

### GET `sellers/<id>/reviews`

> Return all reviews for a seller

### GET `sellers/<id>/reviews?offset=<offset>&limit=<limit>`

> Return `<limit>` reviews for seller starting `<offset>` pages in

### POST `sellers`

> Create a new seller

### POST `sellers/<id>/reviews`

> Create a new review on seller

### PUT `sellers/<id>`

> Update a specific seller

### PUT `sellers/<id>/reviews/<id>`

> Update a specific seller review

### DELETE `sellers/<id>`

> Delete a specific seller

### DELETE `sellers/<id>/reviews/<id>`

> Delete a specific seller review
