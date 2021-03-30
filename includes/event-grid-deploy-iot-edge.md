---
title: ta med fil
description: ta med fil
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/10/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0fc6a62a94f31617209bcc60bfaa95bc8927551a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86050312"
---
## <a name="deploy-event-grid-iot-edge-module"></a>Distribuera Event Grid IoT Edge modul

Det finns flera sätt att distribuera moduler till en IoT Edge-enhet och alla fungerar Azure Event Grid på IoT Edge. I den här artikeln beskrivs stegen för att distribuera Event Grid på IoT Edge från Azure Portal.

>[!NOTE]
> I den här självstudien ska du distribuera Event Grid-modulen utan persistence. Det innebär att alla ämnen och prenumerationer som du skapar i den här självstudien tas bort när du distribuerar om modulen. Mer information om hur du konfigurerar persistence finns i följande artiklar: [sparat tillstånd i Linux](../articles/event-grid/edge/persist-state-linux.md) eller [sparat tillstånd i Windows](../articles/event-grid/edge/persist-state-windows.md). För produktions arbets belastningar rekommenderar vi att du installerar Event Grid-modulen med persistence.

>[!IMPORTANT]
> I den här självstudien distribueras Event Grid-modulen med klientautentisering inaktive rad och tillåta HTTP-prenumeranter. För produktions arbets belastningar rekommenderar vi att du bara aktiverar HTTPS-förfrågningar och-prenumeranter med klientautentisering aktive rad. Mer information om hur du konfigurerar Event Grid modul på ett säkert sätt finns i [säkerhet och autentisering](../articles/event-grid/edge/security-authentication.md).
 
### <a name="select-your-iot-edge-device"></a>Välj din IoT Edge enhet

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Navigera till din IoT Hub.
1. Välj **IoT Edge** på menyn i avsnittet **Automatisk enhets hantering** . 
1. Klicka på mål enhetens ID i listan över enheter
1. Välj **Ange moduler**. Låt sidan vara öppen. Du kommer att fortsätta med stegen i nästa avsnitt.

### <a name="configure-a-deployment-manifest"></a>Konfigurera ett distributions manifest

Ett distributions manifest är ett JSON-dokument som beskriver vilka moduler som ska distribueras, hur data flödar mellan moduler och önskade egenskaper för modulen. Azure Portal har en guide som vägleder dig genom att skapa ett distributions manifest i stället för att skapa JSON-dokumentet manuellt.  Det finns tre steg: **Lägg till moduler**, **Ange vägar** och **Granska distribution**.

### <a name="add-modules"></a>Lägga till moduler

1. I avsnittet **distributions moduler** väljer du **Lägg till**
1. Från typer av moduler i list rutan väljer du **IoT Edge modul**
1. Ange namn, avbildning, behållarens skapande alternativ för behållaren:

[!INCLUDE [event-grid-edge-module-version-update](event-grid-edge-module-version-update.md)]

   * **Namn**: eventgridmodule
   * **Bild-URI**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Alternativ för att skapa behållare**:

```json
    {
      "Env": [
        "inbound__clientAuth:clientCert__enabled=false",
        "outbound__webhook__httpsOnly=false"
      ],
      "HostConfig": {
        "PortBindings": {
          "4438/tcp": [
            {
              "HostPort": "4438"
            }
          ]
        }
      }
    }
```

 1. Klicka på **Spara**
 1. Klicka på **Nästa** för att fortsätta till avsnittet vägar

    > [!NOTE]
    > Om du använder en virtuell Azure-dator som gräns enhet lägger du till en regel för inkommande port för att tillåta inkommande trafik på port 4438. Anvisningar om hur du lägger till regeln finns i [så här öppnar du portar till en virtuell dator](../articles/virtual-machines/windows/nsg-quickstart-portal.md).


### <a name="setup-routes"></a>Installations vägar

 Behåll standard vägarna och välj **Nästa** för att fortsätta till gransknings avsnittet

### <a name="review-deployment"></a>Granska distribution

1. I avsnittet granska visas JSON-distributions manifestet som skapades utifrån dina val i de föregående två avsnitten. Bekräfta att du ser de två modulerna i listan: **$edgeAgent** och **$edgeHub**. Dessa två moduler utgör den IoT Edge körningen och måste vara standardvärden i varje distribution.
1. Granska distributions informationen och välj sedan **Skicka**.

### <a name="verify-your-deployment"></a>Verifiera distributionen

1. När du har skickat distributionen återgår du till IoT Edge sida i IoT Hub.
1. Välj den **IoT Edge enhet** som du har för distributionen för att öppna informationen.
1. I enhets informationen kontrollerar du att modulen Event Grid är listad som både **angiven i distribution** och **rapporteras av enheten**.

Det kan ta en stund innan modulen har startats på enheten och sedan rapporteras tillbaka till IoT Hub. Uppdatera sidan om du vill se en uppdaterad status.