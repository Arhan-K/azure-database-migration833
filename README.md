# Azure Data Migration
This project involved creating and implementing a cloud-based database system using Microsoft Azure. This entailed establishing a production database on a Windows Virtual Machine, installing SQL Server on it, and using this to install SQL Server Management Studio (SSMS) and Azure Data Studio. A local database was restored on the VM using SSMS, and subsequently migrated to a cloud-based Azure SQL database. 

Following this, a disaster recovery scenario was simulated, with intentional data loss from the Azure SQL database, so that a database with the original data values could be restored using Microsoft Azure. 

Furthermore, in order to ensure the availability and accessibility of data under challenging conditions, the data was geo-replicated with a Failover and Fallback protocol implemented.

Finally, in order to enhance security, Microsoft Entra ID integration was employed, thereby adding an added layer of control and access.

## Setting Up the Production Environment

### Virtual Machine
To begin, a Windows Virtual Machine was set up and configured using the Microsoft Azure portal. The VM acted as the home for the production environment, playing the role of an on-premise system used by a company. A VM named ADM-Project was set up in a resource group of the same name. Windows 10 Pro was chosen as the image for the VM (corresponding to that of my local system) and the Central India was chosen as the region. The Standard B2ms (2 vcpus, 8 GiB memory) was selected as the size. The RDP (3389) Port was given access to the VM in order to connect to this using a Remote Desktop Protocol.

A connection was then established to the Windows Virtual Machine using the Microsoft Remote Desktop Protocol (RDP). This involved downloading a .rdp file from the Windows Azure Portal, and logging in with the password set while creating and deploying the VM.

### SQL Server and SSMS
After the connection with the VM was established, Microsoft SQL Server was downloaded and installed within the VM. This was then used to install SQL Server Management Studio using the Basic Installation process, as well as and Azure Data Studio to restore and migrate the database in the subsequent tasks. 

### Creating the Production Database
The production database AdventureWorks2022 was downloaded onto the VM using the backup file provided, which corresponded to the Microsoft SQL Server Database AdventureWorks. 

## Data Migration:
This milestone involved moving the database from the local VM storage to a cloud-based Azure SQL database using Azure Data Studio.

### Setting up Azure Data Studio

### Schema Migration:
After connections to both, the on-premise database, as well as the Azure SQL database were established, the SQL Schema Compare extension was installed on Azure Data Studio, and used to compare the schemas of both the databases, and then migrate the schemas from the source, i.e. the on-premise database, to the target, i.e. the Azure SQL database. 

The migration of the schemas was validated by checking that the Azure SQL database had the same tables as the on-premise database, but none of the values. 

### Data Migration:
Once the schemas were compared and migrated, the migration process for the actual data itself could commence. In order to do this, the extension Azure SQL Migration was first installed. 

The migration of data was validated by comparing the data values of the tables in both databases, and ensuring that they matched.


## Data Backup and Restoration:
This step of the project involved securely storing the database on Microsoft Azure, and provisioning a development environment, which could be tinkered with without the risk of affecting the real data. 
Finally, a weekly automated backup plan was implemented for the the development environment.

### On-premise Database Backup:
The production database hosted on the VM was backed up, thereby duplicating the database to act as a safeguard against any unintended, unforseen corruption of the original database. The duplicate database was stored as AdventureWorks2022_backup.bak and saved on the VM as well as on the personal system.

Once this was done, an Azure Blob Storage Account was created to act as an online repository for any database backups. The backup file was uploaded here, thereby creating an additional online duplicate copy of the database.

### Restoring the Database on a Development Environment:
In order to create a development environment, a new Windows VM was created, with the same configurations as that of the production VM, and the process of restoring the database was repeated.

### Automating Backups on the Development Environment
Once the database was restored, SSMS was used to establish a Management Task that performed automated weekly backups of the development database. This ensured protection for the ongoing work and edits carried out on the database, as well as an easy recovery process.


## Disaster Recovery Simulation
This step of the project involved gaining experience in data recovery procedures by restoring lost data from an Azure SQL backup.

### Mimicking Data Loss:
In order to simulate a situation where data has been compromised, a few lines of data were intentionally deleted from the production database. This was validated by comparing the number of entries in the affected tables before and after the deletion of data. 

### Database Restoration:
Once the data loss from the production database was completed, Azure SQL Database Backup was used to restore the production database. In order for the restored database to contain all the correct data values, the restoration was done using a point in time before the data loss procedure was carried out.

The restored database now assumed the role of the main production database, owing to the simulated data loss. The previous production database was then deleted.

## Geo-Replication and Failover
A geo-replication procedure was set up for the production Azure SQL database, to act as a synchronised replica of the primary production database. The replica was set to a distinct server in the US, as opposed to the primary database. This step minimised any shared risk in the event of data loss from any one physical centre by creating a new redundant copy in a different geographical location.

Once the geo-replication was carried out successfully, a planned failover process was implemented by switching the database to the secondary region. Then, a Failback was done, moving it back to the primary database, demonstrating the cyclical nature of the fallback process.

## Microsoft Entra ID Integration:
Finally, Microsoft Entra ID authentication was enabled for the Azure SQL server hosting the production database, thereby allowing users to authenticate the Entra ID credentials. Following this, an Entra ID administrator was chosen to have control over access and user management. The Entra ID credentials were validated by using them to establish a connection to the server within Azure Data Studio.

Following this, a new user was created, to act as the DB Reader user, which was then assigned with the db_datareader role providing read-only privileges. This was accomplished by executing the following lines of code:

CREATE USER [DB_Reader@yourdomain.com] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [DB_Reader@yourdomain.com];
