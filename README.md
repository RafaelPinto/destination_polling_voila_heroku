# Destination polling

We show how to deploy an interactive jupyter notebook using Voila and Heroku. We were inspired by [Martin Renou's voila_heroku repository](https://github.com/martinRenou/voila_heroku) so we follow closely his steps for deployment, while adding interactivity to the notebook with the use of ipywidgets and ipyleaflet libraries, and postgresql database support with psycopg2.


# Overview

The app is meant to poll a small group of users and present the results of the polling after the user has casted a vote.

You can test-drive the app here: http://destination-polling.herokuapp.com/

If you would like to reproduce it, follow the **Details** and **Deployment steps** sections below.


# Details

This application has 3 key features:

1. A Jupyter Notebook serving as a webapp with Voila.

2. Geolocation using ```geolocation``` library and LocationIQ API: For the geolocation to work you will need a key issued by LocationIQ. This is free and easy to setup. More on this on the **LocationIQ setup** subsection below.

3. We save the data to a Postgresql database. To get a local database use the steps in **Set up local Postgres** subsection below. This is great for testing locally before pushing your app to the world. For using (provisioning) Heroku's database read step 9 on section **Deployment steps** below.


## LocationIQ setup

The are a lot of [geocoding providers](https://geocoder.readthedocs.io/index.html#providers) supported by the geocoder library. Our LocationIQ choice was made based on [minimal restrictions and setup requirements](https://locationiq.com/pricing) for a free account.

### Restrictions

As our geocoding requests will be submitted by people and not machines, we can comply with the first 3 restrictions without too much trouble. For the last three restrictions we annotate the reason it doesn't apply to us:  

1. 10,000 requests /day 
2. 60 requests /minute 
3. 2 requests /second 
4. Street maps only: We won't use this.
5. Limited commercial use: Our tool is not commercial.
6. Cache results for upto 48 hours: At the end of each day we run ```heroku pg:reset followed``` by ```heroku run rake db:migrate``` to remove any geolocated data.


### Setup requirements

First setup your [LocationIQ free account](https://locationiq.com/register). You will only need a valid email address. Just follow the steps on the website.

After successful registration you will be provided with a key. This key must be stored in the text file (key.txt) inside the notebooks directory, i.e. notebooks/key.txt 


## Set up local Postgres

We must keep track of the casted votes to assess the popularity of each destination. Heroku offers free support for Postgresql in their **hobby** [data-base-as-a-service plan](https://www.heroku.com/pricing#databases) for less than 10k rows, i.e. number of unique voted cities in our app. Since we will not exceed this limit, we will use Postgresql to store our data.

However, in the development stage it is best to set up a local database for testing. I used [these steps](https://launchschool.com/blog/how-to-install-postgres-for-linux) in Ubuntu to achieve this:
 
1. ```sudo apt-get update```
    
2. ```sudo apt-get install postgresql postgresql-contrib libpq-dev```
    
3. ```sudo -u postgres createuser --superuser $USER```
    
4. ```sudo -u postgres createdb $USER ```
    
5. Finally, on your **Home** directory create an empty file named .psql_history: ```touch .psql_history```  


# Deployment steps

1. First, create an Heroku account (free account is fine for testing)": https://signup.heroku.com/

2. Install Heroku on your machine: https://devcenter.heroku.com/articles/getting-started-with-python#set-up

3. Clone this repository, or create your own repository that follows the same structure:

    ```git clone https://github.com/RafaelPinto/destination_polling_voila_heroku.git```
    
   ```cd destination_polling_voila_heroku```
   
4. Create your Notebook and put it in the notebooks directory


5. Add the dependencies needed for running your Notebook in the requirements.txt file, e.g.:
    - ipywidgets==7.5.1
    - ipyleaflet==0.11.2
    - voila==0.1.6
    - geocoder==1.38.1
    - psycopg2-binary

6. Edit the Procfile file by replacing notebooks/destination_polling_voila_heroku.ipynb with the path to your awesome Notebook

7. Commit everything
   git commit -m "My awesome app on Heroku!"

8. Create the Heroku app. We will name our app *destination-polling*, you can pick some other name if you prefer:
   heroku create destination-polling

9. Provision Heroku Postgres database: https://devcenter.heroku.com/articles/heroku-postgresql#provisioning-heroku-postgres
    
    ```heroku addons:create heroku-postgresql:hobby-dev```


10. Now deploy your code:
   ```git push heroku master```
   
   
That's it! Easy right? Now you can open your app using:

```heroku open```

or following the URL provided by heroku, e.g: http://destination-polling.herokuapp.com/
