---
title: Etablera enheter automatiskt med DPS med X.509-certifikat – Azure IoT Edge | Microsoft Docs
description: Använd X.509-certifikat för att testa automatisk enhetsetablering för Azure IoT Edge med enhetsetableringstjänsten
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
ms.openlocfilehash: f3c783c57b49b45943882703aec6d735d12bf830
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481964"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>Skapa och etablera en IoT Edge enhet med X.509-certifikat

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Med Azure IoT Hub [Device Provisioning Service (DPS)](../iot-dps/index.yml)kan du automatiskt etablera IoT Edge enheter med X.509-certifikat. Om du inte är bekant med processen för automatisk etablering kan du granska [etableringsöversikten](../iot-dps/about-iot-dps.md#provisioning-process) innan du fortsätter.

Den här artikeln visar hur du skapar en enhetsetableringstjänstregistrering med X.509-certifikat på en IoT Edge enhet med följande steg:

* Generera certifikat och nycklar.
* Skapa antingen en enskild registrering för en enhet eller en gruppregistrering för en uppsättning enheter.
* Installera IoT Edge och registrera enheten med IoT Hub.

Att använda X.509-certifikat som en attesteringsmekanism är ett utmärkt sätt att skala om produktionen och förenkla enhetsetablering. X.509-certifikat är vanligtvis ordnade i en certifikatkedja med förtroende. Från och med ett själv signerat eller betrott rotcertifikat signerar varje certifikat i kedjan nästa lägre certifikat. Det här mönstret skapar en delegerad förtroendekedja från rotcertifikatet nedåt genom varje mellanliggande certifikat till det slutliga lövcertifikatet som är installerat på en enhet.

## <a name="prerequisites"></a>Förutsättningar

* En aktiv IoT Hub.
* En fysisk eller virtuell enhet som ska IoT Edge enhet.
* Den senaste versionen av [Git installerad.](https://git-scm.com/download/)
* En instans av IoT Hub Device Provisioning Service Azure, länkad till din IoT-hubb.
  * Om du inte har en instans av enhetsetableringstjänsten följer du anvisningarna i Konfigurera [IoT Hub DPS.](../iot-dps/quick-setup-auto-provision.md)
  * När enhetsetableringstjänsten körs kopierar du värdet för **ID-omfång** från översiktssidan. Du använder det här värdet när du konfigurerar IoT Edge körning.

## <a name="generate-device-identity-certificates"></a>Generera enhetsidentitetscertifikat

Enhetsidentitetscertifikatet är ett lövcertifikat som ansluter via en certifikatkedja med förtroende till X.509-certifikatet (CA). Enhetsidentitetscertifikatet måste ha det egna namnet (CN) inställt på det enhets-ID som du vill att enheten ska ha i din IoT-hubb.

Enhetsidentitetscertifikat används endast för att etablera IoT Edge och autentisera enheten med Azure IoT Hub. De signerar inte certifikat, till skillnad från de CERTIFIKATutfärdarcertifikat som IoT Edge visar för moduler eller lövenheter för verifiering. Mer information finns i [Azure IoT Edge information om certifikatanvändning.](iot-edge-certs.md)

När du har skapat enhetsidentitetscertifikatet bör du ha två filer: en .cer- eller .pem-fil som innehåller den offentliga delen av certifikatet och en .cer- eller .pem-fil med certifikatets privata nyckel. Om du planerar att använda gruppregistrering i DPS behöver du även den offentliga delen av ett mellanliggande certifikat eller ett rotcertifikatutfärdarcertifikat i samma certifikatkedja med förtroende.

Du behöver följande filer för att konfigurera automatisk etablering med X.509:

* Enhetsidentitetscertifikatet och dess privata nyckelcertifikat. Enhetsidentitetscertifikatet laddas upp till DPS om du skapar en enskild registrering. Den privata nyckeln skickas till den IoT Edge körningen.
* Ett fullständigt kedjecertifikat, som minst ska ha enhetsidentiteten och mellanliggande certifikat i den. Det fullständiga kedjecertifikatet skickas IoT Edge körningskörningen.
* Ett mellanliggande certifikat eller rotcertifikatutfärdarcertifikat från certifikatkedjan för förtroende. Det här certifikatet laddas upp till DPS om du skapar en gruppregistrering.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
> [!NOTE]
> För närvarande förhindrar en begränsning i bibliotek användningen av certifikat som upphör att gälla den 1 januari 2038 eller senare.

:::moniker-end

### <a name="use-test-certificates-optional"></a>Använda testcertifikat (valfritt)

Om du inte har en certifikatutfärdare tillgänglig för att skapa nya identitetscertifikat och vill prova det här scenariot innehåller Azure IoT Edge git-lagringsplatsen skript som du kan använda för att generera testcertifikat. Dessa certifikat är endast utformade för utvecklingstestning och får inte användas i produktion.

Om du vill skapa testcertifikat följer du stegen i [Skapa democertifikat för att IoT Edge av enhetsfunktioner](how-to-create-test-certificates.md). Slutför de två obligatoriska avsnitten för att konfigurera skript för certifikatgenerering och för att skapa ett rotcertifikatutfärdarcertifikat. Följ sedan stegen för att skapa ett enhetsidentitetscertifikat. När du är klar bör du ha följande certifikatkedja och nyckelpar:

Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

Du behöver båda dessa certifikat på IoT Edge enhet. Om du tänker använda enskild registrering i DPS laddar du upp filen .cert.pem. Om du ska använda gruppregistrering i DPS behöver du även ett mellanliggande certifikat eller ett rotcertifikatutfärdarcertifikat i samma certifikatkedja med förtroende för att ladda upp. Om du använder democertifikat använder du certifikatet `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` för gruppregistrering.

## <a name="create-a-dps-individual-enrollment"></a>Skapa en enskild DPS-registrering

Använd dina genererade certifikat och nycklar för att skapa en enskild registrering i DPS för en enskild IoT Edge enhet. Enskilda registreringar tar den offentliga delen av en enhets identitetscertifikat och matchar det med certifikatet på enheten.

Om du vill etablera flera enheter IoT Edge följer du stegen i nästa avsnitt, [Skapa en DPS-gruppregistrering.](#create-a-dps-group-enrollment)

När du skapar en registrering i DPS har du möjlighet att deklarera en första **enhetstvillingtillstånd**. I enhetstvillingen kan du ange taggar för att gruppera enheter efter mått som du behöver i din lösning, till exempel region, miljö, plats eller enhetstyp. De här taggarna används för att [skapa automatiska distributioner](how-to-deploy-at-scale.md).

Mer information om registreringar i Device Provisioning Service finns i [Hantera enhetsregistreringar.](../iot-dps/how-to-manage-enrollments.md)

   > [!TIP]
   > I Azure CLI kan du skapa [](/cli/azure/iot/dps/enrollment-group) en registrering eller  en registreringsgrupp och använda den kantaktiverade flaggan för att ange att en enhet, eller grupp med enheter, IoT Edge enhet. [](/cli/azure/iot/dps/enrollment)

1. I [Azure Portal](https://portal.azure.com)navigerar du till din instans av IoT Hub Device Provisioning Service.

1. Under **Inställningar** väljer du **Hantera registreringar.**

1. Välj **Lägg till enskild** registrering och slutför sedan följande steg för att konfigurera registreringen:  

   * **Mekanism:** Välj **X.509**.

   * **Primär .pem- eller .cer-fil** för certifikat: Ladda upp den offentliga filen från enhetsidentitetscertifikatet. Om du använde skripten för att generera ett testcertifikat väljer du följande fil:

      `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`

   * **IoT Hub enhets-ID:** Ange ett ID för enheten om du vill. Du kan använda enhets-ID:er för att rikta in dig på en enskild enhet för moduldistribution. Om du inte anger ett enhets-ID används eget namn (CN) i X.509-certifikatet.

   * **IoT Edge enhet:** Välj **Sant för** att deklarera att registreringen gäller för en IoT Edge enhet.

   * **Välj de IoT-hubbar** som enheten kan tilldelas: Välj den länkade IoT-hubb som du vill ansluta enheten till. Du kan välja flera hubbar så tilldelas enheten till en av dem enligt den valda allokeringsprincipen.

   * **Inledande enhetstvillingtillstånd:** Lägg till ett taggvärde som ska läggas till i enhetstvillingen om du vill. Du kan använda taggar för att rikta in dig på grupper av enheter för automatisk distribution. Exempel:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. Välj **Spara**.

Nu när det finns en registrering för den här IoT Edge kan körningen automatiskt etablera enheten under installationen. Fortsätt till [avsnittet Installera IoT Edge för att](#install-the-iot-edge-runtime) konfigurera din IoT Edge enhet.

## <a name="create-a-dps-group-enrollment"></a>Skapa en DPS-gruppregistrering

Använd dina genererade certifikat och nycklar för att skapa en gruppregistrering i DPS för flera IoT Edge enheter. Gruppregistreringar använder ett mellanliggande certifikat eller rotcertifikatutfärdarcertifikat från certifikatkedjan som används för att generera enskilda enhetsidentitetscertifikat.

Om du vill etablera en enskild IoT Edge i stället följer du stegen i föregående avsnitt, [Skapa en enskild DPS-registrering.](#create-a-dps-individual-enrollment)

När du skapar en registrering i DPS har du möjlighet att deklarera en första **enhetstvillingstillstånd**. I enhetstvillingen kan du ange taggar för att gruppera enheter efter mått som du behöver i din lösning, till exempel region, miljö, plats eller enhetstyp. De här taggarna används för att [skapa automatiska distributioner](how-to-deploy-at-scale.md).

### <a name="verify-your-root-certificate"></a>Verifiera rotcertifikatet

När du skapar en registreringsgrupp kan du välja att använda ett verifierat certifikat. Du kan verifiera ett certifikat med DPS genom att bevisa att du äger rotcertifikatet. Mer information finns i Så [här gör du innehavarbevis för X.509 CA-certifikat.](../iot-dps/how-to-verify-certificates.md)

1. I [Azure Portal](https://portal.azure.com)navigerar du till din instans av IoT Hub Device Provisioning Service.

1. Välj **Certifikat** på den vänstra menyn.

1. Välj **Lägg till** för att lägga till ett nytt certifikat.

1. Ange ett eget namn för certifikatet och bläddra sedan till .cer- eller .pem-filen som representerar den offentliga delen av X.509-certifikatet.

   Ladda upp certifikatet om du använder `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` democertifikaten.

1. Välj **Spara**.

1. Certifikatet bör nu visas på **sidan** Certifikat. Välj den för att öppna certifikatinformationen.

1. Välj **Generera verifieringskod** och kopiera sedan den genererade koden.

1. Oavsett om du har tagit med ditt eget CA-certifikat eller använder democertifikaten kan du använda verifieringsverktyget som finns på IoT Edge-lagringsplatsen för att verifiera innehavarbeviset. Verifieringsverktyget använder certifikatutfärdaren för att signera ett nytt certifikat som har den angivna verifieringskoden som ämnesnamn.

   * Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. Ladda upp det nyligen genererade verifieringscertifikatet Azure Portal samma certifikatinformationssida.

1. Välj **Verifiera**.

### <a name="create-enrollment-group"></a>Skapa registreringsgrupp

Mer information om registreringar i Device Provisioning Service finns i [Hantera enhetsregistreringar.](../iot-dps/how-to-manage-enrollments.md)

1. I [Azure Portal](https://portal.azure.com)navigerar du till din instans av IoT Hub Device Provisioning Service.

1. Under **Inställningar** väljer du **Hantera registreringar.**

1. Välj **Lägg till registreringsgrupp** och slutför sedan följande steg för att konfigurera registreringen:

   * **Gruppnamn:** Ange ett namn som är lätt att komma ihåg för den här gruppregistreringen.

   * **Attesteringstyp:** Välj **Certifikat**.

   * **IoT Edge enhet:** Välj **Sant.** För en gruppregistrering måste alla enheter vara IoT Edge eller så kan ingen av dem vara det.

   * **Certifikattyp:** Välj **CA-certifikat** om du har ett verifierat CA-certifikat lagrat med DPS eller Mellanliggande **certifikat** om du vill ladda upp en ny fil för just den här registreringen.

   * **Primärt certifikat:** Om du valde CA-certifikat i det sista avsnittet väljer du certifikatet i listrutan. Om du väljer mellanliggande certifikat laddar du upp den offentliga filen från ett CA-certifikat i certifikatkedjan för förtroende som användes för att generera enhetsidentitetscertifikaten.

   * **Välj de IoT-hubbar** som enheten kan tilldelas: Välj den länkade IoT-hubb som du vill ansluta enheten till. Du kan välja flera hubbar så tilldelas enheten till en av dem enligt den valda allokeringsprincipen.

   * **Inledande enhetstvillingtillstånd:** Lägg till ett taggvärde som ska läggas till i enhetstvillingen om du vill. Du kan använda taggar för att rikta in dig på grupper av enheter för automatisk distribution. Exempel:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. Välj **Spara**.

Nu när det finns en registrering för den här IoT Edge kan körningen automatiskt etablera enheten under installationen. Fortsätt till nästa avsnitt för att konfigurera IoT Edge enhet.

## <a name="install-the-iot-edge-runtime"></a>Installera IoT Edge-körningen

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Dess komponenter körs i containrar och gör att du kan distribuera ytterligare containrar till enheten så att du kan köra kod på gränsen.

Följ stegen i [Installera Azure IoT Edge runtime och](how-to-install-iot-edge.md)gå sedan tillbaka till den här artikeln för att etablera enheten.

X.509-etablering med DPS stöds endast i IoT Edge version 1.0.9 eller senare.

## <a name="configure-the-device-with-provisioning-information"></a>Konfigurera enheten med etableringsinformation

När körningen har installerats på enheten konfigurerar du den med den information den använder för att ansluta till Device Provisioning-tjänsten och IoT Hub.

Ha följande information klar:

* Värdet för **DPS-ID-omfång.** Du kan hämta det här värdet från översiktssidan för DPS-instansen i Azure Portal.
* Enhetens identitetscertifikatkedjefil på enheten.
* Enhetens identitetsnyckelfil på enheten.
* Ett valfritt registrerings-ID. Om inget annat anges hämtas ID:t från det gemensamma namnet i enhetsidentitetscertifikatet.

### <a name="linux-device"></a>Linux-enhet

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Öppna konfigurationsfilen på den IoT Edge enheten.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Leta reda på avsnittet etableringskonfigurationer i filen. Avkommentera raderna för DPS X.509-certifikatetablering och se till att andra etableringsrader har kommenterats bort.

   Raden `provisioning:` ska inte ha något föregående blanksteg och kapslade objekt ska dras in med två blanksteg.

   ```yml
   # DPS X.509 provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "x509"
   #   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. Uppdatera värdena för `scope_id` , och med din `identity_cert` `identity_pk` DPS- och enhetsinformation.

   När du lägger till X.509-certifikatet och nyckelinformationen i filen config.yaml ska sökvägarna anges som fil-URI:er. Exempel:

   `file:///<path>/identity_certificate_chain.pem`
   `file:///<path>/identity_key.pem`

1. Du kan också ange `registration_id` en för enheten. Annars lämnar du raden kommenterad för att registrera enheten med identitetscertifikatets CN-namn.

1. Du kan också använda `always_reprovision_on_startup` raderna eller för att konfigurera `dynamic_reprovisioning` enhetens ometableringsbeteende. Om en enhet är inställd på att etableras igen vid start försöker den alltid etablera med DPS först och sedan gå tillbaka till etableringssäkerhetskopia om det misslyckas. Om en enhet är inställd på att dynamiskt återetablera sig IoT Edge startar om och startar om om en ometableringshändelse upptäcks. Mer information finns i [IoT Hub om etablering av enheter.](../iot-dps/concepts-device-reprovision.md)

1. Spara och stäng filen config.yaml.

1. Starta om IoT Edge-körningen så att den hämtar alla konfigurationsändringar som du har gjort på enheten.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1. -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Skapa en konfigurationsfil för enheten baserat på en mallfil som tillhandahålls som en del av IoT Edge installation.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. Öppna konfigurationsfilen på den IoT Edge enheten.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Leta reda **på avsnittet** Etablering i filen. Avkommentera raderna för DPS-etablering med X.509-certifikat och se till att andra etableringsrader har kommenterats bort.

   ```toml
   # DPS provisioning with X.509 certificate
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "x509"
   # registration_id = "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"

   identity_cert = "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

1. Uppdatera värdena för `id_scope` , och med din `identity_cert` `identity_pk` DPS- och enhetsinformation.

   Värdet för identitetscertifikatet kan anges som en fil-URI eller kan utfärdas dynamiskt med hjälp av EST eller en lokal certifikatutfärdare. Avkommentering av endast en rad, baserat på det format som du väljer att använda.

   Värdet för den privata identitetsnyckeln kan anges som en fil-URI eller en PKCS#11-URI. Avkommentering av endast en rad, baserat på det format som du väljer att använda.

   Om du använder PKCS#11-URI:er hittar du avsnittet **PKCS#11** i konfigurationsfilen och anger information om din PKCS#11-konfiguration.

1. Du kan också ange en `registration_id` för enheten. Annars lämnar du den raden kommenterad för att registrera enheten med det gemensamma namnet på identitetscertifikatet.

1. Spara och stäng filen.

1. Tillämpa de konfigurationsändringar som du har gjort IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

### <a name="windows-device"></a>Windows-enhet

1. Öppna ett PowerShell-fönster i administratörsläge. Se till att använda en AMD64-session med PowerShell när du IoT Edge, inte PowerShell (x86).

1. Kommandot **Initialize-IoTEdge** konfigurerar IoT Edge på datorn. Kommandot använder som standard manuell etablering med Windows-containrar, så använd flaggan för att använda automatisk etablering med `-DpsX509` X.509-certifikatautentisering.

   Ersätt platshållarvärdena `{scope_id}` för , och med lämpliga värden från `{identity cert chain path}` `{identity key path}` DPS-instansen och filsökvägarna på enheten.

   Lägg till `-RegistrationId {registration_id}` om du vill ange enhets-ID:t som något annat än identitetscertifikatets CN-namn.

   Lägg till `-ContainerOs Linux` parametern om du använder Linux-containrar i Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsX509 -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >Konfigurationsfilen lagrar certifikat- och nyckelinformationen som fil-URI:er. Dock hanterar Initialize-IoTEdge det här formateringssteget åt dig, så att du kan ange den absoluta sökvägen till certifikat- och nyckelfilerna på enheten.

## <a name="verify-successful-installation"></a>Verifiera att installationen har lyckats

Om körningen har startats kan du gå in på IoT Hub och börja distribuera IoT Edge till enheten.

Du kan kontrollera att den enskilda registrering som du skapade i Enhetsetableringstjänsten har använts. Gå till instansen av enhetsetableringstjänsten i Azure Portal. Öppna registreringsinformationen för den enskilda registrering som du skapade. Observera att statusen för registreringen har **tilldelats och** enhets-ID:t visas.

Använd följande kommandon på enheten för att kontrollera att körningen har installerats och startats.

### <a name="linux-device"></a>Linux-enhet

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Kontrollera status för IoT Edge-tjänsten.

```cmd/sh
systemctl status iotedge
```

Granska tjänstloggar.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Lista moduler som körs.

```cmd/sh
iotedge list
```
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Kontrollera status för IoT Edge-tjänsten.

```cmd/sh
sudo iotedge system status
```

Granska tjänstloggar.

```cmd/sh
sudo iotedge system logs
```

Lista moduler som körs.

```cmd/sh
sudo iotedge list
```
:::moniker-end

### <a name="windows-device"></a>Windows-enhet

Kontrollera status för IoT Edge-tjänsten.

```powershell
Get-Service iotedge
```

Granska tjänstloggar.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Lista moduler som körs.

```powershell
iotedge list
```

## <a name="next-steps"></a>Nästa steg

Registreringsprocessen för enhetsetableringstjänsten låter dig ange enhets-ID och taggar för enhetstvilling samtidigt som du etablerar den nya enheten. Du kan använda dessa värden för att rikta in dig på enskilda enheter eller grupper av enheter med hjälp av automatisk enhetshantering. Lär dig hur [du distribuerar och övervakar IoT Edge moduler i stor skala med hjälp av Azure Portal](how-to-deploy-at-scale.md) eller med Hjälp av Azure [CLI.](how-to-deploy-cli-at-scale.md)
