# 504AZ: what this package does and does not prove

## Implemented if GitHub's deploy job succeeds AND target resources are confirmed in AWS

- AWS VPC subnet segmentation, internet gateway and route tables, two Availability Zones.
- ALB + EC2 web tier, launch template, ASG, restricted security groups, Apache website.
- DynamoDB on-demand table, optional sample items.

## Only proposed until implemented/evidenced

- Private app tier if default placement is public (private subnets exist but EC2 do not use them).
- Relational SQL RDS (with Multi-AZ and suitable encrypted backups).
- Directory Services/Identity Center, corporate/customer RBAC design.
- S3 file services with secure access, retention, lifecycle and encryption design.
- WAF/Shield/GuardDuty/CloudTrail logging, KMS, backup and compliance measures.
- ISO 20000 Service Management Strategy, change/incident/problem management and performance reviews.
- Engagement with CEO/tutor and approved business/regulatory requirements.

Security caveats: The HTTP demo website carries no financial transactions or sensitive data. 'Two AZs' is useful fault isolation but does not by itself establish a disaster-recovery programme or tested autoscaling under load.
