# CodeNotary Technical challenge

# Setup
first run ```docker-compose up --build``` to build

then ```docker-compose up``` to run it another time

Visit the frontend at ```localhost:3000/index```
You can query the api at ```localhost:8080```

Supported features:
  - First query local SQLite, then if not found ask the deps API
  - Bar graph of the count of scores
  - Click on any dependency in the list to search among its dependencies
  - A back button to go to the previous project
  - Fast loading time due to asynchronous goroutines

# SQLite schema

project
- id : unique project ID (primary key)
- open_issues_count : open issues count
- stars_count : star count
- forks_count : fork count
- license : project license
- description : project summary
- homepage : project homepage URL
- scorecard_date : scorecard date
- scorecard_repo_name : repo name in scorecard
- scorecard_repo_commit : repo commit hash
- scorecard_version : scorecard version
- scorecard_commit : scorecard tool commit
- scorecard_overall_score : overall security score

scorecard_checks
- project_id : related project ID (foreign key to project.id)
- name : check name
- short_description : check summary
- url : check details URL
- score : check score
- reason : check reasoning
- details : extra details

packages
- id : unique package ID (primary key)
- name : package name
- version : current version
- description : package summary
- license : package license

package_versions
- package_id : related package ID (foreign key to packages.id)
- version : version number (primary key with package_id)
- release_date : release date

dependency_nodes
- id : unique node ID (primary key)
- package_id : related package ID (foreign key to packages.id)
- version : package version

dependency_edges
- source_id : source node ID (foreign key to dependency_nodes.id)
- target_id : target node ID (foreign key to dependency_nodes.id)


# API

API

Supported Endpoints:
* Add or Update Dependency
- POST /dependency/add
- Add or update a dependency for a project.

- Example request:
{
  "project_name": "example-project",
  "dependency": {
    "versionKey": {
      "system": "GO",
      "name": "example-dependency",
      "version": "v1.0.0"
    },
    "bundled": false,
    "relation": "DIRECT",
    "errors": []
  }
}

* Get Specific Dependency
- GET /dependency/get/{projectName}/{dependencyName}
- Retrieve details of a specific dependency.
- Example response:
{
  "versionKey": {
    "system": "GO",
    "name": "example-dependency",
    "version": "v1.0.0"
  },
  "bundled": false,
  "relation": "DIRECT",
  "errors": [],
  "ossf_score": 8.5
}

* Delete Dependency
- DELETE /dependency/delete/{projectName}/{dependencyName}
- Remove a dependency from a project.

* List Dependencies
- GET /dependencies?name=example&min_score=7.0
- List dependencies with optional filters for name and score.
- Example response:
[
  {
    "versionKey": {
      "system": "GO",
      "name": "example-dependency",
      "version": "v1.0.0"
    },
    "bundled": false,
    "relation": "DIRECT",
    "errors": [],
    "ossf_score": 8.5
  }
]

Retrieve Project Dependencies
GET /dependency/{projectName}
Get a project's dependency graph.
Example response:

{
  "project_name": "example-project",
  "dependencies": [
    {
      "id": "dependency-one",
      "score": 8.5
    }
  ]
}