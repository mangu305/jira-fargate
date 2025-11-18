```markdown
# Secrets Manager formats

This document describes the Secrets Manager JSON shapes required by the CloudFormation template.

1) Database credentials secret (required)
- Parameter name passed to CloudFormation: DatabaseCredentialsSecretArn
- Secret string (JSON) minimum keys:
  {
    "username": "jirauser",
    "password": "your-db-password"
  }

You can also include host/dbname:
  {
    "username": "jirauser",
    "password": "your-db-password",
    "host": "db.example.local",
    "dbname": "jira"
  }

2) Repository credentials secret (optional)
- Parameter name passed to CloudFormation: RepositoryCredentialsSecretArn
- ECS expects the secret to contain registry credentials (username/password). Provide JSON:
  {
    "username": "registry-username",
    "password": "registry-password"
  }

Creating secrets via AWS CLI (examples)
- Create DB secret:
  aws secretsmanager create-secret --name jira/db-credentials --secret-string '{"username":"jirauser","password":"supersecret"}' --region us-east-1

- Create repo credentials secret:
  aws secretsmanager create-secret --name jira/registry-credentials --secret-string '{"username":"reguser","password":"regpass"}' --region us-east-1

Permissions
- Ensure the Task Execution Role (and CloudFormation deployment role if different) has permission to read the secrets (secretsmanager:GetSecretValue) as required.
```