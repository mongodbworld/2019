# Build Web Apps Faster Using Stitch Rules & React
*Author*: [Robbert Kauffman](mailto:robbert.kauffman@mongodb.com)

## Introduction

Modern web applications are often built using API-based front-ends such as React, Angular, 
VueJs. In this session, we will dive into how Stitch can be used to provide APIs for your 
front-end, removing the need for writing and maintaining boilerplate back-end services, and 
having to manage and scale back-end servers. All to make developer’s lives easier.

## Step 1 — Creating a Stitch App

In [Atlas](https://cloud.mongodb.com):
1. Navigate to *Stitch* using the left hand navigation
2. Click *Create New Application*
3. In the dialog window, name the app, select one of your Atlas clusters to link it, and hit 
*Create*.

![Stitch Application](images/step1.png "Atlas interface — Stitch")

## Step 2 — Setting up Stitch App

- Open your app by selecting it from the Stitch Applications list
- On the Getting Started screen: Turn on Anonymous Authentication 
![Stitch Application](images/step2a.png "Stitch — Anonymous Authentication toggle")
- Copy your Stitch App ID in top-left corner
![Stitch Application](images/step2b.png "Stitch — Stitch App ID")

## Step 3 — Setting up MongoMart Front-end

- Go to https://stackblitz.com/edit/mongomart-world-session-1
  - StackBlitz is an online IDE and app server for easily editing code and running apps online

## StackBlitz

![Stitch Application](images/stackblitz.png "StackBlitz interface")

## Step 4 — Setting up MongoMart Front-end

- Edit config.js: change value of Stitch app ID on line 2 to your Stitch App ID
- Click on Open in New Window on top-right. Your screen should look like this:
![Stitch Application](images/step4b.png "MongoMart — Stitch Rules errors")
- Reload page if you get the following error: "default app can only be set once; currently 
set to 'XXX’"

## Exercise 1.1 — Adding a read-rule

- Go to your Stitch app
- Go to *Rules*
- Create a new rule by clicking on … and *Add Database/Collection*
![Stitch Application](images/exercise1.1a.png "Stitch — Add Database/Collection")
  - Enter `mongomart` as database name, and press return or click on *Create*
  - Enter `item` as collection name, and press return or click *Create*
  ![Stitch Application](images/exercise1.1b.png "Stitch — Create collection")
  - Select *Users can only read all data* as template
  - Click the green *Add collection* button at the bottom
- Go back to the live app and reload: do you see any products?
![Stitch Application](images/exercise1.1c.png "MongoMart — Empty product list")

## Exercise 1.2 — Temporary permissions for Importing data

- Go back to the item-rule in *Rules* in your Stitch App
- Enable write-permissions by checking the *Write* checkboxes
- Edit the *default* (1) permissions and:
![Stitch Application](images/exercise1.2a.png "Stitch — Edit default permissions")
  - Enable *Insert Documents* and *Delete Documents*
  ![Stitch Application](images/exercise1.2b.png "Stitch — Enable Insert Documents and Delete Documents")
  - Click *Done Editing*
- Don’t forget to click *Save* on the top right
- Go back to the live app and:
  - Click the *Import Items* link in the footer
  - Reload
  - Products should now be visible!
  ![Stitch Application](images/exercise1.2c.png "MongoMart — Products visible")

## Exercise 1.3 — Fixing the rule

- Go back to the item-rule in *Rules* in your Stitch App
- Disable the write-permissions by unchecking the *Write* checkboxes
- Don’t forget to click *Save* on the top right
- All set now!

## Exercise 2 — Adding a write-rule

- Go back to *Rules* in your Stitch App
- Create a new rule by clicking on … and *Add Database/Collection*
  - Enter `mongomart` as database name
  - Enter `users` as collection name, and press return or click *Create*
  - Select *Users can only read and write their own data* as template
  - Enter `*id` for field name for User ID
  - Click the green *Add collection* button at the bottom
- Go back to live app, reload, and:
  - Cart should now be visible and you should be able to add products to cart from product 
  detail pages!
  ![Stitch Application](images/exercise2.png "MongoMart — Cart")

## Exercise 3 — Adding a Stitch function

- Go to *Functions* in your Stitch App
- *Create New Function*
  - Use the name `setNotification` (case sensitive)
  - Check the *Run As System* toggle
  ![Stitch Application](images/exercise3a.png "Stitch — Run As System")
  - Click *Save*
  - Copy code from *stitch/setNotification.js* from app in Stackblitz
  - Open *Function Editor* tab in Stitch
  ![Stitch Application](images/exercise3b.png "Stitch — Function Editor")
  - Click *Save*
- Go back to live app, reload, and:
  - View the *Coffee Mug* detail page and click on *Notify me when in stock*
  ![Stitch Application](images/exercise3c.png "MongoMart — Notify me when in stock")

## Bonus Exercise 1 — Displaying Reviews

We’re going to display recent reviews on the product detail page
- Uncomment line 17 in *src/LatestReviews/LatestReviews.js*
- Edit in same file:
  - Add following code to *fetchReviews()* on line 21:
    - Get database handle:
    ```js
    const db = this.props.client.getServiceClient(
        RemoteMongoClient.factory, 
        stitchClusterNames.reviews)
    .db(dbName);
    ```
    - Query database:
    ```js
    this.props.clientAuthenticated.then(() => db
        .collection(collNames.reviews)
        .find({})
        .asArray())
    ```
    - Process response:
    ```js
    .then(response => {
      if (response) {
        this.setState({
          reviews: response,
          reviewsError: null
        });
      }
    })
    ```
    - Error handling:
    ```js
    .catch(err => {
      this.setState({
        reviewsError: err
      });
      console.error(err);
    });
    ```
- Test & Review: Go to live app, reload, and check one of the product pages
![Stitch Application](images/exercise-bonus.png "MongoMart — Latest reviews")
