---
title: Hantera den lokala hanteringskonsolen
description: Lär dig mer om lokala hanterings konsol alternativ som säkerhets kopiering och återställning, definierar värd namnet och konfigurerar en proxy till sensorer.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: f3c9f8f78f17153c3d2eb7b014cf616253b3c0c9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618261"
---
# <a name="manage-the-on-premises-management-console"></a>Hantera den lokala hanteringskonsolen

Den här artikeln beskriver lokala hanterings konsol alternativ som säkerhets kopiering och återställning, hämtning av enhets aktiverings fil, uppdatering av certifikat och installation av proxy till sensorer.

Du har publicerat den lokala hanterings konsolen från Azure Portal.

## <a name="upload-an-activation-file"></a>Ladda upp en aktiverings fil

Första gången du loggar in, hämtas en aktiverings fil för den lokala hanterings konsolen. Den här filen innehåller de aggregerade allokerade enheter som definieras under onboarding-processen. Listan innehåller sensorer som är associerade med flera prenumerationer.

Efter den första aktiveringen kan antalet övervakade enheter överstiga antalet allokerade enheter som definierats under onboarding. Den här händelsen kan inträffa, till exempel om du ansluter fler sensorer till hanterings konsolen. Om det finns en avvikelse mellan antalet övervakade enheter och antalet allokerade enheter visas en varning i hanterings konsolen. Om den här händelsen inträffar bör du ladda upp en ny aktiverings fil.

Ladda upp en aktiverings fil:

1. Gå till **pris** sidan för Azure Defender för IoT.
1. Välj fliken **Ladda ned aktiverings filen för hanterings konsolen** . Aktiverings filen har laddats ned.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="Hämta aktiverings filen.":::

1. Välj **system inställningar** från hanterings konsolen.
1. Välj **aktivering**.
1. Välj **Välj en fil** och välj den fil som du sparade.

## <a name="manage-certificates"></a>Hantera certifikat

Efter installationen av den lokala hanterings konsolen skapas ett lokalt självsignerat certifikat som används för att få åtkomst till hanterings konsolens webb program. När administratörs användare loggar in i hanterings konsolen för första gången uppmanas de att tillhandahålla ett SSL/TLS-certifikat. Mer information om installations programmet för första gången finns i [Aktivera och konfigurera den lokala hanterings konsolen](how-to-activate-and-set-up-your-on-premises-management-console.md).

I följande avsnitt finns information om hur du uppdaterar certifikat, arbetar med certifikat-CLI-kommandon och certifikat parametrar som stöds.

### <a name="about-certificates"></a>Om certifikat

Azure Defender för IoT använder SSL-och TLS-certifikat för att:

- Uppfylla särskilda certifikat-och krypterings krav som din organisation har begärt genom att ladda upp det CA-signerade certifikatet.

- Tillåt verifiering mellan hanterings konsolen och anslutna sensorer och mellan en hanterings konsol och en hanterings konsol med hög tillgänglighet. Verifieringen utvärderas mot en lista över återkallade certifikat och certifikatets förfallo datum. *Om verifieringen Miss lyckas stoppas kommunikationen mellan hanterings konsolen och sensorn och ett verifierings fel visas i-konsolen*. Det här alternativet är aktiverat som standard efter installationen.

Regler för vidarebefordran från tredje part har inte verifierats. Exempel är aviserings information som skickas till SYSLOG, Splunk eller ServiceNow; och kommunikation med Active Directory.

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

### <a name="update-certificates"></a>Uppdatera certifikat

Administratörs användare av den lokala hanterings konsolen kan uppdatera certifikat.

Så här uppdaterar du ett certifikat:  

1. Välj **Systeminställningar**.

1. Välj **SSL/TLS-certifikat**.
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

- Lokalt genererat på produkten (lokalt självsignerat) 

  > [!IMPORTANT]
  > Vi rekommenderar inte att du använder ett självsignerat certifikat. Den här typen av anslutning är inte säker och bör endast användas för test miljöer. Eftersom certifikatets ägare inte kan verifieras, och säkerheten för systemet inte kan upprätthållas, ska de självsignerade certifikaten aldrig användas för produktions nätverk.

### <a name="supported-ssl-certificates"></a>SSL-certifikat som stöds 

Följande parametrar stöds: 

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
- Subjekt (OU) org. enhet = definierad; till exempel contoso Labs
- Subject (O) rganisationsnamn = definierad; till exempel contoso Inc

**Nyckel fil**

- Nyckel filen som skapades när du skapade CSR

- RSA 2048 bitar (minst) eller 4096 bitar

 > [!Note]
 > Använda en nyckel längd på 4096bits:
 > - SSL-handskakningen vid starten av varje anslutning blir långsammare.  
 > - CPU-användningen ökar under hand skakningarna. 

**Certifikat kedja**

- Den mellanliggande certifikat fil (om sådan finns) som angavs av din certifikat utfärdare.

- CA-certifikatet som utfärdade serverns certifikat ska vara först i filen, följt av andra upp till roten. 
- Kedjan kan innehålla Bag-attribut.

**Fraser**

- En nyckel stöds.

- Konfigurera när du importerar certifikatet.

Certifikat med andra parametrar kan fungera, men Microsoft stöder inte dem.

#### <a name="encryption-key-artifacts"></a>Artefakter för krypterings nyckel

**. pem: certifikat behållar filen**

Privacy Enhanced Mail-filer (PEM) var den allmänna filtypen som används för att skydda e-postmeddelanden. Nuförtiden, PEM-filer används med certifikat och använder x509 ASN1-nycklar.  

Container filen definieras i RFC 1421 till 1424, ett behållar format som bara kan innehålla det offentliga certifikatet. Exempelvis installerar Apache, ett CA-certifikat, filer, osv. SSL eller certifikat. Detta kan inkludera en hel certifikat kedja, inklusive offentlig nyckel, privat nyckel och rot certifikat.  

Det kan också koda en CSR som PKCS10-format, som kan översättas till PEM.

**. cert. cer. CRT: certifikat behållare fil**

En `.pem` eller en `.der` formaterad fil med ett annat fil namns tillägg. Filen identifieras av Utforskaren som ett certifikat. `.pem`   Filen känns inte igen av Utforskaren i Windows.

**. nyckel: fil för privat nyckel**

En nyckel fil är i samma format som en PEM-fil, men har ett annat fil namns tillägg. 

#### <a name="additional-commonly-available-key-artifacts"></a>Ytterligare viktiga artefakter som ofta är tillgängliga

**. CSR – certifikat signerings förfrågan**.  

Den här filen används för att skicka in till certifikat utfärdare. Det faktiska formatet är PKCS10, som definieras i RFC 2986 och kan innehålla några eller alla nyckel uppgifter för det begärda certifikatet. Till exempel ämne, organisation och tillstånd. Det är den offentliga nyckeln för certifikatet som signeras av CA: n och tar emot ett certifikat i retur.  

Det returnerade certifikatet är det offentliga certifikatet, som innehåller den offentliga nyckeln men inte den privata nyckeln. 

**. PKCS12. pfx. p12 – lösen ords behållare**. 

Som ursprungligen definierades av RSA i Public-Key Cryptography Standards (PKCS) har 12-varianten ursprungligen förbättrats av Microsoft och senare skickades som RFC 7292.  

Det här behållar formatet kräver ett lösen ord som innehåller både offentliga och privata certifikat par. Till skillnad från `.pem`   filer är behållaren fullständigt krypterad.  

Du kan använda OpenSSL för att omvandla filen till en `.pem`   fil med både offentliga och privata nycklar: `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

**. der – binär kodad PEM**.

Sättet att koda ASN. 1-syntaxen i Binary är via en `.pem`   fil som bara är en Base64-kodad `.der` fil. 

OpenSSL kan konvertera dessa filer till en `.pem` :  `openssl x509 -inform der -in to-convert.der -out converted.pem` .  

Windows kommer att identifiera dessa filer som certifikatfiler. Som standard kommer Windows att exportera certifikat som `.der` formaterade filer med ett annat fil namns tillägg.

**. lista över återkallade certifikat**.  

Certifikat utfärdare producerar dessa som ett sätt att de ska godkänna certifikat innan de upphör att gälla. 

#### <a name="cli-commands"></a>CLI-kommandon

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

## <a name="define-backup-and-restore-settings"></a>Definiera inställningar för säkerhets kopiering och återställning

Den lokala hanterings konsolens system säkerhets kopiering utförs automatiskt, varje dag. Data sparas på en annan disk. Standardplatsen är `/var/cyberx/backups`. 

Du kan överföra filen automatiskt till det interna nätverket. 

> [!NOTE]
> Du kan bara utföra säkerhets kopierings-och återställnings proceduren i samma version. 

Så här säkerhetskopierar du den lokala hanterings konsol datorn: 

- Logga in på ett administratörs konto och ange `sudo cyberx-management-backup -full` .

Så här återställer du den senaste säkerhets kopierings filen:

- Logga in på ett administratörs konto och ange `$ sudo cyberx-management-system-restore` .

Så här sparar du säkerhets kopian på en extern SMB-server:

1. Skapa en delad mapp i den externa SMB-servern.  

   Hämta sökvägen till mappen, användar namnet och lösen ordet som krävs för att få åtkomst till SMB-servern. 

2. I Defender för IoT skapar du en katalog för säkerhets kopiorna:

   - `sudo mkdir /<backup_folder_name_on_ server>` 

   - `sudo chmod 777 /<backup_folder_name_on_c_server>/` 

3. Redigera fstab:  

   - `sudo nano /etc/fstab` 

   - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

4. Redigera eller skapa autentiseringsuppgifter för SMB-servern att dela: 

   - `sudo nano /etc/samba/user` 

5. Lägg till: 

   - `username=<user name>`

   - `password=<password>` 

6. Montera katalogen: 

   - `sudo mount -a` 

7. Konfigurera en säkerhets kopierings katalog till den delade mappen i den lokala hanterings konsolen Defender för IoT:  

   - `sudo nano /var/cyberx/properties/backup.properties` 

   - `set Backup.shared_location to <backup_folder_name_on_server>`

## <a name="edit-the-host-name"></a>Redigera värd namnet

Redigera hanterings konsolens värdnamn som kon figurer ATS i organisationens DNS-Server:

1. I hanterings konsolens vänstra ruta väljer du **Systeminställningar**.  

2. I konsol avsnittet nätverk väljer du **nätverk**. 

3. Ange värd namnet som kon figurer ATS i organisationens DNS-server. 

4. Välj **Spara**.

## <a name="define-vlan-names"></a>Definiera VLAN-namn

VLAN-namn synkroniseras inte mellan sensorn och hanterings konsolen. Du bör definiera identiska namn på komponenter.

I nätverks ytan väljer du **VLAN** och lägger till namn till de IDENTIFIERAde VLAN-ID: na. Välj sedan **Spara**.

## <a name="define-a-proxy-to-sensors"></a>Definiera en proxy till sensorer

Förbättra systemsäkerheten genom att förhindra att användare loggar in direkt i sensorn. Använd i stället proxy-tunnlar för att låta användarna komma åt sensorn från den lokala hanterings konsolen med en enda brand Väggs regel. Den här förbättringen begränsar risken för obehörig åtkomst till nätverks miljön utöver sensorn.

Använd en proxy i miljöer där det inte finns någon direkt anslutning till sensorer.

:::image type="content" source="media/how-to-access-sensors-using-a-proxy/proxy-diagram.png" alt-text="Användarvänlig.":::

Följande procedur ansluter en sensor till den lokala hanterings konsolen och aktiverar tunnel i den konsolen:

1. Logga in på den lokala hanterings konsolens utrustning CLI med administratörs behörighet.

2. Skriv `sudo cyberx-management-tunnel-enable` och välj **RETUR**.

4. Skriv `--port 10000` och välj **RETUR**.

## <a name="adjust-system-properties"></a>Justera system egenskaper

System egenskaper styr olika åtgärder och inställningar i hanterings konsolen. Att redigera eller ändra dem kan skada hanterings konsolens åtgärd. Kontakta [Microsoft Support](https://support.microsoft.com) innan du ändrar dina inställningar.

För att komma åt system egenskaper: 

1. Logga in på den lokala hanterings konsolen eller sensorn.

2. Välj **Systeminställningar**.

3. Välj **system egenskaper** i avsnittet **Allmänt** .

## <a name="change-the-name-of-the-on-premises-management-console"></a>Ändra namnet på den lokala hanterings konsolen

Du kan ändra namnet på den lokala hanterings konsolen. De nya namnen visas i konsolens webbläsare, i olika konsol fönster och i fel söknings loggar. Standard namnet är **hanterings konsolen**.

Så här ändrar du namnet:

1. Längst ned i det vänstra fönstret väljer du det aktuella namnet.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/console-name.png" alt-text="Skärm bild av version för den lokala hanterings konsolen.":::

2. Ange det nya namnet i dialog rutan **Redigera konfiguration av hanterings konsol** . Namnet får innehålla högst 25 tecken.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/edit-management-console-configuration.png" alt-text="Skärm bild av redigering av Defender för IoT Platform-konfigurationen.":::

3. Välj **Spara**. Det nya namnet används.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/name-changed.png" alt-text="Skärm bild som visar det ändrade namnet på konsolen.":::

## <a name="password-recovery"></a>Lösen ords återställning

Lösen ords återställning för den lokala hanterings konsolen är knuten till den prenumeration som enheten är ansluten till. Du kan inte återställa ett lösen ord om du inte vet vilken prenumeration en enhet är ansluten till.

Så här återställer du ditt lösenord:

1. Gå till den lokala hanterings konsolens inloggnings sida.
1. Välj **återställning av lösen ord**.
1. Kopiera den unika identifieraren.
1. Gå till sidan Defender för IoT- **platser och sensorer** och välj fliken **Återställ mitt lösen ord** .
1. Ange den unika identifieraren och välj **Återställ**. Aktiverings filen har laddats ned.
1. Gå till sidan för **lösen ords återställning** och överför aktiverings filen.
1. Välj **Nästa**.
 
   Du har nu fått ditt användar namn och ett nytt systemgenererat lösen ord.

> [!NOTE]
> Sensorn är länkad till den prenumeration som den ursprungligen var ansluten till. Du kan bara återställa lösen ordet genom att använda samma prenumeration som den är kopplad till.

## <a name="update-the-software-version"></a>Uppdatera program varu versionen

Följande procedur beskriver hur du uppdaterar program versionen för den lokala hanterings konsolen. Uppdaterings processen tar cirka 30 minuter.

1. Gå till [Azure-portalen](https://portal.azure.com/).

1. Gå till Defender för IoT.

1. Gå till sidan **uppdateringar** .

1. Välj en version i avsnittet lokal hanterings konsol.

1. Välj **Hämta** och spara filen.

1. Logga in på den lokala hanterings konsolen och välj **Systeminställningar** på menyn på sidan.

1. I fönstret **versions uppdatering** väljer du **Uppdatera**.

1. Välj den fil som du laddade ned från sidan Defender för IoT- **uppdateringar** .

## <a name="mail-server-settings"></a>Inställningar för e-postserver

Definiera SMTP-inställningar för e-postservern för den lokala hanterings konsolen.

För att definiera:

1. Logga in på CLI för lokal hantering med administratörs behörighet.
1. Skriv ```nano /var/cyberx/properties/remote-interfaces.properties```.
1. Välj Retur. Följande prompter visas.
```mail.smtp_server= ```
```mail.port=25 ```
```mail.sender=```
1. Ange SMTP-servernamnet och avsändarens namn och välj RETUR.

## <a name="see-also"></a>Se även

[Hantera sensorer från hanterings konsolen](how-to-manage-sensors-from-the-on-premises-management-console.md)

[Hantera enskilda sensorer](how-to-manage-individual-sensors.md)
