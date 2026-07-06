# DevOps Assessment: Terraform + Database Reliability

This repository holds infrastructure blueprints and reliability solutions designed around AWS services and PostgreSQL engine tasks.

🏗️ Step 1: Initialize Your Project Directory
Before executing any commands, make sure your terminal is opened in the root folder of your project (where your docker-compose.yml file lives).

# Check where you are (it should show your project folder name)
pwd

🐳 Step 2: Spin Up the Local Database Engine
We will start by launching your PostgreSQL database container.

# Start the database container in the background
docker compose up -d

⏱️ Note: Wait about 5 seconds after running this command. Docker needs a brief moment to create the database, build the tables , and ingest your 100 mock hotel bookings.

📊 Step 3: Verify the Initial Seed DataLet's make sure your tables were created and that your seed data loaded perfectly. We will ask PostgreSQL to count the total rows in your hotel_bookings table.  

docker exec -it local_hotel_db psql -U postgres -d hotel_db -c "SELECT COUNT(*) FROM hotel_bookings;"

Expected Output: You should see a table output showing exactly 100.  


💾 Step 4: Test Your Backup Script

Now we will test your backup automation file. First, give your script files execution permissions, then trigger the backup.

# Give execution permissions to the scripts
chmod +x scripts/backup.sh scripts/restore.sh

# Run the backup script
./scripts/backup.sh

Expected Output: The terminal will say "Backup successfully written to...". If you look inside your directory, a new folder named backups/ will have appeared containing your .sql snapshot file.  


🗑️ Step 5: Simulate a Database Disaster (Optional but Recommended)

To truly prove your restore script works, let's intentionally wipe out all the data inside your database container.

docker exec -it local_hotel_db psql -U postgres -d hotel_db -c "DELETE FROM hotel_bookings;"

If you run the count check command from Step 3 again, it will now return 0. Your data is gone!

⬇️ Step 6: Test Your Restore Script

Let's fix the database disaster by running your recovery script to restore everything back to normal.  

./scripts/restore.sh

Expected Output: The terminal will print "Database successfully restored."


🔍 Step 7: Final Data Integrity Verification

Let’s run our final validation check to ensure all 100 booking items were recovered successfully

docker exec -it local_hotel_db psql -U postgres -d hotel_db -c "SELECT COUNT(*) FROM hotel_bookings;"

Expected Output: The count will be right back at 100!

🌐 Step 8: Validate the Terraform Blueprints

Lastly, we need to make sure your AWS infrastructure code has no syntax errors or typos. Move into your development environment directory to validate it:

# 1. Change directory to the dev environment
cd infra/envs/dev

# 2. Initialize the backend providers
terraform init

# 3. Check code formatting syntax
terraform fmt -check

# 4. Validate the configuration
terraform validate

# 5. Run a dry-run execution plan
terraform plan -refresh=false


If your Terraform plan displays a list of resources to be created without throwing errors, your entire assessment is fully functional and ready to turn in!


📊 step 9: View the Seed Data (First 5 Rows)

To see what your generated data actually looks like inside the hotel_bookings table, run this command in your terminal:

docker exec -it local_hotel_db psql -U postgres -d hotel_db -c "SELECT * FROM hotel_bookings LIMIT 5;"

📈 Step 10: Show the Output of the Optimized Query
The assignment requires a specific analytical query filtered by city (delhi) and time range. Run this command to view its aggregated output:

docker exec -it local_hotel_db psql -U postgres -d hotel_db -c "
SELECT org_id, status, COUNT(*), SUM(amount)
FROM hotel_bookings
WHERE city = 'delhi'
  AND created_at >= NOW() - INTERVAL '30 days'
GROUP BY org_id, status;"

org_id                |  status   | count |   sum    
--------------------------------------+-----------+-------+----------
 a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11 | CONFIRMED |     9 | 22450.00
 b0eebc99-9c0b-4ef8-bb6d-6bb9bd380a22 | PENDING   |     8 | 21200.00
 c0eebc99-9c0b-4ef8-bb6d-6bb9bd380a33 | CANCELLED |     8 | 23000.00
(3 rows)

🛠️ Step 11: Prove to Reviewers Your Index is Being Used
Your reviewers want to see that your custom index idx_hotel_bookings_perf is actively optimizing the database. You can prove this by running an EXPLAIN ANALYZE on the query:

docker exec -it local_hotel_db psql -U postgres -d hotel_db -c "
EXPLAIN ANALYZE
SELECT org_id, status, COUNT(*), SUM(amount)
FROM hotel_bookings
WHERE city = 'delhi'
  AND created_at >= NOW() - INTERVAL '30 days'
GROUP BY org_id, status;"
