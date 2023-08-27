# {{ cookiecutter.project_title}}

> Be aware that we're using Python 3.11.4

## Local Development

First you need to install [Poetry](https://python-poetry.org/docs/#installation) and get the `.env` file from someone. Then follow the steps below:

- `poetry install`
- `poetry run uvicorn main:app --port 8082 --env-file .env --reload`


## Build the docker image

> Change the PORT if you need

- `docker build -t workflow-extract-text . --build-arg PORT=8080 ...the rest of the variables`
or if you want to use a build.env file:
- Create a build.env file on the root (see sample.build.env)
- Run `docker build -t workflow-extract-text $(for i in `cat build.env`; do out+="--build-arg $i " ; done; echo $out;out="") .`

## Run the docker image
- `docker run --env-file .env -p 8080:8080 workflow-extract-text`

## Usage

### Available Scripts

#### Description script 1
- This script performs a search using the value provided in the QUERY parameter

**Command**
`poetry run ./main.py -query QUERY -document_id DOCUMENT_ID -history_messages HISTORY_MESSAGES`

#### Example

`poetry run ./main.py -query "Can you repeat your last answer?" -document_id b5d26b06-defb-46ef-b1bb-9c6ec318a208 -history_messages "[{'content': 'what is the name of this document?', 'role': 'human'}, {'content': 'The name of this document is \"Mutual Nondisclosure Agreement\".', 'role': 'ai'}]"`

#### Description script 2
- This script focuses on working with Amazon S3 URLs. It uses the source URL

**Command**
`poetry run ./main.py -s3_source_url S3_SOURCE_URL -s3_target_url S3_TARGET_URL`

#### Description script 3
- This script focuses on working with Amazon S3 URLs and allows specifying a callback

**Command**
`poetry run ./main.py -s3_source_url S3_SOURCE_URL [-callback "{'url': 'URL/path/to/endpoint', 'method': 'POST'}"]`

## Updating poetry.lock

You need to generate a codeartifact token using AWS environment variables. Assuming you have a build.env file with those variables, you can run the following commands:

```
export $(cat build.env | grep -v ^\#)

export CODEARTIFACT_AUTH_TOKEN=$(aws codeartifact get-authorization-token --domain $AWS_REPO_DOMAIN --domain-owner $AWS_DOMAIN_OWNER --query authorizationToken --output text)

poetry config http-basic.codeartifact-llm aws $CODEARTIFACT_AUTH_TOKEN
```

> You need to execute the commands above only once.

```poetry lock --no-update```

## Unit Testing
For this test we need access to S3, so make sure you have the ".env" file. To run unit tests execute:
`poetry run pytest tests`
