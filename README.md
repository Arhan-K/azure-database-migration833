# Azure Data Migration
This project involved creating and implementing a cloud-based database system using Microsoft Azure, working as a Cloud Engineer tasked with migrating the on-premise database of a company to the cloud. This entailed establishing a production database on a Windows Virtual Machine, installing SQL Server on it, and using this to install SQL Server Management Studio (SSMS) and Azure Data Studio. A local database was restored on the VM using SSMS, and subsequently migrated to a cloud-based Azure SQL database. 

Following this, a disaster recovery scenario was simulated, with intentional data loss from the Azure SQL database, so that a database with the original data values could be restored using Microsoft Azure. 

Furthermore, in order to ensure the availability and accessibility of data under challenging conditions, the data was geo-replicated with a Failover and Fallback protocol implemented.

Finally, in order to enhance security, Microsoft Entra ID integration was employed, thereby adding an added layer of control and access.

This project was very useful in enabling me to gain a hands-on experience in Cloud Engineering and Database Management through the use of Microsoft Azure. I was able to get a clear idea about the procedures involved in the use of Cloud resources, as well as the purposes behind it.

A flowchart diagram detailing the cloud storage structure, as well as screenshots documenting different stages of the project are shown in the wiki file of the repository.

## Setting Up the Production Environment:
This milestone involved setting up the environment for the cloud-based database, by provisioning a Windows Virtual Machine and installing SQL Server, SQL Server Management Studio, and Azure Data Studio, preparing for the data migration process in the subsequent milestone.

### Virtual Machine
To begin, a Windows Virtual Machine was set up and configured using the Microsoft Azure portal. The VM acted as the home for the production environment, playing the role of an on-premise system used by a company. A VM named ADM-Project was set up in a resource group of the same name. Windows 10 Pro was chosen as the image for the VM (corresponding to that of my local system) and the Central India was chosen as the region. The Standard B2ms (2 vcpus, 8 GiB memory) was selected as the size, as this was the most cost effective and efficient option available. The RDP (3389) Port was given access to the VM in order to connect to this using a Remote Desktop Protocol, by allowing this port in the inbound traffic rules for the Virtual Machine.

A connection was then established to the Windows Virtual Machine using the Microsoft Remote Desktop Protocol (RDP). This involved downloading a .rdp file from the Windows Azure Portal, and logging in with the password set while creating and deploying the VM.

### SQL Server and SSMS
After the connection with the VM was established, the SQL Server Developer installer was downloaded from the Microsoft Download Center, and then run to launch the SQL Server Installation Wizard, using the basic installation option. After reading and accepting the license terms, the SQL Server Installation Wizard was then run to install SQL Server Management Studio, by clicking the Install SSMS button on the SQL Server Installation Wizard. The latest version (19.3) was then downloaded and installed from the Microsoft Download Center.

### Creating the Production Database
The production database AdventureWorks2022 was downloaded onto the VM using the backup file AdventureWorks2022.bak provided, which corresponded to the Microsoft SQL Server Database AdventureWorks. In order to restore this database, SSMS was opened, and the 'Restore Database' option was selected after right-clicking on the Database node at the top-left of the screen. In the dialogue for restoring the database, the Device option was chosen as the source for the backup file. The '...' button was clicked in order to locate and add the AdventureWorks2022.bak file, and the restoration process was commenced by clicking the OK button. A confirmation message appeared once this process was completed.

## Data Migration:
This milestone involved moving the database from the local VM storage to a cloud-based Azure SQL database using Azure Data Studio.

### Setting up Azure Data Studio
Azure Data Studio was downloaded along with SSMS, from the website https://colab.research.google.com/corgiredirector?site=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fsql%2Fazure-data-studio%2Fdownload-azure-data-studio, selecting the appropriate Windows version. After Azure Data Studio was downloaded, the installer was run to finish the installation process.
In order to establish a connection with the local, on-premise database, Azure Data Studio was opened and the 'New Connection' option was chosen after clicking on the 'Server' button in the activity bar. Once the Connection dialogue was opened, the Microsoft SQL Server option was chosen as the server type. The connection details, including the server name, authentication type, and login credentials were filled, with information being obtained from the SQL Database Overview page in Azure Data Studio. The specific connection details are provided in the Wiki.

### Setting up Azure SQL Database
The Create New button on the Azure SQL database was used to open a new SQL Database server, after providing the required credentials, such as the server name, and the region, with the SQL Server Authentication method. The compute and storage mechanism was configured by clicking on the 'Compute + Storage' panel, choosing the basic options for both. Once validated and created, the subscription, resource group and database name were chosen for the server, in order to complete the process.

### Schema Migration:
After connections to both, the on-premise database, as well as the Azure SQL database were established, the SQL Schema Compare extension was installed on Azure Data Studio. and used to compare the schemas of both the databases, and then migrate the schemas from the source, i.e. the on-premise database, to the target, i.e. the Azure SQL database. The local SQL Server database was selected after clicking on the Server icon in the Activity Bar of SQL Schema Compare. A new Migration Project was commenced after right-clicking on the database and choosing the Schema Compare option. Once this was done, the Source and Target databases were configured to the local SQL Server database, and the Azure SQL Database respectively by clicking the '...' button to choose the source and target. Following this, the Schema comparison process was commenced by clicking on the 'Compare' button present at the top of the page. The selected schema changes were then applied in order to migrate the schema from the source to the target by clicking on the Apply button at the top of the page, and selecting Yes when prompted to update the target.

The migration of the schemas was validated by checking that the Azure SQL database had the same tables as the on-premise database, but none of the values. 

### Data Migration:
Once the schemas were compared and migrated, the migration process for the actual data itself could commence. In order to do this, the extension Azure SQL Migration extension was first installed. The Server icon was clicked, and the local SQL Server database was chosen from the list. After right-clicking on the Server, and selecting Manage, the Azure Migration option was selected under the General category. The Migrate to SQL option was chosen and the Azure SQL database was chosen as the target. The Azure SQL target was configured by choosing the subscription, resource group and SQL Database server. The Azure SQL database was chosen as the desired target and the login credentials were filled in. After navigating to Azure Database Migration Services in the Azure Portal, the Create button was clicked in order to create a new service, followed by Select, and the corresponding resource group, region and name were chosen.

Following this, Integration Runtime was downloaded and installed, selecting the latest version. When running the file, the runtime was registered by filling in one of the two keys provided in Azure Data Studio. This step was finalised by launching the Configuration Manager, clicking Next, and filling in the Windows Authentication login credentials to the local SQL Server. The tables for migration were chosen by clicking the Edit button, followed by Select; and then Update followed by Run Validation in order to validate the data. Once this was completed, the Migration step was finally commenced by clicking Start Migration.

The migration of data was validated by comparing the data values of the tables in both databases, and ensuring that they matched.


## Data Backup and Restoration:
This phase of the project involved securely storing the database on Microsoft Azure, and provisioning a development environment, which could be tinkered with without the risk of affecting the real data. 
Finally, a weekly automated backup plan was implemented for the the development environment.

### On-premise Database Backup:
The production database hosted on the VM was backed up, thereby duplicating the database to act as a safeguard against any unintended, unforseen corruption of the original database. This was achieved by opening SSMS and connecting to SQL server, selecting the database, and choosing the Back Up option from the Tasks button. The duplicate database was stored as AdventureWorks2022_backup.bak and saved on the VM as well as on the personal system.

Once this was done, an Azure Blob Storage Account was created to act as an online repository for any database backups. This was carried out by navigating to the Azure's Storage page, and creating a storage account after filling in the subscription type, the resource group, the name, and the region. The backup file was uploaded here, thereby creating an additional online duplicate copy of the database.

### Restoring the Database on a Development Environment:
In order to create a development environment, a new Windows VM was created, with the same configurations as that of the production VM, and the process of restoring the database was repeated.

### Automating Backups on the Development Environment
Once the database was restored, SSMS was used to establish a Management Task that performed automated weekly backups of the development database. This ensured protection for the ongoing work and edits carried out on the database, as well as an easy recovery process. In order to achieve this, SSMS was opened and a connection with the SQL Server was established, followed by opening the SQL Server Agent Node by right-clicking on the Object Explorer, and selecting 'Yes' to the prompt. SQL Server credentials were created by executing the following T-SQL query:
```
CREATE CREDENTIAL [YourCredentialName]  
  WITH IDENTITY = '[Your Azure Storage Account Name]',
  SECRET = 'Access Key'; 
```
First, the New Query option was selected after right-clicking the Server Name button the query was entered. The access keys were found by navigating to the Security + Networking page, and clicking on Access Keys. The credentials were found in the section named Credentials in the Security page. 

The automated backup plan was then set up by opening the Maintenance Plan Wizard. This was achieved by navigating to the Object Explorer, expanding the Management node, and selecting the Maintenance Plan Wizard by right-clicking the Maintenance Plans button. After filling in the name and description of the plan, the schedule was changed from the default option to a weekly backup plan. After selecting 'Next' at the bottom of the page to continue to the Back Up Database task, the database AdventureWorks2022 was selected for backup, along with a URL as the option its destination. After moving on to the destination tab, the SQL Server credentials used to create the Azure Storage Account were selected from the drop-down list. Following this, the details of the Azure Storage Container were filled in, before moving on to the final page, and completing the setup of the Maintenance Plan. 


## Disaster Recovery Simulation
This step of the project involved gaining experience in data recovery procedures by restoring lost data from an Azure SQL backup.

### Mimicking Data Loss:
In order to simulate a situation where data has been compromised, a few lines of data were intentionally deleted from the production database. This was done by opening the previously used Azure SQL database with Azure Data Studio in the production environment, and opening a new query tab. The following T-SQL command was then executed to simulate data loss:
```
DELETE TOP (50) 
FROM Person.EmailAddress; 
```
This procedure does not work with all tables, such as Person.Address, since this is linked with a primary-foreign key, therefore data can not be deleted from it.

The loss of data was then validated by comparing the number of entries in the affected tables before and after the deletion of data. 

### Database Restoration:
Once the data loss from the production database was completed, Azure SQL Database Backup was used to restore the production database. This was carried out by selecting the target database from the Azure SQL dashboard, clicking on the Restore button located on the top bar of the page, selecting the Restore Database option, followed by selecting Restore Point. A new database was created using the same procedure as earlier. In order for the restored database to contain all the correct data values, the restoration was done using a point in time before the data loss procedure was carried out. The restoration process was validated by checking the number of entries in the Person.EmailAddress table and ensuring that they matched the original number.

The restored database now assumed the role of the main production database, owing to the simulated data loss. The previous production database was then deleted.

This process is an important practice in order to ensure that unforseen data loss or corruption is safeguarded against, and provides an experience of how to recover data in the event of such an error.

## Geo-Replication and Failover
A geo-replication procedure was set up for the production Azure SQL database, to act as a synchronised replica of the primary production database. This was carried out on the Azure SQL Dashboard by selecting the primary database, and creating a replica by clicking the 'Replicas' button under the Data Management section in the menu on the left of the screen, and following the process to create a new replica. The details for the replica were filled in, and the SQL authentication method was chosen. Once this was done, the replication process was started by clicking the 'Review + Create' followed by the 'Create' button. The replica was set to a distinct server in the US, as opposed to the primary database. This step minimised any shared risk in the event of data loss from any one physical centre by creating a new redundant copy in a different geographical location. This process was verified by locating the replica database in the resource page.

Once the geo-replication was carried out successfully, a planned failover process was implemented by switching the database to the secondary region. The Failover Group was added by by selecting 'Failover Groups' under the Data Management section. Then, a Failback was done, moving it back to the primary database, demonstrating the cyclical nature of the fallback process.

## Microsoft Entra ID Integration:
For the last phase of the project, Microsoft Entra ID authentication was enabled for the Azure SQL server hosting the production database, thereby allowing users to authenticate the Entra ID credentials. Following this, an Entra ID administrator was chosen to have control over access and user management, by navigating to the SQL Server under the Security section, and clicking Microsoft Entra -> Set Admin -> Users and setting the user as the administrator for the database. The Entra ID credentials were validated by using them to establish a connection to the server within Azure Data Studio, by adding an account and using the Universal MFA support authentication method.

Following this, a new user was created, to act as the DB Reader user, which was then assigned with the db_datareader role providing read-only privileges. This was accomplished by connecting to the database with the Entra ID details, opening a new query and executing the following lines of code:

```
CREATE USER [DB_Reader@yourdomain.com] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [DB_Reader@yourdomain.com];
```

The integration of Microsoft ENtra Directory is advantageous since it provides a more organised manner to manage access to the data. The db_datareader role is provided only with read-only priviliges, which is useful for working in larger groups as it restricts the permission to edit crucial information in the database, thereby protecting against unintended data loss or corruption. 
