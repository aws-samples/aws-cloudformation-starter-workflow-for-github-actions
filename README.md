# AWS CloudFormation Starter Workflow for GitHub Actions

This template repository contains a sample application and sample GitHub Actions workflow files for continuously deploying both application code and infrastructure as code with GitHub Actions.

The sample application is a simple containerized web application that uses Amazon ECS on AWS Fargate behind a public Application Load Balancer (ALB).  The application is split up into two CloudFormation stacks:
1. [infrastructure.yml](cloudformation-templates/infrastructure.yml) deploys the infrastructure resources (for example, the ALB and the ECS cluster)
1. [service.yml](cloudformation-templates/service.yml) deploys the application code (for example, the ECS task definition and ECS service)

This repository contains two starter workflow files for GitHub Actions:
1. [check.yml](.github/workflows/check.yml) runs when a pull request is opened or updated.  This workflow validates that the web application Docker image builds successfully with the proposed code changes.
1. [deploy.yml](.github/workflows/deploy.yml) runs when a new commit is pushed to the master branch.  This workflow deploys both the infrastucture CloudFormation stack and the application CloudFormation stack.

## Create a GitHub repository from this template

Click the "Use this template" button above to create a new repository from this template.

Clone your new repository, and deploy the IAM resources needed to enable GitHub Actions to deploy CloudFormation templates:
```
aws cloudformation deploy \
  --stack-name github-actions-cloudformation-deploy-setup \
  --template-file cloudformation-templates/setup.yml \
  --capabilities CAPABILITY_NAMED_IAM \
  --region us-east-2
```
You can review the permissions that your repository's GitHub Actions deployment workflow will have in the [setup.yml](cloudformation-templates/setup.yml) CloudFormation template.

Retrieve the account ID for the AWS account that GitHub Actions will use for deployments:
```
aws sts get-caller-identity \
  --query Account \
  --output text
```

Create a GitHub Actions secret named `AWS_ACCOUNT_ID` containing the account ID in your GitHub repository,
by going to Settings > Secrets.
Alternatively, you can create these GitHub Actions secrets at the GitHub organization level,
and grant access to the secrets to your new repository.

Go to the Actions tab, select the latest workflow run and its failed job, then select "Re-run jobs" > "Re-run all jobs".

When the workflow successfully completes, expand the "Print service URL" step in the "Deploy web application" job to see the URL for the deployed web application.

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.
