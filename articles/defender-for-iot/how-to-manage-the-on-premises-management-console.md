---
title: Hantera den lokala hanterings konsolen
description: Lär dig mer om lokala hanterings konsol alternativ som säkerhets kopiering och återställning, definierar värd namnet och konfigurerar en proxy till sensorer.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/12/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 34efef4a01b58cc26fd1567336184837a703ade2
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2020
ms.locfileid: "97842000"
---
# <a name="manage-the-on-premises-management-console"></a>Hantera den lokala hanterings konsolen

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

- Tillåt verifiering mellan hanterings konsolen och anslutna sensorer och mellan en hanterings konsol och en hanterings konsol med hög tillgänglighet. Verifieringen utvärderas mot en lista över återkallade certifikat och certifikatets förfallo datum. *Om verifieringen Miss lyckas stoppas kommunikationen mellan hanterings konsolen och sensorn och ett verifierings fel visas i-konsolen.* Det här alternativet är aktiverat som standard efter installationen.

Regler för vidarebefordran från tredje part har inte verifierats. Exempel är aviserings information som skickas till SYSLOG, Splunk eller ServiceNow; och kommunikation med Active Directory.

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
  > Vi rekommenderar inte att du använder självsignerade certifikat. Den här anslutningen är inte säker och bör endast användas för test miljöer. Ägaren av certifikatet kan inte verifieras och säkerheten i systemet kan inte upprätthållas. Självsignerade certifikat ska aldrig användas för produktions nätverk.  

Följande parametrar stöds: 

**Certifikat CRT**

- Den primära certifikat filen för ditt domän namn
- Signaturalgoritm = SHA256RSA
- Hash-algoritm för signatur = SHA256
- Giltigt från = giltigt föregående datum
- Giltigt till = giltigt framtida datum
- Offentlig nyckel = RSA 2048 bitar (minst) eller 4096 bitar
- CRL distributions punkt = URL till. CRL-fil
- Subject CN = URL, kan vara ett certifikat med jokertecken. till exempel www.contoso.com eller \* . contoso.com
- Subject (C) ountry = definierad, till exempel US
- Subjekt (OU) org. enhet = definierad; till exempel contoso Labs
- Subject (O) rganisationsnamn = definierad; till exempel contoso Inc

**Nyckel fil**

- Nyckel filen som skapades när du skapade CSR
- RSA 2048 bitar (minst) eller 4096 bitar

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

Namnet är från Privacy Enhanced Mail (PEM), en historisk metod för säker e-post. Behållar formatet är en Base64-översättning av x509 ASN. 1-nycklar.  

Den här filen definieras i RFC 1421 till 1424: ett behållar format som bara innehåller det offentliga certifikatet (till exempel med Apache-installationer, CERTIFIKATUTFÄRDARCERTIFIKAT och ETC SSL-certifikat). Eller så kan det innehålla en hel certifikat kedja, inklusive en offentlig nyckel, en privat nyckel och rot certifikat.  

Det kan också koda en CSR, eftersom PKCS10-formatet kan översättas till PEM.

**. cert. cer. CRT: certifikat behållare fil**

Detta är en. pem-formaterad fil (eller sällan,. der) med ett annat fil namns tillägg. Utforskaren i Windows känner igen den som ett certifikat. Utforskaren känner inte igen filen. pem.

**. nyckel: fil för privat nyckel**

En nyckel fil är samma format som en PEM-fil, men har ett annat fil namns tillägg. 

#### <a name="cli-commands"></a>CLI-kommandon

Använd `cyberx-xsense-certificate-import` CLI-kommandot för att importera certifikat. Om du vill använda det här verktyget måste du ladda upp certifikatfiler till enheten (med hjälp av verktyg som WinSCP eller wget).

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

## <a name="see-also"></a>Se även

[Hantera sensorer från hanterings konsolen](how-to-manage-sensors-from-the-on-premises-management-console.md)

[Hantera enskilda sensorer](how-to-manage-individual-sensors.md)
