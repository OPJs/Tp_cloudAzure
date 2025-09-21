## Déterminer l'algorithme de chiffrement🌞

On évite RSA car :

Les tailles de clé recommandées sont devenues très grandes (>= 3072 bits) → performances médiocres.

Ed25519 et ECDSA sont aujourd’hui considérés plus sûrs et rapides

sources: https://goteleport.com/blog/comparing-ssh-keys/


## B. PAIRES DE CLES SSH🌞

```powershell
PS C:\Users\stephane> ssh-keygen -t ed25519
Generating public/private ed25519 key pair.
Enter file in which to save the key (C:\Users\stephane/.ssh/id_ed25519): toto
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in toto
Your public key has been saved in toto.pub
The key fingerprint is:
SHA256:Cf5JdBNvnk9KSzgHglOuGF3KszS+JbIOT7iQFXACnyA stephane@DESKTOP-O34FVP0
The key's randomart image is':
+--[ED25519 256]--+
|E .     o .      |
|.* . o *   o     |
|  + . @ + + o    |
|   . * O + * .   |
|  . o * S o * .  |
| o . o * . = =   |
|o o o . o   o .  |
| . *             |
|  . o            |
+----[SHA256]-----+
PS C:\Users\stephane>

````



## C. Agent SSH 🌞

### Étape 1 — Vérifier l’agent SSH
Sous Windows, l’agent SSH est fourni par le service `ssh-agent`.  
Je vérifie d’abord s’il existe et son état :

```powershell
Get-Service ssh-agent



Status   Name               DisplayName
------   ----               -----------
Stopped  ssh-agent          OpenSSH Authentication Agent

````

### Étape 2 — active le service et le rend l'activation automatique au demerage
```powershell
Set-Service -Name ssh-agent -StartupType Automatic
Start-Service ssh-agent
````

### Étape 3 on vérifie
```powershell
Get-Service ssh-agent


Status   Name               DisplayName
------   ----               -----------
Running  ssh-agent          OpenSSH Authentication Agent
````

### Étape 4 on ajoute la clé à l'agent
```powershell


ssh-add $env:USERPROFILE\.ssh\cloud_tp1
````

### Connection à la VM après création dans le azureUi
```powershell


PS C:\Users\stephane\Desktop\Efrei\M1\cloud\Tp_cloudAzure> ssh -i C\Users\stephane\.ssh\cloud_tp1 pierre@4.233.210.139
Warning: Identity file C\Users\stephane\.ssh\cloud_tp1 not accessible: No such file or directory.
The authenticity of host '4.233.210.139 (4.233.210.139)' can't be established.
ED25519 key fingerprint is SHA256:itqji1aZgaRJmYATnQ9fqo4ZOC+49oZ5xYiyhyhgwrU.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 4.233.210.139' (ED25519) to the list of known hosts.
Welcome to Ubuntu 24.04.3 LTS (GNU/Linux 6.11.0-1018-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Tue Sep 16 11:35:22 UTC 2025

  System load:  0.08              Processes:             119
  Usage of /:   5.6% of 28.02GB   Users logged in:       0
  Memory usage: 3%                IPv4 address for eth0: 172.16.0.4
  Swap usage:   0%

Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

pierre@vmAzure:~$ 

```






### CREATION D'UNE VM EN UTILISANT LA COMMANDE AZ
```powershell
PS C:\Users\stephane\Desktop\Efrei\M1\cloud\Tp_cloudAzure> az vm create  --resource-group test --name MaVMSSH --image Ubuntu2204 --admin-username azureuser --ssh-key-values C:\Users\stephane\Desktop\Efrei\M1\cloud\Tp_cloudAzure\tp_1\cloud_tp1.pub --size Standard_B1s 
The default value of '--size' will be changed to 'Standard_D2s_v5' from 'Standard_DS1_v2' in a future release.'
Selecting "northeurope" may reduce your costs. The region you've selected may cost more for the same services. You can disable this message in the future with the command "az config set core.display_region_identified=false". Learn more at https://go.microsoft.com/fwlink/?linkid=222571

{
  "fqdns": "",
  "id": "/subscriptions/a3bd582e-88e4-4000-970e-b3e76914e628/resourceGroups/test/providers/Microsoft.Compute/virtualMachines/MaVMSSH",
  "location": "francecentral",
  "macAddress": "00-22-48-3A-88-04",
  "powerState": "VM running",
  "privateIpAddress": "172.16.0.5",
  "publicIpAddress": "4.178.178.45",
  "resourceGroup": "test"
}
PS C:\Users\stephane\Desktop\Efrei\M1\cloud\Tp_cloudAzure> 
```




CONNECTION avec l'IP PUBLIQUE 

```powershell
PS C:\Users\stephane\Desktop\Efrei\M1\cloud\Tp_cloudAzure> az vm show `
>>   --resource-group test `
>>   --name MaVMSSH `
>>   --show-details `
>>   --query publicIps `
>>   --output tsv
>> 
4.178.178.45
PS C:\Users\stephane\Desktop\Efrei\M1\cloud\Tp_cloudAzure> ssh azureuser@4.178.178.45
The authenticity of host '4.178.178.45 (4.178.178.45)' cant be established.
ED25519 key fingerprint is SHA256:JETrEaCyh8syU2ahgUVZFt+4Oq+2kXhGxE55ynsaAfM.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? y
Please type yes, no or the fingerprint: yes
Warning: Permanently added 4.178.178.45 (ED25519) to the list of known hosts.
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 6.8.0-1031-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Tue Sep 16 14:41:56 UTC 2025

  System load:  0.08              Processes:             105
  Usage of /:   5.4% of 28.89GB   Users logged in:       0
  Memory usage: 29%               IPv4 address for eth0: 172.16.0.5
  Swap usage:   0%

 * Strictly confined Kubernetes makes edge and IoT secure. Learn how MicroK8s
   just raised the bar for easy, resilient and secure K8s cluster deployment.

   https://ubuntu.com/engage/secure-kubernetes-at-the-edge

Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@MaVMSSH:~$  
```


Une fois connecté, prouvez la présence... .du service walinuxagent.service
```powershell
azureuser@MaVMSSH:~$ systemctl status walinuxagent.service 
● walinuxagent.service - Azure Linux Agent
     Loaded: loaded (/lib/systemd/system/walinuxagent.service; enabled; vendor preset: enabled)
    Drop-In: /usr/lib/systemd/system/walinuxagent.service.d
             └─10-Slice.conf
             /run/systemd/system.control/walinuxagent.service.d
             └─50-CPUAccounting.conf, 50-MemoryAccounting.conf
     Active: active (running) since Tue 2025-09-16 13:59:47 UTC; 4 days ago
   Main PID: 760 (python3)
      Tasks: 7 (limit: 1009)
     Memory: 37.8M
        CPU: 5min 44.175s
     CGroup: /system.slice/walinuxagent.service
             ├─ 760 /usr/bin/python3 -u /usr/sbin/waagent -daemon
             └─1059 python3 -u bin/WALinuxAgent-2.14.0.1-py3.12.egg -run-exthandlers

Sep 21 07:25:03 MaVMSSH python3[1059]: 2025-09-21T07:25:03.761556Z INFO CollectLogsHandler ExtHandler Starting log collection...
Sep 21 07:25:14 MaVMSSH python3[1059]: 2025-09-21T07:25:14.644228Z INFO CollectLogsHandler ExtHandler Successfully collected logs. Archive size: 270695 b, elapsed time: 10882 ms.
Sep 21 07:25:14 MaVMSSH python3[1059]: 2025-09-21T07:25:14.654267Z INFO CollectLogsHandler ExtHandler Successfully uploaded logs.
Sep 21 07:32:13 MaVMSSH python3[1059]: 2025-09-21T07:32:13.631835Z INFO ExtHandler ExtHandler [HEARTBEAT] Agent WALinuxAgent-2.14.0.1 is running as the goal state agent [DEBUG HeartbeatCounter: 227;Heartbeat>
Sep 21 08:02:08 MaVMSSH python3[1059]: 2025-09-21T08:02:08.149984Z INFO ExtHandler ExtHandler Downloading agent manifest
Sep 21 08:02:14 MaVMSSH python3[1059]: 2025-09-21T08:02:14.201845Z INFO ExtHandler ExtHandler [HEARTBEAT] Agent WALinuxAgent-2.14.0.1 is running as the goal state agent [DEBUG HeartbeatCounter: 228;Heartbeat>
Sep 21 08:25:14 MaVMSSH python3[1059]: 2025-09-21T08:25:14.705540Z INFO CollectLogsHandler ExtHandler Starting log collection...
Sep 21 08:25:25 MaVMSSH python3[1059]: 2025-09-21T08:25:25.351980Z INFO CollectLogsHandler ExtHandler Successfully collected logs. Archive size: 271459 b, elapsed time: 10646 ms.
```


Une fois connecté, prouvez la présence...du service cloud-init.service

```powershell
azureuser@MaVMSSH:~$ cloud-init status
status: done
azureuser@MaVMSSH:~$ systemctl cloud-init.service
Unknown command verb cloud-init.service.
azureuser@MaVMSSH:~$ systemctl status cloud-init.service
● cloud-init.service - Cloud-init: Network Stage
     Loaded: loaded (/lib/systemd/system/cloud-init.service; enabled; vendor preset: enabled)
     Active: active (exited) since Tue 2025-09-16 13:59:47 UTC; 4 days ago
   Main PID: 510 (code=exited, status=0/SUCCESS)
        CPU: 1.635s

Sep 16 13:59:47 MaVMSSH cloud-init[514]: |...+ + =         |
Sep 16 13:59:47 MaVMSSH cloud-init[514]: |+ = = B o        |
Sep 16 13:59:47 MaVMSSH cloud-init[514]: | * X + =         |
Sep 16 13:59:47 MaVMSSH cloud-init[514]: |+ = E o S        |
Sep 16 13:59:47 MaVMSSH cloud-init[514]: |.* o =           |
Sep 16 13:59:47 MaVMSSH cloud-init[514]: |=.. X .          |
Sep 16 13:59:47 MaVMSSH cloud-init[514]: |.o o.B           |
Sep 16 13:59:47 MaVMSSH cloud-init[514]: |   .+o.          |
Sep 16 13:59:47 MaVMSSH cloud-init[514]: +----[SHA256]-----+
Sep 16 13:59:47 MaVMSSH systemd[1]: Finished Cloud-init: Network Stage.
```