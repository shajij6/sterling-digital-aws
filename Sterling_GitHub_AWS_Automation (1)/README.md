# Sterling Digital Solutions — GitHub Actions + AWS CloudFormation

**Purpose:** Redeploy the user's *Sterling Digital Solutions / 504AZ* AWS practical coursework proof of concept **from scratch**. This is not the earlier Plan-Letting multi-cloud assignment.

**What the workflow creates with `deploy=true`:**
- One VPC (`10.0.0.0/16`) with four subnets: two public and two private, across two AWS Availability Zones.
- One Internet Gateway and separate public/private route tables.
- One public Application Load Balancer using an HTTP/80 listener and an EC2 target group.
- One EC2 Launch Template that installs and enables Apache (`httpd`) on Amazon Linux 2023.
- An Auto Scaling Group with desired capacity 2, maximum 4, across two AZs, with an EC2 CPU target-tracking policy.
- A security group allowing public port 80 **only to the ALB** and an EC2 web security group allowing port 80 **only from the ALB SG**. SSH is closed.
- One on-demand DynamoDB table, plus two clearly marked demo records if selected in the workflow.

**Important:** This is a **functional academic PoC**, NOT a complete FCA-compliant trading/banking system. It intentionally omits production-grade TLS/HTTPS, IAM Identity Center, directory integration, SQL RDS, secure S3 file workflow, compliance logging, and full ISO 20000 Service Management Strategy. Treat these as **proposed architecture/future work**, not deployed services. The user’s separate report/other assignment evidence is not overwritten.

## FIRST RUN — ONE STEP AT A TIME

1. Create a NEW GitHub repository, e.g. `sterling-digital-aws`. Do not overwrite the previous `plan-letting` repository.
2. Unzip this archive and upload **the contents of the folder** to the root of your GitHub repository. The workflow must be located at `.github/workflows/deploy-sterling.yml` and the template at `cloudformation/sterling-foundation.yaml`.
3. Start the AWS Academy **Learner Lab**, then open **AWS Details → AWS CLI** to obtain your own fresh temporary Access Key ID, Secret Access Key and Session Token. **NEVER send keys to ChatGPT or commit them to GitHub.**
4. GitHub repository → **Settings → Secrets and variables → Actions → New repository secret**. Create these three exact names:
   - `AWS_ACCESS_KEY_ID`
   - `AWS_SECRET_ACCESS_KEY`
   - `AWS_SESSION_TOKEN`
5. GitHub → **Actions → Sterling Digital Solutions - validate and deploy AWS → Run workflow**. First run with `deploy` **UNCHECKED**. This validates the template only; it does NOT deploy AWS resources.
6. If validation is green, run it a second time with `deploy` **CHECKED**, `placement=public`, `seed_data=checked`. This time watch for the **Deploy VPC + ALB + EC2 ASG + DynamoDB** job. Successful validation alone is NOT successful deployment.
7. Wait until the CloudFormation stack `sterling-coursework-foundation` is `CREATE_COMPLETE`/`UPDATE_COMPLETE`, then check the **Show stack outputs and website URL** section for the `WebsiteURL`. Open it in your browser. Allow a few minutes for EC2 to install Apache and targets to become healthy.
8. Collect your own screenshots in AWS: CloudFormation stack status/Outputs, VPC resource map and subnets, ALB, target-group health, ASG capacity/instance distribution, EC2 instance checks, browser URL showing ALB DNS, DynamoDB table/items, and GitHub Action deployed job.

**AWS Region:** `us-east-1` (N. Virginia). Keep the lab and Actions region consistent.

## PLACEMENT CHOICE / COST & SECURITY

- `placement=public` **DEFAULT:** EC2 nodes are in public subnets and have public IPs so Amazon Linux can download Apache without NAT. Security groups reject direct inbound HTTP/SSH from the Internet to EC2; HTTP inbound to EC2 only originates from the ALB security group. This is deliberately a **lower-cost demo**, *not* the ideal private-web-tier production architecture. Public IPv4 addresses and EC2/ALB still incur charges.
- `placement=private` **OPTIONAL:** EC2 nodes go into private subnets with NO public IPs. A single NAT Gateway and Elastic IP are created to permit outbound package installation. **NAT Gateway costs extra and may be forbidden in AWS Academy**. A single NAT is a failure dependency for outbound connectivity in the other AZ. Only try this with sufficient lab budget/permissions.
- This project uses an **HTTP listener, not HTTPS**. For a bank, production requires validated domain name, ACM TLS certificate, 443 listener, HTTPS redirect, and additional controls.
- *Do not switch to private mode in the middle of a live demo.* A change to network interfaces/subnets can replace instances, incur charges, and interrupt availability.

## TROUBLESHOOTING

- **Workflow green but nothing in AWS?** Check whether `deploy` was checked. `validate` alone is expected to be green without creating resources.
- **`ExpiredToken` or `UnrecognizedClientException`:** Refresh all three GitHub Secrets from the current Learner Lab session. AWS Academy tokens expire.
- **`AccessDenied` / `explicit deny`:** Lab IAM/SCP permissions restrict the action. Stop and capture the exact error; don't circumvent university restrictions. Tutor may need to help.
- **CloudFormation `ROLLBACK_COMPLETE`:** The stack's Events tab explains the failed resource. Some names stay reserved until failed stack is cleaned up. Do **not** delete unrelated or shared resources; coordinate cleanup with your tutor.
- **ALB returns 503 / unhealthy:** EC2 may still be running the `dnf install httpd` user-data setup. Check Target Group → Targets after several minutes. Public mode needs correct default Internet Gateway route; private mode needs NAT. Check SG: ALB allows 80 inbound from Internet; Web SG allows 80 from ALB SG.
- **Website won't load with `https://`:** The PoC uses `http://` intentionally; production HTTPS is a separate task.
- **No DynamoDB items:** Check the `seed_data` input and IAM permissions. The template creates the table; records are written by the GitHub workflow after deployment.
- **AMI parameter error:** The template resolves AWS's Amazon Linux 2023 SSM public parameter in the chosen region. Confirm region and lab SSM access.

## CAPTURE EVIDENCE

See `docs/EVIDENCE_CHECKLIST.md`. **Only record resources after the current deployment has actually succeeded.** Do not use conceptual architecture diagrams as proof that services were created.

## IMPORTANT ABOUT COST / TEARDOWN

Creating 2–4 EC2 instances, an ALB, public IPv4 addresses and optional NAT incurs charges/consumes AWS Academy budget. The deployment does not automatically delete resources at the end of the workflow. After evidence is collected and the tutor confirms it's safe, you can clean up only this dedicated CloudFormation stack, `sterling-coursework-foundation`, using the AWS CloudFormation console. **Deleting the stack deletes the demo DynamoDB table and records.** Avoid running both this project and old test stacks indefinitely.

## NEXT ACADEMIC PHASES (not included in this deployed PoC)

1. Propose managed SQL Amazon RDS multi-AZ and database isolation, discuss costs/backup.
2. Propose IAM Identity Center/role-based access and approved directory integration.
3. Propose encrypted Amazon S3 for file services, retention, versioning and controlled access.
4. Propose AWS WAF, CloudTrail, CloudWatch, GuardDuty, KMS, backup/restore and continuity controls.
5. Write ISO/IEC 20000-aligned Service Management Strategy covering all brief headings.
6. Verify the CEO (tutor) requirements and capture the real conversation/feedback when it happens.

These are **design recommendations** until independently implemented and evidenced.
