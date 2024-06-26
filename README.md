# SAP HANA Application Migration Assistant

Tool for migrating XS Classic application to XS Advanced Programming Model or Cloud Application Programming Model.  

## XSC to XSA:  

The SAP HANA Application Migration Assistant converts the source XS Classic application which is packaged as a Delivery Unit into XS Advanced application which also includes conversion of synchronous XSJS to async-XSJS.

## XSC to CAP:  

The SAP HANA Application Migration Assistant converts the source XSC application  to a CAP application with SAP HANA Cloud as a database where the Source XSC Repository  artefacts are converted to the corresponding target CAP artefacts.


## Architecture

### Solution Diagram

<p align="center">
<img src="https://github.wdf.sap.corp/storage/user/131107/files/108b8b5a-2ac4-41fb-be56-bef892f660f5" width="600" height="400">
</p>

## Prerequisites

* SAP Cloud Connector
* SAP BTP account
* SAP Business Application Studio

## Configuration

### Install SAP Cloud Connector

1. Install Cloud Connector to your local system from [Cloud-Connector](https://tools.hana.ondemand.com/#cloud)

2. Refer to the help documentation https://help.sap.com/docs/connectivity/sap-btp-connectivity-cf/installation?locale=en-US 
  	   for the installation and prerequisites for cloud connector setup.  
  
3. Once the cloud connector is installed, open it in the browser on the port setup as https://localhost:<port-no>/ and login with the correct 
	   credentials
	
4. Click on connector in the left menu and add your subaccount which has the source database and also add the subaccount where the bas 
	   subscription is created and add the subaccount details  

<p align="center">
<img src="https://github.wdf.sap.corp/storage/user/128039/files/1db1f985-178a-4b85-adce-71417fcec8b1" width="500" height="300">
</p>

5. Choose the subaccount where the source db is present and add a service channel under on-prem to cloud with the following details:  
	
   1. Type: HANA Database  
	
   2. HANA Instance Name: <DB/Schema ID>  
	
   3. Local Instance Number: any number from 00 to 09( this is a double digit number which is used to compute the port number to access sap 			instance in hana cloud, local port is derived from local instance no 3<n<15, for eg if no=7, then local port = 30715)  
	
   4. Connections: 1  


<p align="center">
<img src="https://github.wdf.sap.corp/storage/user/128039/files/86f3b93e-e155-4ee4-8f31-49000d6b28db" width="500" height="350">
</p>


6. In the BTP cf account where the BAS subscription is created, choose cloud to on prem and add mapping with the following details:  
	
   1. Back-end Type: SAP Hana  
	
   2. Protocol: TCP  
	
   3. Internal Host: localhost  
	
   4. Internal Port: <portno>(derived from local instance number)  
	
   5. Virtual Host: myvirtualhost  
	
   6. Virtual Port: same as internal port  
	
   7. Principal Type: None  
		
<p align="center">
<img src="https://github.wdf.sap.corp/storage/user/128039/files/3ce89ee6-9929-4ab8-8a78-8f6b0b85b25d" width="500" height="350">
</p>
	


### Setup BTP Destination
  
1. Go to the BTP cf subaccount and select destination from Connectivity from the left menu pane and create a new destination with the following  	    details: 

   1. Name: <destination name>

   2. Type: HTTP

   3. URL: "https://<internal-host>:<internal-port-no>/" 

   4. ProxyType: on-premise

   5. Authentication: Basic Authentication 

   6. Locationid: Location id as mentioned in cloud connector

   7. User and Password: Hana Db login credentials 

 And the following additional properties: 

   1. HTML5.DynamicDestination : true 

   2. WebIDEEnabled : true 

   3. WebIDEUsage : xs_hdb 
	
<p align="center">
<img src="https://github.wdf.sap.corp/storage/user/128039/files/f1d3639e-15c9-4692-9e55-21bb2fd06fc2" width="500" height="300">
</p>
	


### BAS Configuration:  
	
1. In the sub-account that the destination is present, create a subscription to SAP Business Application Studio.  
	

2. Open BAS from this subscription and select “Create DevSpace”, give it a name and choose the appropriate application type and then select SAP 	   HANA Application Migration Assistant Extension.  


3. Open the DevSpace and create a new workspace, then select the SAP HANA Application Migration Assistant from the Command Palette (View->Command 	     Palette or (Ctrl+Shift+P).  
	

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