devops-assessment/
├── README.md
├── docker-compose.yml
├── infra/
│   ├── modules/
│   │   ├── ecs/
│   │   │   ├── main.tf
│   │   │   ├── outputs.tf
│   │   │   └── variables.tf
│   │   ├── network/
│   │   │   ├── main.tf
│   │   │   ├── outputs.tf
│   │   │   └── variables.tf
│   │   └── rds/
│   │       ├── main.tf
│   │       ├── outputs.tf
│   │       └── variables.tf
│   └── envs/
│       ├── dev/
│       │   ├── backend.tf
│       │   ├── main.tf
│       │   ├── terraform.tfvars
│       │   └── variables.tf
│       └── prod/
│           ├── backend.tf
│           ├── main.tf
│           ├── terraform.tfvars
│           └── variables.tf
├── init-db/
│   ├── 01-schema.sql
│   └── 02-seed.sql
└── scripts/
    ├── backup.sh
    └── restore.sh
