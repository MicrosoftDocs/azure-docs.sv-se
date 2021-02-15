---
title: Hantera sensor från den lokala hanteringskonsolen
description: Lär dig hur du hanterar sensorer från hanterings konsolen, inklusive uppdatering av sensor versioner, översätter system inställningar till sensorer och aktiverar och inaktiverar motorer på sensorer.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 10051bfdf7a93a8d6c83d649a7b0ad008b5e2caf
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523965"
---
# <a name="manage-sensors-from-the-management-console"></a>Hantera sensorer från hanterings konsolen

Den här artikeln beskriver hur du hanterar sensorer från hanterings konsolen, inklusive:

- Skicka Systeminställningar till sensorer

- Aktivera och inaktivera motorer på sensorer

- Uppdatera sensor versioner

## <a name="push-configurations"></a>Push-konfigurationer

Du kan definiera olika Systeminställningar och automatiskt tillämpa dem på sensorer som är anslutna till hanterings konsolen. Detta sparar tid och säkerställer strömlinjeformade inställningar i dina företags sensorer.

Du kan definiera följande sensor system inställningar från hanterings konsolen:

- E-postserver

- SNMP MIB-övervakning

- Active Directory

- DNS-inställningar

- Undernät

- Port Ali Aset

Tillämpa Systeminställningar:

1. I konsolens vänstra ruta väljer du **Systeminställningar**.

2. I fönstret **Konfigurera sensorer** väljer du ett av alternativen.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-system-setting-options.png" alt-text="Systeminställnings alternativen för en sensor.":::

   I följande exempel beskrivs hur du definierar e-postparametrar för dina företags sensorer.

3. Välj **e-postserver**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="Välj din e-postserver på skärmen system inställningar.":::

4. Välj en sensor till vänster.

5. Ange parametrarna för e-postservern och välj **duplicera**. Varje objekt i sensor trädet visas med en kryss ruta bredvid.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/check-off-each-sensor.png" alt-text="Se till att kryss rutorna är markerade för sensorer.":::

6. I sensor trädet väljer du de objekt som du vill tillämpa konfigurationen på.

7. Välj **Spara**.

## <a name="update-versions"></a>Uppdatera versioner

Du kan uppdatera flera sensorer samtidigt från den lokala hanterings konsolen.

Så här uppdaterar du flera sensorer:

1. Gå till [Azure-portalen](https://portal.azure.com/).

2. Gå till Azure Defender för IoT.

3. Gå till sidan **uppdateringar** .

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/update-screen.png" alt-text="Skärm bild av vyn instrument panel för uppdateringar.":::

4. Välj **Hämta** från **sensorer** -avsnittet och spara filen.

5. Logga in i hanterings konsolen och välj **Systeminställningar**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/admin-system-settings.png" alt-text="Skärm bild av menyn Administration för att välja Systeminställningar.":::

6. Markera de sensorer som du vill uppdatera i avsnittet **konfiguration av sensor motor** och välj sedan **automatiska uppdateringar**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensors-select.png" alt-text="Två sensorer som visar inlärnings läge och automatiska uppdateringar.":::

7. Välj **Spara ändringar**.

8. I fönstret **sensorer versions uppgradering** väljer du :::image type="icon" source="media/how-to-manage-sensors-from-the-on-premises-management-console/plus-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/display-files.png" alt-text="Uppgraderings skärm för sensor version för att visa filer.":::

9. Dialog rutan **Ladda upp fil** öppnas. Ladda upp filen som du laddade ned från **uppdaterings** sidan.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/upload-file.png" alt-text="Klicka på knappen Bläddra för att ladda upp filen.":::

10. Under uppdaterings processen visas uppdaterings statusen för varje sensor i fönstret **plats hantering** .

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/progress.png" alt-text="Observera förloppet för din uppdatering.":::

## <a name="update-threat-intelligence-packages"></a>Uppdatera hot informations paket 

Data paketet för hot information tillhandahålls med varje ny version av Defender för IoT, eller om det behövs mellan versioner. Paketet innehåller signaturer (inklusive signaturer för skadlig kod), CVEs och annat säkerhets innehåll. 

Du kan ladda upp filen manuellt från sidan **uppdateringar** i Defender för IoT-portalen och uppdatera den automatiskt till sensorer. 

Så här uppdaterar du Threat Intelligence-data: 

1. Gå till **uppdaterings** sidan för Defender för IoT. 

2. Hämta och spara filen.

3. Logga in i hanterings konsolen. 

4. På menyn på sidan väljer du **Systeminställningar**. 

5. Välj de sensorer som ska ta emot uppdateringen i avsnittet **konfiguration av sensor motor** .  

6. I avsnittet **Select Threat Intelligence-data** väljer du plus tecknet ( **+** ). 

7. Ladda upp paketet som du laddade ned från sidan om **uppdateringar** för Defender för IoT.

## <a name="understand-sensor-disconnection-events"></a>Förstå händelser för sensor från koppling

I fönstret **Site Manager** visas Disconnection-information om sensorer kopplas från den tilldelade lokala hanterings konsolen. Följande information om sensor-från koppling är tillgänglig:

- "Den lokala hanterings konsolen kan inte behandla data som tas emot från sensorn."

- "Tids avvikelse upptäcktes. Den lokala hanterings konsolen har kopplats bort från sensorn.

- "Sensorn kommunicerar inte med den lokala hanterings konsolen. Kontrol lera nätverks anslutningen eller certifikat verifieringen. "

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="Skärm bild av vyn för zon 1.":::

Du kan skicka aviseringar till tredje part med information om frånkopplade sensorer. Mer information finns i [vidarebefordra sensor aviseringar](how-to-manage-individual-sensors.md#forward-sensor-failure-alerts).

## <a name="enable-or-disable-sensors"></a>Aktivera eller inaktivera sensorer

Sensorer skyddas av fem Defender för IoT-motorer. Du kan aktivera eller inaktivera motorer för anslutna sensorer.

| Motor | Beskrivning | Exempelscenario |
|--|--|--|
| Protokolls brotts motor | Ett protokoll fel uppstår när paket strukturen eller fält värden inte följer protokoll specifikationen. | Aviseringen "Ogiltig MODBUS-åtgärd (funktions kod noll)". Den här varningen anger att en primär enhet skickade en begäran med funktions kod 0 till en sekundär enhet. Detta är inte tillåtet enligt protokoll specifikationen och den sekundära enheten kanske inte kan hantera inaktuella inmatade enheter. |
| Princip överträdelse motor | En princip överträdelse inträffar med en avvikelse från bas linje beteendet som definierats i den inlärda eller konfigurerade principen. | Varningen "obehörig HTTP-användare agent". Den här varningen anger att ett program som inte har lärts eller godkänts av principen används som en HTTP-klient på en enhet. Detta kan vara en ny webbläsare eller ett annat program på den enheten. |
| Motor för skadlig kod | Motorn för skadlig kod identifierar skadlig nätverks aktivitet. | "Misstanke om skadlig aktivitet (Stuxnet)"-avisering. Den här varningen anger att sensorn upptäckte misstänkt nätverks aktivitet som är känd för att vara relaterad till den skadliga kod som Stuxnet, vilket är ett avancerat beständigt hot som är avsett för industriella kontroll-och SCADA-nätverk. |
| Avvikelse motor | Motorn för skadlig kod identifierar en avvikelse i nätverks beteendet. | "Periodiskt beteende i kommunikations kanalen." Det här är en komponent som kontrollerar nätverks anslutningar och hittar regelbunden eller cyklisk beteende för data överföring, som är vanligt i industriella nätverk. |
| Drift motor | Den här motorn identifierar drift incidenter eller fel enheter. | `Device is Suspected to be Disconnected (Unresponsive)` varningar. Den här aviseringen utlöses när en enhet inte svarar på begär Anden för en fördefinierad period. Det kan bero på att enheten stängs av, kopplas från eller fungerar dåligt.
|

Aktivera eller inaktivera motorer för anslutna sensorer:

1. I konsolens vänstra ruta väljer du **Systeminställningar**.

2. I avsnittet **konfiguration av sensor motor** väljer du **Aktivera** eller **inaktivera** för motorerna.
         
3. Välj **Spara ändringar**.

   Ett rött utrops tecken visas om det finns en felaktig matchning av aktiverade motorer på någon av dina företags sensorer. Motorn kan ha inaktiverats direkt från sensorn.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/red-exclamation-example.png" alt-text="Aktiverade motorer matchar inte."::: 

## <a name="define-sensor-backup-schedules"></a>Definiera scheman för sensor för säkerhets kopiering

Du kan schemalägga säkerhets kopieringar av sensorer och utföra sensor säkerhets kopieringar på begäran från den lokala hanterings konsolen. Detta hjälper till att skydda mot hård disk haverier och data förlust.

- Vad säkerhets kopie ras: konfigurationer och data.

- Vad säkerhets kopie ras inte: PCAP-filer och loggar. Du kan säkerhetskopiera och återställa PCAPs och loggar manuellt.

Som standard säkerhets kopie ras sensorer automatiskt vid 3:00 per dag. Med funktionen schemaläggning av säkerhets kopiering för sensorer kan du samla in dessa säkerhets kopior och lagra dem i den lokala hanterings konsolen eller på en extern säkerhets kopierings Server. Att kopiera filer från sensorer till den lokala hanterings konsolen sker över en krypterad kanal.

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-backup-schedule-screen.png" alt-text="En vy av sensorn för säkerhets kopiering.":::

När standard platsen för säkerhets kopiering av sensorer ändras, hämtar den lokala hanterings konsolen automatiskt filerna från den nya platsen på sensorn eller en extern plats, förutsatt att konsolen har behörighet att komma åt platsen. 

När sensorer inte är registrerade med den lokala hanterings konsolen anger dialog rutan **schema för säkerhets kopiering** att inga sensorer hanteras.  

Återställnings processen är densamma oavsett var filerna lagras.

### <a name="backup-storage-for-sensors"></a>Lagring av säkerhets kopior för sensorer

Du kan använda den lokala hanterings konsolen för att underhålla upp till nio säkerhets kopior för varje hanterad sensor, förutsatt att de säkerhetskopierade filerna inte överskrider det maximala säkerhets kopierings utrymme som har allokerats. 

Det tillgängliga utrymmet beräknas utifrån den hanterings konsol modell som du arbetar med: 

- **Produktions modell**: standard lagring är 40 GB; gränsen är 100 GB. 

- **Medels Tor modell**: standard lagring är 20 GB; gränsen är 50 GB. 

- **Bärbar modell**: standard lagring är 10 GB; gränsen är 25 GB. 

- **Tunn modell**: standard lagring är 2 GB; gränsen är 4 GB. 

- **Robust modell**: standard lagring är 10 GB; gränsen är 25 GB. 

Standardallokeringen visas i dialog rutan **schema för sensor för säkerhets kopiering** . 

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-mail-server-configuration.png" alt-text="Sidan Redigera konfiguration av e-postserver.":::

Det finns ingen lagrings gräns när du säkerhetskopierar till en extern server. Du måste dock definiera en övre tilldelnings gräns i fältet för **schema** för den  >  **anpassade sökvägen** för sensorn för säkerhets kopiering. Följande siffror och tecken stöds: `/, a-z, A-Z, 0-9, and _` . 

Här är information om överskridande av tilldelnings lagrings gränser:

- Om du överskrider det allokerade lagrings utrymmet säkerhets kopie ras inte sensorn. 

- Om du säkerhetskopierar fler än en sensor försöker hanterings konsolen Hämta sensorer för de hanterade sensorer.  

- Om hämtningen från en sensor överskrider gränsen försöker hanterings konsolen Hämta säkerhets kopierings information från nästa sensor. 

När du överskrider det kvarhållna antalet definierade säkerhets kopior tas den äldsta säkerhets kopian bort för att hantera den nya filen.

Sensor säkerhets kopierings filer får automatiskt namnet i följande format: `<sensor name>-backup-version-<version>-<date>.tar` . Exempel: `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`. 

Så här säkerhetskopierar du sensorer:

1. Välj **schema sensor säkerhets kopiering** från fönstret **system inställningar** . Sensorer som din lokala hanterings konsol hanterar visas i dialog rutan **schema för sensor säkerhets kopiering** .  

2. Aktivera Aktivera **insamling av säkerhets kopior** .  

3. Välj ett kalender intervall, datum och tidszon. Tids formatet baseras på en 24-timmarsklocka. Ange till exempel 6:00 PM som **18:00**. 

4. I fältet **lagrings tilldelning för säkerhets kopiering** anger du det lagrings utrymme som du vill allokera för dina säkerhets kopior. Du får ett meddelande om du har överskridit det maximala utrymmet.

5. I fältet **Behåll sista** anger du antalet säkerhets kopieringar per sensor som du vill behålla. När gränsen överskrids tas den äldsta säkerhets kopian bort.  

6. Välj en plats för säkerhets kopiering:  

   - Om du vill säkerhetskopiera till den lokala hanterings konsolen inaktiverar du den **anpassade sökvägen** . Standardplatsen är `/var/cyberx/sensor-backups`.  

   - Om du vill säkerhetskopiera till en extern server aktiverar du den **anpassade sökvägen** och anger en plats. Följande siffror och tecken stöds: `/, a-z, A-Z, 0-9, and, _` . 

7. Välj **Spara**. 

Säkerhetskopiera direkt: 

- Välj **Säkerhetskopiera nu**. Den lokala hanterings konsolen skapar och samlar in sensor säkerhets kopierings filer. 

### <a name="receiving-backup-notifications-for-sensors"></a>Ta emot meddelanden om säkerhets kopiering för sensorer 

Dialog rutan **schema för sensor för säkerhets kopiering** och säkerhets kopierings loggen listar automatiskt information om lyckade och misslyckade säkerhets kopieringar.  

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-location.png" alt-text="Visa sensorer och var de befinner sig och all relevant information.":::

Fel kan uppstå på grund av följande:    

- Det gick inte att hitta någon säkerhets kopierings fil. 

- En fil hittades men det går inte att hämta den.  

- Det har uppstått ett nätverks anslutnings problem. 

- Det finns inte tillräckligt med utrymme allokerat till den lokala hanterings konsolen för att slutföra säkerhets kopieringen.  

Du kan skicka ett e-postmeddelande, syslog-uppdateringar och system aviseringar när ett fel uppstår. Det gör du genom att skapa en vidarebefordrings regel i **system meddelanden**. 

### <a name="restoring-sensors"></a>Återställa sensorer 

Du kan återställa säkerhets kopior från den lokala hanterings konsolen och med hjälp av CLI.  

Så här återställer du från-konsolen: 

- Välj **Återställ avbildning** från fönstret **sensor system** inställning.

  :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/restore.png" alt-text="Återställ en säkerhets kopia av din avbildning.":::

  Konsolen visar sedan återställnings felen.  

Återställa med hjälp av CLI: 

- Logga in på ett administratörs konto och ange `$ sudo cyberx-xsense-system-restore` . 

### <a name="save-a-sensor-backup-to-an-external-smb-server"></a>Spara en sensor säkerhets kopiering till en extern SMB-server

Så här konfigurerar du en SMB-server så att du kan spara en sensor säkerhets kopia på en extern enhet: 

1. Skapa en delad mapp i den externa SMB-servern. 

2. Hämta sökvägen till mappen, användar namnet och lösen ordet som krävs för att få åtkomst till SMB-servern. 

3. I Defender för IoT skapar du en katalog för säkerhets kopiorna: 

   `sudo mkdir /<backup_folder_name_on_server>` 

   `sudo chmod 777 /<backup_folder_name_on_server>/` 

4. Redigera fstab:  

   `sudo nano /etc/fstab` 

   `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

5. Redigera eller skapa autentiseringsuppgifter som ska delas. Dessa är autentiseringsuppgifterna för SMB-servern: 

   `sudo nano /etc/samba/user` 

6. Lägg till:  

   `username=<user name>` 

   `password=<password>` 

7. Montera katalogen: 

   `sudo mount -a` 

8. Konfigurera en säkerhets kopierings katalog till den delade mappen i Defender for IoT-sensorn:  

   `sudo nano /var/cyberx/properties/backup.properties` 

9. Ange `Backup.shared_location` till `<backup_folder_name_on_cyberx_server>` .

## <a name="next-steps"></a>Nästa steg

[Hantera enskilda sensorer](how-to-manage-individual-sensors.md)
