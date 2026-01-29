Allpool on **õpilastele mõeldud samm-sammuline õpiülesanne**, kus nad **õpivad järk-järgult** AD kasutajate loomist PowerShelliga. 

Sisukord:
* alguses tehakse käsud **käsureal**
* alles lõpus pannakse kõik **skriptiks kokku**
* iga sammu juures on **selgitus, miks ja mida tehakse**
Selge! Ma asendan sinu olemasoleva **8️⃣ sammu – Foreach tsükkel** tervikuna praktilise ja testitava lahendusega, kus õpilane saab CSV-failist kasutajaid lugeda ja nende andmed käsureale väljastada. Samuti ühendame kõik sammud tervikuks nii, et dokument oleks kohe kasutatav õpiülesandena.

---

# 🧑‍🎓 ÕPIÜLESANNE ÕPILASTELE

## Active Directory kasutajate loomine PowerShelliga

---

## 🎯 Õppe-eesmärk

Pärast selle ülesande läbimist oskab õpilane:

* kasutada PowerShelli **Active Directory moodulit**
* kontrollida ja luua **OU-sid**
* luua **kasutajakontosid AD-s**
* lugeda andmeid **CSV-failist**
* koostada **automatiseeritud PowerShelli skripti**

---

## 🛠️ Eeldused

* Sul on ligipääs **Windows Serverile**, kus on Active Directory
* PowerShell käivitatakse **Administratorina**
* ActiveDirectory moodul on paigaldatud (RSAT)

---

## 1️⃣ Samm – Active Directory mooduli laadimine

### 💡 Miks?

Kõik AD käsud (`Get-ADUser`, `New-ADUser`, jne) asuvad **ActiveDirectory moodulis**.

### ▶ Käsk PowerShellis

```powershell
Import-Module ActiveDirectory
```

### ✔ Kontroll

```powershell
Get-Command -Module ActiveDirectory
```

Kui näed AD käske, on moodul laetud.

---

## 2️⃣ Samm – Domeeni struktuuri vaatamine

### 💡 Miks?

Peame teadma, **kuhu kasutajad luuakse** (DN – Distinguished Name).

### ▶ Käsk

```powershell
Get-ADDomain
```

Oluline väli:

```text
DistinguishedName : DC=TEST,DC=LOCAL
```

---

## 3️⃣ Samm – OU olemasolu kontrollimine

### 💡 Miks?

Kasutaja saab lisada **ainult olemasolevasse OU-sse**.

### ▶ Käsk

```powershell
Get-ADOrganizationalUnit -Filter * | Select Name
```

---

## 4️⃣ Samm – OU loomine käsurealt

### 💡 Miks?

Kui OU puudub, tuleb see **enne kasutaja loomist** luua.

### ▶ Käsk

```powershell
New-ADOrganizationalUnit -Name "KASUTAJAD" -Path "DC=TEST,DC=LOCAL"
```

### ▶ Alam-OU loomine

```powershell
New-ADOrganizationalUnit -Name "IT" -Path "OU=KASUTAJAD,DC=TEST,DC=LOCAL"
```

---

## 5️⃣ Samm – Parooli loomine SecureString-ina

### 💡 Miks?

AD ei luba parooli anda **lihttekstina**.

### ▶ Käsk

```powershell
$password = ConvertTo-SecureString "Passw0rd" -AsPlainText -Force
```

---

## 6️⃣ Samm – Ühe kasutaja loomine käsurealt

### 💡 Miks?

Enne automatiseerimist tuleb **mõista ühe kasutaja loomist**.

### ▶ Käsk

```powershell
New-ADUser `
  -Name "Markus Tamm" `
  -GivenName "Markus" `
  -Surname "Tamm" `
  -SamAccountName "markus.tamm" `
  -UserPrincipalName "markus.tamm@TEST.LOCAL" `
  -Path "OU=IT,OU=KASUTAJAD,DC=TEST,DC=LOCAL" `
  -AccountPassword $password `
  -Enabled $true `
  -ChangePasswordAtLogon $true
```

### ✔ Kontroll

```powershell
Get-ADUser markus.tamm
```

---

## 7️⃣ Samm – CSV faili lugemine

### 💡 Miks?

Mitme kasutaja loomine käsitsi on ebaefektiivne – kasutame CSV-d.

### ▶ Näide CSV-st

```csv
FirstName,LastName,OU,Password
Markus,Tamm,IT,Passw0rd
Jüri,Sepp,IT,Pa$$word1
Liis,Kask,IT,Pass1234
```

### ▶ CSV lugemine PowerShellis

```powershell
$users = Import-Csv C:\Scripts\users.csv
```

### ▶ Andmete vaatamine

```powershell
$users
```

---

## 8️⃣ Samm – Foreach tsükkel ja kasutajate testimine CSV-st

### 💡 Miks?

Et teha **sama tegevus iga CSV rea kohta** ja testida, et andmeid saab lugeda.

### ▶ Täielik testitav näide

```powershell
# CSV lugemine
$users = Import-Csv C:\Scripts\users.csv

# Tsükkel iga rea kohta
foreach ($user in $users) {
    # Näita kasutaja andmeid käsureal
    Write-Host "Eesnimi: $($user.FirstName), Perekonnanimi: $($user.LastName), OU: $($user.OU), Parool: $($user.Password)"
}
```

### ✔ Mida see teeb?

1. Loeb CSV-faili kõik read
2. Läbib iga rea (`$user`)
3. Väljastab käsureale iga kasutaja ees- ja perekonnanime, OU ja parooli
4. Õpilane saab kontrollida, et **CSV andmed jõuavad õigesti PowerShelli**

---

### 9️⃣ Samm – OU kontroll skriptis

### 💡 Miks?

Skript peab töötama ka siis, kui OU-d pole veel loodud.

### ▶ Käsk

```powershell
Get-ADOrganizationalUnit `
  -Filter "Name -eq 'IT'" `
  -SearchBase "OU=KASUTAJAD,DC=TEST,DC=LOCAL"
```

## 🔟 Samm – Kasutaja loomine CSV-st

### ▶ Näidisloogika

```powershell
$username = "$($user.FirstName.ToLower()).$($user.LastName.ToLower())"
$password = ConvertTo-SecureString $user.Password -AsPlainText -Force
```

---

## 1️⃣1️⃣ Samm – Kõigi sammude ühendamine skriptiks

### 💡 Tulemus

* Skript loeb CSV
* Kontrollib OU-sid
* Loob kasutajad
* Väldib duplikaate

👉 **See samm viib lõpplahenduseni**

