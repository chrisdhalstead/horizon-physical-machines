# horizon-physical-machines
App to quickly add Physical Machines into VMware Horizon Manual Pool

**<u>There is no support for this tool - it is provided as is.</u>**

Please provide any feedback directly to me - my contact information: 

Chris Halstead - Staff Architect, VMware  
Email: chalstead@vmware.com  
Twitter: @chrisdhalstead  

March 15, 2020

Tons of thanks to Andrew Morgan @andyjmorgan for collaboration on this process.

#### Latest Version

- 1.5 - March 18, 2020
  - Fixed Issue when creating pool and immediately importing into it
    - 400 errors
  - Optimized create manual pool flow
  - Added the name of the call that is made to the API to message box and log for easier troubleshooting
- <a id="raw-url" href="https://raw.githubusercontent.com/chrisdhalstead/project/master/filename">Download FILE</a>

#### Demo / Walkthrough

Opens in YouTube

[! (images/Login.PNG)](https://www.youtube.com/watch?v=tqlcWbuBRrs)

#### Overview

This app is used to quickly add physical machines that have a Horizon agent installed and registered to a connection server to a manual pool and entitle the desktop and pool.   The app reads from a .csv file and checks to see if the specific machine is available and if so it adds it to the selected pool.  The app can be used to create a manual pool to use with unregistered machines.  

if you are processing many machines, it would be recommended to setup a dedicated connection server in the pod if you can to run these processes against so other servers are not affected.  

Please review the logfile that was created after an import - there are lots of details on what was done

#### Features

- All connections to Horizon via REST 
- Certificate Validation
- Multi-threaded
- Logging and Error Trapping
- Manual Pool Creation
- Shows live progress

#### Pre-Requisites

- .NET Framework 4.6.1 or later
- Windows Operating System
- Horizon 7.7 or later (Official Horizon support added - prior versions may work just not supported)
- Admin Credentials to Horizon Connection Server

#### Usage

1. Download and Run `HorizonUnmanagedMachines.exe` from this repository

2. Enter the Horizon Connection Server FQDN and click Connect

   ![Login](Images/Login.PNG)

   

3. Enter Username, Password and Domain and Click OK - the first time you connect you will be asked to accept the certificate on the Horizon server

   ![cert](Images/cert.PNG)

   

4. If you have no manual pools that are configured for unmanaged machines - you will see the following prompt:

   ![Pool](Images/Pool.PNG)

   

   The app will create a manual pool that is configured for use with unregistered machines.  Click OK to continue.
   
5. Enter a Pool Name, and optionally a display name.  This is what users will see if the Horizon Client.  If you leave it blank, users will see the name of the pool.  Select a default protocol (users will be able to change on the client), select an access group and choose if you would like HTML access and Session Collaboration (Blast only).   Click ok to create the pool.   Click OK twice to confirm pool creation and on the box to show the pool is complete.  

   ![CreatePool](Images/CreatePool.PNG)

   ![PoolCreated](Images/PoolCreated.PNG)

   You will now see the pool under Pool Name - this dropdown will show all manual pools that are configured for unmanaged machines

   In the Horizon Console, you can identify pools created by this tool by looking at the description.![Desc](Images/Desc.PNG)

6. Create .CSV File 

   1. This file should contain physical devices which have been registered with the Horizon Connection Server (See More in "Installing Horizon Agent on Physical" below) and the user to associate with the desktop.  This user will have a direct entitlement added to the desktop and one added to the pool. the username must be formatted as domain\username.
   2. The format of the CSV file should be as follows:  
      1. Header Line:  Machine,User
      2. Data: mydesktop,domain\user

   <img src="Images/csv.PNG" alt="csv" style="zoom:150%;" />Sample of CSV File

7. Import CSV file

   1. Click the "Import Mappings from .CSV button" - select the .CSV and click Open.  There are several validations that happen during the import process.  There is a maximum of 2000 items that can be imported at a time.  2000 is the maximum size of a pool in Horizon, so if you have more that 2000 devices to import them should be broken up into .csv files of less that 2000 each.

   ![import_CSV](Images/import_CSV.PNG)

   ![Imported](Images/Imported.PNG)

   

8. Once you have imported the data you are ready to add the machines to the selected pool and entitle them.  Click "Add Machines and Entitle".  Click yes on the message box to start the process.  The listview will be updated live as action are done in Horizon 

   ![ImportedMachines](Images/ImportedMachines.PNG)

   You will see the success or failure of the following actions:

   *Adding Machine to Pool
   *Adding an entitlement for the user to the desktop
   *Adding an entitlement for the user to the pool	

9. The process is finished - you can view the log file to see exactly what happened.   It is called Horizon_Manual[date].log.  It is located in the %temp% directory.

   ![Log](Images/Log.PNG)

   

10. The actions can also be verified in the Horizon Console

    ![Horizon_Pool](Images/Horizon_Pool.PNG)

    ![Horizon_ents](Images/Horizon_ents.PNG)

    

11. Additional manual pools can be created at any time by clicking the "Create Manual Pool" Button.

#### **Installing Horizon Agent on Physical**

You can use the [tool that Andrew Morgan developed](https://github.com/andyjmorgan/HorizonRemotePCHelperScripts) that installs the Agent and creates the .csv file that this utility needs or use any tool for distributing software such as SCCM.  This app will push the Horizon Agent and install it via PSRemoting and will adjust power policies.  It is in active development and he is adding wake on lan in a future release.  

Install string for installing the Horizon Agent as unmanaged and register to a connection server:

`VMware-Horizon-Agent-x86_64-7.XX.0-XXXX.exe /s /v” /qn VDM_VC_MANAGED_AGENT=0 VDM_SERVER_NAME=<connserver> VDM_SERVER_USERNAME=<domain\username> VDM_SERVER_PASSWORD=<pw>”`

#### Change Log

- 1.0 - March 15, 2020 
  - Initial Release
- 1.1 - March 16, 2020
  - Fixed an issue where the CSV contains the quote character - if found, remove it.
- 1.2 - March 16, 2020
  - Prevent a user from being assigned two machines in a pool
  - Fix some API queries that sometimes caused 400 errors
  - Other UI fixes
- 1.3 - March 17, 2020
  - Fix when usernames are similar (ex chalstead and chalstead-adm) - before it would grab the first one that matched (chalstead in this case) causing issues
  - Cache 2000 domain users to speed up entitlement 
    - 2000 is the maximum that can be returned (no pagination support).  
    - The 7.12 REST API may be able to return more - looking into that as a option
  - Persist connection server name, username and domain name in the registry
  - More logging detail
  - Optimized adding machines and entitlements 
  - UI Fixes
  - Hid Pool Details dialog as results were inconsistent across versions of Horizon 
- 1.4 - March 17, 2020
  - Fixed "An item with the same key has already been added" error
  - Generate the new machine name Base64 value directly without making an expensive API call
    - After added to pool
  - Fixed AD Caching - note:  It will pause for 3 seconds while that query is run
  - If a user is not found, but the desktop is - the desktop will be added to the pool
- 1.5 - March 18, 2020
  - Fixed Issue when creating pool and immediately importing into it
    - 400 errors
  - Optimized create manual pool flow
  - Added the name of the call that is made to the API to message box and log for easier troubleshooting

#### Tips

- Windows 7 may work with the RDP protocol
  - Connecting to View desktops using RDP protocol fails with the error code 2825 
    - https://kb.vmware.com/s/article/1034158
    - https://kb.vmware.com/s/article/67832

