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
   - DB_ENDPOINT (if not using a proxy)
   - DB_NAME (optional if your secret includes it)
   - DATABASE_CREDENTIALS_SECRET_ARN (ARN of the DB secret created earlier)
   - EFS_FILESYSTEM_ID (required unless using EBS)
   - EFS_ACCESS_POINT_ID (optional)
   - ALB_LISTENER_ARN
   - REPO_CREDENTIALS_SECRET_ARN (optional)
   - DATABASE_PROXY_ENDPOINT (optional)
   - EBS_VOLUME_ID (optional) — when set the stack will mount the provided EBS volume instead of EFS.

3. Notes about selecting EFS vs EBS
   - Default (recommended): EFS — multi-AZ, POSIX, shared access; required for multi-node Jira / Data Center setups.
   - Optional: EBS — single-attach, AZ-scoped, higher single-client IOPS and lower latency for a single task. If you supply EBS_VOLUME_ID:
     - The CFN template will mount the EBS volume into the Fargate task and force DesiredCount to 1.
     - You must ensure PRIVATE_SUBNETS contains subnets in the same AZ as the EBS volume (CFN does not automatically relocate subnets).
     - EBS-based deployments are not suitable for multi-replica or multi-AZ active nodes; plan snapshot and failover processes.
     - Validate the CloudFormation template in your target region and run functional tests before production use.

4. Run the GitLab pipeline
   - Commit and push this repo to GitLab. The pipeline will run and deploy the CloudFormation stack using the provided parameters.

5. Validation
   - Run: aws cloudformation validate-template --template-body file://cloudformation/jira-fargate.yml
   - Deploy to a staging environment, test reads/writes to JIRA_HOME, verify that secrets are injected correctly, and validate DB connectivity.