Allpool on **lihtne õppematerjal algajale**, kuidas alustada **PowerShelli skripti loomist PowerShell ISE programmis**. Materjal sisaldab samme, selgitusi ja näiteid.

---

# PowerShell skripti loomise algõpetus (PowerShell ISE)

## 1. Mis on PowerShell ISE?

**PowerShell ISE (Integrated Scripting Environment)** on Microsofti programm, mis aitab kirjutada ja testida PowerShelli skripte graafilises keskkonnas. Selle eelis on see, et saad kirjutada koodi, käivitada seda ja näha tulemusi samas aknas. ([Redmondmag][1])

ISE keskkonnas on tavaliselt kaks peamist osa:

* **Script pane** – koht, kuhu kirjutad skripti
* **Console pane** – koht, kus kuvatakse käsu väljund

---

## 2. PowerShell ISE avamine

### Meetod 1

1. Vajuta **Start** menüüle
2. Kirjuta otsingusse **PowerShell ISE**
3. Ava **Windows PowerShell ISE**

### Meetod 2

1. Vajuta **Win + R**
2. Kirjuta:

```
powershell_ise
```

3. Vajuta **Enter**

---

## 3. PowerShell ISE kasutajaliides

![Image](https://learn.microsoft.com/en-us/powershell/docs-conceptual/windows-powershell/ise/media/exploring-the-windows-powershell-ise/full-ise-window.png?view=powershell-7.5)

![Image](https://www.computerperformance.co.uk/images/powershell/ise3_sm.jpg)

![Image](https://redmondmag.com/articles/2013/06/20/~/media/ECG/redmondmag/Images/2013/06/PowerShell%20ISE%206.ashx)

Olulised osad:

| Osa          | Selgitus                          |
| ------------ | --------------------------------- |
| Script Pane  | Skriptide kirjutamiseks           |
| Console Pane | Käskude väljundi nägemiseks       |
| Toolbar      | Skripti käivitamine ja tööriistad |
| Script tabs  | Mitme skripti korraga avamine     |

ISE toetab ka:

* **süntaksi värvimist**
* **automaatset täitmist**
* **debugimist** ([sevenforums.com][2])

---

# 4. Esimese skripti loomine

## Samm 1 – loo uus skript

Klõpsa **New Script** (või vajuta **Ctrl + N**).

## Samm 2 – kirjuta lihtne skript

Näiteks:

```powershell
Write-Host "Tere maailm!"
```

## Samm 3 – salvesta skript

1. File → Save
2. Salvesta näiteks nimega:

```
minu_skript.ps1
```

PowerShell skriptide faililaiend on **.ps1**

---

# 5. Skripti käivitamine

Skripti saab käivitada mitmel viisil.

### Meetod 1 – Run Script

Vajuta:

**F5**

### Meetod 2 – Run Selection

Vali osa koodist ja vajuta:

**F8**

See käivitab ainult valitud koodi.

---

# 6. Näide praktilisest skriptist

Näiteks skript, mis kuvab süsteemi infot:

```powershell
$computer = $env:COMPUTERNAME
$date = Get-Date

Write-Host "Arvuti nimi: $computer"
Write-Host "Kuupäev: $date"
```

Väljund võib olla näiteks:

```
Arvuti nimi: PC-01
Kuupäev: 11.03.2026
```

---

# 7. Kommentaaride kasutamine

Kommentaarid aitavad koodi selgitada.

```powershell
# See on kommentaar

Write-Host "Hello"
```

Mitmerealine kommentaar:

```powershell
<#
See skript
näitab süsteemi infot
#>
```

---

# 8. Skripti käivitamise turvaseaded

Mõnikord ei lase Windows skripte käivitada.

Siis tuleb muuta **Execution Policy**.

Ava PowerShell administraatorina ja käivita:

```powershell
Set-ExecutionPolicy RemoteSigned
```

---

# 9. Kasulikud klaviatuuri kiirklahvid

| Kiirklahv    | Funktsioon        |
| ------------ | ----------------- |
| F5           | Run script        |
| F8           | Run selection     |
| Ctrl + J     | Cmdlet soovitused |
| Ctrl + Space | Autocomplete      |

---

# 10. Hea praktika algajale

Alguses harjuta järgmisi käske:

* `Get-Process`
* `Get-Service`
* `Get-Date`
* `Get-Help`

Näide:

```powershell
Get-Process
```

---

💡 **Oluline märkus:**
PowerShell ISE ei ole enam aktiivses arenduses ja Microsoft soovitab kasutada **Visual Studio Code + PowerShell Extension**. ([techtarget.com][3])

---
[1]: https://redmondmag.com/articles/2013/06/20/powershell-ise.aspx?utm_source=chatgpt.com "Using the PowerShell Integrated Scripting Environment -- Redmondmag.com"
[2]: https://www.sevenforums.com/tutorials/27765-windows-powershell-integrated-system-environment-ise.html?utm_source=chatgpt.com "Windows PowerShell Integrated System Environment (ISE) | Tutorials"
[3]: https://www.techtarget.com/searchwindowsserver/definition/Microsoft-Windows-PowerShell-Integrated-Scripting-Environment-ISE?utm_source=chatgpt.com "What is Windows PowerShell ISE (Integrated Scripting Environment)?"
