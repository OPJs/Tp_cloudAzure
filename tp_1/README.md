## Déterminer l'algorithme de chiffrement🌞

On évite RSA car :

Les tailles de clé recommandées sont devenues très grandes (>= 3072 bits) → performances médiocres.

Ed25519 et ECDSA sont aujourd’hui considérés plus sûrs et rapides

sources: https://goteleport.com/blog/comparing-ssh-keys/

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

