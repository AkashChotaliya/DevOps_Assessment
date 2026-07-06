DevOps Assessment: Terraform & Database Reliability
Production-Grade Documentation & Verification Guide


1. Project Architecture Overview
This repository contains a modular Infrastructure-as-Code (IaC) design using Terraform and a local containerized PostgreSQL environment with optimized data query performance, backup, and point-in-time recovery workflows.
.
├── docker-compose.yml       # Local PostgreSQL infrastructure orchestration
├── init-db/                 # Automated entrypoint initialization scripts
│   ├── 01-schema.sql        # Core DDL Schema tracking bookings and events
│   └── 02-seed.sql          # Automated high-density dataset generator
├── infra/                   # Infrastructure-as-Code ecosystem
│   ├── modules/             # Encapsulated component modules (VPC, ECS, RDS)
│   └── envs/                # Target environment isolation
│       ├── dev/             # Non-prod environment with ephemeral local tracking
│       └── prod/            # Production environment with deletion-safeguards
└── scripts/                 # Operational database reliability tasks
    ├── backup.sh            # Target data capture dump engine
    └── restore.sh           # Clean disaster-recovery engine
   
3. Part 1 & 2: Terraform Infrastructure Validation
The Terraform layout implements multi-environment orchestration (dev and prod) built on reusable infrastructure modules. Sizing structures, backup preservation metrics, and teardown constraints are dynamically managed via variable injection arrays.
• Dev Environment: Minimum compute footprint, 1-day backup snapshots, and zero teardown protection.
• Prod Environment: Enterprise compute footprint, 30-day snapshot preservation metrics, and explicit deletion protection enabled.
Local Structural Validation Verification
Run these step-by-step diagnostic checks inside your terminal to validate configuration correctness without provisioning actual live infrastructure assets:
cd infra/envs/dev
terraform init
terraform fmt -check
terraform validate
terraform plan -refresh=false

5. Part 3: Local Database Runtime Execution
The local runtime database engine is orchestrated using Docker Compose and handles automatic table provisioning and data ingestion on initial boot.
1. Boot up the Database Stack
docker compose up -d
2. Verify Data Seed Performance
docker exec -it local_hotel_db psql -U postgres -d hotel_db -c "SELECT COUNT(*) FROM hotel_bookings;"
4. Part 4: Query Optimization & Index Strategy
SELECT org_id, status, COUNT(*), SUM(amount)
FROM hotel_bookings
WHERE city = 'delhi'
  AND created_at >= NOW() - INTERVAL '30 days'
GROUP BY org_id, status;
The Solution: Multi-Column Composite Index
CREATE INDEX idx_hotel_bookings_perf ON hotel_bookings (city, created_at, org_id, status, amount);
Performance Proof Verification
docker exec -it local_hotel_db psql -U postgres -d hotel_db -c "
EXPLAIN ANALYZE
SELECT org_id, status, COUNT(*), SUM(amount)
FROM hotel_bookings
WHERE city = 'delhi' AND created_at >= NOW() - INTERVAL '30 days'
GROUP BY org_id, status;" 
5. Part 5: Backup & Disaster Recovery Tasks
# Set file execution permissions
chmod +x scripts/*.sh

# Run backup routine
./scripts/backup.sh

# Simulate Disaster (Delete Data)
docker exec -it local_hotel_db psql -U postgres -d hotel_db -c "DELETE FROM hotel_bookings;"

# Run the Database Restore Workflow
./scripts/restore.sh

# Verify Final State Restore
docker exec -it local_hotel_db psql -U postgres -d hotel_db -c "SELECT COUNT(*) FROM hotel_bookings;" 
