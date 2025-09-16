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



