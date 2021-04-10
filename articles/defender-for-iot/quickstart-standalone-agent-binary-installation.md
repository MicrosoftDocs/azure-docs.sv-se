---
title: Installera Defender för IoT Micro agent (för hands version)
description: Lär dig hur du installerar och autentiserar Defender Micro-agenten.
ms.date: 3/9/2021
ms.topic: quickstart
ms.openlocfilehash: ecde9f42acc5cf4a924a657f4f0cdc545c4a668e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782732"
---
# <a name="install-defender-for-iot-micro-agent-preview"></a>Installera Defender för IoT Micro agent (för hands version)

Den här artikeln innehåller en förklaring av hur du installerar och autentiserar Defender Micro-agenten.

## <a name="prerequisites"></a>Förutsättningar

Innan du installerar Defender för IoT-modulen måste du skapa en modul identitet i IoT Hub. Mer information om hur du skapar en modul identitet finns i [skapa en Defender IoT Micro agent-modul, delad (för hands version)](quickstart-create-micro-agent-module-twin.md).

## <a name="install-the-package"></a>Installera paketet

Installera och konfigurera Microsoft Package-lagringsplatsen genom att följa [dessa anvisningar](/windows-server/administration/linux-package-repository-for-microsoft-software). 

För Debian 9 innehåller instruktionerna inte den lagrings plats som behöver läggas till, Använd följande kommandon för att lägga till lagrings platsen: 

```azurecli
curl -sSL https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add - 

sudo apt-get install software-properties-common

sudo apt-add-repository https://packages.microsoft.com/debian/9/multiarch/prod

sudo apt-get update
```

Om du vill installera Defender Micro agent-paketet på Debian, och Ubuntu-baserade Linux-distributioner, använder du följande kommando:

```azurecli
sudo apt-get install defender-iot-micro-agent 
```

## <a name="micro-agent-authentication-methods"></a>Autentiseringsmetoder för Micro agent 

De två alternativen som används för att autentisera Defender för IoT Micro-agenten är: 

- Anslutnings sträng för modul identitet. 

- Certifikatmallens.

### <a name="authenticate-using-a-module-identity-connection-string"></a>Autentisera med hjälp av en anslutnings sträng för modul identitet

Se till att [kraven](#prerequisites) för den här artikeln är uppfyllda och att du skapar en modul identitet innan du startar de här stegen. 

#### <a name="get-the-module-identity-connection-string"></a>Hämta anslutnings strängen för modul identitet

För att hämta anslutnings strängen för modulens identitet från IoT Hub: 

1. Navigera till IoT Hub och välj hubben.

1. På den vänstra menyn, under avsnittet **Utforskare** , väljer du **IoT-enheter**.

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/iot-devices.png" alt-text="Välj IoT-enheter i den vänstra menyn.":::

1. Välj en enhet i listan enhets-ID om du vill visa sidan med **enhets information** .

1. Välj fliken **modul identiteter**   och välj sedan modulen **DefenderIotMicroAgent**   i listan över modul identiteter som är associerade med enheten.

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/module-identities.png" alt-text="Välj fliken modul identiteter.":::

1. På sidan **information om modulens identitet** kopierar du den primära nyckeln genom att välja knappen **Kopiera** .

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/copy-button.png" alt-text="Välj kopierings knappen för att kopiera primär nyckeln.":::

#### <a name="configure-authentication-using-a-module-identity-connection-string"></a>Konfigurera autentisering med hjälp av en anslutnings sträng för modul identitet

Konfigurera agenten att autentisera med hjälp av en anslutnings sträng för modul identitet:

1. Placera en fil med namnet `connection_string.txt` som innehåller anslutnings strängen som kodats i UTF-8 i katalog Sök vägen för Defender-agenten `/var/defender_iot_micro_agent` genom att ange följande kommando:

    ```azurecli
    sudo bash -c 'echo "<connection string" > /var/defender_iot_micro_agent/connection_string.txt' 
    ```

    `connection_string.txt`Ska finnas på följande Sök vägs plats `/var/defender_iot_micro_agent/connection_string.txt` .

1. Starta om tjänsten med det här kommandot:  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

### <a name="authenticate-using-a-certificate"></a>Autentisera med ett certifikat

Autentisera med ett certifikat:

1. Skaffa ett certifikat genom att följa [dessa anvisningar](../iot-hub/iot-hub-security-x509-get-started.md).

1. Placera den PEM offentliga delen av certifikatet och den privata nyckeln i till katalogen Defender-agenten i till filen som heter `certificate_public.pem` , och `certificate_private.pem` . 

1. Placera rätt anslutnings sträng i `connection_string.txt` filen. anslutnings strängen bör se ut så här: 

    `HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true` 

    Den här strängen varnar Defender-agenten för att vänta på att ett certifikat tillhandahålls för autentisering. 

1. Starta om tjänsten med följande kommando:  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service
    ```

### <a name="validate-your-installation"></a>Verifiera installationen

Verifiera installationen:

1. Kontrol lera att Micro-agenten fungerar korrekt med följande kommando:  

    ```azurecli
    systemctl status defender-iot-micro-agent.service
    ```
1. Se till att tjänsten är stabil genom att se till att den är `active` och att drift tiden för processen är lämplig

    :::image type="content" source="media/quickstart-standalone-agent-binary-installation/active-running.png" alt-text="Kontrol lera att tjänsten är stabil och aktiv.":::
 
## <a name="testing-the-system-end-to-end"></a>Testa systemet från slut punkt till slut punkt 

Du kan testa systemet från slut punkt till slut punkt genom att skapa en utlösnings fil på enheten. Trigger-filen kommer att leda till att bas linjen genomsöks i agenten för att identifiera filen som en bas linje överträdelse. 

Skapa en fil i fil systemet med följande kommando:

```azurecli
sudo touch /tmp/DefenderForIoTOSBaselineTrigger.txt 
```
En rekommendation för att verifiera en bas linje inträffar i hubben med en `CceId` av CIS-Debian-9-DEFENDER_FOR_IOT_TEST_CHECKS-0,0: 

:::image type="content" source="media/quickstart-standalone-agent-binary-installation/validation-failure.png" alt-text="Rekommendationen för grundläggande validerings fel som uppstår i hubben." lightbox="media/quickstart-standalone-agent-binary-installation/validation-failure-expanded.png":::

Tillåt upp till en timme för att rekommendationen ska visas i hubben. 

## <a name="micro-agent-versioning"></a>Versions hantering av Micro agent 

Kör följande kommando för att installera en version av Defender IoT Micro agent: 

```azurecli
sudo apt-get install defender-iot-micro-agent=<version>
```

## <a name="next-steps"></a>Nästa steg

[Skapa Defender Micro-agenten från käll koden](quickstart-building-the-defender-micro-agent-from-source.md)