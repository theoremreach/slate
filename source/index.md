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

Don't hesitate to reach out to admin@theoremreach.com with any questions that arise as part of the implementation! We're happy to help!

# Authentication

We use HTTPS combined with an access_key to enforce access controls to secure resources.


```shell
curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X GET http://api-staging.theoremreach.com/api/v1/countries?api_key=30832a87c5bf731cb234fb0f218c1989
```

<aside class="notice">
You must replace <code>testkey</code> with your personal API key. The api_key param must appear in every request.
</aside>

# Integration Guide

## Mapping

The first step of integration is to load and map our data to your system. This will ensure your system is able to properly communicate with our API.  

You must map countries, trait questions and traits before creating any campaigns.

## Campaign Creation

After understanding and creating mappings for the above tables you can start to create a campaign.  When you first create a campaign it will be set to a status of 'Draft'.  Specify the CPI, LOI, Name, and some of the other required pieces of information.  Specifying start and end dates will tell a campaign when to turn on and off.

## Quota Creation

Once the base campaign is created you need to assign your quotas for respondents.  A quota is a stand-alone bucket for which you want to fill a certain demographic.  Each quota works independantly of any other quota.  The campaign will inherit a total quota additive of all active quotas, which you dont need to worry about through the api or manage.  

Once a quota is filled it will automatically inactivate.  If all the quotas are filled it will automatically change the campaign to "Completed".

To change a quota to inactive via the API just the total_completes value to 0.  If you want to open a quota back up and have already filled it, set the total_completes value to a number higher than the remaining_completes count.  If the campaign is Completed you will need to re-enable the campaign. (Set to "In Progress")

## Launch the Campaign

With a valid campaign set up and at least one active quota you can launch the campaign.  Simply set the status to "In Progress".

# Countries

### INDEX - List Available Countries

```shell
curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X GET http://api-staging.theoremreach.com/api/v1/countries?api_key=30832a87c5bf731cb234fb0f218c1989
```

> Example RESPONSE JSON:

```json
[
  {
    "id":9,
    "name":"English - United States"
  }
]
```


This end point allows you to retrieve all active country and language combinations currently supported by TheoremReach.  Only actively support countries will be returned.  The ID must be used when creating campaigns and checking feasibility. Please note the beta version of our API only supports our US based sample. Please contact admin@theoremreach.com if you are interested in running international sample requests.

**HTTP REQUEST**

`GET https://api.theoremreach.com/api/v1/countries?api_key=testkey`

**REQUIRED PARAMETERS**

`None`

**RESPONSE**

Parameter | Type | Description
--------- | ---- | -----------
id | integer | Unique identifier used when making additional feasibility and campaign creation calls
name | string | Name of the country/language combination


# Trait Questions

```shell
curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X GET http://api-staging.theoremreach.com/api/v1/countries/9/trait_questions?api_key=30832a87c5bf731cb234fb0f218c1989
```

> Example RESPONSE JSON:

```json
[
  {
    "id":1,
    "question_text":"What is your age?",
    "question_type":"numeric",
    "trait_question_type":"age",
    "country_id":9,
    "can_check_feasibility": true
  },
  {
    "id":2,
    "question_text":"What is your zip code?",
    "question_type":"numeric",
    "trait_question_type":"zip",
    "country_id":9,
    "can_check_feasibility": false
  }
]
```

Trait questions allow you to check feasibility and build quotas around a respondents profile.  An example trait question may be "What is your age?".  

### INDEX - List Available Trait Questions

This end point allows you to retrieve all active trait questions within a given country.

**HTTP REQUEST**

`GET https://api.theoremreach.com/api/v1/countries/:country_id/trait_questions?api_key=testkey`

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
can_check_feasibility | boolean | Can this trait question be used in the check feasibility call?

# Traits

```shell
curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X GET http://api-staging.theoremreach.com/api/v1/trait_questions/6/traits?api_key=30832a87c5bf731cb234fb0f218c1989
```

> Example RESPONSE JSON:

```json
[
  {
    "id":42199,
    "value_text":"Female"
  },
  {
    "id":42198,
    "value_text":"Male"
  }
]
```

### INDEX - List Available Traits for a Question

This end point allows you to retrieve all trait questions within a given country.

**HTTP REQUEST**

`GET https://api.theoremreach.com/api/v1/trait_questions/:id/traits?api_key=testkey`


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
curl -H "Content-Type: application/json" --data "{\"feasibility\":{\"trait_questions\":{\"1\":[18,10,20,21,22,23,24]}, \"country_id\":9,\"price\":3.0,\"loi\":6,\"incidence\":100}}" http://api-staging.theoremreach.com/api/v1/feasibility?api_key=30832a87c5bf731cb234fb0f218c1989
```

> Example REQUEST JSON:

```json
{
  "feasibility":
    {
      "trait_questions":
        {
          "1":[18,10,20,21,22,23,24]
        }, 
        "country_id":9,
        "price":3.0,
        "loi":6,
        "incidence":100
    }
}
```

> Example RESPONSE JSON:

```json
[
  {
    "trait_questions":{"1":[18,10,20,21,22,23,24]},
    "completes_per_day":1220
  }
]
```

This end point allows you to check the number of completes per day you should receive with a given set of trait questions.  Keep in mind that we only allow feasibility checking for this list of trait_questions, currently.

> Available Trait Questions for Feasibility
```json
  [
    {"id":1, "question":"What is your age?"}, 
    {"id":3, "question":"Are you using a mobile device?"}, 
    {"id":4, "question":"Are you using a mobile device?"}, 
    {"id":6, "question":"What is your gender?"}, 
    {"id":7, "question":"Are you of Hispanic, Latino, or Spanish origin?"}, 
    {"id":8, "question":"What is your race?"}, 
    {"id":10, "question":"What is the highest level of education you have completed?"}, 
    {"id":73, "question":"What is your current employment status?"}, 
    {"id":76, "question":"Please choose which departments/products you have influence or decision making authority on spending/purchasing?"}, 
    {"id":83, "question":"How much total combined income do all members of your household earn before taxes?"}, 
    {"id":101, "question":"What is your state?"}, 
    {"id":102, "question":"What is your DMA?"}, 
    {"id":103, "question":"What is your REGION?"}, 
    {"id":104, "question":"What is your DIVISION?"}
  ]
```

**HTTP REQUEST**

`POST https://api.theoremreach.com/api/v1/feasibility?api_key=testkey`

**REQUIRED PARAMETERS**

Parameter | Type | Description
--------- | ---- | -----------
trait_questions | hash | Subset hash of trait questions ids and array of trait ids (e.g. values which make up this quota). For example, for 18-24 year old males this value would be {"1":[18,19,20,21,22,23,24],"6":[42199]}.
country_id | integer | Country id of the campaign. Must be an active country id.
cpi | decimal | Amount you will pay per complete. This must be a minimum of $1 per complete and increments up in $0.50 increments
incidence | integer | The percentage chance that a random respondent will qualify and complete the survey. Must be a number between 1 and 100
loi | integer | Length of interview (minutes), min 1 max 30

**RESPONSE**

Parameter | Type | Description
--------- | ---- | -----------
trait_questions | hash | Subset hash of trait questions ids and array of trait ids (e.g. values which make up this quota). For example, for 18-24 year old males this value would be {"1":[18,19,20,21,22,23,24],"6":[42199]}.
completes_per_day | integer | Number of completes you can expect per day

# Callbacks

> For a sucessful completion for a respondent with a passed in id of 12345 redirect respondent to:

```shell
https://theoremreach.com/respondent_result?status=10&user_id=12345
```

These are the routes that you redirect a respondent to notify us when to award a user the reward in their app. Note: a respondent must be sent back to one of these routes. Replace the {USER_ID} with the actual ID we pass to you in place of the {USER_ID} in the initial redirect.

Success:
https://theoremreach.com/respondent_result?status=10&user_id={USER_ID}

Overquota:
https://theoremreach.com/respondent_result?status=3&user_id={USER_ID}

Disqualified:
https://theoremreach.com/respondent_result?status=4&user_id={USER_ID}

# Campaigns

## Index - List Your Campaigns

```shell
curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X GET http://api-staging.theoremreach.com/api/v1/campaigns?api_key=30832a87c5bf731cb234fb0f218c1989
```

> Example RESPONSE JSON:

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

`GET https://api.theoremreach.com/api/v1/campaigns?api_key=testkey`

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
curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X GET http://api-staging.theoremreach.com/api/v1/campaigns/57472?api_key=30832a87c5bf731cb234fb0f218c1989
```

> Example RESPONSE JSON:

```json
[
  {
    "id":57472,
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

`GET https://api.theoremreach.com/api/v1/campaigns/:id?api_key=testkey`

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
curl -H "Content-Type: application/json" --data "{\"campaign\":{\"title\":\"TR Test Campaign via API\",\"cpi\":3.0,\"loi\":10, \"incidence\":90,\"survey_url\":\"http://clientsurvey.com/survey/12345?id={USER_ID}\", \"start_date\":\"2016-04-8T11:22:34.961-05:00\", \"end_date\":\"2016-04-13T11:22:34.961-05:00\", \"country_id\":9}}" http://api-staging.theoremreach.com/api/v1/campaigns?api_key=30832a87c5bf731cb234fb0f218c1989
```

> Example REQUEST JSON:

```json
{
   "campaign":
   {
    "title":"TR Test Campaign via API",
    "cpi":3.0,
    "loi":10,
    "incidence":90,
    "survey_url":"http://clientsurvey.com/survey/12345?id={USER_ID}",
    "start_date":"2016-04-8T11:22:34.961-05:00",
    "end_date":"2016-04-13T11:22:34.961-05:00",
    "country_id":9
  }
}
```

> Example RESPONSE JSON:

```json
[
  {
    "id":57472,
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

This end point allows you to create a new campaign.  It will default to a status of Draft until at least one quota is added and then the campaign is set to In Progress. 

**HTTP REQUEST**

`POST https://api.theoremreach.com/api/v1/campaigns?api_key=testkey`

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
curl -H "Content-Type: application/json" --data "{\"campaign\":{\"title\":\"New Fun Campaign\",\"cpi\":4.0,\"loi\":10, \"incidence\":100,\"survey_url\":\"http://clientsurvey.com/survey/12345?id={USER_ID}\", \"start_date\":\"2016-04-17T11:22:34.961-05:00\", \"end_date\":\"2016-06-19T11:22:34.961-05:00\", \"country_id\":9}}" http://api-staging.theoremreach.com/api/v1/campaigns/57472?api_key=30832a87c5bf731cb234fb0f218c1989
```
> Example REQUEST JSON

```json
{
  "campaign":
    {
      "title":"New Fun Campaign",
      "cpi":4.0,
      "loi":10,
      "incidence":100,
      "survey_url":"http://clientsurvey.com/survey/12345?id={USER_ID}",
      "start_date":"2016-04-17T11:22:34.961-05:00",
      "end_date":"2016-06-19T11:22:34.961-05:00",
      "country_id":9
    }
}
```

> Example RESPONSE JSON:

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

Please note - only campaigns with a status of "In Progress" will receive sample. Once a campaign is set to "In Progress" it cannot be set back to "Draft", rather it should be set to "Paused" if you wish to pause receiving sample for that project. 

In order to receive sample you must use this call to set the status to "In Process" and there must be at least one quota that still needs completions.

**HTTP REQUEST**

`PUT https://api.theoremreach.com/api/v1/campaigns/:id?api_key=testkey`

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

TheoremReach has simplified the quota process making it easier to understand and construct.  Essentially you build all your requirements of a quota into a single quota and then set the total_completes you desire for that particular.  Quotas are always cumulative.

`Example:`

* 250 - Males Age 18-24
* 250 - Females Age 18-24

In TheoremReach you would create the above scenario with just two quotas adding the appropriate trait questions and trait ids for those requirements.  Once the quota has been filled.  It will be deactivated and no longer allow respondents to even see it as available.  You also do not need to update the campaign with a total quota amount.

In the above example, you will receive 500 respondents for the survey. 250 Males aged 18-24 and 250 Females aged 18-24.


## Index - All Quotas

```shell
curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X GET http://api-staging.theoremreach.com/api/v1/campaigns/57472/quotas?api_key=30832a87c5bf731cb234fb0f218c1989
```

> Example RESPONSE JSON:

```json

[
  {
    "id":72057,
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

`GET https://api.theoremreach.com/api/v1/campaigns/:campaign_id/quotas?api_key=testkey`

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
trait_questions | hash | Subset hash of trait questions ids and array of trait ids (e.g. values which make up this quota). For example, for 18-24 year old males this value would be {"1":[18,19,20,21,22,23,24],"6":[42199]}.

## Show - Specific Quota

```shell
curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X GET http://api-staging.theoremreach.com/api/v1/quotas/72057?api_key=30832a87c5bf731cb234fb0f218c1989
```

> Example RESPONSE JSON:

```json
[
  {
    "id":72057,
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


Fetch the details about a quota.

**HTTP REQUEST**

`GET https://api.theoremreach.com/api/v1/quotas/:id?api_key=testkey`

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
trait_questions | hash | Subset hash of trait questions ids and array of trait ids (e.g. values which make up this quota). For example, for 18-24 year old males this value would be {"1":[18,19,20,21,22,23,24],"6":[42199]}.

## Create - New Quota

```shell
curl -H "Content-Type: application/json" --data "{\"quota\":{\"title\":\"Age 18-24\",\"total_completes\":100, \"trait_questions\":{\"1\":[18,19,20,21,22,23,24]}}}" http://api-staging.theoremreach.com/api/v1/campaigns/57472/quotas?api_key=30832a87c5bf731cb234fb0f218c1989
```

> Example REQUEST JSON (100 respondents aged 18-24):

```json
  {
    "quota":
      {
        "title":"Age 18-24",
        "total_completes":100,
        "trait_questions":
        {
          "1":[18,19,20,21,22,23,24]
        }
      }
  }
```

> Example RESPONSE JSON:

```json
[
  {
    "id":72057,
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


Create a new quota for a campaign.

**HTTP REQUEST**

`POST https://api.theoremreach.com/api/v1/campaigns/:campaign_id/quotas?api_key=testkey`

**REQUIRED PARAMETERS**

Parameter | Type | Description
--------- | ---- | -----------
id | integer | Unique identifier for the quota
title | string | Name of the quota
total_completes | integer | Total number of completes you wish for this quota
trait_questions | hash | Subset hash of trait questions ids and array of trait ids (e.g. values which make up this quota). For example, for 18-24 year old males this value would be {"1":[18,19,20,21,22,23,24],"6":[42199]}.

**RESPONSE**

Parameter | Type | Description
--------- | ---- | -----------
id | integer | Unique identifier for the quota
title | string | Name of the quota
remaining_completes | integer | How many completes are yet to be delivered
total_completes | integer | Total number of completes you wish for this quota
total_incompletes | integer | Total number of respondents have attempted and failed this quota
state | string | active or inactive
trait_questions | hash | Subset hash of trait questions ids and array of trait ids (e.g. values which make up this quota). For example, for 18-24 year old males this value would be {"1":[18,19,20,21,22,23,24],"6":[42199]}.

## Update - Existing Quota

```shell
curl -H "Content-Type: application/json" -X PUT --data "{\"quota\":{\"title\":\"Age 18-24\",\"total_completes\":100, \"trait_questions\":{\"1\":[18,19,20,21,22,23,24]}}}" http://api-staging.theoremreach.com/api/v1/quotas/72057?api_key=30832a87c5bf731cb234fb0f218c1989
```

> Example REQUEST JSON:

```json
{
  "quota":
    {
      "title":"Age 18-24",
      "total_completes":100,
      "trait_questions":
        {
          "1":[18,19,20,21,22,23,24]
        }
    }
}
```

> Example RESPONSE JSON:

```json
[
  {
    "id":72057,
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


Update a quota for a campaign.

**HTTP REQUEST**

`PUT https://api.theoremreach.com/api/v1/quotas/:id?api_key=testkey`

**REQUIRED PARAMETERS**

`None`

**OPTIONAL PARAMETERS**

Parameter | Type | Description
--------- | ---- | -----------
id | integer | Unique identifier for the quota
title | string | Name of the quota
total_completes | integer | Total number of completes you wish for this quota
trait_questions | hash | Subset hash of trait questions ids and array of trait ids (e.g. values which make up this quota). For example, for 18-24 year old males this value would be {"1":[18,19,20,21,22,23,24],"6":[42199]}.

**RESPONSE**

Parameter | Type | Description
--------- | ---- | -----------
id | integer | Unique identifier for the quota
title | string | Name of the quota
remaining_completes | integer | How many completes are yet to be delivered
total_completes | integer | Total number of completes you wish for this quota
total_incompletes | integer | Total number of respondents have attempted and failed this quota
state | string | active or inactive
trait_questions | hash | Subset hash of trait questions ids and array of trait ids (e.g. values which make up this quota). For example, for 18-24 year old males this value would be {"1":[18,19,20,21,22,23,24],"6":[42199]}.




