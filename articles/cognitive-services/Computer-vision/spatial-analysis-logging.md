---
title: Telemetri och loggning för rums analys behållare
titleSuffix: Azure Cognitive Services
description: Rums analys ger varje behållare ett gemensamt konfigurations ramverk, loggning och säkerhets inställningar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: dda3ece27fd2c687647e0aa289bd1596a87b274f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98186030"
---
# <a name="telemetry-and-troubleshooting"></a>Telemetri och fel sökning

Rumslig analys innehåller en uppsättning funktioner som övervakar systemets hälsotillstånd och hjälper till att diagnostisera problem.

## <a name="enable-visualizations"></a>Aktivera visualiseringar

Om du vill aktivera en visualisering av AI Insights-händelser i en video RAM måste du använda `.debug` versionen av en [spatial analys åtgärd](spatial-analysis-operations.md) på en stationär dator. Visualiseringen är inte möjlig på Azure Stack Edge-enheter. Det finns fyra fel söknings åtgärder tillgängliga.

Om enheten inte är en Azure Stack Edge-enhet redigerar du distributions manifest filen för [Station ära datorer](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) för att använda rätt värde för `DISPLAY` miljö variabeln. Den måste matcha `$DISPLAY` variabeln på värddatorn. När du har uppdaterat distributions manifestet distribuerar du om behållaren.

När distributionen har slutförts kan du behöva kopiera `.Xauthority` filen från värddatorn till behållaren och sedan starta om den. I exemplet nedan `peopleanalytics` är namnet på behållaren på värddatorn.

```bash
sudo docker cp $XAUTHORITY peopleanalytics:/root/.Xauthority
sudo docker stop peopleanalytics
sudo docker start peopleanalytics
xhost +
```


## <a name="collect-system-health-telemetry"></a>Samla in system hälso telemetri

Teleympkvistar är en bild med öppen källkod som fungerar med Spatial analys och är tillgänglig i Microsoft Container Registry. Det tar följande indata och skickar dem till Azure Monitor. Modulen teleympkvistar kan skapas med önskade anpassade indata och utdata. Konfigurationen av netympkvistar-modulen i rums analysen är en del av distributions manifestet (länkat ovan). Den här modulen är valfri och kan tas bort från manifestet om du inte behöver den. 

Tillför 
1. Mät värden för rums analys
2. Diskmått
3. PROCESSOR mått
4. Docker-mått
5. GPU-mått

Utdata
1. Azure Monitor

Den angivna spatiala netympkvistar-modulen kommer att publicera alla telemetridata som genereras av behållaren för rums analys till Azure Monitor. Se [Azure Monitor](../../azure-monitor/overview.md) för information om hur du lägger till Azure Monitor till din prenumeration.

När du har konfigurerat Azure Monitor måste du skapa autentiseringsuppgifter som gör det möjligt för modulen att skicka telemetri. Du kan använda Azure Portal för att skapa ett nytt huvud namn för tjänsten eller använda Azure CLI-kommandot nedan för att skapa ett.

> [!NOTE] 
> Det här kommandot kräver att du har ägar behörighet för prenumerationen. 

```bash
# Find your Azure IoT Hub resource ID by running this command. The resource ID  should start with something like 
# "/subscriptions/b60d6458-1234-4be4-9885-c7e73af9ced8/resourceGroups/...”
az iot hub list

# Create a Service Principal with `Monitoring Metrics Publisher` role in the IoTHub resource:
# Save the output from this command. The values will be used in the deployment manifest. The password won't be shown again so make sure to write it down
az ad sp create-for-rbac --role="Monitoring Metrics Publisher" --name "<principal name>" --scopes="<resource ID of IoT Hub>"
```

I distributions manifestet för din [Azure Stack Edge-enhet](https://go.microsoft.com/fwlink/?linkid=2142179), [Station ära dator](https://go.microsoft.com/fwlink/?linkid=2152270)eller virtuell [Azure-dator med GPU](https://go.microsoft.com/fwlink/?linkid=2152189)söker du efter modulen *Teleympkvistar* och ersätter följande värden med tjänstens huvud namns information från föregående steg och distribuerar om.

```json

"telegraf": { 
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/telegraf:1.0",
  "createOptions":   "{\"HostConfig\":{\"Runtime\":\"nvidia\",\"NetworkMode\":\"azure-iot-edge\",\"Memory\":33554432,\"Binds\":[\"/var/run/docker.sock:/var/run/docker.sock\"]}}"
},
"type": "docker",
"env": {
    "AZURE_TENANT_ID": {
        "value": "<Tenant Id>"
    },
    "AZURE_CLIENT_ID": {
        "value": "Application Id"
    },
    "AZURE_CLIENT_SECRET": {
        "value": "<Password>"
    },
    "region": {
        "value": "<Region>"
    },
    "resource_id": {
        "value": "/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}/providers/Microsoft.Devices/IotHubs/{IotHub}"
    },
...
```

När modulen teleympkvistar har distribuerats kan de rapporterade måtten nås via tjänsten Azure Monitor, eller genom att välja **övervakning** i IoT Hub på Azure Portal.

:::image type="content" source="./media/spatial-analysis/iot-hub-telemetry.png" alt-text="Azure Monitor telemetri-rapport":::

### <a name="system-health-events"></a>System hälso händelser

| Händelsenamn | Beskrivning|
|------|---------|
|archon_exit    |Skickas när en användare ändrar status för spatial Analysis-modulen från att *köras* till *stoppad*.  |
|archon_error   |Skickas när någon av processerna i behållarens krasch. Detta är ett kritiskt fel.  |
|InputRate  |Den hastighet med vilken grafen bearbetar video ingångar. Rapporteras var 5: e minut. | 
|OutputRate     |Den hastighet med vilken diagrammet matar ut AI-insikter. Rapporteras var 5: e minut. |
|archon_allGraphsStarted | Skickas när alla grafer har startats. |
|archon_configchange    | Skickas när en diagram konfiguration har ändrats. |
|archon_graphCreationFailed     |Skickas när grafen med det rapporterade problemet `graphId` inte startar. |
|archon_graphCreationSuccess    |Skickas när grafen med rapporterade `graphId` Starter har slutförts. |
|archon_graphCleanup    | Skickas när grafen med rapporterade `graphId` rensningar och avslut. |
|archon_graphHeartbeat  |Pulsslag skickas varje minut för varje graf i en färdighet. |
|archon_apiKeyAuthFail |Skickas när Visuellt innehåll resurs nyckeln inte kan autentisera behållaren i mer än 24 timmar på grund av följande orsaker: kvoten är ogiltig, offline. |
|VideoIngesterHeartbeat     |Skickas varje timme för att indikera att videon strömmas från video källan, med antalet fel i den timmen. Rapporteras för varje diagram. |
|VideoIngesterState | Rapporter har *stoppats* eller *startats* för video strömning. Rapporteras för varje diagram. |

##  <a name="troubleshooting-an-iot-edge-device"></a>Felsöka en IoT Edge enhet

Du kan använda `iotedge` kommando rads verktyget för att kontrol lera status och loggar för de moduler som körs. Exempel:
* `iotedge list`: Rapporterar en lista över moduler som körs. 
  Du kan söka efter fel med `iotedge logs edgeAgent` . Om `iotedge` det har fastnat kan du prova att starta om den med `iotedge restart edgeAgent`
* `iotedge logs <module-name>`
* `iotedge restart <module-name>` Starta om en angiven modul 

## <a name="collect-log-files-with-the-diagnostics-container"></a>Samla in loggfiler med diagnostik-behållaren

Rums analys genererar Docker fel söknings loggar som du kan använda för att diagnostisera körnings problem eller ta med i support biljetter. Modulen för att analysera spatial analys är tillgänglig i Microsoft Container Registry som du kan hämta. I manifest distributions filen för din [Azure Stack Edge-enhet](https://go.microsoft.com/fwlink/?linkid=2142179), [stationär dator](https://go.microsoft.com/fwlink/?linkid=2152270)eller virtuell [Azure-dator med GPU](https://go.microsoft.com/fwlink/?linkid=2152189) letar du reda på modulen *diagnostik* .

I avsnittet "miljö" lägger du till följande konfiguration:

```json
"diagnostics": {  
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/diagnostics:1.0",
  "createOptions":   "{\"HostConfig\":{\"Mounts\":[{\"Target\":\"/usr/bin/docker\",\"Source\":\"/home/data/docker\",\"Type\":\"bind\"},{\"Target\":\"/var/run\",\"Source\":\"/run\",\"Type\":\"bind\"}],\"LogConfig\":{\"Config\":{\"max-size\":\"500m\"}}}}"
  }
```    

För att optimera loggar som överförs till en fjärrslutpunkt, till exempel Azure Blob Storage, rekommenderar vi att du behåller en liten fil storlek. Se exemplet nedan för den rekommenderade konfiguration av Docker-loggar.

```json
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "500m",
                "max-file": "1000"
            }
        }
    }
}
```

### <a name="configure-the-log-level"></a>Konfigurera logg nivån

Med konfiguration av loggnings nivå kan du kontrol lera utförligheten för de genererade loggarna. De logg nivåer som stöds är: `none` , `verbose` ,, `info` `warning` och `error` . Standard logg utförlig nivå för både noder och plattform är `info` . 

Logg nivåer kan ändras globalt genom att ställa in `ARCHON_LOG_LEVEL` miljövariabeln på ett av de tillåtna värdena.
Det kan också ställas in via IoT Edge modulens dubbla dokument antingen globalt, för alla distribuerade kunskaper eller för varje speciell färdighet genom att ange värdena för `platformLogLevel` och `nodeLogLevel` enligt nedan.

```json
{
    "version": 1,
    "properties": {
        "desired": {
            "globalSettings": {
                "platformLogLevel": "verbose"
            },
            "graphs": {
                "samplegraph": {
                    "nodeLogLevel": "verbose",
                    "platformLogLevel": "verbose"
                }
            }
        }
    }
}
```

### <a name="collecting-logs"></a>Samlar in loggar

> [!NOTE]
> `diagnostics`Modulen påverkar inte loggnings innehållet, det underlättar bara för insamling, filtrering och uppladdning av befintliga loggar.
> Du måste ha Docker-API version 1,40 eller högre för att kunna använda den här modulen.

Manifest filen för distributions exempel för [Azure Stack Edge-enhet](https://go.microsoft.com/fwlink/?linkid=2142179), [stationär dator](https://go.microsoft.com/fwlink/?linkid=2152270)eller virtuell [Azure-dator med GPU](https://go.microsoft.com/fwlink/?linkid=2152189) innehåller en modul med namnet `diagnostics` som samlar in och laddar upp loggar. Den här modulen är inaktive rad som standard och måste aktive ras via IoT Edge module-konfigurationen när du behöver åtkomst till loggar. 

`diagnostics`Samlingen är på begäran och styrs via en IoT Edge Direct-metod och kan skicka loggar till en Azure-Blob Storage.

### <a name="configure-diagnostics-upload-targets"></a>Konfigurera uppladdnings mål för diagnostik

Från IoT Edge-portalen väljer du enheten och sedan **Diagnostics** -modulen. I manifest filen för exempel distribution för din [Azure Stack Edge-enhet](https://go.microsoft.com/fwlink/?linkid=2142179), [Station ära datorer](https://go.microsoft.com/fwlink/?linkid=2152270)eller [virtuell Azure-dator med GPU](https://go.microsoft.com/fwlink/?linkid=2152189) letar du reda på avsnittet **miljövariabler** för diagnostik, med namnet `env` och lägger till följande information:

**Konfigurera uppladdning till Azure Blob Storage**

1. Skapa ditt eget Azure Blob Storage-konto om du inte redan gjort det.
2. Hämta **anslutnings strängen** för ditt lagrings konto från Azure Portal. Den kommer att finnas i **åtkomst nycklar**.
3. Rums analys loggar överförs automatiskt till en Blob Storage-behållare med namnet *rtcvlogs* med följande fil namns format: `{CONTAINER_NAME}/{START_TIME}-{END_TIME}-{QUERY_TIME}.log` .

```json
"env":{
    "IOTEDGE_WORKLOADURI":"fd://iotedge.socket",
    "AZURE_STORAGE_CONNECTION_STRING":"XXXXXX",   //from the Azure Blob Storage account
    "ARCHON_LOG_LEVEL":"info"
}
```

### <a name="uploading-spatial-analysis-logs"></a>Överför rums analys loggar

Loggar överförs på begäran med `getRTCVLogs` metoden IoT Edge, i `diagnostics` modulen. 


1. Gå till sidan IoT Hub Portal, Välj **gräns enheter** och välj sedan din enhet och diagnostik-modulen. 
2. Gå till sidan information i modulen och klicka på fliken Direct- ***metod*** .
3. Ange `getRTCVLogs` metod namn och en JSON-format sträng i nytto lasten. Du kan ange `{}` , vilket är en tom nytto Last. 
4. Ange timeout för anslutning och metod och klicka på **anropa metod**.
5. Välj mål behållare och bygg en nytto Last-JSON-sträng med parametrarna som beskrivs i avsnittet **loggnings syntax** . Klicka på **anropa metod** för att utföra begäran.

>[!NOTE]
> `getRTCVLogs`Att anropa metoden med en tom nytto Last returnerar en lista över alla behållare som har distribuerats på enheten. Metod namnet är Skift läges känsligt. Du får ett 501-fel om ett felaktigt metod namn anges.

:::image type="content" source="./media/spatial-analysis/direct-log-collection.png" alt-text="Anropar metoden getRTCVLogs ":::
![Sidan getRTCVLogs Direct-metod](./media/spatial-analysis/direct-log-collection.png)

 
### <a name="logging-syntax"></a>Syntax för loggning

I tabellen nedan visas de parametrar som du kan använda när du frågar efter loggar.

| Följt | Beskrivning | Standardvärde |
|--|--|--|
| StartTime | Start tid för önskade loggar, i millisekunder UTC. | `-1`, början av behållarens körnings miljö. När `[-1.-1]` används som ett tidsintervall returnerar API: erna loggar från den senaste timmen.|
| EndTime | Slut tid för önskade loggar, i millisekunder UTC. | `-1`, aktuell tid. När `[-1.-1]` ett tidsintervall används returnerar API: erna loggar från den senaste timmen. |
| Hålla | Mål behållare för hämtning av loggar.| `null`, när det inte finns något container-ID. API: et returnerar all tillgänglig container information med ID: n.|
| DoPost | Utför uppladdnings åtgärden. När detta är inställt på `false` , utför den begärda åtgärden och returnerar överförings storleken utan att göra överföringen. När det är inställt på `true` , kommer den asynkrona överföringen av de valda loggarna att initieras | `false`, Ladda inte upp.|
| Begränsning | Ange hur många rader med loggar som ska överföras per batch | `1000`Använder du den här parametern för att justera post hastigheten. |
| Filter | Filtrerar loggar som ska överföras | `null`kan filter anges som nyckel värdes par baserat på strukturen för rums analys loggar: `[UTC, LocalTime, LOGLEVEL,PID, CLASS, DATA]` . Exempelvis: `{"TimeFilter":[-1,1573255761112]}, {"TimeFilter":[-1,1573255761112]}, {"CLASS":["myNode"]`|

I följande tabell visas attributen i svaret på frågan.

| Följt | Beskrivning|
|--|--|
|DoPost| Antingen *Sant* eller *falskt*. Anger om loggarna har överförts eller inte. Om du väljer att inte överföra loggar returnerar API: n informationen ***synkront** _. När du väljer att överföra loggar returnerar API: n 200 om begäran är giltig och börjar ladda upp loggar _ *_asynkront_* *.|
|TimeFilter| Tids filtret som används för loggarna.|
|ValueFilters| Nyckelords filter som tillämpas på loggarna. |
|TimeStamp| Start tid för metod körning. |
|Hålla| Mål container-ID. |
|FetchCounter| Totalt antal logg rader. |
|FetchSizeInByte| Total mängd logg data i byte. |
|MatchCounter| Giltigt antal logg rader. |
|MatchSizeInByte| Giltig mängd logg data i byte. |
|FilterCount| Totalt antal logg rader efter att ha tillämpat filter. |
|FilterSizeInByte| Den totala mängden loggdata i byte efter att ha tillämpat filtret. |
|FetchLogsDurationInMiliSec| Hämtnings åtgärdens varaktighet. |
|PaseLogsDurationInMiliSec| Filter åtgärdens varaktighet. |
|PostLogsDurationInMiliSec| Post-åtgärdens varaktighet. |

#### <a name="example-request"></a>Exempelbegäran 

```json
{
    "StartTime": -1,
    "EndTime": -1,
    "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
    "DoPost": false,
    "Filters": null
}
```

#### <a name="example-response"></a>Exempelsvar 

```json
{
    "status": 200,
    "payload": {
        "DoPost": false,
        "TimeFilter": [-1, 1581310339411],
        "ValueFilters": {},
        "Metas": {
            "TimeStamp": "2020-02-10T04:52:19.4365389+00:00",
            "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
            "FetchCounter": 61,
            "FetchSizeInByte": 20470,
            "MatchCounter": 61,
            "MatchSizeInByte": 20470,
            "FilterCount": 61,
            "FilterSizeInByte": 20470,
            "FetchLogsDurationInMiliSec": 0,
            "PaseLogsDurationInMiliSec": 0,
            "PostLogsDurationInMiliSec": 0
        }
    }
}
```

Kontrol lera hämtnings loggens rader, tider och storlekar, om dessa inställningar ser till att ersätta ***DoPost*** till `true` och som ska skicka loggarna med samma filter till mål. 

Du kan exportera loggar från Azure-Blob Storage när du felsöker problem. 

## <a name="common-issues"></a>Vanliga problem

Om du ser följande meddelande i-modulens loggar, kan det betyda att din Azure-prenumeration måste godkännas: 

"Container är inte i ett giltigt tillstånd. Prenumerations verifieringen misslyckades med status matchnings fel. API-nyckeln är inte avsedd för den aktuella behållar typen. "

Mer information finns i [begära godkännande för att köra behållaren](spatial-analysis-container.md#request-approval-to-run-the-container).

## <a name="troubleshooting-the-azure-stack-edge-device"></a>Felsöka Azure Stack Edge-enheten

I följande avsnitt finns information om hur du felsöker och verifierar status för din Azure Stack Edge-enhet.

### <a name="access-the-kubernetes-api-endpoint"></a>Få åtkomst till Kubernetes API-slutpunkten. 

1. I enhetens lokala användar gränssnitt går du till sidan **enheter** . 
2. Under **enhets slut punkter** kopierar du Kubernetes API-tjänstens slut punkt. Den här slut punkten är en sträng i följande format: `https://compute..[device-IP-address]` .
3. Spara slut punkts strängen. Du kommer att använda detta senare när du konfigurerar `kubectl` för att få åtkomst till Kubernetes-klustret.

### <a name="connect-to-powershell-interface"></a>Ansluta till PowerShell-gränssnittet

Anslut via fjärr anslutning från en Windows-klient. När Kubernetes-klustret har skapats kan du hantera programmen via det här klustret. Du måste ansluta till PowerShell-gränssnittet på enheten. Beroende på klientens operativ system kan procedurerna för fjärr anslutning till enheten vara annorlunda. Följande steg gäller för en Windows-klient som kör PowerShell.

> [!TIP]
> * Innan du börjar kontrollerar du att Windows-klienten kör Windows PowerShell 5,0 eller senare.
> * PowerShell är också [tillgängligt i Linux](/powershell/scripting/install/installing-powershell-core-on-linux).

1. Kör en Windows PowerShell-session som administratör. 
    1. Kontrol lera att tjänsten Windows Remote Management körs på klienten. Skriv i kommando tolken `winrm quickconfig` .

2. Tilldela en variabel för enhetens IP-adress. Till exempel `$ip = "<device-ip-address>"`.

3. Använd följande kommando för att lägga till IP-adressen för enheten i klientens lista över betrodda värdar. 

    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    ```
 
4. Starta en Windows PowerShell-session på enheten. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell
    ```

5. Ange lösen ordet när du uppmanas till det. Använd samma lösen ord som används för att logga in på det lokala webb gränssnittet. Standard lösen ordet för det lokala webb gränssnittet är `Password1` . 

### <a name="access-the-kubernetes-cluster"></a>Åtkomst till Kubernetes-klustret

När Kubernetes-klustret har skapats kan du använda `kubectl` kommando rads verktyget för att få åtkomst till klustret.

1. Skapa ett nytt namn område. 

    ```powershell
    New-HcsKubernetesNamespace -Namespace
    ```

2. Skapa en användare och hämta en konfigurations fil. Det här kommandot kommer att spara konfigurations information för Kubernetes-klustret. Kopiera den här informationen och spara den i en fil med namnet *config*. Spara inte filen som ett fil namns tillägg.
    
    ```powershell
    New-HcsKubernetesUser -UserName
    ```

3. Lägg till *konfigurations* filen i mappen *. Kube* i din användar profil på den lokala datorn.   

4. Koppla namn området till den användare som du skapade.

    ```powershell
    Grant-HcsKubernetesNamespaceAccess -Namespace -UserName
    ```

5. Installera `kubectl` på Windows-klienten med följande kommando:
    
    ```powershell
    curl https://storage.googleapis.com/kubernetesrelease/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    ```

6. Lägg till en DNS-post i värd filen på systemet. 
    1. Kör anteckningar som administratör och öppna *hosts* -filen som finns på `C:\windows\system32\drivers\etc\hosts` . 
    2. Skapa en post i hosts-filen med enhetens IP-adress och DNS-domän som du fick från **enhets** sidan i det lokala användar gränssnittet. Den slut punkt som du bör använda ser ut ungefär så här: `https://compute.asedevice.microsoftdatabox.com/10.100.10.10` .

7. Kontrol lera att du kan ansluta till Kubernetes-poddar.

    ```powershell
    kubectl get pods -n "iotedge"
    ```

Kör följande kommando för att hämta behållar loggar:

```powershell
kubectl logs <pod-name> -n <namespace> --all-containers
```

### <a name="useful-commands"></a>Användbara kommandon

|Kommando  |Beskrivning  |
|---------|---------|
|`Get-HcsKubernetesUserConfig -AseUser`     | Genererar en konfigurations fil för Kubernetes. När du använder kommandot kopierar du informationen till en fil med namnet *config*. Spara inte filen med ett fil namns tillägg.        |
| `Get-HcsApplianceInfo` | Returnerar information om enheten. |
| `Enable-HcsSupportAccess` | Genererar autentiseringsuppgifter för att starta en support-session. |

## <a name="next-steps"></a>Nästa steg

* [Distribuera en webb program inventering](spatial-analysis-web-app.md)
* [Konfigurera åtgärder för rums analys](./spatial-analysis-operations.md)
* [Guide för kamera placering](spatial-analysis-camera-placement.md)
* [Guide för zon-och linje placering](spatial-analysis-zone-line-placement.md)
