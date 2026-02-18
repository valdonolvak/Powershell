
### Ülesanne: Powershelli kontrollskript "Kursuse progressi raport"

**"Viimane väljakutse: Automaatkontrolli loomine"**
*Sinu ülesandeks on kirjutada Powershelli skript, mis toimib nagu 'iseeneslik audit'. Skript peab suutma ilma kasutaja sekkumiseta kontrollida kõiki eelnevaid punkte (alates 02.09 tunnist kuni tänaseni). Raport peab olema selgelt loetav tabel, kus on näha, millised seadistused on süsteemis tuvastatud ja millised on puudu. See on sinu kinnitus õpetajale, et labor on edukalt läbitud."*

**Boonus:** Lisa skripti kontroll, mis vaatab, kas `slmgr -rearm` on tehtud või kui palju aega on jäänud litsentsi aegumiseni.

**Eesmärk:** Luua Powershelli skript, mis kontrollib automaatselt Active Directory keskkonnas tehtud seadistusi ja väljastab raporti: **TEHTUD / TEGEMATA**.

#### Skripti kontrollitavad punktid:

1. **Baasvõrk ja domeen:**
* Kas serveri nimi on AD1 ja IP-aadress 10.0.XXX.10?
* Kas domeen `perenimi.local` on kättesaadav?


2. **Active Directory struktuur:**
* Kas OU-d `KASUTAJAD`, `LEKTORID`, `TUDENGID`, `ARVUTID` on olemas?
* Kas grupid `Lektorid`, `Tudengid`, `RedirectedDirectories` on loodud?


3. **Kasutajad:**
* Kas kasutajad `oppejoud1`, `oppejoud2`, `tudeng1`, `tudeng2` on olemas ja õigetes gruppides?


4. **Serveri rollid ja teenused:**
* Kas `AD-Domain-Services`, `DHCP`, `DNS`, `WDS` ja `Web-Server` (IIS) rollid on paigaldatud?
* Kas DHCP skoop nimega "HKHK" on olemas?


5. **Failiteenused ja DFS:**
* Kas kaustad `F:\DFS_Lektoritele` ja `F:\DFS_Tudengitele` eksisteerivad?
* Kas DFS nimeruumid `\\TEST.LOCAL\Tudengid` on loodud?


6. **GPO-d (Group Policy):**
* Kas GPO-d nimega "7zip", "Chrome", "Wallpaper" jne on listis olemas?


7. **Turvalisus (LAPS):**
* Kas LAPS-i laiendus on paigaldatud (`Get-Command -Module Microsoft.Windows.LAPS`)?

