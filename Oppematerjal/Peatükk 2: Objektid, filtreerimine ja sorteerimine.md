## Peatükk 2: Objektid, filtreerimine ja sorteerimine

### 1. Mis on objekt? (Andmed vs. Objektid)

Tavalises käsureas on väljund lihtsalt tekst. PowerShellis on iga asi **objekt**.
Kujuta ette autot:

* **Omadused (Properties):** Värv, mudel, kiirus, kütusekulu.
* **Meetodid (Methods):** Käivita, pidurda, lülita tuled sisse.

Kui küsid PowerShellilt protsessi (`Get-Process`), ei anna ta sulle lihtsalt nime, vaid terve "objekti", millel on mälukasutus, ID ja omanik.

### 2. Filtreerimine: `Where-Object`

Kuna käsud tagastavad tihti liiga palju infot, peame seda piirama ehk filtreerima. Selleks on `Where-Object` (lühidalt ka `?`).

Selleks kasutame võrdlusoperaatoreid:

* `-eq` (equal) – võrdne
* `-gt` (greater than) – suurem kui
* `-lt` (less than) – väiksem kui
* `-like` – sarnane (kasutatakse metsikuid kaarte nagu `*`)

**Näide:** Leia protsessid, mis kasutavad rohkem kui 500 MB mälu:
`Get-Process | Where-Object {$_.WorkingSet -gt 500MB}`

> `$_.` tähistab "käesolevat objekti", mis parajasti torus liigub.

### 3. Sorteerimine: `Sort-Object`

Andmete rittaseadmine on kriitiline, et neist sotti saada.

* `Sort-Object PropertyName` – sorteerib kasvavalt.
* `Sort-Object PropertyName -Descending` – sorteerib kahanevalt.

### 4. Valimine: `Select-Object`

Mõnikord on objektil 50 omadust, aga meid huvitavad ainult 2 (nt nimi ja staatus).
`Get-Service | Select-Object Name, Status`

---

### Iseseisvad harjutused (Objektidega mässamine)

1. **Harjutus: Täpne valik.** Võta kõik protsessid (`Get-Process`), aga kuva ekraanile ainult kolm tulpa: `ProcessName`, `ID` ja `CPU`.
2. **Harjutus: Suured failid.** Mine oma C-kettale (või mõnda mahukasse kausta) ja kasuta `Get-ChildItem`. Sorteeri failid nende suuruse (`Length`) järgi nii, et kõige suuremad oleksid eespool.
3. **Harjutus: Nimeline filter.** Leia kõik teenused (`Get-Service`), mille staatus on "Stopped", aga mille nimi algab tähega "m". *Vihje: Kasuta `-like "m*"`.*
4. **Harjutus: Meetodite uurimine.** Kasuta käsku `Get-Date | Get-Member`. Leia nimekirjast meetod, mis võimaldab kuupäevale päevi juurde liita. Proovi seda rakendada (nt `$a = Get-Date; $a.AddDays(5)`).

---

### Iseseisev praktiline töö: "Süsteemiressursside detektiiv"

**Ülesanne:** Koosta skript, mis aitab süsteemiadministraatoril tuvastada potentsiaalseid probleeme arvutis. Tulemus peab olema puhas ja loetav tabel.

**Töö käik ja nõuded:**

1. **Sorteeritud protsessid:** Leia kõik protsessid, mis on käimas. Sorteeri need ID järgi kahanevas järjekorras.
2. **Mälukasutuse filter:** Vali nendest protsessidest ainult need, mille mälukasutus (WorkingSet) on suurem kui 100 MB.
3. **Andmete piiramine:** Jäta väljundisse alles ainult protsessi nimi (`Name`), ID ja mälukasutus (`WorkingSet`).
4. **Arvutatav väärtus (Lisaülesanne edasijõudnutele):** Muuda mälukasutus baitidest megabaitideks, et see oleks loetavam.
5. **Vormistamine:** Väljasta tulemus ekraanile tabelina, kasutades käsku `Format-Table`.

**Esitamiseks:**

* Esita skripti kood (tekstina).
* Lisa selgitus: Mida tähendab sümbol `$_` sinu `Where-Object` filtris?
* Esita screenshot tulemusest, mis jookseb sinu PowerShelli aknas.

---
