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


TERRAFORM

```powershell
PS C:\Users\stephane\Desktop\Efrei\M1\cloud\Tp_cloudAzure> cd .\terraform\
PS C:\Users\stephane\Desktop\Efrei\M1\cloud\Tp_cloudAzure\terraform> terraform init
Initializing the backend...
Initializing provider plugins...
- Finding latest version of hashicorp/azurerm...
- Installing hashicorp/azurerm v4.45.0...
- Installed hashicorp/azurerm v4.45.0 (signed by HashiCorp)
Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run terraform init in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
PS C:\Users\stephane\Desktop\Efrei\M1\cloud\Tp_cloudAzure\terraform> 
```






```powershell
PS C:\Users\stephane\Desktop\Efrei\M1\cloud\Tp_cloudAzure\terraform> terraform plan

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # azurerm_linux_virtual_machine.main will be created
  + resource "azurerm_linux_virtual_machine" "main" {
      + admin_username                                         = "jjk"
      + allow_extension_operations                             = (known after apply)
      + bypass_platform_safety_checks_on_user_schedule_enabled = false
      + computer_name                                          = (known after apply)
      + disable_password_authentication                        = (known after apply)
      + disk_controller_type                                   = (known after apply)
      + extensions_time_budget                                 = "PT1H30M"
      + id                                                     = (known after apply)
      + location                                               = "francecentral"
      + max_bid_price                                          = -1
      + name                                                   = "super-vm"
      + network_interface_ids                                  = (known after apply)
      + os_managed_disk_id                                     = (known after apply)
      + patch_assessment_mode                                  = (known after apply)
      + patch_mode                                             = (known after apply)
      + platform_fault_domain                                  = -1
      + priority                                               = "Regular"
      + private_ip_address                                     = (known after apply)
      + private_ip_addresses                                   = (known after apply)
      + provision_vm_agent                                     = (known after apply)
      + public_ip_address                                      = (known after apply)
      + public_ip_addresses                                    = (known after apply)
      + resource_group_name                                    = "test"
      + size                                                   = "Standard_B1s"
      + virtual_machine_id                                     = (known after apply)
      + vm_agent_platform_updates_enabled                      = (known after apply)

      + admin_ssh_key {
          + public_key = <<-EOT
                ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIAT6qFBe4GCDW71H2kuHJBfmsbfmgyZ8bY5U7UnzkjcO stephane@DESKTOP-O34FVP0
            EOT
          + username   = "jjk"
        }

      + os_disk {
          + caching                   = "ReadWrite"
          + disk_size_gb              = (known after apply)
          + id                        = (known after apply)
          + name                      = "vm-os-disk"
          + storage_account_type      = "Standard_LRS"
          + write_accelerator_enabled = false
        }

      + source_image_reference {
          + offer     = "0001-com-ubuntu-server-focal"
          + publisher = "Canonical"
          + sku       = "20_04-lts"
          + version   = "latest"
        }

      + termination_notification (known after apply)
    }

  # azurerm_network_interface.main will be created
  + resource "azurerm_network_interface" "main" {
      + accelerated_networking_enabled = false
      + applied_dns_servers            = (known after apply)
      + id                             = (known after apply)
      + internal_domain_name_suffix    = (known after apply)
      + ip_forwarding_enabled          = false
      + location                       = "francecentral"
      + mac_address                    = (known after apply)
      + name                           = "vm-nic"
      + private_ip_address             = (known after apply)
      + private_ip_addresses           = (known after apply)
      + resource_group_name            = "test"
      + virtual_machine_id             = (known after apply)

      + ip_configuration {
          + gateway_load_balancer_frontend_ip_configuration_id = (known after apply)
          + name                                               = "internal"
          + primary                                            = (known after apply)
          + private_ip_address                                 = (known after apply)
          + private_ip_address_allocation                      = "Dynamic"
          + private_ip_address_version                         = "IPv4"
          + public_ip_address_id                               = (known after apply)
          + subnet_id                                          = (known after apply)
        }
    }

  # azurerm_public_ip.main will be created
  + resource "azurerm_public_ip" "main" {
      + allocation_method       = "Dynamic"
      + ddos_protection_mode    = "VirtualNetworkInherited"
      + fqdn                    = (known after apply)
      + id                      = (known after apply)
      + idle_timeout_in_minutes = 4
      + ip_version              = "IPv4"
      + location                = "francecentral"
      + name                    = "vm-ip"
      + resource_group_name     = "test"
      + sku                     = "Basic"
      + ip_version              = "IPv4"
      + location                = "francecentral"
      + name                    = "vm-ip"
      + resource_group_name     = "test"
      + sku                     = "Basic"
      + location                = "francecentral"
      + name                    = "vm-ip"
      + resource_group_name     = "test"
      + sku                     = "Basic"
      + name                    = "vm-ip"
      + resource_group_name     = "test"
      + sku                     = "Basic"
      + sku_tier                = "Regional"
      + resource_group_name     = "test"
      + sku                     = "Basic"
      + sku_tier                = "Regional"
    }
      + sku                     = "Basic"
      + sku_tier                = "Regional"
    }
      + sku_tier                = "Regional"
    }

  # azurerm_resource_group.main will be created
    }

  # azurerm_resource_group.main will be created
  + resource "azurerm_resource_group" "main" {

  # azurerm_resource_group.main will be created
  + resource "azurerm_resource_group" "main" {
  # azurerm_resource_group.main will be created
  + resource "azurerm_resource_group" "main" {
      + id       = (known after apply)
      + location = "francecentral"
      + name     = "test"
  + resource "azurerm_resource_group" "main" {
      + id       = (known after apply)
      + location = "francecentral"
      + name     = "test"
    }
      + id       = (known after apply)
      + location = "francecentral"
      + name     = "test"
    }
      + name     = "test"
    }

  # azurerm_subnet.main will be created
  + resource "azurerm_subnet" "main" {
    }

  # azurerm_subnet.main will be created
  + resource "azurerm_subnet" "main" {
      + address_prefixes                              = [
          + "10.0.1.0/24",
        ]
      + default_outbound_access_enabled               = true
  + resource "azurerm_subnet" "main" {
      + address_prefixes                              = [
          + "10.0.1.0/24",
        ]
      + default_outbound_access_enabled               = true
      + id                                            = (known after apply)
      + address_prefixes                              = [
          + "10.0.1.0/24",
        ]
      + default_outbound_access_enabled               = true
      + id                                            = (known after apply)
      + name                                          = "vm-subnet"
        ]
      + default_outbound_access_enabled               = true
      + id                                            = (known after apply)
      + name                                          = "vm-subnet"
      + private_endpoint_network_policies             = "Disabled"
      + private_link_service_network_policies_enabled = true
      + id                                            = (known after apply)
      + name                                          = "vm-subnet"
      + private_endpoint_network_policies             = "Disabled"
      + private_link_service_network_policies_enabled = true
      + resource_group_name                           = "test"
      + name                                          = "vm-subnet"
      + private_endpoint_network_policies             = "Disabled"
      + private_link_service_network_policies_enabled = true
      + resource_group_name                           = "test"
      + virtual_network_name                          = "vm-vnet"
    }
      + resource_group_name                           = "test"
      + virtual_network_name                          = "vm-vnet"
    }
      + virtual_network_name                          = "vm-vnet"
    }

  # azurerm_virtual_network.main will be created
    }

  # azurerm_virtual_network.main will be created
  + resource "azurerm_virtual_network" "main" {

  # azurerm_virtual_network.main will be created
  + resource "azurerm_virtual_network" "main" {
      + address_space                  = [
  + resource "azurerm_virtual_network" "main" {
      + address_space                  = [
      + address_space                  = [
          + "10.0.0.0/16",
          + "10.0.0.0/16",
        ]
        ]
      + dns_servers                    = (known after apply)
      + guid                           = (known after apply)
      + guid                           = (known after apply)
      + id                             = (known after apply)
      + location                       = "francecentral"
      + name                           = "vm-vnet"
      + private_endpoint_vnet_policies = "Disabled"
      + resource_group_name            = "test"
      + subnet                         = (known after apply)
    }

Plan: 6 to add, 0 to change, 0 to destroy.

────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── 

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
```



```powershell
PS C:\Users\stephane\Desktop\Efrei\M1\cloud\Tp_cloudAzure\terraform> terraform apply
azurerm_resource_group.main: Refreshing state... [id=/subscriptions/a3bd582e-88e4-4000-970e-b3e76914e628/resourceGroups/test2]
azurerm_public_ip.main: Refreshing state... [id=/subscriptions/a3bd582e-88e4-4000-970e-b3e76914e628/resourceGroups/test2/providers/Microsoft.Network/publicIPAddresses/vm-ip]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # azurerm_linux_virtual_machine.main will be created
  + resource "azurerm_linux_virtual_machine" "main" {
      + admin_username                                         = "jjk"
      + allow_extension_operations                             = (known after apply)
      + bypass_platform_safety_checks_on_user_schedule_enabled = false
      + computer_name                                          = (known after apply)
      + disable_password_authentication                        = (known after apply)
      + disk_controller_type                                   = (known after apply)
      + extensions_time_budget                                 = "PT1H30M"
      + id                                                     = (known after apply)
      + location                                               = "francecentral"
      + max_bid_price                                          = -1
      + name                                                   = "super-vm"
      + network_interface_ids                                  = (known after apply)
      + os_managed_disk_id                                     = (known after apply)
      + patch_assessment_mode                                  = (known after apply)
      + patch_mode                                             = (known after apply)
      + platform_fault_domain                                  = -1
      + priority                                               = "Regular"
      + private_ip_address                                     = (known after apply)
      + private_ip_addresses                                   = (known after apply)
      + provision_vm_agent                                     = (known after apply)
      + public_ip_address                                      = (known after apply)
      + public_ip_addresses                                    = (known after apply)
      + resource_group_name                                    = "test2"
      + size                                                   = "Standard_B1s"
      + virtual_machine_id                                     = (known after apply)
      + vm_agent_platform_updates_enabled                      = (known after apply)

      + admin_ssh_key {
          + public_key = <<-EOT
                ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIAT6qFBe4GCDW71H2kuHJBfmsbfmgyZ8bY5U7UnzkjcO stephane@DESKTOP-O34FVP0
            EOT
          + username   = "jjk"
        }

      + os_disk {
          + caching                   = "ReadWrite"
          + disk_size_gb              = (known after apply)
          + id                        = (known after apply)
          + name                      = "vm-os-disk"
          + storage_account_type      = "Standard_LRS"
          + write_accelerator_enabled = false
        }

      + source_image_reference {
          + offer     = "0001-com-ubuntu-server-focal"
          + publisher = "Canonical"
          + sku       = "20_04-lts"
          + version   = "latest"
        }

      + termination_notification (known after apply)
    }

  # azurerm_network_interface.main will be created
  + resource "azurerm_network_interface" "main" {
      + accelerated_networking_enabled = false
      + applied_dns_servers            = (known after apply)
      + id                             = (known after apply)
      + internal_domain_name_suffix    = (known after apply)
      + ip_forwarding_enabled          = false
      + location                       = "francecentral"
      + mac_address                    = (known after apply)
      + name                           = "vm-nic"
      + private_ip_address             = (known after apply)
      + private_ip_addresses           = (known after apply)
      + resource_group_name            = "test2"
      + virtual_machine_id             = (known after apply)

      + ip_configuration {
          + gateway_load_balancer_frontend_ip_configuration_id = (known after apply)
          + name                                               = "internal"
          + primary                                            = (known after apply)
          + private_ip_address                                 = (known after apply)
          + private_ip_address_allocation                      = "Dynamic"
          + private_ip_address_version                         = "IPv4"
          + public_ip_address_id                               = "/subscriptions/a3bd582e-88e4-4000-970e-b3e76914e628/resourceGroups/test2/providers/Microsoft.Network/publicIPAddresses/vm-ip"
          + subnet_id                                          = (known after apply)
        }
    }

  # azurerm_subnet.main will be created
  + resource "azurerm_subnet" "main" {
      + address_prefixes                              = [
          + "10.0.1.0/24",
        ]
      + default_outbound_access_enabled               = true
      + id                                            = (known after apply)
      + name                                          = "vm-subnet"
      + private_endpoint_network_policies             = "Disabled"
      + private_link_service_network_policies_enabled = true
      + resource_group_name                           = "test2"
      + virtual_network_name                          = "vm-vnet"
    }

  # azurerm_virtual_network.main will be created
  + resource "azurerm_virtual_network" "main" {
      + address_space                  = [
          + "10.0.0.0/16",
        ]
      + dns_servers                    = (known after apply)
      + guid                           = (known after apply)
      + id                             = (known after apply)
      + location                       = "francecentral"
      + name                           = "vm-vnet"
      + private_endpoint_vnet_policies = "Disabled"
      + resource_group_name            = "test2"
      + subnet                         = (known after apply)
    }

Plan: 4 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

azurerm_virtual_network.main: Creating...
azurerm_virtual_network.main: Creation complete after 4s [id=/subscriptions/a3bd582e-88e4-4000-970e-b3e76914e628/resourceGroups/test2/providers/Microsoft.Network/virtualNetworks/vm-vnet]
azurerm_subnet.main: Creating...
azurerm_subnet.main: Creation complete after 5s [id=/subscriptions/a3bd582e-88e4-4000-970e-b3e76914e628/resourceGroups/test2/providers/Microsoft.Network/virtualNetworks/vm-vnet/subnets/vm-subnet]
azurerm_network_interface.main: Creating...
azurerm_network_interface.main: Still creating... [00m10s elapsed]
azurerm_network_interface.main: Creation complete after 11s [id=/subscriptions/a3bd582e-88e4-4000-970e-b3e76914e628/resourceGroups/test2/providers/Microsoft.Network/networkInterfaces/vm-nic]
azurerm_linux_virtual_machine.main: Creating...
azurerm_linux_virtual_machine.main: Still creating... [00m10s elapsed]
azurerm_linux_virtual_machine.main: Still creating... [00m20s elapsed]
azurerm_linux_virtual_machine.main: Still creating... [00m30s elapsed]
azurerm_linux_virtual_machine.main: Still creating... [00m40s elapsed]
azurerm_linux_virtual_machine.main: Creation complete after 48s [id=/subscriptions/a3bd582e-88e4-4000-970e-b3e76914e628/resourceGroups/test2/providers/Microsoft.Compute/virtualMachines/super-vm]

Apply complete! Resources: 4 added, 0 changed, 0 destroyed.
PS C:\Users\stephane\Desktop\Efrei\M1\cloud\Tp_cloudAzure\terraform> 
```