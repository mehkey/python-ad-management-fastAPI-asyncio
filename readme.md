

# Ad Management


## Installation

1. Install the python dependencies: `pipenv install`
2. Install a local version of the database: `docker compose up --build --remove-orphans -d`
3. Run database migration to have the latest schemas: `alembic upgrade head`
4. Run the app locally: `uvicorn app.main:app --reload`
5. Run the worker locally: `python ad_management_worker.py`

6. Run Tests for the app locally: ` pytest ./app/main_test.py`

## Usage

The easiest way to get started with the api is to head over to the docs generated by fastapi at `localhost:8000/docs`.

1. Create an
   advertisor,
   campaign,
   adgroup,
   ad
   Using the endpoint
     `POST /queues`
2. Add players to the queue using the `POST /players`. Each players can have a set of attributes (for example a rating) that can be used in the cost function.
3. Call the `GET /players/{player_id}` endpoint to get the status of a player. When a player gets a match, its status will have the value `MATCHED`.
4. Using the match id of a player, call the `GET /matches/{match_id}` endpoint to get the list of players in the matching.



## Useful commands

```
python3 -m venv fenv

source fenv/bin/activate


pip install pipenv

pipenv install

pipenv install <package> --dev

alembic revision -m "create all tables"

alembic upgrade head

alembic downgrade -1


export PYTHONPATH=$PWD

```


# Schema

## Users
+ `id`: a unique identifier for each user, this field is the primary key and is of type `SERIAL`.
+ `username`: a text field that stores the username of the user, this field is unique and is of type `VARCHAR(255)`.
+ `email`: a text field that stores the email of the user, this field is unique and is of type `VARCHAR(255)`.
+ `password_hash`: a text field that stores the hashed password of the user, this field is of type `VARCHAR(255)`.

## Advertisers
+ `id`: a unique identifier for each advertiser, this field is the primary key and is of type `SERIAL`.
+ `name`: a text field that stores the name of the advertiser, this field is of type `VARCHAR(255)`.
+ `email`: a text field that stores the email of the advertiser, this field is of type `VARCHAR(255)`.
+ `phone_number`: a text field that stores the phone number of the advertiser, this field is of type `VARCHAR(255)`.

## AdCampaigns
+ `id`: a unique identifier for each ad campaign, this field is the primary key and is of type `SERIAL`.
+ `name`: a text field that stores the name of the ad campaign, this field is of type `VARCHAR(255)`.
+ `start_date`: a date field that stores the start date of the ad campaign, this field is of type `DATE`.
+ `end_date`: a date field that stores the end date of the ad campaign, this field is of type `DATE`.
+ `budget`: a float field that stores the budget of the ad campaign, this field is of type `FLOAT`.
+ `advertiser_id`: a foreign key that references the advertiser who created the ad campaign, this field is of type `INTEGER` and references the `id` field of the `Advertisers` table.
+ `status`: a text field that stores the current status of the ad campaign (e.g. "created", "running", "finished"), this field is of type `VARCHAR(255)`.

## AdGroups
+ `id`: a unique identifier for each ad group, this field is the primary key and is of type `SERIAL`.
+ `name`: a text field that stores the name of the ad group, this field is of type `VARCHAR(255)`.
+ `targeting_criteria`: a text field that stores the targeting criteria for the ad group, this field is of type `VARCHAR(255)`.
+ `ad_campaign_id`: a foreign key that references the ad campaign that the ad group belongs to, this field is of type `INTEGER` and references the `id` field of the `AdCampaigns` table.

## Ads
+ `id`: a unique identifier for each ad, this field is the primary key and is of type SERIAL.
+ `title`: a text field that stores the title of the ad, this field is of type VARCHAR(255).
+ `image_url`: a text field that stores the URL of the ad's image, this field is of type VARCHAR(255).
+ `destination_url`: a text field that stores the URL that the ad should redirect to when clicked, this field is of type VARCHAR(255).
+ `ad_group_id`: a foreign key that references the ad group that the ad belongs to, this field is of type INTEGER and references the id field of the AdGroups table.


# API

## POST /campaigns

Create a new campaign.

### Request Body

- name: string *
- start_date: date *
- end_date: date *
- budget: float *
- advertiser_id: int *
- status: string
- ad_groups: 
  - "name": "string" 
  - "targeting_criteria": "string"
  - "ads": 
    - "title": "string"
    - "image_url": "string"
    - "destination_url": "string"


### Example
```json
{
    "name": "Test Ad Campaign",
    "start_date": "2022-01-01T00:00:00",
    "end_date": "2022-12-31T23:59:59",
    "budget": 1000.0,
    "status": "CREATED",
    "ad_groups": [
        {
            "name": "Test Ad Group",
            "targeting_criteria": "Test targeting criteria",
            "ads": [
                {
                    "title": "Test Ad",
                    "image_url": "http://test-image-url.com",
                    "destination_url": "http://test-destination-url.com"
                }
            ]
        }
    ]
}
```

## GET /campaigns/{id}

Get a campaign by ID.

### Path Parameters

- id: int *

### Responses

- 200: OK
- 401: Unauthorized
- 404: Campaign not found
- 500: Internal Server Error



Example Result
```json
[
    {
        "id": 1,
        "name": "Test Ad Campaign",
        "start_date": "2022-01-01T00:00:00",
        "end_date": "2022-12-31T23:59:59",
        "budget": 1000.0,
        "status": "CREATED",
        "ad_groups": [
            {
                "id": 1,
                "name": "Test Ad Group",
                "targeting_criteria": "Test targeting criteria",
                "ads": [
                    {
                        "id": 1,
                        "title": "Test Ad",
                        "image_url": "http://test-image-url.com",
                        "destination_url": "http://test-destination-url.com"
                    }
                ]
            }
        ]
    }
]
```


## FAST API

![FAST API](./image/fast.png)

