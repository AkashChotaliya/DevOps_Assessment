# 🏨 Hotel Booking DevOps Assessment

A complete DevOps project demonstrating:

✅ Dockerized PostgreSQL Database  
✅ Automated Database Backup & Restore Scripts  
✅ Terraform Infrastructure as Code (IaC)  
✅ Modular AWS Infrastructure Design  
✅ Environment Separation (Dev & Prod)

---

# 📌 Project Architecture

```text
                    ┌──────────────────┐
                    │   Developer      │
                    │     (VS Code)    │
                    └────────┬─────────┘
                             │
                             ▼
                  ┌───────────────────┐
                  │ Docker Compose    │
                  │ PostgreSQL DB     │
                  └────────┬──────────┘
                           │
            ┌──────────────┴──────────────┐
            │                             │
            ▼                             ▼
   ┌────────────────┐          ┌────────────────┐
   │ Backup Script  │          │ Restore Script │
   │ backup.sh      │          │ restore.sh     │
   └────────────────┘          └────────────────┘

                           │
                           ▼

                ┌────────────────────┐
                │ Terraform Modules  │
                │ Network            │
                │ ECS                │
                │ RDS                │
                └────────────────────┘
                           │
                           ▼
                ┌────────────────────┐
                │ AWS Infrastructure │
                └────────────────────┘
```

---

# 📂 Project Structure

```text
devops-assessment/
├── README.md
├── docker-compose.yml
├── infra/
│   ├── modules/
│   │   ├── ecs/
│   │   │   ├── main.tf
│   │   │   ├── variables.tf
│   │   │   └── outputs.tf
│   │   ├── network/
│   │   │   ├── main.tf
│   │   │   ├── variables.tf
│   │   │   └── outputs.tf
│   │   └── rds/
│   │       ├── main.tf
│   │       ├── variables.tf
│   │       └── outputs.tf
│   │
│   └── envs/
│       ├── dev/
│       │   ├── backend.tf
│       │   ├── main.tf
│       │   ├── terraform.tfvars
│       │   └── variables.tf
│       │
│       └── prod/
│           ├── backend.tf
│           ├── main.tf
│           ├── terraform.tfvars
│           └── variables.tf
│
├── init-db/
│   ├── 01-schema.sql
│   └── 02-seed.sql
│
└── scripts/
    ├── backup.sh
    └── restore.sh
```

---

# 🚀 Project Execution Steps

## Step 1: Open Project Directory

```bash
pwd
```

---

## Step 2: Start PostgreSQL Container

```bash
docker compose up -d
```

Wait for 5 seconds.

---

## Step 3: Verify Seed Data

```bash
docker exec -it local_hotel_db psql -U postgres -d hotel_db -c "SELECT COUNT(*) FROM hotel_bookings;"
```

### Expected Output

```text
 count
-------
 100
```

---

## Step 4: Run Backup Script

```bash
chmod +x scripts/backup.sh scripts/restore.sh
./scripts/backup.sh
```

### Expected Output

```text
Backup successfully written to backups/
```

---

## Step 5: Simulate Database Failure

```bash
docker exec -it local_hotel_db psql -U postgres -d hotel_db -c "DELETE FROM hotel_bookings;"
```

Verify:

```bash
docker exec -it local_hotel_db psql -U postgres -d hotel_db -c "SELECT COUNT(*) FROM hotel_bookings;"
```

Expected:

```text
0
```

---

## Step 6: Restore Database

```bash
./scripts/restore.sh
```

Expected:

```text
Database successfully restored.
```

---

## Step 7: Verify Recovery

```bash
docker exec -it local_hotel_db psql -U postgres -d hotel_db -c "SELECT COUNT(*) FROM hotel_bookings;"
```

Expected:

```text
100
```

---

# ☁️ Terraform Validation

## Change Directory

```bash
cd infra/envs/dev
```

## Initialize Terraform

```bash
terraform init
```

## Check Formatting

```bash
terraform fmt -check
```

## Validate Configuration

```bash
terraform validate
```

## Generate Execution Plan

```bash
terraform plan -refresh=false
```

---

# 🛠️ Technologies Used

- Docker
- Docker Compose
- PostgreSQL
- Bash Scripting
- Terraform
- AWS ECS
- AWS RDS
- AWS VPC
- Git & GitHub
- VS Code

---

# 🎯 Learning Outcomes

✔ Infrastructure as Code (IaC)

✔ Containerization

✔ Database Backup & Recovery

✔ Terraform Modular Design

✔ AWS Infrastructure Provisioning

✔ Disaster Recovery Testing

✔ DevOps Best Practices

---

ScreenShorts

Add screenshots of terraform plan output, the running ECS service, and a successful psql query here once you've applied the infrastructure.

Terraform Init, Format & Validate — Dev

<img width="1276" height="693" alt="image" src="https://github.com/user-attachments/assets/9c5b061f-508a-4e62-bccf-99c450c7f401" />


