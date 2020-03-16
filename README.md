# horizon-physical-machines
App to quickly add Physical Machines into VMware Horizon Manual Pool



#### Resources:

**Installing Horizon Agent on Physical:**

You can use the tool that Andrew Morgan developed that installs the Agent and creates the .csv file that this utility needs or use any tool for distributing software such as SCCM

`VMware-Horizon-Agent-x86_64-7.12.0-15579917.exe /s /v” /qn VDM_VC_MANAGED_AGENT=0 VDM_SERVER_NAME=<connserver> VDM_SERVER_USERNAME=<domain\username> VDM_SERVER_PASSWORD=<pw>”`



**RDP Error:**

https://kb.vmware.com/s/article/1034158