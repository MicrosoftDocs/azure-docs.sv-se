---
title: Distribuera video analys i real tid på en IoT Edge enhet – Azure
description: Den här artikeln innehåller de steg som hjälper dig att distribuera video analys på din IoT Edge-enhet. Du skulle göra detta, till exempel om du har åtkomst till en lokal Linux-dator och/eller om du tidigare har skapat ett Azure Media Services-konto.
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: 38d138b43441016e06fca5003bc09c940cb23efe
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97883324"
---
# <a name="deploy-live-video-analytics-on-an-iot-edge-device"></a>Distribuera video analys i real tid på en IoT Edge enhet

Den här artikeln innehåller de steg som hjälper dig att distribuera video analys på din IoT Edge-enhet. Du skulle göra detta, till exempel om du har åtkomst till en lokal Linux-dator och/eller om du tidigare har skapat ett Azure Media Services-konto.

> [!NOTE]
> Stöd för ARM64-enheter finns i video analys i real tid på IoT Edge build-versioner `1.0.4` och senare.
> Stöd för att köra Azure IoT Edge runtime på ARM64-enheter finns i [offentlig för hands version](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

* En x86-64-eller ARM64-enhet som kör ett av de [Linux-operativsystem som stöds](../../iot-edge/support.md#operating-systems)
* Azure-prenumeration som du har [ägar behörighet](../../role-based-access-control/built-in-roles.md#owner) till
* [Skapa och konfigurera IoT Hub](../../iot-hub/iot-hub-create-through-portal.md)
* [Registrera IoT Edge enhet](../../iot-edge/how-to-manual-provision-symmetric-key.md)
* [Installera Azure IoT Edge-körningen på Debian-baserade Linux-system](../../iot-edge/how-to-install-iot-edge.md)
* [Skapa ett Azure Media Services-konto](../latest/create-account-howto.md)

    * Använd något av följande regioner: östra USA 2, östra USA, centrala USA, norra centrala USA, Östra Japan, västra USA, västra USA 2, västra centrala USA, Östra Kanada, Storbritannien, södra, Frankrike, centrala, Frankrike, Schweiz, norra, Schweiz, västra och Japan, västra.
    * Vi rekommenderar att du använder GPv2-lagrings konton (General-Purpose v2)

## <a name="configuring-azure-resources-for-using-live-video-analytics"></a>Konfigurera Azure-resurser för att använda live video analys

### <a name="create-custom-azure-resource-manager-role"></a>Skapa anpassad Azure Resource Manager-roll

Se [Skapa anpassad Azure Resource Manager roll](create-custom-azure-resource-manager-role-how-to.md) och tilldela den till ett tjänst huvud namn för användning med live video analys.

### <a name="set-up-a-premium-streaming-endpoint"></a>Konfigurera en Premium-slutpunkt för direkt uppspelning

Om du tänker använda real tids analys för att spela in video kontinuerligt i molnet och sedan använda [fråge-API: er](playback-recordings-how-to.md#query-api) innan du spelar upp den igen, rekommenderar vi att du uppdaterar medie tjänsten för att använda en [slut punkt för Premium-direktuppspelning](../latest/streaming-endpoint-concept.md#types).  

Detta är ett valfritt steg. Du kan använda det här Azure CLI-kommandot för att göra det:

```azurecli
az ams streaming-endpoint scale --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --scale-units 1
```

Du kan använda det här kommandot för att starta slut punkten för direkt uppspelning 

> [!IMPORTANT]
> Din prenumeration kommer att börja debiteras nu.

```azurecli
az ams streaming-endpoint start --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --no-wait
```

Följ stegen i den här artikeln för att få autentiseringsuppgifter för att få åtkomst till media service-API: er: [åtkomst till media service-API: er](../latest/access-api-howto.md?tabs=portal) och välj Portal-fliken.

## <a name="create-and-use-local-user-account-for-deployment"></a>Skapa och Använd ett lokalt användar konto för distribution
Om du vill köra live video analys i IoT Edge-modulen skapar du ett lokalt användar konto med så få behörigheter som möjligt. Du kan till exempel köra följande kommandon på Linux-datorn:

```
sudo groupadd -g 1010 localuser
sudo adduser --home /home/edgeuser --uid 1010 -gid 1010 edgeuser
```

## <a name="granting-permissions-to-device-storage"></a>Bevilja behörigheter till enhets lagring

Nu när du har skapat ett lokalt användar konto, 

* Du behöver en lokal mapp för att lagra programmets konfigurations data. Skapa en mapp och ge behörighet till lokal användare-kontot Skriv till den mappen med hjälp av följande kommandon:

```
sudo mkdir /var/lib/azuremediaservices
sudo chown -R edgeuser /var/lib/azuremediaservices
```

* Du behöver också en mapp för att [spela in videor till en lokal fil](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources). Använd följande kommandon för att skapa en lokal mapp för samma:

```
sudo mkdir /var/media
sudo chown -R edgeuser /var/media
```

## <a name="deploy-live-video-analytics-edge-module"></a>Distribuera Live Video Analytics Edge-modulen

Live video analys på IoT Edge exponerar modulens dubbla egenskaper som dokumenteras i [modulens dubbla konfigurations schema](module-twin-configuration-schema.md). 

### <a name="deploy-using-the-azure-portal"></a>Distribuera med hjälp av Azure-portalen

Azure Portal vägleder dig genom att skapa ett distributions manifest och distribuera distributionen till en IoT Edge enhet.  
#### <a name="select-your-device-and-set-modules"></a>Välj din enhet och ange moduler

1. Logga in på [Azure Portal](https://ms.portal.azure.com/) och navigera till din IoT-hubb.
1. Välj **IoT Edge** på menyn.
1. Klicka på mål enhetens ID i listan över enheter.
1. Välj **Ange moduler**.

#### <a name="configure-a-deployment-manifest"></a>Konfigurera ett distributions manifest

Ett distributions manifest är ett JSON-dokument som beskriver vilka moduler som ska distribueras, hur data flödar mellan moduler och önskade egenskaper för modulen. Azure Portal har en guide som vägleder dig genom att skapa ett distributions manifest. Den har tre steg indelade i flikar: **moduler**, **vägar** och **Granska + skapa**.

#### <a name="add-modules"></a>Lägga till moduler

1. I avsnittet **IoT Edge moduler** på sidan klickar du på list rutan **Lägg till** och väljer **IoT Edge modul** för att visa sidan **Lägg till IoT Edge-modul** .
1. Ange ett namn för modulen på fliken **Inställningar** och ange sedan URI: n för behållar avbildningen:   
    Exempel:
    
    * **IoT Edge modulens namn**: lvaEdge
    * **Bild-URI**: MCR.Microsoft.com/media/Live-Video-Analytics:2.0    
    
    ![Skärm bild som visar fliken modul inställningar.](./media/deploy-iot-edge-device/add.png)
    
    > [!TIP]
    > Välj inte **Lägg till** förrän du har angett värden för **modulens inställningar**, alternativet för att **skapa behållare** och fliken **dubbla inställningar** enligt beskrivningen i den här proceduren.
    
    > [!WARNING]
    > Azure IoT Edge är Skift läges känslig när du gör anrop till moduler. Anteckna den exakta strängen som du använder som modulnamn.

1. Öppna fliken **miljövariabler** .
   
   Lägg till följande värden i inmatade rutor som du ser ![ miljövariabler](./media/deploy-iot-edge-device/environment-variables.png) 

1. Öppna fliken **behållare skapa alternativ** .

    ![Alternativ för att skapa behållare](./media/deploy-iot-edge-device/container-create-options.png)
 
    Kopiera och klistra in följande JSON i rutan för att begränsa storleken på loggfilerna som skapas av modulen.
    
    ```    
    {
        "HostConfig": {
            "LogConfig": {
                "Type": "",
                "Config": {
                    "max-size": "10m",
                    "max-file": "10"
                }
            },
            "Binds": [
               "/var/lib/azuremediaservices:/var/lib/azuremediaservices",
               "/var/media:/var/media"
            ]
        }
    }
    ````
   
   Avsnittet "binds" i JSON har 2 poster:
   1. "/var/lib/azuremediaservices:/var/lib/azuremediaservices": används för att binda de beständiga program konfigurations data från behållaren och lagra dem på gräns enheten.
   1. "/var/media:/var/media": detta binder Media-mapparna mellan gräns enheten och behållaren. Detta används för att lagra videoinspelningarna när du kör en Media Graph-topologi som har stöd för lagring av video klipp på gräns enheten.
   
1. På fliken **dubbla inställningar för modul** kopierar du följande JSON och klistrar in den i rutan.
 
    ![Dubbla inställningar](./media/deploy-iot-edge-device/twin-settings.png)

    Real tids analys av video på IoT Edge kräver en uppsättning obligatoriska dubbla egenskaper för att kunna köras, enligt vad som anges i [modul, dubbel konfigurations schema](module-twin-configuration-schema.md).  

    JSON-filen som du måste ange i modulens inställningar för dubbla inställningar ser ut så här:    
    ```
    {
        "applicationDataDirectory": "/var/lib/azuremediaservices",
        "azureMediaServicesArmId": "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{AMS-account-name}",
        "aadTenantId": "{the-ID-of-your-tenant}",
        "aadServicePrincipalAppId": "{the-ID-of-the-service-principal-app-for-ams-account}",
        "aadServicePrincipalSecret": "{secret}"
    }
    ```
    Dessa är **obligatoriska** egenskaper och för JSON ovan.  
    * {subscriptionID} – det här är ditt Azure-prenumerations-ID
    * {resourceGroupName} – den resurs grupp som ditt medie tjänst konto tillhör
    * {AMS-Account-name} – det här är namnet på ditt Media Services konto
    
    Om du vill hämta de andra värdena går du [till åtkomst Azure Media Services API](../latest/access-api-howto.md?tabs=portal) och väljer Portal-fliken.  
    * aadTenantId – detta är ID: t för din klient och är samma som "AadTenantId" från länken ovan.
    * aadServicePrincipalAppId – detta är app-ID: t för tjänstens huvud namn för ditt Media Service-konto och är detsamma som "AadClientId" från länken ovan.
    * aadServicePrincipalSecret – det här är lösen ordet för tjänstens huvud namn och är detsamma som "AadSecret" från länken ovan.

    Nedan visas ytterligare **rekommenderade** egenskaper som kan läggas till i JSON och hjälper till att övervaka modulen. Mer information finns i [övervakning och loggning](monitoring-logging.md):
    
    ```
    "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
    "OperationalEventsOutputName": "lvaEdgeOperational",
    "logLevel": "Information",
    "logCategories": "Application,Events"
    ```
    
    Här följer några **valfria** egenskaper som du kan lägga till i JSON:
    
    ```
    "aadEndpoint": "https://login.microsoftonline.com",
    "aadResourceId": "https://management.core.windows.net/",
    "armEndpoint": "https://management.azure.com/",
    "allowUnsecuredEndpoints": true
    ```

   > [!Note]
   > Den dubbla egenskapen **allowUnsecuredEndpoints** anges som sant för självstudiernas syfte och snabb starter.   
   Du bör ange den här egenskapen till **falskt** när den körs i produktions miljön. På så sätt kan du se till att programmet blockerar alla oskyddade slut punkter och för att köra graf-topologierna krävs giltiga autentiseringsuppgifter för anslutning.  
   
    Välj Lägg till för att lägga till modulens dubbla egenskaper.
1. Välj **Nästa: vägar** för att fortsätta till avsnittet vägar.
    Ange vägar.

Behåll standard vägarna och välj **Nästa: granska + skapa** för att fortsätta till gransknings avsnittet.

#### <a name="review-deployment"></a>Granska distribution

I avsnittet granska visas JSON-distributions manifestet som skapades utifrån dina val i de föregående två avsnitten. Det finns också två moduler som har deklarerats att du inte har lagt till: $edgeAgent och $edgeHub. Dessa två moduler utgör den IoT Edge körningen och måste vara standardvärden i varje distribution.

Granska distributions informationen och välj sedan skapa.

### <a name="verify-your-deployment"></a>Verifiera distributionen

När du har skapat distributionen återgår du till IoT Edge sida i IoT Hub.

1. Välj den IoT Edge enhet som du har för distributionen för att öppna informationen.
2. I enhets informationen kontrollerar du att Blob Storage-modulen anges som båda **anges i distributionen och rapporteras av enheten**.

Det kan ta en stund innan modulen har startats på enheten och sedan rapporteras tillbaka till IoT Hub. Uppdatera sidan om du vill se en uppdaterad status.
Status kod: 200 – OK innebär att [den IoT Edge körningen](../../iot-edge/iot-edge-runtime.md) är felfri och fungerar bra.

![Skärm bild visar ett status värde för en IoT Edge Runtime.](./media/deploy-iot-edge-device/status.png)

#### <a name="invoke-a-direct-method"></a>Anropa en direktmetod

Sedan kan du testa exemplet genom att anropa en direkt metod. Läs [direkt metoder för video analys i real tid på IoT Edge](direct-methods.md) för att förstå alla direkta metoder som tillhandahålls av vår lvaEdge-modul.

1. När du klickar på den Edge-modul som du skapade, kommer du till konfigurations sidan.  

    ![Skärm bild som visar konfigurations sidan för en Edge-modul.](./media/deploy-iot-edge-device/modules.png)
1. Klicka på meny alternativet Direct-metod.

    > [!NOTE] 
    > Du måste lägga till ett värde i anslutnings Strängs avsnitten som du kan se på den aktuella sidan. Du behöver inte dölja eller ändra något i avsnittet **inställnings namn** . Det är OK att låta det vara offentligt.

    ![Direkt metod](./media/deploy-iot-edge-device/module-details.png)
1. Ange sedan "GraphTopologyList" i rutan metod namn.
1. Kopiera och klistra sedan in nedanstående JSON-nyttolast i nytto Last rutan.
    
    ```
    {
        "@apiVersion" : "1.0"
    }
    ```
1. Klicka på alternativet "anropa metod" överst på sidan

    ![Direkta metoder](./media/deploy-iot-edge-device/direct-method.png)
1. Du bör se ett status 200-meddelande i resultat rutan

    ![Status 200-meddelande](./media/deploy-iot-edge-device/connection-timeout.png) 

## <a name="next-steps"></a>Nästa steg

Prova [snabb start: kom igång – direktsända video analyser på IoT Edge](get-started-detect-motion-emit-events-quickstart.md#deploy-modules-on-your-edge-device)

> [!TIP]
> Om du fortsätter med ovanstående snabb start, när du anropar de direkta metoderna med Visual Studio Code, använder du enheten som lades till i IoT Hub via den här artikeln, i stället för standardvärdet `lva-sample-device` .