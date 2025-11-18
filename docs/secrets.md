# Secrets format

This project expects certain secrets to be stored in AWS Secrets Manager.

1) Database credentials (required)
- Create a Secrets Manager secret that contains at minimum:
  {"username":"jirauser","password":"supersecret"}
- Save the secret ARN and provide it to the pipeline via the CI variable:
  DATABASE_CREDENTIALS_SECRET_ARN

2) (Optional) Repository credentials for private registries (e.g. Iron Bank private repo)
- Create a secret that contains registry auth compatible with ECS repository credentials.
  Example JSON:
    {"username":"registry-user","password":"registry-password"}
- Provide the ARN via REPO_CREDENTIALS_SECRET_ARN in CI.

Notes:
- The CloudFormation template uses Secrets Manager ValueFrom mappings in the TaskDefinition:
  - ATL_DB_USER -> ${DatabaseCredentialsSecretArn}:SecretString:username::
  - ATL_DB_PASSWORD -> ${DatabaseCredentialsSecretArn}:SecretString:password::
- Do not include plaintext passwords in CloudFormation parameters or repository files.