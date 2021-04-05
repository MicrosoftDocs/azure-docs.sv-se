---
title: Använda certifikat med Azure Stack Edge Pro GPU | Microsoft Docs
description: Beskriver hur du använder certifikat med Azure Stack Edge Pro GPU-enhet, inklusive varför du ska använda, vilka typer och hur du laddar upp certifikat på enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 534870e6bd67b7aa5273289f3154a794a2b9bd22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519018"
---
# <a name="use-certificates-with-azure-stack-edge-pro-gpu-device"></a>Använda certifikat med Azure Stack Edge Pro GPU-enhet

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

I den här artikeln beskrivs vilka typer av certifikat som kan installeras på din Azure Stack Edge Pro-enhet. Artikeln innehåller också information om varje certifikat typ tillsammans med proceduren för att installera och identifiera förfallo datumet.  

## <a name="about-certificates"></a>Om certifikat

Ett certifikat tillhandahåller en länk mellan en **offentlig nyckel** och en entitet (till exempel domän namn) som har **signerats** (verifierats) av en betrodd tredje part (till exempel en **certifikat utfärdare**).  Ett certifikat är ett bekvämt sätt att distribuera betrodda offentliga krypterings nycklar. Certifikat garanterar att kommunikationen är betrodd och att du skickar krypterad information till rätt server. 

När din Azure Stack Edge Pro-enhet konfigureras första gången skapas automatiskt signerade certifikat. Du kan också ta med dina egna certifikat. Det finns rikt linjer som du måste följa om du planerar att ta med dina egna certifikat.

## <a name="types-of-certificates"></a>Typer av certifikat

De olika typerna av certifikat som används på din Azure Stack Edge Pro-enhet är följande: 
- Signeringscertifikat
    - Rotcertifikatutfärdare
    - Medel

- Slut punkts certifikat
    - Node-certifikat
    - Lokala UI-certifikat
    - Azure Resource Manager certifikat
    - Blob Storage-certifikat
    - IoT-enhetens certifikat
    <!--- WiFi certificates
    - VPN certificates-->

- Krypteringscertifikat
    - Stöd för certifikatutfärdarcertifikat

Vart och ett av dessa certifikat beskrivs i detalj i följande avsnitt.

## <a name="signing-chain-certificates"></a>Certifikat för signerings kedja

Detta är certifikaten för den myndighet som signerar certifikaten eller signerings certifikat utfärdaren. 

### <a name="types"></a>Typer

Dessa certifikat kan vara rot certifikat eller mellanliggande certifikat. Rot certifikaten är alltid självsignerade (eller signerade av sig själv). De mellanliggande certifikaten är inte självsignerade och signeras av signerings utfärdaren.

### <a name="caveats"></a>Varningar

- Rot certifikaten ska vara signerings kedjas certifikat.
- Rot certifikaten kan laddas upp på enheten i följande format: 
    - **Der** – dessa är tillgängliga som ett `.cer` fil namns tillägg.
    - **Base-64-kodad** – dessa är tillgängliga som `.cer` fil namns tillägg.
    - **P7b** – det här formatet används endast för signerings kedjas certifikat som innehåller rot-och mellanliggande certifikat.
- Signerings kedjans certifikat laddas alltid ned innan du laddar upp andra certifikat.


## <a name="node-certificates"></a>Node-certifikat

<!--Your Azure Stack Edge Pro device could be a 1-node device or a 4-node device.--> Alla noder i enheten kommunicerar ständigt med varandra och måste därför ha en förtroende relation. Med hjälp av Node-certifikat kan du upprätta förtroendet. Node-certifikat kommer också att spelas upp när du ansluter till noden enhet med hjälp av en fjärran sluten PowerShell-session över https.

### <a name="caveats"></a>Varningar

- Node-certifikatet ska anges i `.pfx` formatet med en privat nyckel som kan exporteras. 
- Du kan skapa och ladda upp ett certifikat med jokertecken eller fyra enskilda Node-certifikat. 
- Ett Node-certifikat måste ändras om DNS-domänen ändras men enhets namnet inte ändras. Om du tar med ditt eget nodnamn kan du inte ändra enhetens serie nummer. du kan bara ändra domän namnet.
- Använd följande tabell för att vägleda dig när du skapar ett Node-certifikat.
   
    |Typ |Ämnes namn (SN)  |Alternativt namn för certifikat mottagare (SAN)  |Exempel på ämnes namn |
    |---------|---------|---------|---------|
    |Nod|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
   

## <a name="endpoint-certificates"></a>Slut punkts certifikat

För alla slut punkter som enheten visar krävs ett certifikat för betrodd kommunikation. Slut punkts certifikaten omfattar de som krävs vid åtkomst till Azure Resource Manager och blob-lagring via REST-API: erna. 

När du använder ett signerat certifikat måste du också ha motsvarande signerings kedja av certifikatet. För signerings kedjan, Azure Resource Manager och blob-certifikaten på enheten behöver du även motsvarande certifikat på klient datorn för att autentisera och kommunicera med enheten.

### <a name="caveats"></a>Varningar

- Slut punkts certifikaten måste vara i `.pfx` formatet med en privat nyckel. Signerings kedjan ska vara DER-format ( `.cer` fil namns tillägg). 
- När du använder dina egna slut punkts certifikat kan det vara enskilda certifikat eller certifikat för en annan domän. 
- Om du ansluter till en signerings kedja måste du ladda upp certifikatet för signerings kedjan innan du överför ett slut punkts certifikat.
- Dessa certifikat måste ändras om enhets namnet eller DNS-domännamnen ändras.
- Ett slut punkts certifikat med jokertecken kan användas.
- Egenskaperna för slut punkts certifikaten liknar dem för ett typiskt SSL-certifikat. 
- Använd följande tabell när du skapar ett slut punkts certifikat:

    |Typ |Ämnes namn (SN)  |Alternativt namn för certifikat mottagare (SAN)  |Exempel på ämnes namn |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob Storage|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Flera SAN-certifikat för båda slut punkterna|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |


## <a name="local-ui-certificates"></a>Lokala UI-certifikat

Du kan komma åt din enhets lokala webb gränssnitt via en webbläsare. För att säkerställa att den här kommunikationen är säker kan du överföra ditt eget certifikat. 

### <a name="caveats"></a>Varningar

- Det lokala UI-certifikatet laddas också upp i ett `.pfx` format med en privat nyckel som kan exporteras.
- När du har laddat upp det lokala UI-certifikatet måste du starta om webbläsaren och rensa cacheminnet. Se de detaljerade anvisningarna för din webbläsare.

    |Typ |Ämnes namn (SN)  |Alternativt namn för certifikat mottagare (SAN)  |Exempel på ämnes namn |
    |---------|---------|---------|---------|
    |Lokalt användar gränssnitt| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
   

## <a name="iot-edge-device-certificates"></a>IoT Edge enhets certifikat

Din Azure Stack Edge Pro-enhet är också en IoT-enhet med den beräkning som har Aktiver ATS av en IoT Edge enhet som är ansluten till den. För säker kommunikation mellan den här IoT Edge enheten och de efterföljande enheterna som kan ansluta till den, kan du också ladda upp IoT Edge certifikat. 

Enheten har självsignerade certifikat som kan användas om du bara vill använda beräknings scenariot med enheten. Om Azure Stack Edge Pro-enheten dock är ansluten till efterföljande enheter måste du ta med dina egna certifikat.

Det finns tre IoT Edge-certifikat som du måste installera för att aktivera förtroende relationen:

- **Rot certifikat utfärdare eller ägarens certifikat utfärdare**
- **Enhetens certifikat utfärdare** 
- **Enhets nyckel certifikat**

### <a name="caveats"></a>Varningar

- IoT Edge certifikaten laddas upp i `.pem` formatet. 


Mer information om IoT Edge certifikat finns i [Azure IoT Edge certifikat information](../iot-edge/iot-edge-certs.md#iot-edge-certificates).

## <a name="support-session-certificates"></a>Stöd för certifikatutfärdarcertifikat

Om din Azure Stack Edge Pro-enhet drabbas av några problem kan en fjärrpowershell-supportbegäran öppnas på enheten för att felsöka problemen. Om du vill aktivera en säker, krypterad kommunikation över den här support-sessionen kan du ladda upp ett certifikat.

### <a name="caveats"></a>Varningar

- Kontrol lera att motsvarande `.pfx` certifikat med privat nyckel är installerat på klient datorn med hjälp av dekrypterings verktyget.
- Kontrol lera att fältet för **nyckel användning** för certifikatet inte är **certifikat signering**. Kontrol lera detta genom att högerklicka på certifikatet, välja **Öppna** och på fliken **information** hittar du **nyckel användning**. 


### <a name="caveats"></a>Varningar

- Support sessionens certifikat måste anges som DER-format med ett `.cer` tillägg.


<!--## VPN certificates

If VPN is configured on your Azure Stack Edge Pro device, then you will also need a certificate for any communication that occurs over the VPN channel. You can bring your own VPN certificate to ensure the communication is trusted.

### Caveats

- The VPN certificate must be uploaded as a pfx format with a  private key.
- The VPN certificate is not dependant on the device name, device serial number, or device configuration. It only requires the external FQDN.
- Make sure that the client OID is set.-->

<!--## WiFi certificates

If your device is configured to operate on a wireless network, then you will also need a WiFi certificate for any communication that occurs over the wireless network. 

### Caveats

- The WiFi certificate must be uploaded as a pfx format with a private key.
- Make sure that the client OID is set.-->


## <a name="create-certificates-optional"></a>Skapa certifikat (valfritt)

I följande avsnitt beskrivs hur du skapar signerings kedjan och slut punkts certifikat.

### <a name="certificate-workflow"></a>Certifikat arbets flöde

Du kommer att ha ett definierat sätt att skapa certifikat för de enheter som används i din miljö. Du kan använda de certifikat som du fått av IT-administratören. 

**I utvecklings-eller test syfte kan du också använda Windows PowerShell för att skapa certifikat i det lokala systemet.** Följ dessa rikt linjer när du skapar certifikat för klienten:

1. Du kan skapa någon av följande typer av certifikat:

    - Skapa ett enskilt certifikat som är giltigt för användning med ett enda fullständigt kvalificerat domän namn (FQDN). Till exempel *mydomain.com*.
    - Skapa ett certifikat med jokertecken för att skydda huvud domän namnet och flera under domäner också. Till exempel **. mydomain.com*.
    - Skapa ett certifikat för alternativt namn på certifikat mottagare (SAN) som ska gälla flera domän namn i ett enda certifikat. 

2. Om du tar med ditt eget certifikat behöver du ett rot certifikat för signerings kedjan. Se steg för att [Skapa certifikat för signerings kedja](#create-signing-chain-certificate).

3. Du kan sedan skapa slut punkts certifikaten för det lokala gränssnittet för installationen av enheten, blobben och Azure Resource Manager. Du kan skapa tre separata certifikat för enheten, blobben och Azure Resource Manager, eller så kan du skapa ett certifikat för alla tre slut punkterna. Detaljerade anvisningar finns i [skapa signerings-och slut punkts certifikat](#create-signed-endpoint-certificates).

4. Oavsett om du skapar tre separata certifikat eller ett certifikat, anger du ämnes namn (SN) och alternativa namn för certifikat mottagare (SAN) enligt de rikt linjer som anges för varje certifikat typ. 

### <a name="create-signing-chain-certificate"></a>Skapa certifikat för signerings kedja

Skapa dessa certifikat via Windows PowerShell som körs i administratörs läge. **De certifikat som skapas på det här sättet bör endast användas i utvecklings-eller test syfte.**

Signerings kedjans certifikat behöver bara skapas en gång. De andra slut punkts certifikaten kommer att referera till det här certifikatet för signering.
 

```azurepowershell
$cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature -Subject "CN=RootCert" -HashAlgorithm sha256 -KeyLength 2048 -CertStoreLocation "Cert:\LocalMachine\My" -KeyUsageProperty Sign -KeyUsage CertSign
```


### <a name="create-signed-endpoint-certificates"></a>Skapa signerade slut punkts certifikat

Skapa dessa certifikat via Windows PowerShell som körs i administratörs läge.

I de här exemplen skapas slut punkts certifikat för en enhet med:
    - Enhets namn: `DBE-HWDC1T2`
    - DNS-domän: `microsoftdatabox.com`

Ersätt med namnet och DNS-domänen för din enhet för att skapa certifikat för enheten.
 
**Certifikat för BLOB-slutpunkt**

Skapa ett certifikat för BLOB-slutpunkten i ditt personliga arkiv.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "*.blob.$AppName.$domain" -Subject "CN=*.blob.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Azure Resource Manager slut punkts certifikat**

Skapa ett certifikat för Azure Resource Manager slut punkter i ditt personliga arkiv.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "management.$AppName.$domain","login.$AppName.$domain" -Subject "CN=management.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Lokalt webb GRÄNSSNITTs certifikat för enhet**

Skapa ett certifikat för det lokala webb gränssnittet på enheten i ditt personliga arkiv.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Ett enda multi-SAN-certifikat för alla slut punkter**

Skapa ett enskilt certifikat för alla slut punkter i ditt personliga arkiv.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"
$DeviceSerial = "HWDC1T2"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain","$DeviceSerial.$domain","management.$AppName.$domain","login.$AppName.$domain","*.blob.$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

När certifikaten har skapats är nästa steg att ladda upp certifikaten på din Azure Stack Edge Pro-enhet


## <a name="upload-certificates"></a>Ladda upp certifikat 

De certifikat som du skapade för enheten som standard finns i det **personliga arkivet** på klienten. Dessa certifikat måste exporteras till klienten i lämpliga format filer som sedan kan överföras till din enhet.

1. Rot certifikatet måste exporteras som DER-format med `.cer` tillägget. Detaljerade anvisningar finns i [Exportera certifikat som der-format](#export-certificates-as-der-format).
2. Slut punkts certifikaten måste exporteras som *PFX* -filer med privata nycklar. Detaljerade anvisningar finns i [Exportera certifikat som *. pfx* -fil med privata nycklar](#export-certificates-as-pfx-format-with-private-key). 
3. Rot-och slut punkts certifikaten laddas sedan upp på enheten med alternativet **+ Lägg till certifikat** på sidan certifikat i det lokala webb gränssnittet. 

    1. Ladda upp rot certifikaten först. I det lokala webb gränssnittet går du till **certifikat > + Lägg till certifikat**.

        ![Lägg till signerings kedjans certifikat 1](media/azure-stack-edge-series-manage-certificates/add-cert-1.png)

    2. Ladda sedan upp slut punkts certifikaten. 

        ![Lägg till certifikat för signerings kedja 2](media/azure-stack-edge-series-manage-certificates/add-cert-2.png)

        Välj certifikatfiler i *PFX* -format och ange lösen ordet du angav när du exporterade certifikatet. Det kan ta några minuter att använda det Azure Resource Manager certifikatet.

        Om signerings kedjan inte uppdateras först och du försöker överföra slut punkts certifikaten får du ett fel meddelande.

        ![Använd certifikat fel](media/azure-stack-edge-series-manage-certificates/apply-cert-error-1.png)

        Gå tillbaka och ladda upp certifikatet för signerings kedjan och ladda upp och Använd slut punkts certifikaten.

> [!IMPORTANT]
> Om enhets namnet eller DNS-domänen ändras måste nya certifikat skapas. Klient certifikaten och enhets certifikaten bör sedan uppdateras med det nya enhets namnet och DNS-domänen. 

## <a name="import-certificates-on-the-client-accessing-the-device"></a>Importera certifikat på klienten som har åtkomst till enheten

De certifikat som du har skapat och laddat upp till din enhet måste importeras på Windows-klienten (åtkomst till enheten) till lämpligt certifikat arkiv.

1. Rot certifikatet som du exporterade som DER bör nu importeras i **betrodda rot certifikat utfärdare** på klient systemet. Detaljerade anvisningar finns i [Importera certifikat till arkivet Betrodda rot certifikat utfärdare](#import-certificates-as-der-format).

2. Slut punkts certifikaten som du exporterade som `.pfx` måste exporteras som der med `.cer` tillägget. Detta `.cer` importeras sedan i det **personliga certifikat arkivet** på datorn. Detaljerade anvisningar finns i [Importera certifikat till det personliga certifikat arkivet](#import-certificates-as-der-format).

### <a name="import-certificates-as-der-format"></a>Importera certifikat som DER-format

Gör så här om du vill importera certifikat på en Windows-klient:

1. Högerklicka på filen och välj **Installera certifikat**. Den här åtgärden startar guiden Importera certifikat.

    ![Importera certifikat 1](media/azure-stack-edge-series-manage-certificates/import-cert-1.png)

2. För **lagrings plats** väljer du **lokal dator** och väljer sedan **Nästa**.

    ![Importera certifikat 2](media/azure-stack-edge-series-manage-certificates/import-cert-2.png)

3. Välj **Placera alla certifikat i följande Arkiv** och välj **Bläddra**. 

    - Om du vill importera till ett personligt arkiv går du till det personliga arkivet för Fjärrvärdet och väljer sedan **Nästa**.

        ![Importera certifikat 4](media/azure-stack-edge-series-manage-certificates/import-cert-4.png)


    - Om du vill importera till ett betrott Arkiv går du till den betrodda rot certifikat utfärdaren och väljer sedan **Nästa**.

        ![Importera certifikat 3](media/azure-stack-edge-series-manage-certificates/import-cert-3.png)

 
4. Välj **Slutför**. Ett meddelande om att importen lyckades visas.

### <a name="export-certificates-as-pfx-format-with-private-key"></a>Exportera certifikat som PFX-format med privat nyckel

Utför följande steg för att exportera ett SSL-certifikat med privat nyckel på en Windows-dator. 

> [!IMPORTANT]
> Utför de här stegen på samma dator som du använde för att skapa certifikatet. 

1. Kör *certlm. msc* för att starta den lokala datorns certifikat arkiv.

1. Dubbelklicka på den **personliga** mappen och sedan på **certifikat**.

    ![Exportera certifikat 1](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-1.png)
 
2. Högerklicka på det certifikat som du vill säkerhetskopiera och välj **alla aktiviteter > exportera...**

    ![Exportera certifikat 2](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-2.png)

3. Följ guiden Exportera certifikat för att säkerhetskopiera certifikatet till en. pfx-fil.

    ![Exportera certifikat 3](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-3.png)

4. Välj **Ja, exportera den privata nyckeln**.

    ![Exportera certifikat 4](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-4.png)

5. Välj **Inkludera alla certifikat i certifikat Sök vägen, om möjligt**, **Exportera alla utökade egenskaper** och **Aktivera certifikat sekretess**. 

    > [!IMPORTANT]
    > Välj inte **alternativet ta bort privat nyckel om exporten lyckades**.

    ![Exportera certifikat 5](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-5.png)

6. Ange ett lösen ord som du kommer ihåg. Bekräfta lösenordet. Lösen ordet skyddar den privata nyckeln.

    ![Exportera certifikat 6](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-6.png)

7. Välj att spara filen på en angiven plats.

    ![Exportera certifikat 7](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-7.png)
  
8. Välj **Slutför**.

    ![Exportera certifikat 8](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-8.png)

9. Du får ett meddelande om att exporten har slutförts. Välj **OK**.

    ![Exportera certifikat 9](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-9.png)

Säkerhets kopian av. pfx-filen sparas nu på den plats som du har valt och är redo att flyttas eller lagras för säker förvaring.


### <a name="export-certificates-as-der-format"></a>Exportera certifikat som DER-format

1. Kör *certlm. msc* för att starta den lokala datorns certifikat arkiv.

1. I det personliga certifikat arkivet väljer du rot certifikatet. Högerklicka på och välj **alla aktiviteter > exportera...**

    ![Exportera certifikat DER 1](media/azure-stack-edge-series-manage-certificates/export-cert-cer-1.png)

2. Certifikat guiden öppnas. Välj formatet som **der-kodad binär X. 509 (. cer)**. Välj **Nästa**.

    ![Exportera certifikat DER 2](media/azure-stack-edge-series-manage-certificates/export-cert-cer-2.png)

3. Bläddra och välj den plats där du vill exportera. cer-format filen.

    ![Exportera certifikat DER 3](media/azure-stack-edge-series-manage-certificates/export-cert-cer-3.png)

4. Välj **Slutför**.

    ![Exportera certifikat DER 4](media/azure-stack-edge-series-manage-certificates/export-cert-cer-4.png)


## <a name="supported-certificate-algorithms"></a>Algoritmer som stöds

 Endast Rivest – Shamir – Adleman-certifikat (RSA) stöds med din Azure Stack Edge Pro-enhet. ECDSA-certifikat (Elliptic Curve Digital Signature Algorithm) stöds inte.

 Certifikat som innehåller en offentlig RSA-nyckel kallas RSA-certifikat. Certifikat som innehåller en offentlig nyckel för Elliptic Curve-kryptografiska (ECC) kallas ECDSA (Elliptic Curve Digital Signature Algorithm). 


## <a name="view-certificate-expiry"></a>Visa certifikatets förfallo datum

Om du använder dina egna certifikat upphör certifikaten normalt att gälla om 1 år eller 6 månader. Om du vill visa förfallo datumet för certifikatet går du till sidan **certifikat** i enhetens lokala webb gränssnitt. Om du väljer ett visst certifikat kan du se förfallo datumet för ditt certifikat.

<!--## Rotate certificates

Rotation of certificates is not implemented in this release. You are also not notified of the pending expiration date on your certificate. 

View the certificate expiration date on the **Certificates** page in the local web UI of your device. Once the certificate expiration is approaching, create and upload new certificates as per the detailed instructions in [Create and upload certificates]().-->

## <a name="next-steps"></a>Nästa steg

[Distribuera din Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-deploy-prep.md)