---
title: Etablera enhet med hjälp av symmetrisk nyckel attestering-Azure IoT Edge
description: Använd symmetrisk nyckel attestering för att testa automatisk enhets etablering för Azure IoT Edge med enhets etablerings tjänsten
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: bfb61a5434089fffab9d8ceb9c7b0fbca528cac5
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430619"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Skapa och etablera en IoT Edge enhet med hjälp av symmetrisk nyckel attestering

Azure IoT Edge enheter kan konfigureras automatiskt med [enhets etablerings tjänsten](../iot-dps/index.yml) , precis som enheter som inte är Edge-aktiverade. Om du inte är bekant med processen för automatisk etablering, granskar du [etablerings](../iot-dps/about-iot-dps.md#provisioning-process) översikten innan du fortsätter.

Den här artikeln visar hur du skapar en individuell registrering av enhets etablerings tjänsten med hjälp av symmetrisk nyckel attestering på en IoT Edge enhet med följande steg:

* Skapa en instans av IoT Hub Device Provisioning Service (DPS).
* Skapa en enskild registrering för enheten.
* Installera IoT Edge Runtime och Anslut till IoT Hub.

Symmetrisk nyckel attestering är en enkel metod för att autentisera en enhet med en enhets etablerings tjänst instans. Den här attesterings metoden representerar en "Hello World"-upplevelse för utvecklare som är nya för enhets etablering eller som inte har strikta säkerhets krav. Enhets attestering med hjälp av [TPM](../iot-dps/concepts-tpm-attestation.md) -eller [X. 509-certifikat](../iot-dps/concepts-x509-attestation.md) är säkrare och bör användas för mer långtgående säkerhets krav.

## <a name="prerequisites"></a>Förutsättningar

* En aktiv IoT Hub
* En fysisk eller virtuell enhet

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Konfigurera IoT Hub Device Provisioning Service

Skapa en ny instans av IoT Hub Device Provisioning Service i Azure och länka den till din IoT-hubb. Du kan följa anvisningarna i [konfigurera IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

När du har installerat enhets etablerings tjänsten kopierar du värdet för **ID-omfång** från översikts sidan. Du använder det här värdet när du konfigurerar IoT Edge Runtime.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Välj ett unikt registrerings-ID för enheten

Ett unikt registrerings-ID måste definieras för att identifiera varje enhet. Du kan använda MAC-adressen, serie numret eller någon unik information från enheten.

I det här exemplet använder vi en kombination av en MAC-adress och ett serie nummer som utgör följande sträng för registrerings-ID: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6` .

Skapa ett unikt registrerings-ID för din enhet. Giltiga tecken är alfanumeriska bokstäver och bindestreck (-).

## <a name="create-a-dps-enrollment"></a>Skapa en DPS-registrering

Använd enhetens registrerings-ID för att skapa en enskild registrering i DPS.

När du skapar en registrering i DPS har du möjlighet att deklarera en **första enhets dubbla tillstånd**. I enheten är det enkelt att ange taggar för att gruppera enheter efter eventuella mått som du behöver i din lösning, t. ex. region, miljö, plats eller enhets typ. De här taggarna används för att skapa [automatiska distributioner](how-to-deploy-at-scale.md).

> [!TIP]
> Grupp registreringar är också möjliga när du använder symmetrisk nyckel attestering och inbegriper samma beslut som enskilda registreringar.

1. I [Azure Portal](https://portal.azure.com)navigerar du till din instans av IoT Hub Device Provisioning service.

1. Under **Inställningar** väljer du **Hantera registreringar**.

1. Välj **Lägg till enskild registrering** och slutför sedan följande steg för att konfigurera registreringen:  

   1. För **mekanism** väljer du **symmetrisk nyckel**.

   1. Markera kryss rutan **generera nycklar automatiskt** .

   1. Ange det **registrerings-ID** som du har skapat för din enhet.

   1. Ange ett **IoT Hub enhets-ID** för din enhet om du vill. Du kan använda enhets-ID: n för att rikta in en enskild enhet för modul distribution. Om du inte anger något enhets-ID används registrerings-ID: t.

   1. Välj **Sant** för att deklarera att registreringen är för en IoT Edge enhet. För en grupp registrering måste alla enheter vara IoT Edge enheter eller så kan ingen av dem vara.

   > [!TIP]
   > I Azure CLI kan du skapa en [registrering](/cli/azure/ext/azure-iot/iot/dps/enrollment) eller en [registrerings grupp](/cli/azure/ext/azure-iot/iot/dps/enrollment-group) och använda den **Edge-aktiverade** flaggan för att ange att en enhet, eller en grupp av enheter, är en IoT Edge enhet.

   1. Acceptera standardvärdet från enhets etablerings tjänstens Allokeringsregel för **hur du vill tilldela enheter till hubbar** eller välja ett annat värde som är särskilt för den här registreringen.

   1. Välj den länkade **IoT Hub** som du vill ansluta enheten till. Du kan välja flera hubbar och enheten tilldelas en av dem enligt den valda allokeringsregeln.

   1. Välj **hur du vill att enhets data ska hanteras vid ny etablering** när enheter begär etablering efter första gången.

   1. Lägg till ett tagg-värde till den **första enheten** i ett läge om du vill. Du kan använda taggar för att ange mål grupper för enheter för modul distribution. Exempel:

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

   1. Se till att **Aktivera post** är **aktive** rad.

   1. Välj **Spara**.

Nu när en registrering finns för den här enheten kan IoT Edge runtime automatiskt etablera enheten under installationen. Se till att kopiera din registrerings **primär nyckel** värde som ska användas när du installerar IoT Edge runtime, eller om du ska skapa enhets nycklar för användning med en grupp registrering.

## <a name="derive-a-device-key"></a>Härled en enhets nyckel

> [!NOTE]
> Det här avsnittet krävs endast om du använder en grupp registrering.

Varje enhet använder den härledda enhets nyckeln med ditt unika registrerings-ID för att utföra symmetrisk nyckel attestering med registreringen under etableringen. Om du vill generera enhets nyckeln använder du nyckeln som du kopierade från din DPS-registrering för att beräkna en [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) av det unika registrerings-ID: t för enheten och konvertera resultatet till base64-format.

Ta inte med din registrerings primära eller sekundära nyckel i enhets koden.

### <a name="linux-workstations"></a>Linux-arbetsstationer

Om du använder en Linux-arbetsstation kan du använda OpenSSL för att generera en härledd enhets nyckel som visas i följande exempel.

Ersätt värdet för **Key** med den **primära nyckel** du noterade tidigare.

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

### <a name="windows-based-workstations"></a>Windows-baserade arbets stationer

Om du använder en Windows-baserad arbets Station kan du använda PowerShell för att generera en härledd enhets nyckel som visas i följande exempel.

Ersätt värdet för **Key** med den **primära nyckel** du noterade tidigare.

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

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Komponenterna körs i behållare och gör att du kan distribuera ytterligare behållare till enheten så att du kan köra kod i kanten.

Följ stegen i [installera Azure IoT Edge runtime](how-to-install-iot-edge.md)och gå sedan tillbaka till den här artikeln för att etablera enheten.

## <a name="configure-the-device-with-provisioning-information"></a>Konfigurera enheten med etablerings information

När körningen har installerats på enheten konfigurerar du enheten med den information som används för att ansluta till enhets etablerings tjänsten och IoT Hub.

Ha följande information redo:

* DPS **-ID omfångs** värde
* ID för enhets **registrering** som du har skapat
* Den **primära nyckeln** som du kopierade från DPS-registreringen

> [!TIP]
> För grupp registreringar behöver du varje enhets [härledda nyckel](#derive-a-device-key) snarare än DPS-registrerings nyckeln.

### <a name="linux-device"></a>Linux-enhet

1. Öppna konfigurations filen på den IoT Edge enheten.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Hitta konfigurations avsnittet för etablering i filen. Ta bort kommentarer till raderna för etablering av DPS symmetrisk nyckel och se till att alla andra etablerings rader är kommenterade.

   `provisioning:`Raden ska inte ha något föregående blank steg, och kapslade objekt bör dras av två blank steg.

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

   Du kan också använda `always_reprovision_on_startup` raderna eller om `dynamic_reprovisioning` du vill konfigurera enhetens etablerings beteende. Om en enhet har ställts in för att etablera vid start försöker den alltid etableras med DPS först och sedan återgår till etablerings säkerhets kopieringen om det inte går. Om en enhet är inställd på att dynamiskt Ometablera sig själv startas IoT Edge om och reetableras om en reetablerings händelse upptäcks. Mer information finns i [IoT Hub metoder för att etablera enheter](../iot-dps/concepts-device-reprovision.md).

1. Uppdatera värdena för `scope_id` , `registration_id` och `symmetric_key` med din DPS-och enhets information.

1. Starta om IoT Edge runtime så att den hämtar alla konfigurations ändringar som du har gjort på enheten.

   ```bash
   sudo systemctl restart iotedge
   ```

### <a name="windows-device"></a>Windows-enhet

1. Öppna ett PowerShell-fönster i administratörs läge. Se till att använda en AMD64-session av PowerShell när du installerar IoT Edge, inte PowerShell (x86).

1. Kommandot **Initialize-IoTEdge** konfigurerar IoT Edge runtime på din dator. Kommandot använder standardinställningen manuell etablering med Windows-behållare, så Använd `-DpsSymmetricKey` flaggan för att använda automatisk etablering med symmetrisk nyckel autentisering.

   Ersätt plats hållarnas värden för `{scope_id}` , `{registration_id}` och `{symmetric_key}` med de data som du samlade in tidigare.

   Lägg till `-ContainerOs Linux` parametern om du använder Linux-behållare i Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsSymmetricKey -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>Verifiera lyckad installation

Om körningen har startats kan du gå till IoT Hub och börja distribuera IoT Edge moduler till enheten. Använd följande kommandon på enheten för att kontrol lera att körningen har installerats och startats.

### <a name="linux-device"></a>Linux-enhet

Kontrollera status för IoT Edge-tjänsten.

```cmd/sh
systemctl status iotedge
```

Undersök tjänst loggar.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Lista med moduler som körs.

```cmd/sh
iotedge list
```

### <a name="windows-device"></a>Windows-enhet

Kontrollera status för IoT Edge-tjänsten.

```powershell
Get-Service iotedge
```

Undersök tjänst loggar.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Lista med moduler som körs.

```powershell
iotedge list
```

Du kan kontrol lera att den enskilda registrering som du skapade i enhets etablerings tjänsten användes. Navigera till din enhets etablerings tjänst instans i Azure Portal. Öppna registrerings informationen för den enskilda registrering som du har skapat. Observera att statusen för registreringen är **tilldelad** och att enhets-ID visas.

## <a name="next-steps"></a>Nästa steg

Med registrerings processen för enhets etablerings tjänsten kan du ange enhets-ID och enhets dubbla taggar samtidigt som du etablerar den nya enheten. Du kan använda dessa värden för att rikta in enskilda enheter eller grupper av enheter med automatisk enhets hantering. Lär dig hur du [distribuerar och övervakar IoT Edge moduler i skala med hjälp av Azure Portal](how-to-deploy-at-scale.md) eller [med hjälp av Azure CLI](how-to-deploy-cli-at-scale.md).