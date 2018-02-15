# Reimagined-Fortnight

A Coding Exercise to develop an automotive listing application with customer review support.

_Jason Wilson, Developer_

## Platform Architecture

The following is my proposed stack for the supporting platform of this application. As I intend to attempt building the final product for the exercise I decided it was important to lay out how I would architect the solution.

### Client Application

The client application will be hosted on Apache. Persistent data from user sessions, such as recently viewed listings, will be stored through a Redis instance. Images added to the listings will be stored in an S3 Bucket through a CloudFront CDN. The listings, reviews, and supporting data tables will be stored with a PostgreSQL instance and listing data will be indexed in an Elasticsearch instance to improve the performance of searches.

![Application Architecture](https://github.com/SgiobairOg/reimagined-fortnight/blob/master/docs/assets/architecture.png)

### Management Console Application

The management console is beyond the scope of the exercise and will be detailed if time permits.

## Application Stack

### Client App Technologies

The client side listing application will be built using Apache to serve a Laravel and Vue.JS app. Laravel provides a secure framework with good support and a familliar MVC structure. Laravel’s templating also provides an easy way to integrate with the Vue components. Vue was selected for its ease of use in building reactive web apps. Components will handle the search form and listings pages which will allow for instant feedback for users without the need to pull in JQuery to edit the DOM or refresh entire pages.

### API Technology

The API will be built using Golang. Using Golang will provide better performance for the API as opposed to using Node/Express or Python.

### Database Technology

The database will be built using PostgreSQL. Using a relational database over a NoSQL database fits with the related nature of the data and will avoid any issues with duplicating data which comes out of using NoSQL Document structures to model elements with the same types. Relational data will also allow for the update of manufacturers and models without needing to edit the listings that reference them.

## Data Model

### Listings

| Column           | Type                                                                       | PKFK |
| ---------------- | -------------------------------------------------------------------------- | :--: |
| listingId        | UID                                                                        |  PK  |
| makeId           | UID _has one Make_                                                         |      |
| modelId          | UID _has one Model_                                                        |      |
| sellerId         | UID _has one Seller_                                                       |      |
| shortDescription | Text                                                                       |      |
| description      | Text                                                                       |      |
| category         | Text                                                                       |      |
| pricing          | Array _JSON key-value pairs for the different pricing options needed_      |      |
| keywords         | Array _JSON key-value pairs containing specifications and their value_     |      |
| photos           | Array _JSON objects containing the photo URL, alt text, and display order_ |      |
| dateAdded        | Datetime                                                                   |      |

### Makes

| Column      | Type                    | PKFK |
| ----------- | ----------------------- | :--: |
| makeId      | UID                     |  PK  |
| listingId   | UID _has many Listings_ |  FK  |
| name        | Text                    |      |
| description | Text                    |      |
| website     | Text                    |      |

### Models

| Column      | Type                    | PKFK |
| ----------- | ----------------------- | :--: |
| modelId     | UID                     |  PK  |
| listingId   | UID _has many Listings_ |  FK  |
| makeId      | UID _has one Make_      |  FK  |
| name        | Text                    |      |
| description | Text                    |      |
| year        | Int                     |      |

### Sellers

| Column       | Type                    | PKFK |
| ------------ | ----------------------- | :--: |
| sellerId     | UID                     |  PK  |
| listingId    | UID _has many Listings_ |  FK  |
| reviewId     | UID _has many Reviews_  |  FK  |
| sellerTypeId | UID _has one type_      |  FK  |
| name         | Text                    |      |
| street       | Text                    |      |
| city         | Text                    |      |
| state        | Text                    |      |
| zip          | Int                     |      |
| phone        | Text                    |      |
| email        | Text                    |      |
| website      | Text                    |      |

### SellerTypes

| Column       | Type                   | PKFK |
| ------------ | ---------------------- | :--: |
| sellerTypeId | UID                    |  PK  |
| sellerId     | UID _has many sellers_ |  PK  |
| name         | Text                   |      |
| description  | Text                   |      |

### Reviews

| Column   | Type                 | PKFK |
| -------- | -------------------- | :--: |
| reviewId | UID                  |  PK  |
| sellerId | UID _has one seller_ |  PK  |
| name     | Text                 |      |
| email    | Text                 |      |
| title    | Text                 |      |
| body     | Text                 |      |
| rating   | smallInt             |      |
