See on viimane samm teekonnal algajast skriptijaks. Selles peatükis paneme kõik seni õpitu kokku üheks terviklikuks ja turvaliseks tööriistaks. Skriptimine ei ole ainult käskude ritta seadmine, vaid ka protsessi jälgimine ja vigade ennetamine.

---

## Peatükk 5: Skriptid ja logimine

### 1. Skripti olemus ja täitmispoliitika (Execution Policy)

PowerShelli skript on tekstifail laiendiga `.ps1`. Vaikimisi on Windowsis skriptide käivitamine turvalisuse huvides keelatud.

* **Kuidas kontrollida:** `Get-ExecutionPolicy`
* **Kuidas lubada (õppetööks):** `Set-ExecutionPolicy RemoteSigned` (lubab kohalikud skriptid).

### 2. Skripti parameetrid (Param)

Hea skript on universaalne. Selle asemel, et kirjutada kasutajanimi koodi sisse, kasuta parameetreid. Nii saab skripti käivitada erinevate andmetega ilma koodi muutmata.

```powershell
param (
    [string]$Kasutaja,
    [int]$Vanus
)
Write-Host "Kasutaja $Kasutaja on $Vanus aastat vana."

```

### 3. Vigade haldus (Try-Catch)

IT-süsteemides lähevad asjad vahel valesti (faili pole, õigused puuduvad). Selleks, et skript "pauguga" ei lõpetaks, kasutame `Try-Catch` blokki.

```powershell
try {
    # Proovime teha midagi ohtlikku
    Remove-Item "C:\KaitstudFail.txt" -ErrorAction Stop
}
catch {
    # Kui tekkis viga, siis tee seda:
    Write-Warning "Viga: Faili ei saanud kustutada. Teade: $_"
}

```

### 4. Logimine (Tegevuste talletamine)

Administraator peab teadma, mis juhtus siis, kui ta ekraani ei vaadanud. Logimine tähendab info salvestamist faili.

* **Lihtne logi:** `Add-Content -Path "logi.txt" -Value "Skript käivitus kell $(Get-Date)"`
* **Tee seda alati:** Lisa igale olulisele sammule logikirje, et hiljem oleks võimalik probleeme diagnoosida.

---

### Iseseisvad harjutused (Skriptimine ja vead)

1. **Harjutus: Turvakontroll.** Koosta skript, mis küsib kasutajalt (parameetrina) kausta teed ja kontrollib `Test-Path` abil, kas see on olemas. Kui on, väljasta "Olemas", kui mitte, väljasta "Puudu".
2. **Harjutus: Veapüüdja.** Kirjuta skript, mis üritab peatada teenust nimega "OlematuTeenus". Kasuta `Try-Catch` blokki, et skript ei kuvaks punast veateadet, vaid ütleks viisakalt: "Seda teenust ei leitud."
3. **Harjutus: Kellajaga logi.** Loo skript, mis lisab faili `history.log` rea "Kasutaja [SinuNimi] käivitas skripti" koos täpse kuupäeva ja kellaajaga.
4. **Harjutus: Parameetritega arvutamine.** Loo skript `Arvuta.ps1`, mis võtab kaks parameetrit (arvu) ja logib nende summa ekraanile ja faili `tulemused.txt`.

---

### Iseseisev praktiline töö: "Automaatne süsteemimonitor"

**Ülesanne:** Koosta professionaalne skript, mis jälgib süsteemi ressursse, tegeleb vigade haldusega ja peab toimingute kohta logi.

**Nõuded skriptile:**

1. **Parameetrid:** Skript peab võtma parameetrina logifaili asukoha (vaikimisi `C:\Temp\monitor.log`).
2. **Logi alustamine:** Skripti alguses peab logisse kirjutama: "--- SEIRE ALUSTATUD: [Kuupäev] ---".
3. **Andmete kogumine:**
* Kogu info 3 kõige rohkem mälu kasutava protsessi kohta.
* Kogu info vaba kettapinna kohta C: kettal.


4. **Vigade haldus:** Ürita kontrollida mõnda võrguketast või kausta, millele sul ligipääsu pole. Kasuta `Try-Catch` blokki, et püüda kinni viga ja kirjutada see logifaili teatega: "VIGA: Juurdepääs keelatud asukohale X".
5. **Vormistamine:** Logifaili sisu peab olema loetav (kasuta tühje ridu või eraldusjooni).
6. **Lõpetamine:** Skripti lõpus logi: "--- SEIRE LÕPETATUD ---".

**Esitamiseks õpetajale:**

* Skriptifail (`.ps1`).
* Skripti käivitamise tulemusel tekkinud logifail (`monitor.log`).
* Lühike kirjalik selgitus: Miks on `Try-Catch` kasutamine oluline kriitiliste süsteemihaldus-skriptide puhul?

---
