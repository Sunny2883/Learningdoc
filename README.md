# LearningDoc

<div align="center">
<strong>Learning Materials</strong>
</div>

## Objective
To collect all important learning materials.

## Description
This repository includes important sections about my learning journey, covering topics that are useful for my development and growth.



<strong>Docker cheatsheets</strong>

| Comamnd | Description|
|--|--|
|docker image prune -af|delete all unused images from sever|
|docker container prune|Removes all stopped containers.|
|docker stats|Displays a live stream of container resource usage statistics.|
|docker cp [container]:[path] [local_path]|Copies files or folders between a container and the local filesystem..|



 <strong>	Steps to create a backup file of a db using shell scripting. </strong>
-•  Step 1: write a backup file ex: backup_script.sh
        
             #!/bin/bash

             # Variables
             DB_HOST="bookstoredb.postgres.database.azure.com"
             DB_NAME="dbname"
             DB_USER="username"
             BACKUP_DIR="/home/azureuser/backup_directory"  # Replace with your desired local backup directory
             DATE=$(date +%Y-%m-%d)
             BACKUP_FILE="${BACKUP_DIR}/${DB_NAME}_backup_${DATE}.sql"
             # S3_BUCKET="s3://your-s3-bucket-name"  # Replace with your actual S3 bucket name if you want to upload to S3

             # Create the backup directory if it does not exist
             mkdir -p "$BACKUP_DIR"

             # Perform the database backup and compress the output
             PGPASSWORD=your_pass pg_dump -h "$DB_HOST" -U "$DB_USER" "$DB_NAME" | gzip > "$BACKUP_FILE"

             # Check if the backup was successful
             if [ $? -eq 0 ]; then
                 echo "Database backup successful: $BACKUP_FILE"
             else
                 echo "Database backup failed!" >&2
                 exit 1
             fi


Step 2:
run and test the script on any server

Step 3:

set crontab using crontab -e command on your server where script is
set the crontab according to your need 
            # m h  dom mon dow   command
            #*/5 * * * * /home/azureuser/backup_script.sh >> /home/azureuser/backup_directory/backup_log.txt 2>&1



this command runs the script after every 5 min and store the logs of crontab at /home/azureuser/backup_directory/backup_log.txt

Note :If script has not necessary permission set the permission using command chmod +x script.sh

<strong>Optional</strong>

              Optional: Upload the backup to the S3 bucket (uncomment the lines below if needed)
              aws s3 cp "$BACKUP_FILE" "$S3_BUCKET"
              if [ $? -eq 0 ]; then
                  echo "Backup uploaded to S3: $S3_BUCKET"
              else
                  echo "Failed to upload backup to S3!" >&2
                  exit 1
              fi

              Optional: Remove old backup files (older than 7 days)


              find "$BACKUP_DIR" -type f -name "*.sql.gz" -mtime +7 -exec rm {} \;

              echo "Backup script completed successfully."


++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

<strong>command to convert json file to env format</strong>

                 
                jq -r 'to_entries | map("\(.key)=\(.value | tojson | gsub("\\\""; ""))") | .[]' filename.json > .env



+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
## Command Timeout in GitHub Actions

When using the `appleboy/ssh-action` in a GitHub Actions workflow, you can set a command timeout to prevent your workflow from failing due to long-running processes. 

### Example Usage:

```yaml
- name: Build and Deploy Backend
  uses: appleboy/ssh-action@v0.1.10
  with:
    command_timeout: 30m  # Set timeout to 30 minutes

```


++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
Managing Dependencies in Terragrunt with Mock Outputs
Issue: When using Terragrunt with modules that have dependencies, destroying infrastructure can fail due to the dependency chain.

Solution: Implement mock outputs in the dependency configuration.

What Mock Outputs Do:

Mock outputs allow you to define placeholder values for outputs from dependent modules.
They enable you to bypass the need for actual output values during operations like destruction.
Benefits:

By using mock outputs, you can prevent dependency-related issues when running the terragrunt destroy-all command.
This approach simplifies the destruction process by avoiding conflicts caused by the interdependencies of resources.


=================================================================================================================

ECS Resource Requirements for Task Deployment
Issue: While deploying a .NET application on AWS ECS with EC2, the task failed to start.

Reason: AWS ECS requires a minimum of:

0.25 CPU units (256 CPU shares)
0.5 GiB (512 MiB) of memory
Implication:

Values below these limits, such as 0.1 CPU units and 0.1 GiB of memory, are considered insufficient and may result in task failure or rejection.

================================================================================================================


Zero Downtime Deployment on AWS ECS
Issue: During the deployment of an application on AWS ECS with EC2, zero downtime deployment was not achieved due to insufficient memory.

Solution: Adjusted the deployment_configuration settings:

minimum_healthy_percent: Specifies the lower limit on the number of healthy tasks during a deployment.
maximum_percent: Specifies the upper limit on the number of tasks that can run during a deployment.
Result: Making these adjustments enabled successful zero downtime deployment.



