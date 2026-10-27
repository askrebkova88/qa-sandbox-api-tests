# QA Sandbox API Tests

Automated REST API testing project using **Postman** and JavaScript.

## Overview

This project contains a Postman collection for testing the QA Sandbox REST API.

The collection covers authentication and CRUD operations for mythology characters, including positive and negative test scenarios, response validation, data persistence checks, and test data cleanup.

**API Base URL:** `https://api.qasandbox.org/api`

## Tech Stack

- Postman
- JavaScript
- Postman Collection Runner
- REST API
- JSON
- Chai Assertions (Postman `pm.expect`)

## Test Coverage

### Authentication

Authentication test requests are organized in the `01-Authentication` folder and included in the full collection execution.

### Mythology API

The `02-Mythology` folder covers CRUD operations on mythology characters.

| Method | Endpoint | Test Coverage |
|---|---|---|
| GET | `/mythology` | Retrieve all characters, validate response structure and field types |
| GET | `/mythology/:id` | Retrieve character by ID, invalid ID format, nonexistent character |
| POST | `/mythology` | Create character, missing required fields, unknown category, missing token, null and omitted image |
| PUT | `/mythology/:id` | Full update, persistence verification, missing required fields, protected ID, nonexistent ID, missing token |
| PATCH | `/mythology/:id` | Partial update, multiple fields, persistence verification, empty body, protected ID, nonexistent ID, invalid ID format, missing token |
| DELETE | `/mythology/:id` | Successful deletion, empty response body, deletion verification, protected ID, nonexistent ID, missing token |

## Test Scenarios

### Positive Testing

- Verify successful API responses and expected HTTP status codes.
- Validate JSON response structure and required fields.
- Verify response field types and values.
- Create new mythology characters.
- Update existing characters using PUT.
- Partially update characters using PATCH.
- Verify that updated data persists using subsequent GET requests.
- Delete created characters.
- Verify deletion by requesting deleted characters.

### Negative Testing

- Missing required fields (`name`, `category`).
- Requests without authentication tokens.
- Invalid character ID formats.
- Requests with nonexistent character IDs.
- Attempts to modify or delete protected characters.
- PATCH requests with empty bodies.

### Optional Fields and Edge Cases

Additional scenarios verify the behavior of the optional `img` field.

- Creating a character with `"img": null`.
- Creating a character without the `img` property.
- Verifying that the API returns `img: null` in both scenarios.
- Verifying that `img: null` persists after creation using GET requests.

Testing also confirmed that the API accepts unknown category values when creating characters.

## Postman Collection Structure

```text
QA Sandbox API Tests
├── 01-Authentication
└── 02-Mythology
    ├── GET-Read
    ├── POST-Create
    ├── PUT-Update
    ├── PATCH-Update
    └── DELETE-Remove
```

## Test Automation

Tests are implemented using JavaScript in Postman's post-response scripts.

Assertions cover:

- HTTP status codes
- JSON response format
- Response object structure
- Required fields
- Data types
- Response values
- Data persistence
- Error responses
- Empty response bodies

Example:

```javascript
pm.test("Status code is 201", function () {
    pm.response.to.have.status(201);
});

pm.test("Response body is JSON", function () {
    pm.response.to.be.json;
});

const response = pm.response.json();

pm.test("Created character ID is valid", function () {
    pm.expect(response.id).to.be.a("number");
    pm.expect(response.id).to.be.above(0);
    pm.expect(Number.isInteger(response.id)).to.be.true;
});
```

## Dynamic Test Data

The collection uses Postman environment variables to store IDs of newly created characters.

These IDs are reused in subsequent GET, PUT, PATCH, and DELETE requests.

Example:

```javascript
pm.environment.set("createdCharacterId", response.id);
```

This allows dependent requests to work with dynamically created test data rather than hardcoded IDs.

## Test Data Cleanup

Created test characters are deleted after the relevant test scenarios.

DELETE responses are validated using:

- HTTP status `204 No Content`
- Empty response body

Environment variables containing temporary character IDs can be removed after successful cleanup.

```javascript
pm.environment.unset("createdCharacterId");
```

## How to Run the Tests

1. Import the Postman collection into Postman.
2. Configure the `QA Sandbox - Dev` environment.
3. Set the API base URL and required authentication variables.
4. Select the environment.
5. Open the collection in Postman Collection Runner.
6. Run the entire collection, including `01-Authentication` and `02-Mythology`.
7. Review test results, passed and failed assertions, and response details.

For dependent test scenarios, preserve the request execution order.

## Test Execution Results

The full collection, including authentication and mythology API tests, was executed successfully using Postman Collection Runner.

**Latest recorded execution:**

| Metric | Result |
|---|---|
| Total assertions | 193 |
| Passed | 193 |
| Failed | 0 |
| Errors | 0 |
| Skipped | 0 |
| Iterations | 1 |
| Execution duration | 4.109 s |
| Average response time | 62 ms |
| Environment | QA Sandbox - Dev |

**Result: 100% of executed assertions passed.**

## Key Findings

**Optional image field**

The API successfully creates characters when the `img` field is explicitly set to `null` or omitted from the request. In both cases, the returned `img` value is `null`.

**Unknown category**

The API accepts an unknown category value during character creation and returns a successful `201 Created` response.

These behaviors were verified through additional test scenarios and persistence checks.

## Future Improvements

- Run the collection using Newman or Postman CLI.
- Integrate automated execution into a CI/CD pipeline.
- Generate automated test execution reports.
- Extend boundary-value and invalid-data testing.
- Improve test data isolation and cleanup.

## Project Purpose

This project demonstrates practical REST API testing skills, including test design, automated assertions, positive and negative testing, environment variable management, authentication testing, CRUD workflow validation, and test data lifecycle management.
