# Reimagined-Fortnight

A Coding Exercise to develop an automotive listing application with customer review support.

Jason Wilson, Developer

## Platform Architecture

The following is my proposed stack for the supporting platform of this application. As I intend to attempt building the final product for the exercise I decided it was important to lay out how I would architect the solution.

### Client Application

The client application will be hosted on Apache. Persistent data from user sessions, such as recently viewed listings, will be stored through a Redis instance. Images added to the listings will be stored in an S3 Bucket through a CloudFront CDN. The listings, reviews, and supporting data tables will be stored with a PostgreSQL instance and listing data will be indexed in an Elasticsearch instance to improve the performance of searches.

![Application Architecture](https://github.com/SgiobairOg/reimagined-fortnight/blob/master/docs/assets/architecture.png)

### Management Console Application

The management console is beyond the scope of the exercise and will be detailed if time permits.

## Application Stack

### Client App Technologies

The client for the listing application would be built using a serverside MVC framework serving a client-side VueJS app. The MVC framework helps separate out the concerns giving a model to speak with the API, controllers to take actions on the model, and views to handle purely presentational tasks. I've worked with CakePHP for PHP and Ruby on Rails for Ruby but would like to use Laravel here for as the templating engine and support seem to be better. Vue was selected for the client-side for its ease of use in building reactive web apps. Components will handle the search form and listings pages which will allow for instant feedback for users without the need to pull in JQuery or other scripting to edit the DOM or refresh entire pages. This will help speed up the user interactions.

CloudFront and S3 will be used to serve and store images respectively. CoudFront, as a CDN front for the S3 bucket will serve the images from the closest edge point to an end-user helping to speed up the serving of the images.

### API Technology

The API will be built using Golang. Using Golang will provide better performance for the API as opposed to using Node/Express or Python.

### Database Technology

The database will be built using PostgreSQL. Using a relational database over a NoSQL database fits with the related nature of the data and will avoid any issues with duplicating data which comes out of using NoSQL Document structures to model elements with the same types. Relational data will also allow for the update of manufacturers and models without needing to edit the listings that reference them.

## Data Model

![Database Diagram](https://github.com/SgiobairOg/reimagined-fortnight/blob/master/docs/assets/db.png)

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

## Objects and Methods

### Session Object

The session object would store information on the user's activity for example keeping a list of the last five listings the user has visited to display in a recent listings section. The Object would define the structure of the session data and would have the following methods:

* Create a new session

* Store a session in Redis

* Retrieve a session from Redis

* Update session data in Redis

* Delete session data from Redis

### Listings Object

The Listings object would contain the model and methods for retrieving and displaying listings. The methods needed for the app as described would be:

* Retrieve listing IDs that match a query from the API by pages

* Retrieve listings from the API by pages

* Retrieve the next page of listings from the API

* Retrieve the previous page of listings from the API

* Retrieve the details of an individual listing

### Sellers Object

The sellers data would not be managed separately in the scope of the exercise but I decided it shoudl be a separate table and object as, down the line, the seller data could be changed through a dashboard and I wouldn't want the data to be stored in the listings themselves. Seller data would be displayed through a join on the Listing's or Review's SellerID field when looking at a Listing or Review. For the scope of the exercise the Sellers object would not be needed as the data won't be manipulated, just read from the DB through the API.

### Reviews Object

The review object contains the model and methods for retrieving reviews for a seller. The Seller Information is passed to the Reviews object from the Listings object so that the seller info can be displaye don the page and the Seller Id can be used to retrieve reviews. The methods needed for the app as described would be:

* Retrieve number of reviews for Seller

* Retrieve reviews from the API by pages for Seller

* Retrieve the next page of Reviews

* Retrieve the previous page of Reviews

* Retrieve an individual review

## Third-Party Components

### Email Handler

Emails would be sent using PHPMailer and their provided classes for handling email creation and sending. This saves the trouble of building out a mail handler on top of PHP mail().

### S3 and CloudFront

For the scope of the exercise images for the listings would be manually added to S3 but in the event a management app was built, the AWS SDK would be used to add the images. The cloudfront root URL would be stored as a configuration property for the client so that files could be retrieved for display using the CDN.

## Performance Enhancement
