# SWYNEX Technologies — Task 3: Monitoring and Cost Awareness

- **Intern Name:** Yogiraj Solanki
- **Intern ID:** SWX-APP-2026-C3794
- **Offer ID:** SWX-OFR-2026-5DC45
- **Domain:** Cloud & Emerging Technology
- **Monitored Deployment (Task 2):** `yogiraj-swynex-portfolio-3157` (`ap-southeast-2`)
- **Live AWS Endpoint:** http://yogiraj-swynex-portfolio-3157.s3-website-ap-southeast-2.amazonaws.com

---

## 1. Objective
Implement operational monitoring, automated alerting, cost governance, and access-control auditing for the AWS S3 static portfolio deployment built in Task 2.

---

## 2. Logs & Status Checks

### A. AWS S3 Server Access Logging
- **Target Website Bucket:** `yogiraj-swynex-portfolio-3157`
- **Dedicated Log Destination Bucket:** `yogiraj-swynex-logs-3157` (private bucket with *Block all public access* enabled)
- **Captured Telemetry:** Requester IP, timestamp, HTTP method (`GET`), requested object key (`index.html`, `style.css`, `script.js`), HTTP response code (`200`, `403`, `404`), and bytes sent.

![Private Logs Bucket](screenshots/01-logs-bucket.png)

![S3 Server Access Logging Enabled](screenshots/02-s3-access-logging.png)

### B. Endpoint Availability & Status Checks
- **Endpoint Monitored:** `http://yogiraj-swynex-portfolio-3157.s3-website-ap-southeast-2.amazonaws.com`
- **Health Verification:** Verified HTTP `200 OK` response codes and sub-200ms asset delivery across `index.html`, `style.css`, and `script.js`.

![HTTP 200 Status Check](screenshots/03-http-status-check.png)

---

## 3. Simple Alert Idea (Availability & Cost Alerts)

### A. Billing & Cost Threshold Alert (Implemented)
- **Service:** AWS Budgets (Zero-Spend Budget Template)
- **Trigger Condition:** Actual or forecasted AWS spend exceeds **$0.01** (Free Tier threshold).
- **Notification Action:** Instant email alert sent to `yogirajsolanki3157@gmail.com` so unexpected storage or data-transfer charges are caught immediately.

![AWS Zero Spend Budget](screenshots/04-aws-billing-budget.png)

### B. Uptime & Error Rate Alert Idea (Architecture Design)
- **Synthetic Health Check:** Configure an **Amazon CloudWatch Synthetics Canary** (or external HTTP monitor) pinging `http://yogiraj-swynex-portfolio-3157.s3-website-ap-southeast-2.amazonaws.com` every 5 minutes.
- **Alarm Condition:** Trigger an **Amazon CloudWatch Alarm** if the HTTP status code is not `200` (e.g., `403 Forbidden` or `404 Not Found`) for 2 consecutive checks.
- **Notification Channel:** Route the CloudWatch Alarm through an **Amazon SNS (Simple Notification Service)** topic to deliver an immediate email/SMS alert.

---

## 4. Cost & Access-Control Checklist

### Cost Optimization & Governance Checklist
- [x] **AWS Free Tier Alignment:** Hosted static assets (~10 KB total) on Amazon S3 Standard, well within the 5 GB storage and 20,000 GET requests Free Tier allowance.
- [x] **Zero Idle Compute Cost:** Used serverless S3 Static Website Hosting instead of running an EC2 instance 24/7.
- [x] **Log Retention Lifecycle:** Apply an S3 Lifecycle rule on `yogiraj-swynex-logs-3157` to automatically expire/delete access logs older than 30 days to prevent log storage accumulation.
- [x] **Proactive Billing Alert:** Configured an AWS Zero-Spend Budget alert (`$0.01` threshold) to prevent surprise charges.

### Security & Access-Control Checklist
- [x] **Least-Privilege Public Bucket Policy:** Granted public access *only* to `s3:GetObject` (read-only) on `arn:aws:s3:::yogiraj-swynex-portfolio-3157/*`; denied all public `PutObject`, `DeleteObject`, and `ListBucket` actions.
- [x] **Private Log Storage:** Kept *Block all public access* enabled (`ON`) on the `yogiraj-swynex-logs-3157` bucket so server access logs remain strictly private.
- [x] **Root Account Protection:** Avoided using AWS root credentials for daily operations and enabled Multi-Factor Authentication (MFA).
- [x] **No Hardcoded Secrets:** Verified that `index.html`, `style.css`, and `script.js` contain zero AWS Access Keys, Secret Keys, or private tokens.
-
