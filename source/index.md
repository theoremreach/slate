---
title: Theorem Reach API Reference

language_tabs:
  - shell

toc_footers:
  - API V1.1
  - <a href='http://theoremreach.com/contact'>Sign Up for a Developer Key</a>
  - <a href='http://github.com/tripit/slate'>Documentation Powered by Slate</a>

search: true
---

# Introduction

Welcome to the TheoremReach API documentation! Our API enables you to create and modify research projects on our platform, calculate feasibility, start/stop the flow of sample, and reconcile completes.

# Getting Started

Before you can begin creating survey sample requests using our API, you will need to contact admin@theoremreach.com for an API token. This token will be used for authentication purposes described in the section below.

The first API token that we send you will only grant you access to our staging server, http://api-staging.theoremreach.com. Please use this as your base URL until you are ready to go live.

When you are ready to go live, please contact us for a live api token. Update your routes to point to https://api.theoremreach.com. Please note the use of SSL/HTTPS for production.

# Authentication

We use HTTPS combined with an access_key to enforce access controls to secure resources


```shell
curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X GET https://api.theoremreach.com/api/v1/countries?access_token=testkey
```

<aside class="notice">
You must replace <code>testkey</code> with your personal API key.  access_token must appear in every request.
</aside>

# Integration Guide

## Mapping

The first step of integration is to load and map our data to your system. This will ensure your system is able to properly communicate with our API.  

# Countries

### INDEX - List Available Countries

```shell
curl -i -H "Accept: application/json" 
-H "Content-Type: application/json" -X GET 
https://api.theoremreach.com/api/v1/countries
```

> The above command returns JSON structured like this:

```json
[
  {
    "id":9,
    "name":"English - United States"
  }
]
```


This end point allows you to retrieve all active country and language combinations supported by TheoremReach.  Only actively support countries will be returned.  The ID must be used when creating campaigns and checking feasibility.

**HTTP REQUEST**

`GET https://api.theoremreach.com/api/v1/countries`

**REQUIRED PARAMETERS**

`None`

**RESPONSE**

Parameter | Type | Description
--------- | ---- | -----------
id | integer | Unique identifier used when making additional feasibility and campaign creation calls
name | string | Name of the country/language combination


# Trait Questions

```shell
curl -i -H "Accept: application/json" 
-H "Content-Type: application/json" -X GET 
http://api.theoremreach.com/api/v1/countries/9/trait_questions
```

> The above command returns JSON structured like this:

```json
[
  {
    "id":1,
    "question_text":"What is your age?",
    "question_type":"numeric",
    "trait_question_type":"age",
    "country_id":9
  },
  {
    "id":2,
    "question_text":"What is your zip code?",
    "question_type":"numeric",
    "trait_question_type":"zip",
    "country_id":9
  }
]
```

Trait questions allow you to check feasibility and build quotas around a respondents profile.  An example trait question may be "What is your age?".  

### INDEX - List Available Trait Questions

This end point allows you to retrieve all active trait questions within a given country.

**HTTP REQUEST**

`GET http://api.theoremreach.com/api/v1/countries/:country_id/trait_questions`


**REQUIRED PARAMETERS**

`None`


**RESPONSE**

Parameter | Type | Description
--------- | ---- | -----------
id | integer | Unique identifier used when making additional feasibility and campaign creation calls
question_text | string | The text of the question that the user will see
question_type | string | The type of question (single, multiple, numeric, etc.)
trait_question_type | string | Short description of the question
country_id | integer | The id of the country the trait question belongs to



# Traits



```shell
curl -i -H "Accept: application/json" 
-H "Content-Type: application/json" -X GET 
http://api.theoremreach.com/api/v1/trait_questions/1/traits
```

> The above command returns JSON structured like this:

```json
[
  {
    "id":1,
    "value_text":"Ford Taurus"
  },
  {
    "id":2,
    "value_text":"Ford Aerostar"
  }
]
```

### INDEX - List Available Traits for a Question

This end point allows you to retrieve all trait questions within a given country.

**HTTP REQUEST**

`GET http://api.theoremreach.com/api/v1/trait_questions/1/traits`


**REQUIRED PARAMETERS**

`None`


**RESPONSE**

Parameter | Type | Description
--------- | ---- | -----------
id | integer | Unique identifier used when making additional feasibility and campaign creation calls
value_text | string | The answer to the question

# Feasibility

### POST - Feasibility For Trait Questions

```shell
curl -H "Content-Type: application/json" --data 
"{\"access_token\":\"testkey\",
\"feasibility\":{\"trait_questions\":{\"1\":[18,10,20,21,22,23,24]},
\"country_id\":9,\"price\":5,\"loi\":10,\"incidence\":100}}" 
https://api.theoremreach.com/api/v1/feasibility
```

> The above command returns JSON structured like this:

```json
[
  {
    "trait_questions":{"1":[18,10,20,21,22,23,24]},
    "completes_per_day":1220
  }
]
```


This end point allows you to check the number of completes per day you should receive with a given set of trait questions.

**HTTP REQUEST**

`POST https://api.theoremreach.com/api/v1/feasibility`

**REQUIRED PARAMETERS**

Parameter | Type | Description
--------- | ---- | -----------
trait_questions | hash | Trait Question Hash
country_id | integer | Country id of the campaign. Must be an active country id.
cpi | decimal | Amount you will pay per complete. This must be a minimum of $1 per complete and increments up in $0.50 increments
incidence | integer | The percentage chance that a random respondent will qualify and complete the survey. Must be a number between 1 and 100
loi | integer | Length of interview (minutes), min 1 max 30

**RESPONSE**

Parameter | Type | Description
--------- | ---- | -----------
trait_questions | hash | Trait Question Hash
completes_per_day | integer | Number of completes you can expect per day


# Campaigns

## Index - List Your Campaigns

```shell
curl -i -H "Accept: application/json" 
-H "Content-Type: application/json" -X GET 
https://api.theoremreach.com/api/v1/campaigns
```

> The above command returns JSON structured like this:

```json
[
  {
    "id":1,
    "title":"TR Test Campaign via API",
    "start_date":"2016-04-08",
    "end_date":"2016-04-13",
    "status":"In Progress",
    "loi":10,
    "incidence":90,
    "country_id":9,
    "survey_url":"http://clientsurvey.com/survey/12345?id={USER_ID}",
    "test_survey_url":"http://clientsurvey.com/survey/12345?id={USER_ID}&test=1",
    "cpi":"3.0"
  }
]
```


This end point allows you to retrieve all the campaigns that you've created regardless of their status.

**HTTP REQUEST**

`GET https://api.theoremreach.com/api/v1/campaigns`

**REQUIRED PARAMETERS**

`None`

**RESPONSE**

Parameter | Type | Description
--------- | ---- | -----------
id | integer | Unique identifier used when making additional feasibility and campaign update calls
title | string | Name of the country/language combination
start_date | date | When should the survey start
end_date | date | When should the survey end
status | string | 'Completed', 'In Progress', 'Paused', 'Draft' (Draft is default and cannot be set)
loi | integer | Length of interview (minutes)
incidence | integer | The percentage chance that a random respondent will qualify and complete the survey. Must be a number between 1 and 100
country_id | integer | Country id of the campaign. Must be an active country id.
survey_url | string | Production url for campaign. Must contain a parameter that stores the user's ID in the format of {USER_ID}
test_survey_url | string | Test url for campaign. Must contain a parameter that stores the user's ID in the format of {USER_ID}
cpi | decimal | Amount you will pay per complete. This must be a minimum of $1 per complete and increments up in $0.50 increments


## Show - Show One of Your Campaigns

```shell
curl -i -H "Accept: application/json" 
-H "Content-Type: application/json" -X GET 
https://api.theoremreach.com/api/v1/campaigns/1
```

> The above command returns JSON structured like this:

```json
[
  {
    "id":1,
    "title":"TR Test Campaign via API",
    "start_date":"2016-04-08",
    "end_date":"2016-04-13",
    "status":"In Progress",
    "loi":10,
    "incidence":90,
    "country_id":9,
    "survey_url":"http://clientsurvey.com/survey/12345?id={USER_ID}",
    "test_survey_url":"",
    "cpi":"3.0"
  }
]
```


This end point allows you to retrieve information about a specific campaign that you've created.

**HTTP REQUEST**

`GET https://api.theoremreach.com/api/v1/campaigns/:id`

**REQUIRED PARAMETERS**

`None`

**RESPONSE**

Parameter | Type | Description
--------- | ---- | -----------
id | integer | Unique identifier used when making additional feasibility and campaign creation calls
title | string | Name of the country/language combination
start_date | date | When should the survey start
end_date | date | When should the survey end
status | string | 'Completed', 'In Progress', 'Paused', 'Draft' (Draft is default and cannot be set)
loi | integer | Length of interview (minutes)
incidence | integer | The percentage chance that a random respondent will qualify and complete the survey. Must be a number between 1 and 100
country_id | integer | Country id of the campaign
survey_url | string | Production url for campaign. Must contain a parameter that stores the user's ID in the format of {USER_ID}
test_survey_url | string | Test url for campaign. Must contain a parameter that stores the user's ID in the format of {USER_ID}
cpi | decimal | Amount you will pay per complete. This must be a minimum of $1 per complete and increments up in $0.50 increments




## Create - Create a New Campaign

```shell
curl -H "Content-Type: application/json" --data 
"{\"access_token\":\"testkey\",
\"campaign\":{\"title\":\"Fish\",\"cpi\":5.0,\"loi\":10,
\"incidence\":100,\"survey_url\":\"http://\",
\"start_date\":\"2016-04-17T11:22:34.961-05:00\",
\"end_date\":\"2016-04-19T11:22:34.961-05:00\",
\"country_id\":9}}" 
https://api.theoremreach.com/api/v1/campaigns
```

> The above command returns JSON structured like this:

```json
[
  {
    "id":1,
    "title":"TR Test Campaign via API",
    "start_date":"2016-04-08",
    "end_date":"2016-04-13",
    "status":"In Progress",
    "loi":10,
    "incidence":90,
    "country_id":9,
    "survey_url":"http://clientsurvey.com/survey/12345?id={USER_ID}",
    "test_survey_url":"",
    "cpi":"3.0"
  }
]
```


This end point allows you to create a new campaign.  It will default to a status of Draft until atleast one quota is added and then the campaign is set to In Progress

**HTTP REQUEST**

`POST https://api.theoremreach.com/api/v1/campaigns`

**REQUIRED PARAMETERS**

Parameter | Type | Description
--------- | ---- | -----------
title | string | Name of the country/language combination
start_date | date | When should the survey start
end_date | date | When should the survey end
loi | integer | Length of interview (minutes)
incidence | integer | The percentage chance that a random respondent will qualify and complete the survey. Must be a number between 1 and 100
country_id | integer | Country id of the campaign
survey_url | string | Production url for campaign. Must contain a parameter that stores the user's ID in the format of {USER_ID}
cpi | decimal | Amount you will pay per complete. This must be a minimum of $1 per complete and increments up in $0.50 increments

**OPTIONAL PARAMETERS**

Parameter | Type | Description
--------- | ---- | -----------
test_survey_url | string | Test url for campaign. Must contain a parameter that stores the user's ID in the format of {USER_ID}

**RESPONSE**

Parameter | Type | Description
--------- | ---- | -----------
id | integer | Unique identifier used when making additional feasibility and campaign creation calls
title | string | Name of the country/language combination
start_date | date | When should the survey start
end_date | date | When should the survey end
status | string | 'Completed', 'In Progress', 'Paused', 'Draft' (Draft is default and cannot be set)
loi | integer | Length of interview (minutes)
incidence | decimal | The percentage chance that a random respondent will qualify and complete the survey
country_id | integer | Country of the campaign
survey_url | string | Production url for campaign. Must contain a parameter that stores the user's ID in the format of {USER_ID}
test_survey_url | string | Test url for campaign. Must contain a parameter that stores the user's ID in the format of {USER_ID}
cpi | decimal | Amount you will pay per complete. This must be a minimum of $1 per complete and increments up in $0.50 increments


## Update - Update an Existing Campaign

```shell
curl -H "Content-Type: application/json" --data 
"{\"access_token\":\"testkey\",
\"campaign\":{\"title\":\"New Fun Campaign\",\"cpi\":5.0,\"loi\":10,
\"incidence\":100,\"survey_url\":\"http://\",
\"start_date\":\"2016-04-17T11:22:34.961-05:00\",
\"end_date\":\"2016-06-19T11:22:34.961-05:00\",
\"cosuntapi.theoremreach.comttp://localhost:3000/api/v1/campaigns
```

> The above command returns JSON structured like this:

```json
[
  {
    "id":1,
    "title":"New Fun Campaign",
    "start_date":"2016-04-17",
    "end_date":"2016-06-19",
    "status":"Draft",
    "loi":10,
    "incidence":100,
    "country_id":9,
    "survey_url":"http://clientsurvey.com/survey/12345?id={USER_ID}",
    "test_survey_url":"",
    "cpi":"4.0"
  }
]
```


This end point allows you to update an existing campaign.

**HTTP REQUEST**

`PUT https://api.theoremreach.com/api/v1/campaigns/1`

**REQUIRED PARAMETERS**

`None`

**OPTIONAL PARAMETERS**

Parameter | Type | Description
--------- | ---- | -----------
title | string | Name of the country/language combination
start_date | date | When should the survey start
end_date | date | When should the survey end
status | string | 'Completed', 'In Progress', 'Paused', 'Draft' (Draft is default and cannot be set)
loi | integer | Length of interview (minutes)
incidence | decimal | The percentage chance that a random respondent will qualify and complete the survey
country_id | integer | Country of the campaign
survey_url | string | Production url for campaign. Must contain a parameter that stores the user's ID in the format of {USER_ID}
test_survey_url | string | Test url for campaign. Must contain a parameter that stores the user's ID in the format of {USER_ID}
cpi | decimal | Amount you will pay per complete. This must be a minimum of $1 per complete and increments up in $0.50 increments


**RESPONSE**

Parameter | Type | Description
--------- | ---- | -----------
id | integer | Unique identifier used when making additional feasibility and campaign creation calls
title | string | Name of the country/language combination
start_date | date | When should the survey start
end_date | date | When should the survey end
status | string | 'Completed', 'In Progress', 'Paused', 'Draft' (Draft is default and cannot be set)
loi | integer | Length of interview (minutes)
incidence | decimal | The percentage chance that a random respondent will qualify and complete the survey
country_id | integer | Country of the campaign
survey_url | string | Production url for campaign. Must contain a parameter that stores the user's ID in the format of {USER_ID}
test_survey_url | string | Test url for campaign. Must contain a parameter that stores the user's ID in the format of {USER_ID}
cpi | decimal | Amount you will pay per complete. This must be a minimum of $1 per complete and increments up in $0.50 increments




# Quotas

Quotas are the access control for your campaigns.  Think of them as both the gate keeper and also the ticket counter when entering a concert.  

TheoremReach has simplified the quota process making it easier to understand and construct.  Essentially you build all your requirements of a quota into a single quota and then set the total_completes you desire.  Quotas are always cumulative.

`Example:`

* 250 - Males Age 18-24
* 250 - Females Age 18-24

In TheoremReach you would create the above scenario with just two quotas adding the appropriate trait questions and trait ids for those requirements.  Once the quota has been filled.  It will be deactivated and no longer allow respondents to even see it as available.  You also do not need to update the campaign with a total quota amount.

In the above example, you will receive 500 respondents for the survey. 250 Males aged 18-24 and 250 Females aged 18-24.


## Index - All Quotas

```shell
curl -i -H "Accept: application/json" 
-H "Content-Type: application/json" -X GET 
https://api.theoremreach.com/api/v1/campaigns/1/quotas
```

> The above command returns JSON structured like this for a quota of males aged 18-24:

```json

[
  {
    "id":1,
    "title":"Age 18-24",
    "remaining_completes":13,
    "total_completes":15,
    "total_incompletes":0,
    "state":"active",
    "trait_questions":
      {
        "1":[18,19,20,21,22,23,24]
      }
  }
]
```


This end point will allow you to fetch all of the quotas for a campaign.

**HTTP REQUEST**

`GET https://api.theoremreach.com/api/v1/campaigns/:campaign_id/quotas`

**REQUIRED PARAMETERS**

`None`

**RESPONSE**

Parameter | Type | Description
--------- | ---- | -----------
id | integer | Unique identifier for the quota
title | string | Name of the quota
remaining_completes | integer | How many completes are yet to be delivered
total_completes | integer | Total number of completes you wish for this quota
total_incompletes | integer | Total number of respondents have attempted and failed this quota
state | string | active or inactive
trait_questions | hash | Subset hash of trait questions and array of trait ids (or) values which make up this quota

## Show - Specific Quota

```shell
curl -i -H "Accept: application/json" 
-H "Content-Type: application/json" -X GET 
https://api.theoremreach.com/api/v1/quotas/1
```

> The above command returns JSON structured like this:

```json
[
  {
    "id":1,
    "title":"Age 18-24",
    "remaining_completes":13,
    "total_completes":15,
    "total_incompletes":0,
    "state":"active",
    "trait_questions":
      {
        "1":[18,19,20,21,22,23,24]
      }
  }
]
```


Fetch the details about a quota

**HTTP REQUEST**

`GET https://api.theoremreach.com/api/v1/quotas/:id`

**REQUIRED PARAMETERS**

`None`

**RESPONSE**

Parameter | Type | Description
--------- | ---- | -----------
id | integer | Unique identifier for the quota
title | string | Name of the quota
remaining_completes | integer | How many completes are yet to be delivered
total_completes | integer | Total number of completes you wish for this quota
total_incompletes | integer | Total number of respondents have attempted and failed this quota
state | string | active or inactive
trait_questions | hash | Subset hash of trait questions and array of trait ids (or) values which make up this quota

## Create - New Quota

```shell
curl -H "Content-Type: application/json" --data 
"{\"access_token\":\"testkey\",
\"quota\":{\"title\":\"Age 18-24\",\"total_completes\":100,
\"trait_questions\":{\"1\":[18,19,20,21,22,23,24]}}}"  
https://api.theoremreach.com/api/v1/campaigns/1/quotas
```

> The above command returns JSON structured like this to receive 100 respondents aged 18-24:

```json
[
  {
    "id":1,
    "title":"Age 18-24",
    "remaining_completes":100,
    "total_completes":100,
    "total_incompletes":0,
    "state":"active",
    "trait_questions":
      {
        "1":[18,19,20,21,22,23,24]
      }
  }
]
```


Create a new quota for a campaign

**HTTP REQUEST**

`POST https://api.theoremreach.com/api/v1/campaigns/:campaign_id/quotas`

**REQUIRED PARAMETERS**

Parameter | Type | Description
--------- | ---- | -----------
id | integer | Unique identifier for the quota
title | string | Name of the quota
total_completes | integer | Total number of completes you wish for this quota
trait_questions | hash | Subset hash of trait questions and array of trait ids (or) values which make up this quota

**RESPONSE**

Parameter | Type | Description
--------- | ---- | -----------
id | integer | Unique identifier for the quota
title | string | Name of the quota
remaining_completes | integer | How many completes are yet to be delivered
total_completes | integer | Total number of completes you wish for this quota
total_incompletes | integer | Total number of respondents have attempted and failed this quota
state | string | active or inactive
trait_questions | hash | Subset hash of trait questions and array of trait ids (or) values which make up this quota



## Update - Existing Quota

```shell
curl -H "Content-Type: application/json" -X PUT 
--data "{\"access_token\":\"testkey\",
\"quota\":{\"title\":\"Age 18-24\",\"total_completes\":100,
\"trait_questions\":{\"1\":[18,19,20,21,22,23,24]}}}"
https://api.theoremreach.com/api/v1/quotas/1
```

> The above command returns JSON structured like this:

```json
[
  {
    "id":1,
    "title":"Age 18-24",
    "remaining_completes":100,
    "total_completes":100,
    "total_incompletes":0,
    "state":"active",
    "trait_questions":
      {
        "1":[18,19,20,21,22,23,24]
      }
  }
]
```


Update a quota for a campaign

**HTTP REQUEST**

`PUT https://api.theoremreach.com/api/v1/quotas/:id`

**REQUIRED PARAMETERS**

`None`

**OPTIONAL PARAMETERS**

Parameter | Type | Description
--------- | ---- | -----------
id | integer | Unique identifier for the quota
title | string | Name of the quota
total_completes | integer | Total number of completes you wish for this quota
trait_questions | hash | Subset hash of trait questions and array of trait ids (or) values which make up this quota

**RESPONSE**

Parameter | Type | Description
--------- | ---- | -----------
id | integer | Unique identifier for the quota
title | string | Name of the quota
remaining_completes | integer | How many completes are yet to be delivered
total_completes | integer | Total number of completes you wish for this quota
total_incompletes | integer | Total number of respondents have attempted and failed this quota
state | string | active or inactive
trait_questions | hash | Subset hash of trait questions and array of trait ids (or) values which make up this quota




