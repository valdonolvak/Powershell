See on kursuse viimane ja kõige praktilisem "tõelise administraatori" peatükk. **Remote Management** (kaughaldus) võimaldab sul hallata sadu arvuteid oma töölaua taga istudes. Selle aluseks on tehnoloogia nimega **WinRM** (Windows Remote Management) ja käsk **Invoke-Command**.

---

## Peatükk 10: Kaughaldus domeenis

### 1. Kaughalduse ettevalmistus

Et teistesse masinatesse "sisse minna", peab sihtarvutis olema lubatud PowerShell Remoting. Domeenikeskkonnas tehakse seda tavaliselt rühmapoliitika (GPO) abil, aga käsitsi saab seda testida käsuga:

* `Enable-PSRemoting -Force`

Kõige olulisem käsk kaughalduseks: **`Invoke-Command`**. See saadab koodibloki `{ }` sihtarvutisse, jooksutab selle seal ja toob tulemuse sulle tagasi.

### 2. Tarkvara haldamine (Vaatamine, Paigaldamine, Eemaldamine)

Windowsis on tarkvara haldamiseks tänapäeval parim tööriist **Winget** või MSI-pakkide haldus.

* **Vaatamine (mis on installitud):**

```powershell
Invoke-Command -ComputerName "KlientPC01" -ScriptBlock {
    Get-Package -Name "*Chrome*" # Otsib tarkvara nime järgi
}

```

* **Paigaldamine (Winget abil):**

```powershell
Invoke-Command -ComputerName "KlientPC01" -ScriptBlock {
    winget install --id Microsoft.VisualStudioCode --silent --accept-source-agreements
}

```

* **Eemaldamine (MSI pakid):**

```powershell
Invoke-Command -ComputerName "KlientPC01" -ScriptBlock {
    Get-Package -Name "VLC media player" | Uninstall-Package -Force
}

```

### 3. Tulemüüri (Firewall) reeglite haldus

Tulemüür on tihti takistuseks programmide töös. PowerShelliga saad reegleid lisada ja eemaldada sekunditega.

* **Reegli lisamine (Ava port 8080):**

```powershell
Invoke-Command -ComputerName "KlientPC01" -ScriptBlock {
    New-NetFirewallRule -DisplayName "Lubame Port 8080" -Direction Inbound -LocalPort 8080 -Protocol TCP -Action Allow
}

```

* **Reegli eemaldamine:**

```powershell
Invoke-Command -ComputerName "KlientPC01" -ScriptBlock {
    Remove-NetFirewallRule -DisplayName "Lubame Port 8080"
}

```

### 4. Kettatäituvuse vaatamine

Administraatorina pead teadma, kui kaugele on kliendi kõvaketas täitunud.

```powershell
Invoke-Command -ComputerName "KlientPC01" -ScriptBlock {
    Get-Volume -DriveLetter C | Select-Object DriveLetter, 
        @{Name="SizeGB"; Expression={[Math]::Round($_.Size / 1GB, 2)}},
        @{Name="FreeGB"; Expression={[Math]::Round($_.SizeRemaining / 1GB, 2)}}
}

```

---

### Iseseisvad harjutused (Kaughalduse harjutamine)

1. **Harjutus: Süsteemi info kaugelt.** Kasuta `Invoke-Command`, et küsida kaughallatava arvuti operatsioonisüsteemi versiooni (`Get-ComputerInfo`).
2. **Harjutus: Teenuse restart.** Kliendi arvutis "Klient01" hangus trükiteenus (`Spooler`). Kirjuta käsk, mis taaskäivitab selle teenuse distantsilt.
3. **Harjutus: Tulemüüri kontroll.** Leia käsk, millega loetleda kõik aktiivsed tulemüüri reeglid, mis on suunaga "Inbound" (Sissetulev).
4. **Harjutus: Tarkvara inventuur.** Koosta skript, mis küsib kolme arvuti käest (massiiv `$arvutid`), kas neisse on installeeritud "Adobe Reader".

---

### Iseseisev praktiline töö: "Kaugtoe automaatika"

**Ülesanne:** Koosta administraatori tööriist (skript), mis teeb kliendi arvutile kiire "tervisekontrolli" ja tarkvarauuenduse.

**Töö käik ja nõuded:**

1. **Sihtmärk:** Skript peab võtma parameetrina arvuti nime (`-ComputerName`).
2. **Kettakontroll:** Kontrolli C-ketta vaba pinda. Kui vaba pinda on vähem kui 10 GB, peab skript väljastama punase hoiatuse: "KRIITILINE: Kettapind on otsakorral!".
3. **Tarkvara haldus:**
* Kontrolli, kas arvutis on tarkvara nimega "7-Zip".
* Kui seda **ei ole**, siis paigalda see (kasutades `winget` või võrgukettal olevat MSI faili).


4. **Turvalisus:**
* Lisa tulemüüri reegel, mis lubab "Remote Desktop" ühendused, kui see peaks puuduma.
* Logi reegli lisamine kohalikku logifaili.


5. **Raport:** Skripti lõpus kuva ekraanile koondtabel: Arvuti nimi, C-ketta vaba pind, 7-Zip staatus (Installitud/Puudu).

**Esitamiseks õpetajale:**

* Skripti kood (`.ps1`).
* Selgitus: Mis on vahet `Enter-PSSession` ja `Invoke-Command` vahel? (Millal kasutaksid ühte ja millal teist?).
* Screenshot skripti väljundist, kus on näha kettatäituvuse kontrolli tulemus.

---

**Õpetaja märkus:** Kaughaldus nõuab administraatori õigusi. Kui testid seda kooli või töö laboris, veendu, et käivitad PowerShelli "Run as Administrator" ja sinu kasutajal on sihtarvutis vastavad õigused!

**See oli viimane peatükk! Palju õnne – oled läbinud PowerShelli täiskursuse algajast kuni domeeni kaughalduse spetsialistini!** Kas soovid, et koostaksin sulle kursuse lõputesti oma teadmiste kontrollimiseks?
