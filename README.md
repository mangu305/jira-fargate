```markdown
# jira-ecs-fargate-ironbank

Deploy Jira on AWS ECS Fargate using a CloudFormation template and GitLab CI. This repo is configured to accept only Iron Bank images (via the IRONBANK_IMAGE CI variable) and to use AWS Secrets Manager for all secrets.

Quick overview
- .gitlab-ci.yml: Deploy-only GitLab CI pipeline. Requires IRONBANK_IMAGE and DATABASE_CREDENTIALS_SECRET_ARN (for DB creds). Optional REPO_CREDENTIALS_SECRET_ARN if pulling from a private registry.
- cloudformation/jira-fargate.yml: CloudFormation template that creates ECS cluster, Fargate task definition, service, target group, and ALB listener rule. Secrets (DB creds, optional repo creds) must come from AWS Secrets Manager.
- docs/: Documentation for Secrets Manager secret formats and pipeline usage.
- examples/: Example IRONBANK_IMAGE value.

Required GitLab CI/CD variables
- AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY, AWS_DEFAULT_REGION
- IRONBANK_IMAGE (required) - full image path and tag, e.g.: registry1.dso.mil/ironbank/my-org/jira:8.20.0
- VPC_ID, PRIVATE_SUBNETS (comma-separated), TASK_SECURITY_GROUP_IDS (comma-separated)
- TASK_EXECUTION_ROLE_ARN, TASK_ROLE_ARN
- DB_ENDPOINT, DB_NAME (optional if placed in secret), DATABASE_CREDENTIALS_SECRET_ARN (required)
- EFS_FILESYSTEM_ID, ALB_LISTENER_ARN
- REPO_CREDENTIALS_SECRET_ARN (optional, if the Iron Bank registry requires Secrets Manager credentials)

Important notes
- Fargate cannot use EBS; the template mounts Jira home from EFS.
- All credentials (DB username/password and optional registry credentials) must be stored in AWS Secrets Manager and provided as secret ARNs to the CloudFormation deployment (see docs/secrets.md).
- Ensure your task role and execution role have appropriate permissions to access Secrets Manager and EFS.

How to create the repository zip
- Run the included create-zip.sh script to create a zip of the repo directory:
  chmod +x create-zip.sh
  ./create-zip.sh jira-ecs-fargate-ironbank.zip

See docs/usage.md for detailed usage instructions and examples.
```