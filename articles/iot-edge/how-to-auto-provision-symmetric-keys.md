---
title: Etablera en enhet med symmetrisk nyckel attestation – Azure IoT Edge
description: Använd attestering med symmetrisk nyckel för att testa automatisk enhetsetablering Azure IoT Edge med enhetsetableringstjänsten
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 66e1e561c14b169d41028e151ac054888830b881
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481981"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Skapa och etablera en IoT Edge enhet med symmetrisk nyckel atterstation

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Azure IoT Edge enheter kan etableras automatiskt med [enhetsetableringstjänsten](../iot-dps/index.yml) precis som enheter som inte är gränsaktiverade. Om du inte är bekant med processen för automatisk etablering kan du granska [etableringsöversikten](../iot-dps/about-iot-dps.md#provisioning-process) innan du fortsätter.

Den här artikeln visar hur du skapar en enhetsetableringstjänst för enskild registrering eller gruppregistrering med symmetrisk nyckel atterstation på en IoT Edge enhet med följande steg:

* Skapa en instans av IoT Hub Device Provisioning Service (DPS).
* Skapa en registrering för enheten.
* Installera IoT Edge och anslut till IoT Hub.

Atterstation för symmetrisk nyckel är en enkel metod för att autentisera en enhet med en instans av enhetsetableringstjänsten. Den här attestationsmetoden representerar en "Hello world"-upplevelse för utvecklare som är nybörjare på enhetsetablering eller som inte har strikta säkerhetskrav. Attestering av enheter med [ett TPM-](../iot-dps/concepts-tpm-attestation.md) eller [X.509-certifikat](../iot-dps/concepts-x509-attestation.md) är säkrare och bör användas för strängare säkerhetskrav.

## <a name="prerequisites"></a>Förutsättningar

* En aktiv IoT Hub
* En fysisk eller virtuell enhet

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Konfigurera IoT Hub Device Provisioning Service

Skapa en ny instans av IoT Hub Device Provisioning Service Azure och länka den till din IoT-hubb. Du kan följa instruktionerna i [Konfigurera IoT Hub DPS.](../iot-dps/quick-setup-auto-provision.md)

När enhetsetableringstjänsten körs kopierar du värdet för **ID-omfång** från översiktssidan. Du använder det här värdet när du konfigurerar IoT Edge körning.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Välj ett unikt registrerings-ID för enheten

Ett unikt registrerings-ID måste definieras för att identifiera varje enhet. Du kan använda MAC-adressen, serienummer eller unik information från enheten.

I det här exemplet använder vi en kombination av en MAC-adress och ett serienummer som bildar följande sträng för ett registrerings-ID: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6` .

Skapa ett unikt registrerings-ID för din enhet. Giltiga tecken är alfanumeriska gemener och bindestreck (-).

## <a name="create-a-dps-enrollment"></a>Skapa en DPS-registrering

Använd enhetens registrerings-ID för att skapa en enskild registrering i DPS.

När du skapar en registrering i DPS har du möjlighet att deklarera en första **enhetstvillingtillstånd**. I enhetstvillingen kan du ange taggar för att gruppera enheter efter mått som du behöver i din lösning, till exempel region, miljö, plats eller enhetstyp. De här taggarna används för att [skapa automatiska distributioner](how-to-deploy-at-scale.md).

> [!TIP]
> Gruppregistreringar är också möjliga när du använder symmetrisk nyckel atterstation och involverar samma beslut som enskilda registreringar.

1. I [Azure Portal](https://portal.azure.com)navigerar du till din instans av IoT Hub Device Provisioning Service.

1. Under **Inställningar** väljer du **Hantera registreringar.**

1. Välj **Lägg till enskild** registrering och slutför sedan följande steg för att konfigurera registreringen:  

   1. För **Mekanism** väljer du **Symmetrisk nyckel.**

   1. Markera **kryssrutan Generera nycklar** automatiskt.

   1. Ange **registrerings-ID:t** som du skapade för din enhet.

   1. Ange ett **IoT Hub enhets-ID** för enheten om du vill. Du kan använda enhets-ID:er för att rikta in dig på en enskild enhet för moduldistribution. Om du inte anger ett enhets-ID används registrerings-ID:t.

   1. Välj **Sant** för att deklarera att registreringen gäller för en IoT Edge enhet. För en gruppregistrering måste alla enheter vara IoT Edge eller så kan ingen av dem vara det.

      > [!TIP]
      > I Azure CLI kan du [](/cli/azure/iot/dps/enrollment) skapa [](/cli/azure/iot/dps/enrollment-group) en registrering eller en registreringsgrupp och använda den **gränsaktiverade** flaggan för att ange att en enhet, eller en grupp av enheter, IoT Edge enhet.

   1. Acceptera standardvärdet från Enhetsetableringstjänstens **allokeringsprincip** för hur du vill tilldela enheter till hubbar eller välja ett annat värde som är specifikt för den här registreringen.

   1. Välj den **länkade IoT Hub** som du vill ansluta enheten till. Du kan välja flera hubbar så tilldelas enheten till en av dem enligt den valda allokeringsprincipen.

   1. Välj **hur du vill att enhetsdata ska hanteras vid ometablering** när enheter begär etablering efter första gången.

   1. Lägg till ett taggvärde i **inledande enhetstvillingtillstånd** om du vill. Du kan använda taggar för att rikta in dig på grupper av enheter för moduldistribution. Exempel:

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

   1. Kontrollera **att Enable entry** (Aktivera post) är inställt på Enable **(Aktivera).**

   1. Välj **Spara**.

Nu när det finns en registrering för den här IoT Edge kan körningen automatiskt etablera enheten under installationen. Se till att kopiera  värdet för din registrering primärnyckel som ska användas när du installerar IoT Edge-körningen, eller om du ska skapa enhetsnycklar för användning med en gruppregistrering.

## <a name="derive-a-device-key"></a>Härled en enhetsnyckel

> [!NOTE]
> Det här avsnittet krävs endast om du använder en gruppregistrering.

Varje enhet använder sin härledda enhetsnyckel med ditt unika registrerings-ID för att utföra symmetrisk nyckel attestation med registreringen under etableringen. Generera enhetsnyckeln genom att använda den nyckel som du kopierade från DPS-registreringen för att beräkna [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) för enhetens unika registrerings-ID och konvertera resultatet till Base64-format.

Inkludera inte registreringens primära eller sekundära nyckel i enhetskoden.

### <a name="linux-workstations"></a>Linux-arbetsstationer

Om du använder en Linux-arbetsstation kan du använda openssl för att generera din härledda enhetsnyckel enligt följande exempel.

Ersätt värdet för **KEY med** den **primärnyckel som** du antecknade tidigare.

Ersätt värdet för **REG_ID** med enhetens registrerings-ID.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

### <a name="windows-based-workstations"></a>Windows-baserade arbetsstationer

Om du använder en Windows-baserad arbetsstation kan du använda PowerShell för att generera den härledda enhetsnyckeln enligt följande exempel.

Ersätt värdet för **KEY med** den **primärnyckel som** du antecknade tidigare.

Ersätt värdet för **REG_ID** med enhetens registrerings-ID.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

## <a name="install-the-iot-edge-runtime"></a>Installera IoT Edge-körningen

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Dess komponenter körs i containrar och gör att du kan distribuera ytterligare containrar till enheten så att du kan köra kod vid gränsen.

Följ stegen i Installera [Azure IoT Edge körtid och](how-to-install-iot-edge.md)gå sedan tillbaka till den här artikeln för att etablera enheten.

## <a name="configure-the-device-with-provisioning-information"></a>Konfigurera enheten med etableringsinformation

När körningen har installerats på enheten konfigurerar du den med den information den använder för att ansluta till Device Provisioning-tjänsten och IoT Hub.

Ha följande information klar:

* Värdet för **DPS-ID-omfång**
* **Enhetsregistrerings-ID:t** som du skapade
* Primärnyckeln **som** du kopierade från DPS-registreringen

> [!TIP]
> För gruppregistreringar behöver du varje enhets härledda nyckel [i stället](#derive-a-device-key) för primärnyckeln för DPS-registrering.

### <a name="linux-device"></a>Linux-enhet

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
1. Öppna konfigurationsfilen på den IoT Edge enheten.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Leta reda på avsnittet etableringskonfigurationer i filen. Avkommentera raderna för dps symmetrisk nyckeletablering och se till att andra etableringsrader kommenteras bort.

   Raden `provisioning:` ska inte ha något föregående blanksteg och kapslade objekt ska vara indragna med två blanksteg.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "symmetric_key"
       registration_id: "<REGISTRATION_ID>"
       symmetric_key: "<SYMMETRIC_KEY>"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. Uppdatera värdena för `scope_id` , och med din `registration_id` `symmetric_key` DPS- och enhetsinformation.

1. Du kan också använda `always_reprovision_on_startup` raderna eller för att konfigurera `dynamic_reprovisioning` enhetens återetableringsbeteende. Om en enhet är inställd på att etableras igen vid start försöker den alltid etablera med DPS först och sedan gå tillbaka till etableringssäkerhetskopiorna om det misslyckas. Om en enhet är inställd på att dynamiskt återetablera sig IoT Edge startar om och återetablera om en ometableringshändelse upptäcks. Mer information finns i [IoT Hub om etablering av enheter.](../iot-dps/concepts-device-reprovision.md)

1. Starta om IoT Edge-körningen så att den hämtar alla konfigurationsändringar som du har gjort på enheten.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

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

1. Leta reda **på avsnittet** Etablering i filen. Avkommentera raderna för DPS-etablering med symmetrisk nyckel och se till att andra etableringsrader har kommenterats bort.

   ```toml
   # DPS provisioning with symmetric key
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "symmetric_key"
   registration_id = "<REGISTRATION_ID>"

   symmetric_key = "<PRIMARY_KEY OR DERIVED_KEY>"
   ```

1. Uppdatera värdena för `id_scope` , och med din `registration_id` `symmetric_key` DPS- och enhetsinformation.

   Parametern för den symmetriska nyckeln kan acceptera ett värde för en infogade nyckel, en fil-URI eller en PKCS#11-URI. Avkommenteringen är bara en symmetrisk nyckellinje, baserat på vilket format du använder.

   Om du använder PKCS#11-URI:er hittar du avsnittet **PKCS#11** i konfigurationsfilen och anger information om din PKCS#11-konfiguration.

1. Spara och stäng filen config.toml.

1. Tillämpa de konfigurationsändringar som du har gjort IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

### <a name="windows-device"></a>Windows-enhet

1. Öppna ett PowerShell-fönster i administratörsläge. Se till att använda en AMD64-session med PowerShell när du IoT Edge, inte PowerShell (x86).

1. Kommandot **Initialize-IoTEdge** konfigurerar IoT Edge på datorn. Kommandot använder som standard manuell etablering med Windows-containrar, så använd flaggan för att använda `-DpsSymmetricKey` automatisk etablering med autentisering med symmetrisk nyckel.

   Ersätt platshållarvärdena `{scope_id}` för , och med de data som du samlade in `{registration_id}` `{symmetric_key}` tidigare.

   Lägg till `-ContainerOs Linux` parametern om du använder Linux-containrar i Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsSymmetricKey -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>Verifiera att installationen har lyckats

Om körningen har startats kan du gå in på IoT Hub och börja distribuera IoT Edge till enheten. Använd följande kommandon på enheten för att kontrollera att körningen har installerats och startats.

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

Du kan kontrollera att den enskilda registrering som du skapade i Device Provisioning Service har använts. Gå till instansen av enhetsetableringstjänsten i Azure Portal. Öppna registreringsinformationen för den enskilda registrering som du skapade. Observera att statusen för registreringen har **tilldelats och** enhets-ID:t visas.

## <a name="next-steps"></a>Nästa steg

Registreringsprocessen för enhetsetableringstjänsten låter dig ange enhets-ID och taggar för enhetstvilling samtidigt som du etablerar den nya enheten. Du kan använda dessa värden för att rikta in dig på enskilda enheter eller grupper av enheter med hjälp av automatisk enhetshantering. Lär dig hur [du distribuerar och övervakar IoT Edge moduler i stor skala med hjälp Azure Portal](how-to-deploy-at-scale.md) eller med Hjälp av Azure [CLI.](how-to-deploy-cli-at-scale.md)
