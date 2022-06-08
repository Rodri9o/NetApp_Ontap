# Ontap Automation Account

Create an automation user following the principle of least privilege

Go to https://<ONTAP_URL>/sysmgr/admin/clusterusers

Created "ansible" user  limited to http and api access ( web UI nor SSH console access are allowed)

![image](https://user-images.githubusercontent.com/9559297/172509558-e3883892-19c5-4c32-b8ee-86c3613989ed.png)


Created and assigned "ansible" role  to said user restricted to  volume qtree  and vserver cifs share commands.

![image](https://user-images.githubusercontent.com/9559297/172509806-3d172ce8-6f60-4d8c-a7d3-4a80fcb40ea2.png)

_enter additional commands (orange rectangle) as needed_


Enter "ansible" user credentials in Tower as per this publication https://netapp.io/2020/01/10/tower-credential-type-credential/

![image](https://user-images.githubusercontent.com/9559297/172509932-cf509e84-070a-4d6f-affb-7010e533ccad.png)
