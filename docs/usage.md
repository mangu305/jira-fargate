```markdown
# Usage

1. Create Secrets Manager secrets (manually for security)
   - Database credentials: create a secret and copy its ARN.
     Example:
       aws secretsmanager create-secret --name jira/db-credentials --secret-string '{"username":"jirauser","password":"supersecret"}' --region us-east-1
   - (Optional) Repository credentials for private registry:
       aws secretsmanager create-secret --name jira/registry-credentials --secret-string '{"username":"reguser","password":"regpass"}' --region us-east-1

2. Set GitLab CI/CD variables for your project
   - AWS_ACCESS_KEY_ID
   - AWS_SECRET_ACCESS_KEY
   - AWS_DEFAULT_REGION
   - IRONBANK_IMAGE (required) — full image path and tag e.g. registry1.dso.mil/ironbank/my-org/jira:8.20.0
   - VPC_ID
   - PRIVATE_SUBNETS (comma-separated)
   - TASK_SECURITY_GROUP_IDS (comma-separated)
   - TASK_EXECUTION_ROLE_ARN
   - TASK_ROLE_ARN
   - DB_ENDPOINT
   - DB_NAME (optional if your secret includes it)
   - DATABASE_CREDENTIALS_SECRET_ARN (ARN of the DB secret created earlier)
   - EFS_FILESYSTEM_ID
   - ALB_LISTENER_ARN
   - REPO_CREDENTIALS_SECRET_ARN (optional)

3. Run the GitLab pipeline
   - Commit and push this repo to GitLab. The pipeline will run and deploy the CloudFormation stack using the provided parameters.

Notes
- The CloudFormation template mounts an EFS filesystem; ensure your EFS exists and is accessible from the task subnets/security groups.
- Confirm the task execution role has permission to pull from the registry (if private) and to read secrets from Secrets Manager.
```