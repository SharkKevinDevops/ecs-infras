✅ ECS FARGATE LAB – TODO LIST (END-TO-END)

🟦 PHASE 0 – Access Lab Environment

☐ Log in to AWS Console
☐ Navigate to Systems Manager → Session Manager
☐ Connect to EC2 Dev Instance
☐ Verify installed tools:

docker --version
aws --version
git --version


🟦 PHASE 1 – Pull Image from Docker Hub (Public)

☐ Pull image:

docker pull kevinndevops/started-app:1.0.0

☐ Check image:

docker images


🟦 PHASE 2 – Create ECR Repository (Student's AWS Account)

☐ Create ECR repository:

aws ecr create-repository \
  --repository-name started-app \
  --region ap-southeast-1

☐ Note down ECR Repository URI


🟦 PHASE 3 – Push Image to ECR

🔹 Login to ECR

☐ Authenticate Docker with ECR:

aws ecr get-login-password --region ap-southeast-1 \
| docker login \
--username AWS \
--password-stdin <ACCOUNT_ID>.dkr.ecr.ap-southeast-1.amazonaws.com

🔹 Retag Image

☐ Retag image:

docker tag kevinndevops/started-app:1.0.0 \
<ACCOUNT_ID>.dkr.ecr.ap-southeast-1.amazonaws.com/started-app:1.0.0

🔹 Push Image

☐ Push image to ECR:

docker push <ACCOUNT_ID>.dkr.ecr.ap-southeast-1.amazonaws.com/started-app:1.0.0


🟦 PHASE 4 – Create ECS Cluster (Fargate)

☐ Go to ECS → Clusters
☐ Click Create Cluster
☐ Cluster type: Networking only (Fargate)
☐ Cluster name: ecs-lab-cluster


🟦 PHASE 5 – Create Task Definition

☐ Create Task Definition
☐ Launch type: Fargate
☐ OS: Linux
☐ CPU: 0.25 vCPU
☐ Memory: 0.5 GB
☐ Container configuration:
  ☐ Image: ECR Image URI
  ☐ Container port: 3000
  ☐ Log driver: awslogs


🟦 PHASE 6 – Create ECS Service + ALB

☐ Create Service
☐ Launch type: Fargate
☐ Desired tasks: 1
☐ Load Balancer configuration:
  ☐ Application Load Balancer
  ☐ Internet-facing
  ☐ Listener: HTTP :80
  ☐ Target Group:
    ☐ Type: IP
    ☐ Port: 3000
☐ Networking:
  ☐ ECS Tasks: Private Subnets
  ☐ ALB: Public Subnets


🟦 PHASE 7 – Test Application

☐ Copy ALB DNS Name
☐ Access application:

http://<ALB_DNS>

☐ Verify API / Todo app is working


🟦 PHASE 8 – Install Load Test Tool on EC2

☐ Download & install hey:

curl -Lo hey https://hey-release.s3.us-east-2.amazonaws.com/hey_linux_amd64
chmod +x hey
sudo mv hey /usr/local/bin/

☐ Verify:

hey --version


🟦 PHASE 9 – Load Test & Observe Scaling

☐ Run load test:

hey -z 2m -c 50 http://<ALB_DNS>

☐ Observe:
  ☐ ECS Service → Tasks increase
  ☐ Target Group health check
  ☐ CloudWatch metrics:
    - CPUUtilization
    - MemoryUtilization


🟦 PHASE 10 – Auto Scaling (Optional)

☐ Configure Service Auto Scaling:
  ☐ Metric: CPUUtilization
  ☐ Target value: 50%
  ☐ Min tasks: 1
  ☐ Max tasks: 4
☐ Run load test again
☐ Observe scale out / scale in


🟦 PHASE 11 – Cleanup (MANDATORY)

☐ Delete ECS Service
☐ Delete ECS Cluster
☐ Delete ALB + Target Group
☐ Delete ECR Repository
☐ Delete CloudFormation Stack (foundation)


🎯 EXPECTED OUTCOMES AFTER COMPLETION

✓ Understand Docker image lifecycle
✓ Understand Docker Hub vs ECR
✓ Deploy real ECS Fargate workload
✓ ALB + Target Group working
✓ Real Auto Scaling
✓ Observe CloudWatch metrics
✓ Proper AWS cleanup
