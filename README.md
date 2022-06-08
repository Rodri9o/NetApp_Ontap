# NetApp Ontap

Ansible [NetApp.Ontap Collection](https://docs.ansible.com/ansible/latest/collections/netapp/ontap/index.html#netapp-ontap) Related Notes.

<br />

## Create Ontap Account for Ansible Automation 
https://github.com/Rodri9o/NetApp_Ontap/blob/main/automation_account/ansible.md


## Install a CA Signed Certificate on Ontap System Manager

<small>Source: [Install CA Signed Cert on System Manager](https://kb.netapp.com/Advice_and_Troubleshooting/Data_Storage_Software/ONTAP_OS/How_to_install_a_Certificate_Authority_(CA)_signed_certificate_in_ONTAP_for_System_Manager_use)</small>


- Obtain System Manager Vserver 

    `vserver show -type admin`
    
    (we'll use _cluster1_ as an example)

- Install Certificate

  `security certificate install -vserver cluster1 -type server`

    When prompted, copy & paste the certificate, private key, intermediate cert and root cert one at a time.
    
    Make sure they are in PEM format.
    
    Certificate type can be a wildcard or FQDN specific.

- Verify Installation

  `sec ssl show -vserver cluster1`


Caveats:
- Depending upon your particular CA there might be multiple intermediate certificates as well as a root certificate in use. 
- Be certain to enter them all, one at a time, in the specified order below.  **Do not use a chain formatted certificate here as it will not work.**
- If the certificate chain is in .p7b format export each certificate within the chain to "Base-64 encoded X.509 (.CER)" format to get the certificate contents to add into the ONTAP command. 

<br/>

## Renew Expired Self-signed Certificate on Ontap System Manager

<small>Source: [Install Self Signed Cert on System Manager](https://kb.netapp.com/Advice_and_Troubleshooting/Data_Storage_Software/ONTAP_OS/How_to_install_a_Certificate_Authority_(CA)_signed_certificate_in_ONTAP_for_System_Manager_use)</small>

- Obtain System Manager Vserver 

    `vserver show -type admin`
    
    (we'll use _cluster1_ as an example)

- Copy the Issuing CA, Common Name and Serial Number

  `sec ssl show -vserver cluster1 `

   (we'll use _16155D05785D389D_ as an example)

- Advance mode

  `set advanced`

- Disable SSL Service

  `ssl modify -vserver cluster1 -server-enabled false`

- Erase cert

  `security certificate delete -vserver cluster1 -common-name cluster1 -ca cluster1 -type server -serial 16155D05785D389D`

- Gen 10 year cert

  `security certificate create -vserver cluster1 -common-name cluster1 -type server -size 2048 -country US -expire-days 3650 -hash-function SHA256`

- Verify

  `security certificate show -vserver cluster1 -common-name cluster1 -instance`

- Enable SSL

  `ssl modify -vserver cluster1 -server-enabled true`

<br/>

## Ansible Error

<small>Source: [Ansible_is_not_able_to_connect_to_ONTAP](https://kb.netapp.com/Advice_and_Troubleshooting/Data_Storage_Software/ONTAP_OS/Ansible_is_not_able_to_connect_to_ONTAP)</small>


__Error:__

`An exception occurred during task execution. To see the full traceback, use -vvv. The error was: netapp_lib.api.zapi.zapi.NaApiError: NetApp API failed. Reason - Unable to connect:(ConnectionRefusedError(111, 'Connection refused'),)`

<br>


__Cause:__

`Ansible is connecting via port 80, not port 443`

http is [disabled by default](https://library.netapp.com/ecmdocs/ECMP1196993/html/GUID-45B42EA2-E12A-45F4-9357-E441C82B2430.html) on NetApp Ontap.

<br>


__Solution:__

- To run Ansible modules over https the following lines need to be added to each modules arguments

    `https: true`
    
    `validate_certs: false`

- Alternate Workaround: Enable http access in ONTAP

  `cluster1::> set advanced`
  
  `cluster1::*> system service web modify -http-enabled true`

