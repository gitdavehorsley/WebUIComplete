# ECS Service Split Changes

## Overview
The CloudFormation template has been modified to split the single ECS service into two separate services for better troubleshooting and independent scaling.

## Changes Made

### 1. Task Definitions Split
- **WebUITaskDefinition**: Dedicated task definition for Open WebUI
  - 1 vCPU, 4GB RAM
  - EFS volume mounted at `/webui` for data persistence
  - Environment variables configured for Bedrock Gateway communication

- **GatewayTaskDefinition**: Dedicated task definition for Bedrock Gateway
  - 0.5 vCPU, 1GB RAM
  - No persistent storage needed
  - Minimal resource footprint

### 2. ECS Services Split
- **WebUIService**: Separate service for Open WebUI
  - Loads WebUITaskDefinition
  - Connected to ALBTargetGroupWebUI
  - Independent scaling and health checks

- **GatewayService**: Separate service for Bedrock Gateway
  - Loads GatewayTaskDefinition
  - Connected to ALBTargetGroupGateway
  - Independent scaling and health checks

### 3. Auto Scaling Configuration
- **WebUIScalingTarget/Policy**: Dedicated scaling for WebUI service
  - Min: 1, Max: 5 instances
  - CPU-based scaling at 70% utilization

- **GatewayScalingTarget/Policy**: Dedicated scaling for Gateway service
  - Min: 1, Max: 3 instances
  - CPU-based scaling at 70% utilization

### 4. Communication Between Services
- WebUI communicates with Gateway via the internal ALB
- API calls go through: `http://{ALB_DNS}/api/v1`
- This ensures proper load balancing and health checking

### 5. Updated Outputs
- Separate service name outputs for each service
- Maintains backward compatibility with existing exports

## Benefits of This Split

1. **Independent Troubleshooting**: Each service can be debugged separately
2. **Independent Scaling**: Services can scale based on their own resource needs
3. **Better Resource Allocation**: Each service gets appropriate CPU/memory
4. **Easier Monitoring**: Separate CloudWatch metrics and logs
5. **Fault Isolation**: Issues in one service don't affect the other

## Deployment Notes

- Both services will be deployed in the same ECS cluster
- They share the same security groups and subnets
- The ALB routes traffic appropriately based on path patterns
- EFS is only used by the WebUI service for data persistence

## Troubleshooting Commands

```bash
# Check WebUI service status
aws ecs describe-services --cluster webui-bedrock-cluster --services webui-bedrock-webui-service

# Check Gateway service status  
aws ecs describe-services --cluster webui-bedrock-cluster --services webui-bedrock-gateway-service

# View WebUI logs
aws logs tail /ecs/webui-bedrock --follow --prefix webui

# View Gateway logs
aws logs tail /ecs/webui-bedrock --follow --prefix gateway
``` 