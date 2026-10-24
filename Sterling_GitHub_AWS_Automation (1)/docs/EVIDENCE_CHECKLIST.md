# Sterling Digital Solutions — evidence screenshots after real deployment

Take these screenshots yourself with the AWS region and the relevant resources visible:

1. GitHub Actions → green **deploy** job (not just validate); Show stack outputs in the run log.
2. CloudFormation → `sterling-coursework-foundation` → `CREATE_COMPLETE` or `UPDATE_COMPLETE`, Events and Outputs.
3. VPC → VPCs → `sterling-coursework-foundation-vpc` → resource map: 4 subnets over 2 AZs.
4. VPC → route tables: public route `0.0.0.0/0 → Internet Gateway`; private routes only when using private placement.
5. EC2 → Load Balancers → load balancer created by stack `sterling-coursework-foundation` → Status Active + associated subnets and listener HTTP/80.
6. EC2 → Target Groups → two **healthy** EC2 targets, one in each AZ; capture if older targets are draining, but distinguish active from terminated.
7. EC2 → Auto Scaling Groups → desired capacity 2 and instances across 2 AZs.
8. EC2 → Instances → web instances running and status checks `2/2 passed`.
9. Open the CloudFormation **WebsiteURL** in a browser, keep `http://ALB-DNS` visible to establish that traffic flows through the ALB (not a direct EC2 IP).
10. DynamoDB → Tables → `${stack}-TradingMarketData` and Explore items → demo AAPL / TSLA records.
11. Optional: GitHub → validated source commit; application security group inbound rule sourced from ALB security group.

Be accurate in your report: a diagram is an intended target design; AWS Console/CLI output is deployment evidence; a browser screenshot of an instance's direct public IP is *not* ALB evidence. No secret key/session token screenshots.
