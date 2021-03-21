---
title: Hantera enskilda sensorer
description: Lär dig hur du hanterar enskilda sensorer, inklusive hur du hanterar aktiverings filer, utför säkerhets kopieringar och uppdaterar en fristående sensor.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 02/02/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: c8bb44d323574f6815aa570b271ed4c0df1fc6be
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100526926"
---
# <a name="manage-individual-sensors"></a>Hantera enskilda sensorer

Den här artikeln beskriver hur du hanterar enskilda sensorer. Uppgifterna omfattar hantering av aktiverings filer, säkerhets kopiering och uppdatering av en fristående sensor.

Du kan också utföra vissa sensor hanterings uppgifter från den lokala hanterings konsolen där flera sensorer kan hanteras samtidigt.

Du använder Azure Portal för sensorer för onboarding och registrering.

## <a name="manage-sensor-activation-files"></a>Hantera sensor aktiverings filer

Din sensor har registrerats med Azure Defender för IoT från Azure Portal. Varje sensor har registrerats antingen som en lokalt ansluten sensor eller en molnbaserad sensor.

En unik aktiverings fil överförs till varje sensor som du distribuerar. Mer information om när och hur du använder en ny fil finns i [Ladda upp nya aktiverings filer](#upload-new-activation-files). Om du inte kan ladda upp filen läser du [Felsöka aktivering av överföring av filer](#troubleshoot-activation-file-upload).

### <a name="about-activation-files-for-locally-connected-sensors"></a>Om aktiverings filer för lokalt anslutna sensorer

Lokalt anslutna sensorer är associerade med en Azure-prenumeration. Aktiverings filen för lokalt anslutna sensorer innehåller ett förfallo datum. En månad före det här datumet visas ett varnings meddelande överst i sensor konsolen. Varningen är kvar tills du har uppdaterat aktiverings filen.

:::image type="content" source="media/how-to-manage-individual-sensors/system-setting-screenshot.png" alt-text="Skärm bilden av systeminställningarna.":::

Du kan fortsätta att arbeta med Defender för IoT-funktioner även om aktiverings filen har upphört att gälla. 

### <a name="about-activation-files-for-cloud-connected-sensors"></a>Om aktiverings filer för moln anslutna sensorer

Sensorer som är anslutna till molnet är associerade med Defender för IoT Hub. Sensorerna är inte begränsade med tids perioder för aktiverings filen. Aktiverings filen för moln anslutna sensorer används för att säkerställa anslutning till Defender för IoT Hub.

### <a name="upload-new-activation-files"></a>Ladda upp nya aktiverings filer

Du kan behöva ladda upp en ny aktiverings fil för en onboardd sensor när:

- En aktiverings fil upphör att gälla på en lokalt ansluten sensor. 

- Du vill arbeta i ett annat läge för sensor hantering. 

- Du vill tilldela en ny Defender för IoT-hubb till en molnbaserad sensor.

Så här lägger du till en ny aktiverings fil:

1. Gå till sidan **sensor hantering** .

2. Välj den sensor som du vill ladda upp en ny aktiverings fil för.

3. Ta bort den.

4. Publicera sensorn igen från **onboarding** -sidan i det nya läget eller med en ny Defender för IoT Hub.

5. Hämta aktiverings filen från sidan **Hämta aktiverings fil** .

6. Spara filen.

    :::image type="content" source="media/how-to-manage-individual-sensors/download-activation-file.png" alt-text="Hämta aktiverings filen från Defender för IoT Hub.":::

7. Logga in på konsolen Defender för IoT-sensorn.

8. I sensor konsolen väljer du   >  **Återaktivering** av system inställningar.

    :::image type="content" source="media/how-to-manage-individual-sensors/reactivation.png" alt-text="Återaktivera val på skärmen system inställningar.":::

9. Välj **Ladda upp** och välj den fil som du sparade.

    :::image type="content" source="media/how-to-manage-individual-sensors/upload-the-file.png" alt-text="Överför filen som du sparade.":::

10. Välj **Aktivera**.

### <a name="troubleshoot-activation-file-upload"></a>Felsöka aktiverings fil uppladdning

Du får ett fel meddelande om det inte gick att överföra aktiverings filen. Följande händelser kan ha inträffat:

- **För lokalt anslutna sensorer**: aktiverings filen är inte giltig. Om filen inte är giltig går du till Defender för IoT-portalen. På sidan **sensor hantering** väljer du sensorn med den ogiltiga filen och laddar ned en ny aktiverings fil.

- **För moln anslutna sensorer**: sensorn kan inte ansluta till Internet. Kontrol lera sensorns nätverks konfiguration. Om sensorn behöver ansluta via en webbproxy för att få åtkomst till Internet kontrollerar du att proxyservern har kon figurer ATS korrekt på skärmen **sensor nätverks konfiguration** . Kontrol lera att \* . Azure-Devices.net:443 är tillåtet i brand väggen och/eller proxyservern. Om jokertecken inte stöds eller om du vill ha mer kontroll, ska FQDN för din speciella Defender för IoT Hub öppnas i brand väggen och/eller proxyservern. Mer information finns i [referens-IoT Hub slut punkter](../iot-hub/iot-hub-devguide-endpoints.md).  

- **För moln anslutna sensorer**: aktiverings filen är giltig men Defender för IoT avvisade den. Om du inte kan lösa det här problemet kan du hämta en annan aktivering från sidan platser och sensorer i Defender for IoT-portalen. Kontakta Microsoft Support om detta inte fungerar.

## <a name="manage-certificates"></a>Hantera certifikat

Efter sensor installation skapas ett lokalt självsignerat certifikat som används för att få åtkomst till sensor webb programmet. När du loggar in på sensorn för första gången uppmanas administratörs användare att tillhandahålla ett SSL/TLS-certifikat.  Mer information om installation i första gången finns i [Logga in och aktivera en sensor](how-to-activate-and-set-up-your-sensor.md).

Den här artikeln innehåller information om hur du uppdaterar certifikat, arbetar med certifikat-CLI-kommandon och certifikat parametrar som stöds.

### <a name="about-certificates"></a>Om certifikat

Azure Defender för IoT använder SSL/TLS-certifikat för att:

- Uppfylla särskilda certifikat-och krypterings krav som din organisation har begärt genom att ladda upp det CA-signerade certifikatet.

- Tillåt verifiering mellan hanterings konsolen och anslutna sensorer och mellan en hanterings konsol och en hanterings konsol med hög tillgänglighet. Valideringar utvärderas mot en lista över återkallade certifikat och certifikatets förfallo datum. *Om verifieringen Miss lyckas stoppas kommunikationen mellan hanterings konsolen och sensorn och ett verifierings fel visas i-konsolen*. Det här alternativet är aktiverat som standard efter installationen.

 Regler för vidarebefordran från tredje part, till exempel aviserings information som skickas till SYSLOG, Splunk eller ServiceNow; eller kommunikation med Active Directory verifieras inte.

#### <a name="ssl-certificates"></a>SSL-certifikat

Defender för IoT-sensorn och den lokala hanterings konsolen använder SSL och TLS-certifikat för följande funktioner: 

 - Säker kommunikation mellan användare och webb konsolen för-enheten. 
 
 - Skydda kommunikationen till REST API på sensorn och den lokala hanterings konsolen.
 
 - Säker kommunikation mellan sensorer och en lokal hanterings konsol. 

När den har installerats genererar enheten ett lokalt självsignerat certifikat för att tillåta preliminär åtkomst till webb konsolen. Enterprise SSL och TLS-certifikat kan installeras med hjälp av [`cyberx-xsense-certificate-import`](#cli-commands) kommando rads verktyget.

 > [!NOTE]
 > För integrerings-och vidarebefordrings regler där installationen är klienten och initieraren av sessionen används vissa certifikat och är inte relaterade till system certifikaten.  
 >
 >I dessa fall tas certifikaten vanligt vis emot från servern eller använda asymmetrisk kryptering där ett särskilt certifikat tillhandahålls för att konfigurera integrationen.

Enheter kan använda unika certifikatfiler. Om du behöver ersätta ett certifikat har du laddat upp.

- Från version 10,0 kan certifikatet ersättas från menyn system inställningar.

- För tidigare versioner än 10,0 kan SSL-certifikatet ersättas med kommando rads verktyget.

### <a name="update-certificates"></a>Uppdatera certifikat

Användare av sensor administratörer kan uppdatera certifikat.

Så här uppdaterar du ett certifikat:  

1. Välj **Systeminställningar**.

1. Välj **SSL/TLS-certifikat.**
1. Ta bort eller redigera certifikatet och Lägg till ett nytt.

    - Lägg till ett certifikat namn.
    
    - Ladda upp en CRT-fil och nyckel fil och ange en lösen fras.
    - Ladda upp en PEM-fil om det behövs.

Ändra validerings inställningen:

1. Aktivera eller inaktivera växling av **Aktivera certifikat validering** .

1. Välj **Spara**.

Om alternativet är aktiverat och verifieringen Miss lyckas, stoppas kommunikationen mellan hanterings konsolen och sensorn och ett verifierings fel visas i-konsolen.

### <a name="certificate-support"></a>Certifikat stöd

Följande certifikat stöds:

- Privata och företags nyckel infrastruktur (privat PKI)

- Infrastruktur för offentliga nycklar (offentlig PKI) 

- Lokalt genererat på produkten (lokalt självsignerat). 

> [!IMPORTANT]
> Vi rekommenderar inte att du använder ett självsignerat certifikat. Den här typen av anslutning är inte säker och bör endast användas för test miljöer. Eftersom certifikatets ägare inte kan verifieras, och säkerheten för systemet inte kan upprätthållas, ska de självsignerade certifikaten aldrig användas för produktions nätverk.

### <a name="supported-ssl-certificates"></a>SSL-certifikat som stöds 

Följande parametrar stöds. 

**Certifikat CRT**

- Den primära certifikat filen för ditt domän namn

- Signaturalgoritm = SHA256RSA
- Hash-algoritm för signatur = SHA256
- Giltigt från = giltigt föregående datum
- Giltigt till = giltigt framtida datum
- Offentlig nyckel = RSA 2048 bitar (minst) eller 4096 bitar
- CRL distributions punkt = URL till. CRL-fil
- Subject CN = URL, kan vara ett certifikat med jokertecken. till exempel sensor. contoso. <span> com eller *. contoso. <span> com
- Subject (C) ountry = definierad, till exempel US
- Subjekt (OU) org Unit = definierad, till exempel contoso Labs
- Subject (O) rganisationsnamn = definierad, till exempel contoso Inc.

**Nyckel fil**

- Nyckel filen som skapades när du skapade CSR.

- RSA 2048 bitar (minst) eller 4096 bitar.

 > [!Note]
 > Använda en nyckel längd på 4096bits:
 > - SSL-handskakningen vid starten av varje anslutning blir långsammare.  
 > - CPU-användningen ökar under hand skakningarna. 

**Certifikatskedja**

- Den mellanliggande certifikat fil (om sådan finns) som angavs av din certifikat utfärdare

- CA-certifikatet som utfärdade serverns certifikat ska vara först i filen, följt av andra upp till roten. 
- Kan innehålla Bag-attribut.

**Fraser**

- En nyckel stöds.

- Konfigurera när du importerar certifikatet.

Certifikat med andra parametrar kan fungera, men Microsoft stöder inte dem.

#### <a name="encryption-key-artifacts"></a>Artefakter för krypterings nyckel

**. PEM – certifikat container fil**

Privacy Enhanced Mail-filer (PEM) var den allmänna filtypen som används för att skydda e-postmeddelanden. Nuförtiden, PEM-filer används med certifikat och använder x509 ASN1-nycklar.  

Container filen definieras i RFC 1421 till 1424, ett behållar format som bara kan innehålla det offentliga certifikatet. Exempelvis installerar Apache, ett CA-certifikat, filer, osv. SSL eller certifikat. Detta kan inkludera en hel certifikat kedja, inklusive offentlig nyckel, privat nyckel och rot certifikat.  

Det kan också koda en CSR som PKCS10-format, som kan översättas till PEM.

**. cert. cer. CRT – certifikat behållare fil**

En `.pem` eller en `.der` formaterad fil med ett annat fil namns tillägg. Filen identifieras av Utforskaren som ett certifikat. `.pem`   Filen känns inte igen av Utforskaren i Windows.

**. nyckel – privat nyckel fil**

En nyckel fil är i samma format som en PEM-fil, men har ett annat fil namns tillägg.

#### <a name="additional-commonly-available-key-artifacts"></a>Ytterligare viktiga artefakter som ofta är tillgängliga

**. CSR – certifikat signerings förfrågan**.  

Den här filen används för att skicka in till certifikat utfärdare. Det faktiska formatet är PKCS10, som definieras i RFC 2986 och kan innehålla några eller alla nyckel uppgifter för det begärda certifikatet. Till exempel ämne, organisation och tillstånd. Det är den offentliga nyckeln för certifikatet som signeras av CA: n och tar emot ett certifikat i retur.  

Det returnerade certifikatet är det offentliga certifikatet, som innehåller den offentliga nyckeln men inte den privata nyckeln. 

**. PKCS12. pfx. p12 – lösen ords behållare**. 

Som ursprungligen definierades av RSA i Public-Key Cryptography Standards (PKCS) har 12-varianten ursprungligen förbättrats av Microsoft och senare skickades som RFC 7292.  

Det här behållar formatet kräver ett lösen ord som innehåller både offentliga och privata certifikat par. Till skillnad från `.pem`   filer är behållaren fullständigt krypterad.  

Du kan använda OpenSSL för att göra detta till en `.pem`   fil med både offentliga och privata nycklar: `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

**. der – binär kodad PEM**.

Sättet att koda ASN. 1-syntaxen i Binary är via en `.pem`   fil som bara är en Base64-kodad `.der` fil. 

OpenSSL kan konvertera dessa filer till en `.pem` :  `openssl x509 -inform der -in to-convert.der -out converted.pem` .  

Windows kommer att identifiera dessa filer som certifikatfiler. Som standard kommer Windows att exportera certifikat som `.der` formaterade filer med ett annat fil namns tillägg.  

**. lista över återkallade certifikat**.  
Certifikat utfärdare producerar dessa filer som ett sätt att de ska godkänna certifikat innan de upphör att gälla.
 
##### <a name="cli-commands"></a>CLI-kommandon

Använd `cyberx-xsense-certificate-import` CLI-kommandot för att importera certifikat. Om du vill använda det här verktyget måste du ladda upp certifikatfiler till enheten med hjälp av verktyg som WinSCP eller wget.

Kommandot stöder följande ingångs flaggor:

- `-h`: Visar kommando rads hjälpens syntax.

- `--crt`: Sökväg till en certifikat fil (. CRT-tillägg).

- `--key`:  \* . nyckel fil. Nyckel längden måste vara minst 2 048 bitar.

- `--chain`: Sökväg till en certifikat kedjas fil (valfritt).

- `--pass`: Lösen frasen som används för att kryptera certifikatet (valfritt).

- `--passphrase-set`: Standard = `False` , oanvänd. Ange till `True` att använda den tidigare lösen frasen som angavs med det tidigare certifikatet (valfritt).

När du använder CLI-kommandot:

- Kontrol lera att certifikatmallarna är läsbara på enheten.

- Kontrol lera att domän namnet och IP-adressen i certifikatet matchar konfigurationen som IT-avdelningen har planerat.

### <a name="use-openssl-to-manage-certificates"></a>Använda OpenSSL för att hantera certifikat

Hantera dina certifikat med följande kommandon:

| Beskrivning | CLI-kommando |
|--|--|
| Generera en ny privat nyckel och certifikat signerings förfrågan | `openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout privateKey.key` |
| Generera ett självsignerat certifikat | `openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt` |
| Generera en certifikat signerings förfrågan (CSR) för en befintlig privat nyckel | `openssl req -out CSR.csr -key privateKey.key -new` |
| Generera en begäran om certifikat signering baserat på ett befintligt certifikat | `openssl x509 -x509toreq -in certificate.crt -out CSR.csr -signkey privateKey.key` |
| Ta bort en lösen fras från en privat nyckel | `openssl rsa -in privateKey.pem -out newPrivateKey.pem` |

Använd de här kommandona om du behöver kontrol lera informationen i ett certifikat, CSR eller privat nyckel.

| Beskrivning | CLI-kommando |
|--|--|
| Kontrol lera en begäran om certifikat signering (CSR) | `openssl req -text -noout -verify -in CSR.csr` |
| Kontrol lera en privat nyckel | `openssl rsa -in privateKey.key -check` |
| Kontrol lera ett certifikat | `openssl x509 -in certificate.crt -text -noout`  |

Om du får ett fel meddelande om att den privata nyckeln inte matchar certifikatet, eller om ett certifikat som har installerats på en plats inte är betrott, använder du följande kommandon för att åtgärda felet.

| Beskrivning | CLI-kommando |
|--|--|
| Kontrol lera en MD5-hash av den offentliga nyckeln för att kontrol lera att den matchar det som finns i en CSR eller privat nyckel | 81.1. `openssl x509 -noout -modulus -in certificate.crt | openssl md5` <br /> 11.2. `openssl rsa -noout -modulus -in privateKey.key | openssl md5` <br /> 3. `openssl req -noout -modulus -in CSR.csr | openssl md5 ` |

Om du vill konvertera certifikat och nycklar till olika format så att de är kompatibla med vissa typer av servrar, eller program, använder du dessa kommandon.

| Beskrivning | CLI-kommando |
|--|--|
| Konvertera en DER-fil (. CRT. cer. der) till PEM  | `openssl x509 -inform der -in certificate.cer -out certificate.pem`  |
| Konvertera en PEM-fil till DER | `openssl x509 -outform der -in certificate.pem -out certificate.der`  |
| Konvertera en PKCS # 12-fil (. pfx. P12) som innehåller en privat nyckel och certifikat till PEM | `openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes` <br />Du kan lägga till `-nocerts` enbart för att skriva ut den privata nyckeln eller lägga till `-nokeys` enbart för att spara certifikaten. |
| Konvertera en PEM certifikat fil och en privat nyckel till PKCS # 12 (. pfx. P12) | `openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt` |

## <a name="connect-a-sensor-to-the-management-console"></a>Ansluta en sensor till hanterings konsolen

I det här avsnittet beskrivs hur du säkerställer anslutningen mellan sensorn och den lokala hanterings konsolen. Du måste göra detta om du arbetar i ett Air-gapped-nätverk och vill skicka enhets-och aviserings information till hanterings konsolen från sensorn. Den här anslutningen gör det också möjligt för hanterings konsolen att skicka Systeminställningar till sensorn och utföra andra hanterings uppgifter på sensorn.

För att ansluta:

1. Logga in på den lokala hanterings konsolen.

2. Välj **Systeminställningar**.

3. I avsnittet **sensor installation – anslutnings sträng** kopierar du den automatiskt genererade anslutnings strängen.

   :::image type="content" source="media/how-to-manage-individual-sensors/connection-string-screen.png" alt-text="Kopiera anslutnings strängen från den här skärmen."::: 

4. Logga in på sensor konsolen.

5. I den vänstra rutan väljer du **Systeminställningar**.

6. Välj **hanterings konsol anslutning**.

    :::image type="content" source="media/how-to-manage-individual-sensors/management-console-connection-screen.png" alt-text="Skärm bild av dialog rutan anslutning till hanterings konsol.":::

7. Klistra in anslutnings strängen i rutan **anslutnings sträng** och välj **Anslut**.

8. I den lokala hanterings konsolen, i fönstret **plats hantering** , tilldelar du sensorn till en zon.

## <a name="change-the-name-of-a-sensor"></a>Ändra namnet på en sensor

Du kan ändra namnet på din sensor konsol. Det nya namnet kommer att visas i konsolens webbläsare, i olika konsol fönster och i fel söknings loggar.

Processen för att ändra sensor namn varierar för lokalt anslutna sensorer och moln anslutna sensorer. Standard namnet är **sensor**.

### <a name="change-the-name-of-a-locally-connected-sensor"></a>Ändra namnet på en lokalt ansluten sensor

Så här ändrar du namnet:

1. Längst ned i det vänstra fönstret i konsolen väljer du den aktuella sensor etiketten.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/label-name.png" alt-text="Skärm bild som visar sensor etiketten.":::

1. Ange ett namn i dialog rutan **Redigera sensor namn** .

1. Välj **Spara**. Det nya namnet används.

### <a name="change-the-name-of-a-cloud-connected-sensor"></a>Ändra namnet på en molnbaserad sensor

Om sensorn har registrerats som en molnbaserad sensor definieras sensor namnet av namnet som tilldelats under registreringen. Namnet ingår i aktiverings filen som du laddade upp när du loggade in för första gången. Om du vill ändra namnet på sensorn måste du ladda upp en ny aktiverings fil.

Så här ändrar du namnet:

1. På Azure Defender för IoT-portalen går du till sidan platser och sensorer.

1. Ta bort sensorn från sidan platser och sensorer.

1. Registrera med det nya namnet genom att välja **onboard sensor** på sidan komma igång.

1. Hämta den nya aktiverings filen.

1. Logga in på konsolen Defender för IoT-sensorn.

1. I sensor konsolen väljer du **Systeminställningar** och väljer sedan **Återaktivering**.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Ladda upp aktiverings filen för att återaktivera sensorn.":::

1. Välj **överför** och välj den fil som du sparade.

1. Välj **Aktivera**.

## <a name="update-the-sensor-network-configuration"></a>Uppdatera sensor nätverks konfigurationen

Sensor nätverks konfigurationen definierades under sensor installationen. Du kan ändra konfigurations parametrar. Du kan också konfigurera en proxykonfiguration.

Om du skapar en ny IP-adress kan du behöva logga in igen.

Ändra konfigurationen:

1. På menyn på sidan väljer du **Systeminställningar**.

2. I fönstret **system inställningar** väljer du **nätverk**.

    :::image type="content" source="media/how-to-manage-individual-sensors/edit-network-configuration-screen.png" alt-text="Konfigurera dina nätverks inställningar.":::

3. Ange parametrarna:

    | Parameter | Beskrivning |
    |--|--|
    | IP-adress | Sensorns IP-adress |
    | Nätmask | Mask adressen |
    | Standard gateway | Standardgateway-adressen |
    | DNS | DNS-serveradressen |
    | Värdnamn | Sensorns värdnamn |
    | Proxy | Proxyserverns värd och port namn |

4. Välj **Spara**.

## <a name="synchronize-time-zones-on-the-sensor"></a>Synkronisera tids zoner på sensorn

Du kan konfigurera sensorns tid och region så att alla användare ser samma tid och region.

:::image type="content" source="media/how-to-manage-individual-sensors/time-and-region.png" alt-text="Konfigurera tid och region.":::

| Parameter | Beskrivning |
|--|--|
| Tidszon | Tids zon definitionen för:<br />– Aviseringar<br />-Trender och statistik-widgetar<br />– Data utvinnings rapporter<br />   – Riskbedömnings rapporter<br />– Angrepps vektorer |
| Datum format | Välj något av följande format alternativ:<br />-DD/MM/ÅÅÅÅ HH: mm: SS<br />-MM/DD/ÅÅÅÅ HH: mm: SS<br />-åååå/MM/dd HH: mm: SS |
| Datum och tid | Visar aktuellt datum och lokal tid i det format som du har valt.<br />Om den faktiska platsen till exempel är America och New York, men tids zonen har angetts till Europa och Berlin, visas tiden enligt Berlin lokal tid. |

Så här konfigurerar du sensor tiden:

1. På menyn på sidan väljer du **Systeminställningar**.

2. I fönstret **Systeminställningar** väljer du **tid & regional**.

3. Ange parametrarna och välj **Spara**.

## <a name="set-up-backup-and-restore-files"></a>Konfigurera säkerhets kopiering och återställning av filer

System säkerhets kopiering utförs automatiskt vid 3:00 per dag. Data sparas på en annan disk i sensorn. Standardplatsen är `/var/cyberx/backups`.

Du kan överföra filen automatiskt till det interna nätverket.

> [!NOTE]
> - Proceduren för säkerhets kopiering och återställning kan bara utföras mellan samma versioner.
> - I vissa arkitekturer är säkerhets kopieringen inaktive rad. Du kan aktivera det i `/var/cyberx/properties/backup.properties` filen.

När du styr en sensor med hjälp av den lokala hanterings konsolen kan du använda sensorns säkerhets kopierings schema för att samla in dessa säkerhets kopior och lagra dem i hanterings konsolen eller på en extern säkerhets kopierings Server.

**Vad säkerhets kopie ras:** Konfigurationer och data.

**Vad säkerhets kopie ras inte:** PCAP-filer och loggar. Du kan säkerhetskopiera och återställa PCAPs och loggar manuellt.

Sensor säkerhets kopierings filer får automatiskt namnet i följande format: `<sensor name>-backup-version-<version>-<date>.tar` . Ett exempel är `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`.

Så här konfigurerar du säkerhets kopiering:

- Logga in på ett administratörs konto och ange `$ sudo cyberx-xsense-system-backup` .

Så här återställer du den senaste säkerhets kopierings filen:

- Logga in på ett administratörs konto och ange `$ sudo cyberx-xsense-system-restore` .

Så här sparar du säkerhets kopian på en extern SMB-server:

1. Skapa en delad mapp i den externa SMB-servern.

    Hämta sökvägen till mappen, användar namnet och lösen ordet som krävs för att få åtkomst till SMB-servern.

2. I sensorn skapar du en katalog för säkerhets kopiorna:

    - `sudo mkdir /<backup_folder_name_on_cyberx_server>`

    - `sudo chmod 777 /<backup_folder_name_on_cyberx_server>/`

3. Redigera `fstab` :

    - `sudo nano /etc/fstab`

    - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifsrw,credentials=/etc/samba/user,vers=X.X,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0`

4. Redigera och skapa autentiseringsuppgifter för att dela SMB-servern:

    `sudo nano /etc/samba/user` 

5. Lägg till:

    - `username=&gt:user name&lt:`

    - `password=<password>`

6. Montera katalogen:

    `sudo mount -a`

7. Konfigurera en säkerhets kopierings katalog till den delade mappen i Defender for IoT-sensorn:  

    - `sudo nano /var/cyberx/properties/backup.properties`

    - `set backup_directory_path to <backup_folder_name_on_cyberx_server>`

### <a name="restore-sensors"></a>Återställa sensorer

Du kan återställa säkerhets kopior från sensor konsolen och med hjälp av CLI.

**Så här återställer du från-konsolen:**

- Välj **Återställ avbildning** från sensorns **system inställningar** -fönster.

:::image type="content" source="media/how-to-manage-individual-sensors/restore-image-screen.png" alt-text="Återställ din avbildning genom att välja knappen.":::

I konsolen visas återställnings problem.

**Återställa med hjälp av CLI:**

- Logga in på ett administratörs konto och ange `$ sudo cyberx-management-system-restore` .

## <a name="update-a-standalone-sensor-version"></a>Uppdatera en fristående sensor version

Följande procedur beskriver hur du uppdaterar en fristående sensor med hjälp av sensor konsolen. Uppdaterings processen tar cirka 30 minuter.

1. Gå till [Azure-portalen](https://portal.azure.com/).

2. Gå till Defender för IoT.

3. Gå till sidan **uppdateringar** .

   :::image type="content" source="media/how-to-manage-individual-sensors/updates-page.png" alt-text="Skärm bild av sidan uppdateringar i Defender för IoT.":::

4. Välj **Hämta** från **sensorer** -avsnittet och spara filen.

5. I sensor konsolens sidpanel väljer du **Systeminställningar**.

6. I fönstret **versions uppgradering** väljer du **Uppgradera**.

    :::image type="content" source="media/how-to-manage-individual-sensors/upgrade-pane-v2.png" alt-text="Skärm bild av uppgraderings fönstret.":::

7. Välj den fil som du laddade ned från sidan Defender för IoT- **uppdateringar** .

8. Uppdaterings processen startar, och den tid då systemet startas om två gånger. Efter den första omstarten (innan uppdaterings processen har slutförts) öppnas systemet med inloggnings fönstret. När du har loggat in visas uppgraderings versionen längst ned till vänster på sido panelen.

    :::image type="content" source="media/how-to-manage-individual-sensors/defender-for-iot-version.png" alt-text="Skärm bild av uppgraderings versionen som visas när du loggar in.":::

## <a name="forward-sensor-failure-alerts"></a>Aviseringar om vidarebefordrande sensor

Du kan vidarebefordra aviseringar till tredje part för att ge information om:

- Frånkopplade sensorer

- Fjärranslutna säkerhets kopierings problem

Den här informationen skickas när du skapar en vidarebefordrings regel för system meddelanden.

> [!NOTE]
> Administratörer kan skicka system meddelanden.

Skicka meddelanden:

1. Logga in på den lokala hanterings konsolen.
1. Välj **vidarebefordra** på menyn på sidan.
1. Skapa en vidarebefordrings regel.
1. Välj **rapport system meddelanden**.

Mer information om regler för vidarebefordran finns i [vidarebefordra aviserings information](how-to-forward-alert-information-to-partners.md).

## <a name="adjust-system-properties"></a>Justera system egenskaper

System egenskaper styr olika åtgärder och inställningar i sensorn. Att redigera eller ändra dem kan skada driften av sensor konsolen.

Kontakta [Microsoft Support](https://support.microsoft.com/) innan du ändrar inställningarna.

För att komma åt system egenskaper:

1. Logga in på den lokala hanterings konsolen eller sensorn.

2. Välj **Systeminställningar**.

3. Välj **system egenskaper** i avsnittet **Allmänt** .

## <a name="see-also"></a>Se även

[Forsknings och paket för hot information](how-to-work-with-threat-intelligence-packages.md)

[Hantera sensorer från hanterings konsolen](how-to-manage-sensors-from-the-on-premises-management-console.md)