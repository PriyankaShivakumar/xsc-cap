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
<img src="images\TAM.png" width="600" height="400">
</p>

## Requirements
- XSC on-premise database source system with the [HCO_DEMOCONTENT](https://github.com/SAP-samples/hana-shine/releases/download/v2.5.0/HCO_DEMOCONTENT-1.205.0.tgz) delivery unit.
- SAP Business Technology Platform subaccount with `SAP Hana Cloud` and `SAP Hana Schemas and HDI Containers` service instances .
- SAP Business Application Studio Subscription.
- SAP Cloud Connector

## Where to Start
We have successfully migrated the HCO_DEMOCONTENT sample delivery unit using the SAP HANA Application Migration Assistant. The path followed for this Migration involves the below steps:

1. Install and Configure the SAP Cloud Connector.
2. Setup an SAP BTP Destination to connect to the source system.
3. Create a SAP Business Application Studio Devspace with the SAP HANA Application Migration Assistant Extension installed.
4. Migrate using the SAP HANA Application Migration Assistant.
5. Post Migration Changes.
6. Deployment of the Migrated database artifacts.

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
	<img src="images\dest1.png" width="600" height="400">
</p>

4. Select the subaccount where the source database is located, then add a service channel under 'on-prem to cloud' using the following details:  
	
   - **Type**: HANA Database
	
   - **HANA Instance Name**: < DB/Schema ID >
	
   - **Local Instance Number**: Input any two-digit number between 00 and 09. This number is used to compute the port number needed to access the SAP instance in the Hana Cloud. The local port is calculated from the local instance number (3<n<15). For example, if the number is 7, then the local port would be 30715.
	
   - **Connections**: 1

<p align="center">
	<img src="images\dest2.png" width="600" height="400">
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
	<img src="images\dest3.png" width="600" height="400">
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
	<img src="images\dest4.png" width="600" height="400">
</p>

## Step-3: Create a SAP Business Application Studio Devspace with the SAP HANA Application Migration Assistant Extension installed  
	
1. In the sub-account where you created the destination, establish a subscription to SAP Business Application Studio (BAS).

2. Open the BAS from the subscription and select "Create Dev Space". Assign a desired name to your Dev Space and select the "Full Stack Cloud Application" type. Then, choose the `SAP HANA Application Migration Assistant` Extension to help with migration, as well as the `SAP Hana Tools` Extension which will be required later for deployment. Finally, click on "Create Dev Space".
   
3. Wait for the status of your newly created Dev Space to change to "Running". Once it's running, you can open it by clicking on the name of the Dev space that you just created.
   
4. Navigate to the folder by clicking on File -> Open Folder. Enter the path `/home/user/projects/` and click on OK.
   
5. Once the folder opens, you can select the SAP HANA Application Migration Assistant from the Command Palette (You can access the Command Palette from View -> Command Palette).

## Step-4: Migrate using the SAP HANA Application Migration Assistant

1. Open the the Command Palette and type "SAP HANA Application Migration Assistant" and select the command when it appears.
	
2. When the Migration Assistant Wizard opens, select the migration path. Since we are migrating from XSC to CAP, select `XSC to CAP` as your migration path.		

<p align="center">
  <img width="536" alt="HomeScreen" src="images\homescreen.png">
</p>


3. In the Data Source page of the wizard, choose the destination you previously created from the dropdown menu. 
	
<p align="center">
  <img width="544" alt="DestinationList" src="images\destList.png">
</p>

4. Enter the user credentials for the SAP HANA Database Migration User - username and password - into their respective fields. Hit the login button to authorize these credentials.
	
<p align="center">
<img width="545" alt="LoginPage" src="images\login.png">
</p>

5. To proceed, click on the Next button.  
		
<p align="center">
<img width="300" alt="optionEnabled" src="images\loginenabled.png" >
</p>

6. Select the Source system type.

<p align="center">
<img width="300"  src="images\selectType.png">
</p>

7. In the "Migration Options" page, select "Delivery Unit" as your source type from the drop-down menu.

<p align="center">
  <img width="524" alt="selectType" src="https://github.com/PriyankaShivakumar/xsc-cap/assets/159874418/eb2aa3d1-0efd-4761-bfa2-bee1a8c49efc">
</p>

7. Enter the name of your Source Delivery Unit - in this case, it would be `HCO_DEMOCONTENT`.	

<p align="center">
	<img width="587" alt="DU1" src="https://github.com/PriyankaShivakumar/xsc-cap/assets/159874418/0886d6e8-2cd7-4ae3-a8f3-24e1589b60ac">
</p>

8. Choose the target directory. This is where the migration results will be stored.
   
   **Note:** Ensure that the directory you select is a sub-directory of `/home/user/projects`.	

<p align="center">
	<img width="586" alt="DU2" src="https://github.com/PriyankaShivakumar/xsc-cap/assets/159874418/a1186e8a-8b9c-4daa-b857-1208706d0805">
</p>

9. Specify a unique name for the Target Folder, where the migration results will be saved. Once you've entered the name, click on Finish.

<p align="center">
	<img width="588" alt="DU3" src="https://github.com/PriyankaShivakumar/xsc-cap/assets/159874418/da36d176-1f91-4393-9ed8-a824b11bc4cf">
</p>

10. Once you see the pop-up notification at the bottom right corner of your screen, it means that the migration process is underway. This notification will keep you updated on all the steps that follow. At the end of the process, a CAP project with the revised database artifacts will be created. Additionally, a `report.html` file will be generated within the project. This file contains detailed information about your project's migration.

<p align="center">
	<img width="290" alt="end2" src="https://github.com/PriyankaShivakumar/xsc-cap/assets/159874418/5db3148a-4efd-433f-9d82-6d04876b4092">
</p>

## Step-5: Post Migration Changes
Once the project is created, there are some adjustments we need to make manually as these are not currently handled by the Assistant.
 1. If your project contains any files from a different schema, these need to be migrated before migrating the current Delivery Unit and included in this project. If this can't be done immediately, you can remove them for the time being. To utilize objects from other containers, please refer to the HANA Cloud help documentation and configure accordingly.
    
    For the HCO_DEMOCONTENT project, make the following changes:
    - Delete the uis folder from db/cfg
    - Delete `synonym-grantor-service.hdbgrants` and `synonym-grantor-service.hdbsynonymconfig` from db/cfg
    - Delete `synonym-grantor-service.hdbsynonym` from db/src/uis/db
 2. In order to access objects from other public schemas, you will need to either create a new hdbsynonym file or modify an existing one.
    For the HCO_DEMOCONTENT project, edit the `db/src/synonym-grantor-service.hdbsynonym` file with the following configuration:
    ```
    {
    	"SAP_HANA_DEMOCONTENT_EPM_DUMMY": {
        	"target": {
        		"schema": "SYS",
        		"object": "DUMMY"
        	}
   	},
    	"SAP_HANA_DEMOCONTENT_EPM_M_TIME_DIMENSION": {
        	"target": {
        		"schema": "_SYS_BI",
        		"object": "M_TIME_DIMENSION"
        	}
    	},
    	"M_TIME_DIMENSION":{
        	"target": {
        		"schema": "_SYS_BI",
        		"object": "M_TIME_DIMENSION"
        	}
    	}
    }
    ```
 3. In addition, please take the time to clear out unused role names from the `default_access_role.hdbrole` file located in the db/src/defaults folder. If there are specific roles required for your project, ensure to add them as needed.
    For the HCO_DEMOCONTENT project, Remove `SAP_HANA_DEMOCONTENT_EPM_MIGRATION_ALL_ANALYTIC_PRIV` role under names. 
 4. The assistant will modify the name of the artifacts in your project. Therefore, ensure to update the references to these artifacts accordingly.
    For the HCO_DEMOCONTENT project, you'll need to adjust the references for entities under the `currencyConversionTables` tag. This is located in the `db/src/models/PURCHASE_COMMON_CURRENCY.hdbcalculationview` file. Make the changes as follows:
    ```
    <currencyConversionTables rates="SAP_HANA_DEMOCONTENT_EPM_DATA_CONVERSIONS_TCURR" configuration="SAP_HANA_DEMOCONTENT_EPM_DATA_CONVERSIONS_TCURV" prefactors="SAP_HANA_DEMOCONTENT_EPM_DATA_CONVERSIONS_TCURF" notations="SAP_HANA_DEMOCONTENT_EPM_DATA_CONVERSIONS_TCURN" precisions="SAP_HANA_DEMOCONTENT_EPM_DATA_CONVERSIONS_TCURX"/>
    ```
 5.  Unused configurations should be removed from hdbrole files, or these files should be adjusted to add supported options.
     For the HCO_DEMOCONTENT project, make the following alterations:
     - In `db/src/roles/User.hdbrole`, eliminate the following unused configurations:
       ```
       {
    	 "reference": "_SYS_BIC",
    	 "privileges": [
       		"EXECUTE",
        	"SELECT"
    	 ]
       },
       {
    	 "reference": "_SYS_REPO",
    	 "privileges": [
        	"EXECUTE",
        	"SELECT"
    	 ]
       },
       {
    	 "reference": "_SYS_RT",
    	 "privileges": [
        	"SELECT"
    	 ]
       }
       ```
       **Reason**: During the deployment step, access permissions will be granted by executing certain SQL commands.
       
     - In `db/src/roles/Admin.hdbrole`, eliminate the following unused configurations:
       ```
       {
    	 "name": "REPOSITORY_REST",
    	 "type": "PROCEDURE",
    	 "privileges": 
         [
            "EXECUTE"
         ]
       }
       ```
       **Reason**: Roles are not needed for executing procedures in the same container. Instead, you can add authorization based on users in CAP.
     - Alter `db/src/roles/Admin.hdbrole` by replacing the existing schema privileges and adding schema analytic privileges:
       ```
       "schema_privileges": [
   	{
      		"reference": "< Schema Name >",
      		"privileges": [
         		"SELECT METADATA",
         		"SELECT CDS METADATA",
         		"SELECT",
         		"INSERT",
         		"EXECUTE",
         		"DELETE",
         		"UPDATE",
         		"CREATE TEMPORARY TABLE"
      		]
   	}
	],
       "schema_analytic_privileges": [
   	{
      		"schema_reference": "< Schema Name >",
      		"privileges": [
         		"SAP_HANA_DEMOCONTENT_EPM_MODELS_AP_SALES_ORDER_6",
         		"SAP_HANA_DEMOCONTENT_EPM_MODELS_AP_SALES_ORDER",
         		"SAP_HANA_DEMOCONTENT_EPM_MODELS_AP_SALES_ORDER_1",
         		"SAP_HANA_DEMOCONTENT_EPM_MODELS_AP_SALES_ORDER_2",
         		"SAP_HANA_DEMOCONTENT_EPM_MODELS_AP_SALES_ORDER_4",
         		"SAP_HANA_DEMOCONTENT_EPM_MODELS_AP_SALES_ORDER_12",
         		"SAP_HANA_DEMOCONTENT_EPM_MODELS_AP_SALES_ORDER_3",
         		"SAP_HANA_DEMOCONTENT_EPM_MODELS_AP_SALES_ORDER_9",
         		"SAP_HANA_DEMOCONTENT_EPM_MODELS_AP_PURCHASE_ORDER_2",
         		"SAP_HANA_DEMOCONTENT_EPM_MODELS_AP_SALES_ORDER_10",
         		"SAP_HANA_DEMOCONTENT_EPM_MODELS_AP_PURCHASE_ORDER_PROD_CAT_1",
         		"SAP_HANA_DEMOCONTENT_EPM_MODELS_AP_SALES_ORDER_16",
         		"SAP_HANA_DEMOCONTENT_EPM_MODELS_AP_PURCHASE_ORDER_3",
         		"SAP_HANA_DEMOCONTENT_EPM_MODELS_AP_PURCHASE_ORDER_PROD_CAT_1_1054430",
         		"SAP_HANA_DEMOCONTENT_EPM_MODELS_AP_SALES_ORDER_5",
         		"SAP_HANA_DEMOCONTENT_EPM_MODELS_AP_PURCHASE_ORDER_1",
         		"SAP_HANA_DEMOCONTENT_EPM_MODELS_AP_SALES_ORDER_7",
         		"SAP_HANA_DEMOCONTENT_EPM_MODELS_AP_PURCHASE_ORDER_PROD_CAT_2",
         		"SAP_HANA_DEMOCONTENT_EPM_MODELS_AP_PURCHASE_ORDER",
         		"SAP_HANA_DEMOCONTENT_EPM_MODELS_AP_PURCHASE_ORDER_PROD_CAT",
        	 	"SAP_HANA_DEMOCONTENT_EPM_MODELS_AP_SALES_ORDER_11",
         		"SAP_HANA_DEMOCONTENT_EPM_MODELS_AP_SALES_ORDER_13",
         		"SAP_HANA_DEMOCONTENT_EPM_MODELS_AP_SALES_ORDER_15",
         		"SAP_HANA_DEMOCONTENT_EPM_MODELS_AP_SALES_ORDER_8",
         		"SAP_HANA_DEMOCONTENT_EPM_MIGRATION_ALL_PRIV",
        	 	"SAP_HANA_DEMOCONTENT_EPM_MODELS_AP_SALES_ORDER_14"
      		]
   	}
	],
       ```
       **Reason**: The modifications in the hdbrole file are needed to access calculation views with analytic privileges.
 6. Assign the permission to users with the admin role for accessing the schema.
    For the HCO_DEMOCONTENT project, create an `Admin.hdbroleconfig` file in the `db/src/roles/` directory. The file should contain the following configuration:
    ```
    {
   	"SAP_HANA_DEMOCONTENT_EPM_ROLES_ADMIN": {
      		"< Schema Name >": {
         		"schema": "< Schema Name >"
      		}
   	}
    }
    ```
 7. Adjust SQL syntax in procedures. For instance, "UPDATE FROM" should be changed to "MERGE INTO", and "TRUNCATE" statements should be replaced with "DELETE FROM" statements.
 8. Currently, changes to Flowgraph, Reptask, and Replication artifacts are not covered. You will need to modify these manually. Unsupported types and functions in the calculation view such as "CE_FUNCTION", "CACHE", etc., need to be noted. Please refer to the [HANA Cloud Documentation](https://help.sap.com/docs/hana-cloud/sap-hana-cloud-overview-guide/sap-hana-cloud-overview-guide) for more details on how to handle these.
    
## Step-6: Deployment of the Migrated database artifacts.

1. In your dev space, the database connection and artifacts of your project will be visible under the "SAP HANA Projects" section.
   
2. Next, log in to your Cloud Foundry account using the following steps:

   - Open a new terminal in the Business Application Studio
   - Run the command `cf login -a < API_URL >` and input your username and password

3. For the Database Connection of the project, click "Bind" and then select "Bind to an HDI Container" and finally select the HDI container created in your SAP BTP Space. Once successfully bound, you will see a .env file with the VCAP services created in the db folder of your project.
   
4. Inside .env, extract the current schema value from VCAP services - this is your schema name. Replace < Schema Name > with this value in the `Admin.hdbroleconfig` and `Admin.hdbrole` files.

5. Click the "Open HDI Container" button to open the database explorer.
   
6. Open an SQL console with your DBADMIN user or with admin privileges.
   
7. Run the following query in SAP HANA Cloud to grant access: <Schema Name> should be replaced with your specific schema name.
   ```
   GRANT SELECT ON SCHEMA "_SYS_BI" TO "< Schema Name >#OO";

   GRANT SELECT ON "_SYS_BI"."M_TIME_DIMENSION" TO "< Schema Name >#OO";

   GRANT UPDATE ON "_SYS_BI"."M_TIME_DIMENSION" TO "< Schema Name >#OO" WITH GRANT OPTION;

   GRANT INSERT, SELECT, UPDATE ON "_SYS_BI"."M_TIME_DIMENSION" TO "< Schema Name >#OO" WITH GRANT OPTION;

   GRANT SELECT ON SCHEMA "_SYS_BI" TO "< Schema Name >#OO" WITH GRANT OPTION;
   ```
8. To deploy your application, select the "Deploy" button located in the "SAP HANA Projects" section.

## Known Issues in SAP HANA Application Migration Assistant
- If the package name provided does not exist in the source system, the migration process will still continue without any disruption. In this case, a template project without any artifacts will be created.
- In the SAP Hana Migration Assistant, even if you change your password after a successful login, it will not update in the environment even though it appears updated in the user interface. The Assistant retrieves it from the environment and the Migration proceeds without issue. If you wish to confirm the password change, after altering the password field, simply click the login button. This will update the password in the environment.

## Features that are currently out of scope in SAP HANA Application Migration Assistant:

1. Converting xsodata into cap service definition
   
2. Converting “xsjs”,”xsjslib” into cap nodejs
   
3. Creating proxy cds for cross container schema
   
4. Following Artifacts are not currently supported '.hdbreptask', '.hdbvirtualtable', '.hdbflowgraph'

## How to Obtain Support

In case you find a bug, or you need additional support, please open an issue here in [GitHub](https://github.wdf.sap.corp/XSA-Migration/xs-migration-bas-extn/issues).

## License

This extension is provided under the terms of the [SAP Developer License Agreement](https://tools.hana.ondemand.com/developer-license-3_2.txt).
