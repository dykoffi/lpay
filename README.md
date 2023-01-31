# lpay documentation for begining

### Prerequises

- [Nodejs](https://nodejs.org/) >= 14.x
- [Git](https://git-scm.com/) for code versioning
- [gh](https://cli.github.com/) (optional) for github management
- [circleci](https://circleci.com/docs/local-cli/) (optional) for ci/cd pipelines
- [Docker](https://docs.docker.com/get-docker/)
- [Docker-compose](https://docs.docker.com/compose/)

### Databases supported

- [PostreSQL](https://www.prisma.io/docs/concepts/database-connectors/postgresql)
- [Microsoft SQL Server](https://www.prisma.io/docs/concepts/database-connectors/sql-server)
- [Mysql](https://www.prisma.io/docs/concepts/database-connectors/mysql)
- [Sqlite](https://www.prisma.io/docs/concepts/database-connectors/sqlite)
- [MongoDB](https://www.mongodb.com/)

### Documentation

- [Prisma](https://www.prisma.io/docs/reference/api-reference/prisma-schema-reference) For making good schema.
- [Swagger](https://github.com/OAI/OpenAPI-Specification/blob/main/versions/3.0.3.md#schemaObject) For making good api's documentation.
- [cqx](https://www.npmjs.com/package/cqx)
- [Supertest](https://www.npmjs.com/package/supertest) For making Api test.
- [Express Js](https://expressjs.com/fr/4x/api.html)

### Run on local

1. Your dev database connection is define in `.env` file
2. Use ``yarn start:stack`` to run all stack the project needs to run (like database)
3. Define your model in **prisma/schema.prisma**
4. Use `yarn update` to update API and database structure following your model in schema.prisma file
5. Use `yarn start:dev` to run project in dev environment
6. Use `yart test` to run tests
7. Use `yarn lint` to lint the code

### Manage versionning (Only at first launch)

```bash
# Initialize the local repository
git init

# Define the main branch
git branch -M main

## Add files
git add .

# Commit your code
git commit -am "First commit"

# Create git repository 
gh repo create lpay --source . --remote origin --public --push

# Open the repository in the rowser 
gh browse
```

### Manage CI/CI with circleci (Only at first launch)

1. Make sure you have installed [circleci cli](https://circleci.com/docs/local-cli/)
2. Before using the CLI, you need to generate a CircleCI API token from the [Personal API Token tab](https://app.circleci.com/settings/user/tokens)
```bash
# After you get your token, configure the CLI by running: 
circleci setup

# Validate a CircleCI config with 
ciclceci config validate

# Attempt to follow the project for the current git repository
circleci follow

# Open the project in the browser
circleci open
```

### Git pre-commit considerations
 
If you need to validate your commits by checking the results of your tests or the linting of your code, you can use the prepare-commit-msg hook. To do so, you can use the :

1. Rename the file .git/hooks/prepare-commmit-msg.sample to .git/hooks/prepare-commit-msg 

```bash
mv .git/hooks/prepare-commmit-msg.sample .git/hooks/prepare-commit-msg
```

2. Add the following lines to the file .git/hooks/prepare-commit-msg :

```bash
# If the circleci config file is valid and all of the test success
circleci config validate && yarn lint && yarn jest
   ```

### Deploy on gcloud (Cloud run)

```bash
# Build the docker image using 
# (don't forget to change the name of the image as you like)
yarn docker:build

# Create your gcloud docker repo 
gcloud artifacts repositories create lpay --location <region> --repository-format docker

# Get permissions to push image on it using 
gcloud auth configure-docker <region>-docker.pkg.dev

# Push your docker image by using 
docker push <region>-docker.pkg.dev/<project-id>/lpay/api

# Deploy your app on cloud run
gcloud run deploy lpay \
   --image=<region>-docker.pkg.dev/<project-id>/lpay/api \
   --port=80 \
   --min-instances=0 \
   --max-instances=100 \
   --cpu=1 \
   --memory=1024Mi \
   --region=<region> \
   --labels=app=lpay,env=staging \
   --set-env-vars=""
```
