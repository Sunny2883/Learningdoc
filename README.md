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
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++


Steps to Connect to Azure PostgreSQL Flexible Server 

Since pgAdmin does not support direct tunneling through Azure Bastion, you'll need to set up port forwarding through an SSH tunnel. Follow these steps:

Step 1: Set Up an SSH VM in the VNet
Create a Virtual Machine (VM):

Deploy a small Linux VM (e.g., Ubuntu) in the same VNet and subnet where your PostgreSQL Flexible Server is located. This VM will act as the intermediary for the SSH tunnel.
Ensure the VM has SSH access configured.
Install PostgreSQL Client on the VM (optional):

Log into the VM using Azure Bastion and install the PostgreSQL client to verify connectivity with the database.
Run:

            sudo apt update
            sudo apt install postgresql-client
            
Test connectivity (replace <db-hostname>, <username>, and <db-name>):

             psql -h <db-hostname> -U <username> -d <db-name>
             
Step 2: Create an SSH Tunnel for pgAdmin Access
To access the database from your local machine using pgAdmin, you’ll need to set up port forwarding through SSH.

SSH Tunnel Command:

Use an SSH client (e.g., OpenSSH) on your local machine to create a tunnel through the VM to the PostgreSQL Flexible Server.
Replace <vm-public-ip>, <vm-username>, <db-private-ip>, and <local-port> (e.g., 5433):


              ssh -i C:\path\to\your\private_key -L <local-port>:<db-private-ip>:5432 <vm-username>@<vm-public-ip>


local-port: This is the port on your local machine where pgAdmin will connect (e.g., 5433).
db-private-ip: The private IP address of your PostgreSQL Flexible Server.
vm-public-ip: The public IP address of your SSH VM.
Keep the Tunnel Open:

Leave this SSH session open, as it establishes the connection for pgAdmin to connect through.
Step 3: Configure pgAdmin to Connect via the SSH Tunnel
Open pgAdmin on your local machine.
Create a New Server in pgAdmin:
Go to File > Add New Server in pgAdmin.
Configure Connection Settings:
Name: Provide a name for the connection.
Connection tab:
Host: localhost
Port: Use the local-port from the SSH tunnel (e.g., 5433).
Username: Your PostgreSQL database username.
Password: Your PostgreSQL database password.
Save and Connect



<strong>Troubleshoot Azure-to-Azure VM replication errors</strong>


Sign in as a root user. The hash symbol (#) is the default command prompt.

To change the directory, run this command:

cd /etc/ssl/certs

Check whether the Symantec root CA certificate is present:

ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem

If the Symantec root CA certificate isn't found, run the following command to download the file. Check for any errors and follow recommended actions for network failures.

               wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem

Check whether the Baltimore root CA certificate is present:

               ls Baltimore_CyberTrust_Root.pem

If the Baltimore root CA certificate isn't found, run this command to download the certificate:

                 wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem

Check whether the DigiCert_Global_Root_CA certificate is present:

               ls DigiCert_Global_Root_CA.pem

If the DigiCert_Global_Root_CA isn't found, run the following commands to download the certificate:


                wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt

                openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem
To update the certificate subject hashes for the newly downloaded certificates, run the rehash script:

                c_rehash

To check whether the subject hashes as symlinks were created for the certificates, run these commands:

            ls -l | grep Baltimore

            ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5

             ls -l | grep DigiCert_Global_Root

Create a copy of the file VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem with filename b204d74a.0:

              cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0

Create a copy of the file Baltimore_CyberTrust_Root.pem with filename 653b494a.0:

              cp Baltimore_CyberTrust_Root.pem 653b494a.0

Create a copy of the file DigiCert_Global_Root_CA.pem with filename 3513523f.0:

             cp DigiCert_Global_Root_CA.pem 3513523f.0

Check that the files are present:



<h1>Install azcopy on linux server</h1>
      wget -O azcopy.tar.gz "https://aka.ms/downloadazcopy-v10-linux"
# Extract the downloaded tar.gz file
      tar -xvf azcopy.tar.gz
# Move the azcopy binary to /usr/local/bin
      sudo mv ./azcopy_linux_amd64_*/azcopy /usr/local/bin/
# verify installation
      azcopy --version


              ls -l 653b494a.0 b204d74a.0 3513523f.0




