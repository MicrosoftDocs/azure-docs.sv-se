---
title: Distribuera IoT Edge säkerhetsmodul
description: Lär dig mer om hur du distribuerar en Defender for IoT-säkerhetsagent IoT Edge.
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: 71efb0bb12d1e20f918481a086fd411d3a237e33
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813604"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Distribuera en säkerhetsmodul på din IoT Edge enhet

**Defender for IoT-modulen** tillhandahåller en omfattande säkerhetslösning för dina IoT Edge enheter.
Säkerhetsmodulen samlar in, aggregerar och analyserar rådata från ditt operativsystem och containersystem till användbara säkerhetsrekommendationer och aviseringar.
Mer information finns i [Säkerhetsmodul för IoT Edge](security-edge-architecture.md).

I den här artikeln får du lära dig hur du distribuerar en säkerhetsmodul på din IoT Edge enhet.

## <a name="deploy-security-module"></a>Distribuera säkerhetsmodul

Använd följande steg för att distribuera en Defender for IoT-säkerhetsmodul för IoT Edge.

### <a name="prerequisites"></a>Förutsättningar

1. I din IoT Hub kontrollerar du att enheten är [registrerad som en IoT Edge enhet](../iot-edge/how-to-register-device.md#register-a-new-device).

1. Defender for IoT Edge-modulen kräver att [AuditD-ramverket](https://linux.die.net/man/8/auditd) är installerat IoT Edge enheten.

    - Installera ramverket genom att köra följande kommando på din IoT Edge enhet:

    `sudo apt-get install auditd audispd-plugins`

    - Kontrollera att AuditD är aktivt genom att köra följande kommando:

    `sudo systemctl status auditd`<br>
    - Förväntat svar är: `active (running)`

### <a name="deployment-using-azure-portal"></a>Distribution med Azure Portal

1. Från Azure Portal du **Marketplace.**

1. Välj **Sakernas Internet** och sök sedan efter **Azure Security Center IoT** och välj det.

   :::image type="content" source="media/howto/edge-onboarding-8.png" alt-text="Välj Defender för IoT":::

1. Välj **Skapa** för att konfigurera distributionen.

1. Välj **Azure-prenumerationen** för IoT Hub och välj sedan **din IoT Hub**.<br>Välj **Distribuera till en enhet för** att rikta en enskild enhet eller välj Distribuera i **skala** för att rikta in dig på flera enheter och välj **Skapa.** Mer information om hur du distribuerar i stor skala finns [i Så här distribuerar du](../iot-edge/how-to-deploy-at-scale.md).

    >[!Note]
    >Om du har **valt Distribuera i skala** lägger du till enhetsnamnet och informationen innan du fortsätter till fliken **Lägg** till moduler i följande anvisningar.

Slutför varje steg för att slutföra IoT Edge distributionen för Defender for IoT.

#### <a name="step-1-modules"></a>Steg 1: Moduler

1. Välj modulen **AzureSecurityCenterforIoT.**
1. På fliken **Modulinställningar** ändrar du namnet **till** **azureiotsecurity**.
1. På fliken **Miljövariabler** lägger du till en variabel om det behövs (du kan till exempel lägga till *felsökningsnivå* och ange den till något av följande värden: "Fatal", "Error", "Warning" eller "Information").
1. Lägg till **följande konfiguration på** fliken Alternativ för att skapa containrar:

    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }
    ```

1. Lägg till **följande konfiguration på** fliken Inställningar för modultvilling:

   Modultvillingegenskap:
   
   ``` json
     "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration"
   ```

   Egenskapsinnehåll för modultvilling: 

   ```json
     {

     }
   ```
    
   Mer information om hur du konfigurerar agenten finns [i Konfigurera säkerhetsagenter.](./how-to-agent-configuration.md)

1. Välj **Uppdatera**.

#### <a name="step-2-runtime-settings"></a>Steg 2: Körningsinställningar

1. Välj **Körningsinställningar.**
2. Under **Edge Hub** ändrar du **avbildningen** till **mcr.microsoft.com/azureiotedge-hub:1.0.8.3**.

    >[!Note]
    > Version 1.0.8.3 eller senare stöds för närvarande.

3. Kontrollera **att Skapa** alternativ är inställt på följande konfiguration:

    ``` json
    {
       "HostConfig":{
          "PortBindings":{
             "8883/tcp":[
                {
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[
                {
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[
                {
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```

4. Välj **Spara**.

5. Välj **Nästa**.

#### <a name="step-3-specify-routes"></a>Steg 3: Ange vägar

1. På fliken **Ange vägar** kontrollerar du att du har en väg (explicit eller implicit) som vidarebefordrar meddelanden från **modulen azureiotsecurity** **till $upstream** enligt följande exempel. Välj Nästa endast när vägen är **på plats.**

   Exempelvägar:

    ```Default implicit route
    "route": "FROM /messages/* INTO $upstream"
    ```

    ```Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ```

1. Välj **Nästa**.

#### <a name="step-4-review-deployment"></a>Steg 4: Granska distributionen

- På fliken **Granska distribution** granskar du distributionsinformationen och väljer sedan Skapa **för** att slutföra distributionen.

## <a name="diagnostic-steps"></a>Diagnostiksteg

Om du stöter på ett problem är containerloggar det bästa sättet att lära dig om tillståndet för en IoT Edge-säkerhetsmodulenhet. Använd kommandona och verktygen i det här avsnittet för att samla in information.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Kontrollera att nödvändiga containrar är installerade och fungerar som förväntat

1. Kör följande kommando på din IoT Edge enhet:

    `sudo docker ps`

1. Kontrollera att följande containrar körs:

   | Name | BILD |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |

   Om de minsta nödvändiga containrarna inte finns kontrollerar du om IoT Edge distributionsmanifestet är justerat med de rekommenderade inställningarna. Mer information finns i Distribuera [IoT Edge modulen](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>Granska modulloggarna och se om det finns fel

1. Kör följande kommando på din IoT Edge enhet:

   `sudo docker logs azureiotsecurity`

1. Om du vill ha mer utförliga loggar lägger du till följande miljövariabel i **distributionen av azureiotsecurity-modulen:** `logLevel=Debug` .

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om konfigurationsalternativ fortsätter du till i guiden för modulkonfiguration.
> [!div class="nextstepaction"]
> [Guide för modulkonfiguration](./how-to-agent-configuration.md)