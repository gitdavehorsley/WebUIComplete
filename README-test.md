# CodeBuild Test Template

This is a simplified CloudFormation template for testing CodeBuild functionality without ECR Public dependencies.

## Purpose

This template is designed to:
- Test CodeBuild project creation and configuration
- Verify ECR repository access and image pushing
- Validate IAM roles and permissions
- Test Docker build process in a controlled environment

## What's Included

### Resources Created:
1. **ECR Repository** - `codebuild-test/test-app`
2. **CodeBuild Project** - Simple Docker build and push
3. **IAM Role** - Proper permissions for CodeBuild
4. **S3 Bucket** - For build artifacts
5. **Security Groups** - Basic network access
6. **CloudWatch Logs** - Build logging

### Build Process:
- Creates a simple Alpine-based Docker image
- Pushes to ECR repository
- Generates deployment artifacts
- No external dependencies (no ECR Public)

## Deployment

### Prerequisites:
- Existing VPC with private subnets
- AWS CLI configured
- CloudFormation permissions

### Parameters:
- `ProjectName`: Default `codebuild-test`
- `Environment`: Default `test`
- `VpcId`: Your existing VPC ID
- `PrivateSubnetIds`: List of private subnet IDs
- `AllowedCidrBlocks`: Network access (defaults to private ranges)

### Deploy Command:
```bash
aws cloudformation create-stack \
  --stack-name codebuild-test \
  --template-body file://codebuild-test.template \
  --parameters \
    ParameterKey=VpcId,ParameterValue=vpc-xxxxxxxxx \
    ParameterKey=PrivateSubnetIds,ParameterValue=subnet-xxxxxxxxx,subnet-yyyyyyyyy \
  --capabilities CAPABILITY_NAMED_IAM
```

## Testing the Build

1. **Manual Build Test:**
```bash
aws codebuild start-build --project-name codebuild-test-build
```

2. **Check Build Status:**
```bash
aws codebuild list-builds --project-name codebuild-test-build
```

3. **View Logs:**
```bash
aws logs describe-log-streams --log-group-name /aws/codebuild/codebuild-test
```

## Expected Results

- ✅ CodeBuild project creates successfully
- ✅ ECR repository accessible
- ✅ Docker image builds and pushes
- ✅ No ECR Public dependencies
- ✅ Proper IAM permissions

## Troubleshooting

### Common Issues:
1. **VPC Configuration**: Ensure private subnets have NAT Gateway
2. **IAM Permissions**: Verify CodeBuild role has ECR access
3. **Docker Build**: Check if base image is accessible

### Debug Commands:
```bash
# Check ECR repository
aws ecr describe-repositories --repository-names codebuild-test/test-app

# Verify IAM role
aws iam get-role --role-name codebuild-test-codebuild-role

# Test ECR login
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin
```

## Cleanup

```bash
aws cloudformation delete-stack --stack-name codebuild-test
```

## Next Steps

Once this test template works successfully:
1. Use the working configuration as a base
2. Add your specific application Dockerfile
3. Integrate with your CI/CD pipeline
4. Scale up to production requirements 