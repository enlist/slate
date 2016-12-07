---
title: enlist's API Documentation

language_tabs:
  - JSON

includes:
  - errors

search: true
---

# Introduction

Welcome to enlist's boards API! It will help you create your own careers site
that's integrated with enlist. You can use this API to get a list of all your
active jobs, the details of an active job, or create a new application for an
active job.

It's a RESTful API that serves and accepts JSON. You should make every request
over SSL.

In case you have any questions that this documentation does not answer, please
send us an e-mail at [support@enlist.io](mailto:support@enlist.io).

# Jobs
## Get all jobs

> The response will look something like this:

```ruby

{
  jobs: [
    {
      id: "36f98718-bc77-11e6-a4a6-cec0c932ce01",
      title: "Software Engineer",
      description: "...",
      remote: false,
      account_id: "51dd77ce-bc77-11e6-a4a6-cec0c932ce01",
      category: "Development",
      job_type: "Full time",
      requirements: [...],
      location_id: "6cee5d3a-bc77-11e6-a4a6-cec0c932ce01"
    }
  ],

  locations: [
    {
      id: "6cee5d3a-bc77-11e6-a4a6-cec0c932ce01",
      area: "Avenue 42",
      city: "Paris",
      state: "Grand Est",
      country: "France"
    }
  ]
}
```

Lists all active jobs in your account.


### Request

`GET https://jobs.enlist.io/{YOUR-USERNAME}/jobs.json`

### Response

Here's what you will get in response:

Object    | Description
--------- | -----------
jobs      | An array of all active jobs
locations | An array of locations associated with those jobs

## Get a single job

> The response will look something like this:

```ruby

{
  job: {
    id: "36f98718-bc77-11e6-a4a6-cec0c932ce01",
    title: "Software Engineer",
    description: "...",
    remote: false,
    account_id: "51dd77ce-bc77-11e6-a4a6-cec0c932ce01",
    category: "Development",
    job_type: "Full time",
    requirements: [...],
    location_id: "6cee5d3a-bc77-11e6-a4a6-cec0c932ce01"
  },

  locations: [
    {
      id: "6cee5d3a-bc77-11e6-a4a6-cec0c932ce01",
      area: "Avenue 42",
      city: "Paris",
      state: "Grand Est",
      country: "France"
    }
  ],

  forms: [
    {
      id: "8d09f771-2e60-4ef4-be0f-c86c0a245a2e",
      questions: [
        {
          id: "694d02f7-9144-4d6d-b773-b1f00b253946",
          field: "file",
          name: "resume",
          label: "Resume",
          description: "...",
          required: true,
          metadata: {}
        },
        {
          id: "694d02f7-9144-4d6d-b773-b1f00b253946",
          field: "text",
          name: "first_name",
          label: "First Name",
          description: "...",
          required: true,
          metadata: {}
        },
        {
          id: "72775cae-62ec-4617-8c9d-7c390416c281",
          field: "text",
          name: "last_name",
          label: "Last Name",
          description: "...",
          required: true,
          metadata: {}
        },
        {
          id: "b848b070-d71c-480f-8875-e83f28e17ac7",
          field: "textarea",
          name: "why_are_you_interested",
          label: "Why are you interested in joining us?",
          description: "Be as detailed as you can get.",
          required: true,
          metadata: {}
        },
        {
          id: "c925973d-2879-46af-b67f-82d16c0e6530",
          field: "dropdown",
          name: "will_you_relocate",
          label: "Are you willing to relocate?",
          description: "...",
          required: false,
          metadata: {
            dropdown_options: [
              "Yes",
              "No"
            ]
          }
        },
      ]
    }
  ]
}
```

Returns the job you request.

### Request

`GET https://jobs.enlist.io/{username}/jobs/${jobId}.json`

### Response

Here's what you will get in response:

Object    | Description
--------- | -----------
job       | The job you have requested
locations | An array of the locations associated with the job
questions | An array of the questions associated with the job's application form

### Understanding the question object

To create an application form, you need to understand the question object. Here's an explanation
of its fields:

Object      | Description
---------   | -----------
id          | A unique identifier for the question
field       | One of `text`, `email`, `phone`, `textarea`, `dropdown`, `code`, or `file`
label       | The question itself
description | Instructions attached to the question, if any
required    | Boolean indicating if the question is required
metadata    | An object with extra details attached to the question

<aside class="notice">
  In case of a dropdown question, the metadata will have a <code>dropdown_options</code>
  array that contains the options that the candidate can choose from.
</aside>

# Applications
## Submitting an application

Create a new application for a specific job.

### Request

`POST https://jobs.enlist.io/{username}/jobs/${jobId}/apply.json`

### Parameters

Parameter | Required    | Description
--------- | ----------- | -------------
answers   | Yes         | A JSON object with the answers
source    | No          | Source of the application, if any


> ### Understanding the answers object
>
> The answers object is a simple JSON object with the IDs of questions and their respective answers.
> Here's a sample:

```
  {
    "694d02f7-9144-4d6d-b773-b1f00b253946": "John",
    "72775cae-62ec-4617-8c9d-7c390416c281": "Doe",
    "b848b070-d71c-480f-8875-e83f28e17ac7": "john.doe@example.org"
  }
```

> The key is the ID of the question and the value is the candidate's answer to the question.

<aside class="warning">
  You <strong>must</strong> submit answers to all the questions that are required.
</aside>

### Response

On successful submission, it will return 201 Created.

enlist will also figure out the first name, the last name, the e-mail, and all
other attributes that are associated with a candidate. It will then:

- create the candidate, and
- create the answers and then associate them with the candidate

In case there are validation errors, it will return 422 Unprocessable Entity.
