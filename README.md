# XS Classic Programming Model To SAP Cloud Application Programming Model Migration Guide
 
The SAP HANA Application Migration Assistant converts the source XS Classic application which is packaged as a Delivery Unit to a CAP application with SAP HANA Cloud as a database where the Source XSC Repository artifacts are converted to the corresponding target CAP artifacts.

## Introduction
SAP HANA Interactive Education or SHINE is a demo application that is packaged as [HCO_DEMOCONTENT](https://github.com/SAP-samples/hana-shine/releases/download/v2.5.0/HCO_DEMOCONTENT-1.205.0.tgz) Delivery Unit. It includes the following features:
- **HDI Features:**
  - Table
  - HDBDD Views
  - Sequence
  - Calculation Views
  - Analytical Views
  - Attribute Views
  - Associations
  - Table Functions
  - Synonyms
  - Procedures
  - Spatial Features
  - Local Time Data Generation
  - Index
  - Structured Privilege
  - Analytical Privilege

HCO_DEMOCONTENT follows the XS Classic Programming Model(XSC) and uses SAP HANA on-premise for the database. This article describes the steps to be followed to Migrate this Delivery Unit from XSC to the Cloud Application Programming Model(CAP) with SAP HANA Cloud as the database using the SAP HANA Application Migration Assistant.

### Solution Diagram

<p align="center">
<img src="https://github.wdf.sap.corp/storage/user/131107/files/108b8b5a-2ac4-41fb-be56-bef892f660f5" width="600" height="400">
</p>

## Requirements
- XSC on-premise database source system with the [HCO_DEMOCONTENT](https://github.com/SAP-samples/hana-shine/releases/download/v2.5.0/HCO_DEMOCONTENT-1.205.0.tgz) delivery unit.
- SAP Business Technology Platform subaccount with `SAP Hana Cloud` and `SAP Hana Schemas and HDI Containers` service instances .
- SAP Business Application Studio Subscription.
- SAP Cloud Connector

## Where to Start
We have successfully migrated the HCO_DEMOCONTENT sample delivery unit using the SAP HANA Application Migration Assistant. The path followed for this Migration involves the below steps:

- Install and Configure the SAP Cloud Connector.
- Setup an SAP BTP Destination to connect to the source system.
- Create a SAP Business Application Studio Devspace with the SAP HANA Application Migration Assistant Extension installed.
- Migrate using SAP HANA Application Migration Assistant.
- Post Migration Changes.
- Deployment of the Migrated database artifacts.

#### **Note:** 
#### 1. SAP HANA Application Migration Assistant covers only the migration of the database artifacts from SAP Hana on-premise to SAP Hana Cloud.
#### 2. The migration steps should be tested in a development environment before production.
#### 3. This guide is directed at single-tenant-applications.

## Steps
## Step-1: Install and Configure the SAP Cloud Connector

1. Install the [SAP Cloud Connector](https://tools.hana.ondemand.com/#cloud) on your local system. For the installation and setup of the cloud connector, please refer to this [Documentation](https://help.sap.com/docs/connectivity/sap-btp-connectivity-cf/installation?locale=en-US).  
  
2. After installing the cloud connector, you can access it by opening your web browser and going to `https://localhost:<port-no>/`. Use your credentials to log in.
   
3. Once you've successfully logged in, you should set up two connections in the Cloud Connector. The first connection should link to the subaccount with the source database, and the second connection should be for the target subaccount with the SAP Hana Cloud. To establish these connections, click on the connector button in the left menu. Now, enter the necessary details for your subaccount - this includes the Region, Subaccount ID, Display Name, Subaccount User, Password, and Location ID. After entering all the information, click on 'Save'.

<p align="center">
<img src="https://github.wdf.sap.corp/storage/user/128039/files/1db1f985-178a-4b85-adce-71417fcec8b1" width="500" height="300">
</p>

4. Select the subaccount where the source database is located, then add a service channel under 'on-prem to cloud' using the following details:  
	
   - **Type**: HANA Database
	
   - **HANA Instance Name**: < DB/Schema ID >
	
   - **Local Instance Number**: Input any two-digit number between 00 and 09. This number is used to compute the port number needed to access the SAP instance in the Hana Cloud. The local port is calculated from the local instance number (3<n<15). For example, if the number is 7, then the local port would be 30715.
	
   - **Connections**: 1

<p align="center">
<img src="https://github.wdf.sap.corp/storage/user/128039/files/86f3b93e-e155-4ee4-8f31-49000d6b28db" width="500" height="350">
</p>

5. In the SAP Business Technology Platform (BTP) Cloud Foundry account where the Business Application Studio (BAS) subscription is created, select 'Cloud to On-Prem' and add a mapping with the following details:  
	
   - **Back-end Type**: SAP Hana  
	
   - **Protocol**: TCP  
	
   - **Internal Host**: localhost  
	
   - **Internal Port**: < portno > (The port number derived from your local instance number)  
	
   - **Virtual Host**: myvirtualhost  
	
   - **Virtual Port**: This should be the same as your internal port
	
   - **Principal Type**: None  
		
<p align="center">
<img src="https://github.wdf.sap.corp/storage/user/128039/files/3ce89ee6-9929-4ab8-8a78-8f6b0b85b25d" width="500" height="350">
</p>
	
## Step-2: Setup an SAP BTP Destination to connect to the source system
  
Navigate to the BTP Cloud Foundry subaccount and select 'Destination' under 'Connectivity' from the left menu pane. Create a new destination using the following details:
 
 - **Name**: < Destination name >
 - **Type**: HTTP
 - **URL**: `https://<internal-host>:<internal-port-no>/`
 - **ProxyType**: on-premise
 - **Authentication**: Basic Authentication
 - **Locationid**: Location id as mentioned in cloud connector
 - **User and Password**: SAP Hana Database login credentials 

And the following additional properties: 
 - **HTML5.DynamicDestination** : true
 - **WebIDEEnabled** : true
 - **WebIDEUsage** : xs_hdb 
	
<p align="center">
<img src="https://github.wdf.sap.corp/storage/user/128039/files/f1d3639e-15c9-4692-9e55-21bb2fd06fc2" width="500" height="300">
</p>

## Step-3: Create a SAP Business Application Studio Devspace with the SAP HANA Application Migration Assistant Extension installed  
	
1. In the sub-account where you created the destination, establish a subscription to SAP Business Application Studio (BAS).

2. Open BAS from this subscription and select "Create DevSpace". Assign a name to it and choose relevant application type, and then choose the `SAP HANA Application Migration Assistant Extension`. Also Choose `SAP Hana Tools` Extension which is required later for deployment.
   
3. Open the DevSpace you’ve created and create a new workspace. From within this workspace, select the SAP HANA Application Migration Assistant from the Command Palette (You can access the Command Palette from View -> Command Palette).

# SAP HANA Application Migration Assistant

## How to launch Guided Development

	Use Command Palette -> CMD/CTRL + Shift + P
	
	Type command -> SAP HANA Application Migration Assistant
	
1. Select the Migration Path - XSC->XSA or XSC->CAP.  	
	

<p align="center">
  <img width="536" alt="MicrosoftTeams-image (22)" src="https://github.com/I562818/xsc-cap/assets/159874418/f9af13e0-08f5-44f3-ba27-c511f89e675e">
</p>



2. Configure the Data Source:  
	
   Select your Destination from the dropdown.  
	
	

<p align="center">
  <img width="544" alt="MicrosoftTeams-image (23)" src="https://github.com/I562818/xsc-cap/assets/159874418/7a00a7af-f6db-408a-8b36-9a7d2184cc7f">
</p>





   Enter the Hana DB Credentials for the Technical Username and Password fields and click on login to complete the authentication.

	
<p align="center">
<img width="545" alt="MicrosoftTeams-image (24)" src="https://github.com/I562818/xsc-cap/assets/159874418/373cf1ed-b503-47e5-96e9-1f42d7ac1f3d">
</p>




   Click on Next.  
		

<p align="center">
<img src="https://github.wdf.sap.corp/storage/user/128039/files/a3ee9d97-3ee6-48f8-91c0-6ee76f7f8db8" width="300" height="200">
</p>

	
3. Enter the Source Delivery Unit name.  	

<p align="center">
<img src="https://github.wdf.sap.corp/storage/user/128039/files/c50cbf48-2d80-49cd-9a01-c7dac60bfdc6" width="500" height="300">
</p>
	




4. Choose the target directory where the migration results will be stored.  	

<p align="center">
<img src="https://github.wdf.sap.corp/storage/user/128039/files/61026d83-a977-4879-bb0e-bb6abb2efdcf" width="500" height="320">
</p>



5. Enter the Target Folder name for the migration results folder and click on Finish. 

<p align="center">
<img src="https://github.wdf.sap.corp/storage/user/128039/files/431d9a83-7e13-4383-b995-34967a0a4c68" width="550" height="400">
</p>




6. You will now see a pop-up at the bottom right of your window mentioning that the migration has started and all the respective steps there onward. 


<p align="center">
<img src="https://github.wdf.sap.corp/storage/user/128039/files/47e72bfc-a1bf-47a4-baff-67087e3278e0" width="350" height="220">
</p>

# Limitations

1. Creating proxy cds for “.hdbtable”, “.hdbview”, “.hdbcalculationview”

2. Converting xsodata into cap service definition

3. Converting “xsjs”,”xsjslib” into cap nodejs

4. Creating proxy cds for cross container schemas

5. Unsupported datatypes in calculation views ex: the date() function is not supported in SAP HANA Cloud, hence it needs to be converted into daydate()

6. SQL syntax changes in procedure is not integrated ex: UPDATE FROM has to be changed to MERGE INTO, TRUNCATE statement has to be changed to DELETE FROM statement

7. Flowgraph and Replication Artefacts Changes are not supported


## How to Obtain Support

In case you find a bug, or you need additional support, please open an issue here in [GitHub](https://github.wdf.sap.corp/XSA-Migration/xs-migration-bas-extn/issues).

## License

This extension is provided under the terms of the [SAP Developer License Agreement](https://tools.hana.ondemand.com/developer-license-3_2.txt).
