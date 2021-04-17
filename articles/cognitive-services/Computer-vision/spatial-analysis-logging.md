---
title: Telemetri och loggning för spatiala analyscontainrar
titleSuffix: Azure Cognitive Services
description: Rumslig analys ger varje container en gemensam konfigurationsramverksinformation, loggning och säkerhetsinställningar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 901e857a346b0955726c5755e23595efefbc2ca1
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589507"
---
# <a name="telemetry-and-troubleshooting"></a>Telemetri och felsökning

Rumslig analys innehåller en uppsättning funktioner för att övervaka systemets hälsotillstånd och hjälpa till med diagnostiseringsproblem.

## <a name="enable-visualizations"></a>Aktivera visualiseringar

Om du vill aktivera Alla insikter av händelser i en videoram måste du använda versionen av en `.debug` [spatial analysåtgärd](spatial-analysis-operations.md) på en stationär dator. Visualiseringen är inte möjlig på Azure Stack Edge enheter. Det finns fyra tillgängliga felsökningsåtgärder.

Om enheten inte är en Azure Stack Edge redigera distributionsmanifestfilen för stationära datorer så att rätt värde används för [](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) `DISPLAY` miljövariabeln. Den måste matcha `$DISPLAY` variabeln på värddatorn. När du har uppdaterat distributionsmanifestet distribuerar du om containern.

När distributionen är klar kan du behöva kopiera `.Xauthority` filen från värddatorn till containern och starta om den. I exemplet nedan `peopleanalytics` är namnet på containern på värddatorn.

```bash
sudo docker cp $XAUTHORITY peopleanalytics:/root/.Xauthority
sudo docker stop peopleanalytics
sudo docker start peopleanalytics
xhost +
```


## <a name="collect-system-health-telemetry"></a>Samla in telemetri om systemhälsa

Telegraf är en bild med öppen källkod som fungerar med rumslig analys och är tillgänglig i Microsoft Container Registry. Den tar följande indata och skickar dem till Azure Monitor. Telegraf-modulen kan byggas med önskade anpassade indata och utdata. Telegraf-modulkonfigurationen i Spatial Analysis är en del av distributionsmanifestet (länkat ovan). Den här modulen är valfri och kan tas bort från manifestet om du inte behöver den. 

Ingångar: 
1. Mått för rumslig analys
2. Diskmått
3. CPU-mått
4. Docker-mått
5. GPU-mått

Utgångar:
1. Azure Monitor

Den angivna telegraf-modulen för rumslig analys publicerar alla telemetridata som skickas av containern för rumslig analys till Azure Monitor. Se informationen [Azure Monitor](../../azure-monitor/overview.md) om hur du lägger till Azure Monitor i din prenumeration.

När du Azure Monitor måste du skapa autentiseringsuppgifter som gör att modulen kan skicka telemetri. Du kan använda Azure Portal för att skapa ett nytt huvudnamn för tjänsten eller använda Azure CLI-kommandot nedan för att skapa ett.

> [!NOTE] 
> Det här kommandot kräver att du har ägarbehörighet för prenumerationen. 

```bash
# Find your Azure IoT Hub resource ID by running this command. The resource ID  should start with something like 
# "/subscriptions/b60d6458-1234-4be4-9885-c7e73af9ced8/resourceGroups/..."
az iot hub list

# Create a Service Principal with `Monitoring Metrics Publisher` role in the IoTHub resource:
# Save the output from this command. The values will be used in the deployment manifest. The password won't be shown again so make sure to write it down
az ad sp create-for-rbac --role="Monitoring Metrics Publisher" --name "<principal name>" --scopes="<resource ID of IoT Hub>"
```

Leta upp *telegraf-modulen* i distributionsmanifestet för din Azure Stack Edge-enhet, [](https://go.microsoft.com/fwlink/?linkid=2152270)stationära dator eller virtuella [Azure-dator](https://go.microsoft.com/fwlink/?linkid=2152189)med GPU och ersätt följande värden med informationen om tjänstens huvudnamn från föregående steg och distribuera om. [](https://go.microsoft.com/fwlink/?linkid=2142179)

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

När telegraf-modulen har distribuerats kan de rapporterade måtten nås antingen via Azure Monitor-tjänsten eller genom att välja Övervakning i IoT Hub på Azure Portal. 

:::image type="content" source="./media/spatial-analysis/iot-hub-telemetry.png" alt-text="Azure Monitor telemetrirapport":::

### <a name="system-health-events"></a>Systemhälsohändelser

| Händelsenamn                  | Description    |
|-----------------------------|-------------------------------------------------------------------------------------------|
| archon_exit                 | Skickas när en användare ändrar modulstatus för rumslig analys från att *köras till* *stoppad*.  |
| archon_error                | Skickas när någon av processerna i containern kraschar. Det här är ett kritiskt fel.      |
| InputRate                   | Den hastighet med vilken grafen bearbetar videoindata. Rapporteras var femte minut.              |
| OutputRate                  | Den hastighet med vilken grafen matar ut AI-insikter. Rapporteras var femte minut.                |
| archon_allGraphsStarted     | Skickas när alla grafer har börjat.                                           |
| archon_configchange         | Skickas när en grafkonfiguration har ändrats.                                              |
| archon_graphCreationFailed  | Skickas när grafen med rapporterade `graphId` inte startar.                           |
| archon_graphCreationSuccess | Skickas när grafen med den rapporterade `graphId` startar korrekt.                      |
| archon_graphCleanup         | Skickas när grafen med den rapporterade `graphId` rensningen rensas och avslutas.                      |
| archon_graphHeartbeat       | Pulsslag som skickas varje minut för varje graf i en färdighet.                                   |
| archon_apiKeyAuthFail       | Skickas när Visuellt innehåll-resursnyckeln inte kan autentisera containern i mer än 24 timmar, på grund av följande orsaker: Utanför kvoten, Ogiltig, Offline. |
| VideoIngesterPatibeat      | Skickas varje timme för att indikera att videon strömmas från videokällan, med antalet fel under den timmen. Rapporteras för varje graf. |
| VideoIngesterState          | Rapporter *stoppade* eller *startade för* videoströmning. Rapporteras för varje graf.              |

##  <a name="troubleshooting-an-iot-edge-device"></a>Felsöka en IoT Edge enhet

Du kan använda `iotedge` kommandoradsverktyget för att kontrollera status och loggar för de moduler som körs. Exempel:
* `iotedge list`: Rapporterar en lista över moduler som körs. 
  Du kan ytterligare söka efter fel med `iotedge logs edgeAgent` . Om `iotedge` fastnar kan du prova att starta om den med `iotedge restart edgeAgent`
* `iotedge logs <module-name>`
* `iotedge restart <module-name>` för att starta om en specifik modul 

## <a name="collect-log-files-with-the-diagnostics-container"></a>Samla in loggfiler med diagnostikcontainern

Rumslig analys genererar Docker-felsökningsloggar som du kan använda för att diagnostisera körningsproblem eller inkludera dem i supportärenden. Diagnostikmodulen för rumslig analys finns i Microsoft Container Registry du kan ladda ned. Leta upp diagnostikmodulen i [manifestdistributionsfilen](https://go.microsoft.com/fwlink/?linkid=2142179)för Azure Stack Edge [enhet,](https://go.microsoft.com/fwlink/?linkid=2152270)stationär dator eller virtuell Azure-dator  med [GPU.](https://go.microsoft.com/fwlink/?linkid=2152189)

I avsnittet "env" lägger du till följande konfiguration:

```json
"diagnostics": {  
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/diagnostics:1.0",
  "createOptions":   "{\"HostConfig\":{\"Mounts\":[{\"Target\":\"/usr/bin/docker\",\"Source\":\"/home/data/docker\",\"Type\":\"bind\"},{\"Target\":\"/var/run\",\"Source\":\"/run\",\"Type\":\"bind\"}],\"LogConfig\":{\"Config\":{\"max-size\":\"500m\"}}}}"
  }
```    

Om du vill optimera loggar som laddats upp till en fjärrslutpunkt, till Azure Blob Storage, rekommenderar vi att du behåller en liten filstorlek. Se exemplet nedan för den rekommenderade konfigurationen av Docker-loggar.

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

### <a name="configure-the-log-level"></a>Konfigurera loggnivån

Med loggnivåkonfigurationen kan du kontrollera de genererade loggarnas verbositet. Loggnivåer som stöds är: `none` , , , och `verbose` `info` `warning` `error` . Standardloggens utförliga nivå för både noder och plattform är `info` . 

Loggnivåer kan ändras globalt genom att ange `ARCHON_LOG_LEVEL` miljövariabeln till något av de tillåtna värdena.
Den kan också anges via dokumentet IoT Edge-modultvilling antingen globalt, för alla distribuerade färdigheter eller för varje specifik kunskap genom att ange värdena för och `platformLogLevel` `nodesLogLevel` enligt nedan.

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
                    "nodesLogLevel": "verbose",
                    "platformLogLevel": "verbose"
                }
            }
        }
    }
}
```

### <a name="collecting-logs"></a>Samla in loggar

> [!NOTE]
> Modulen `diagnostics` påverkar inte loggningsinnehållet, utan hjälper bara till att samla in, filtrera och ladda upp befintliga loggar.
> Du måste ha Docker API version 1.40 eller senare för att kunna använda den här modulen.

Exempeldistributionsmanifestfilen [för din Azure Stack Edge,](https://go.microsoft.com/fwlink/?linkid=2142179)stationär dator eller virtuell [Azure-dator](https://go.microsoft.com/fwlink/?linkid=2152189) med GPU innehåller en modul med namnet som samlar in och laddar upp [](https://go.microsoft.com/fwlink/?linkid=2152270) `diagnostics` loggar. Den här modulen är inaktiverad som standard och bör aktiveras via IoT Edge-modulkonfigurationen när du behöver komma åt loggar. 

Samlingen `diagnostics` är på begäran och styrs via en IoT Edge direktmetod och kan skicka loggar till en Azure Blob Storage.

### <a name="configure-diagnostics-upload-targets"></a>Konfigurera mål för uppladdning av diagnostik

Från IoT Edge-portalen väljer du din enhet och sedan **diagnostikmodulen.** I exemplet distributionsmanifestfil för din [](https://go.microsoft.com/fwlink/?linkid=2152270) [Azure Stack Edge, stationära](https://go.microsoft.com/fwlink/?linkid=2142179)datorer eller [virtuella Azure-datorer](https://go.microsoft.com/fwlink/?linkid=2152189) med GPU letar du efter avsnittet **Miljövariabler** för diagnostik med namnet och lägger `env` till följande information:

**Konfigurera Uppladdning till Azure Blob Storage**

1. Skapa ett Azure Blob Storage konto om du inte redan har gjort det.
2. Hämta **anslutningssträngen** för ditt lagringskonto från Azure Portal. Den finns i **Åtkomstnycklar**.
3. Loggar för rumslig analys överförs automatiskt till en Blob Storage med namnet *rtcvlogs* med följande filnamnsformat: `{CONTAINER_NAME}/{START_TIME}-{END_TIME}-{QUERY_TIME}.log` .

```json
"env":{
    "IOTEDGE_WORKLOADURI":"fd://iotedge.socket",
    "AZURE_STORAGE_CONNECTION_STRING":"XXXXXX",   //from the Azure Blob Storage account
    "ARCHON_LOG_LEVEL":"info"
}
```

### <a name="uploading-spatial-analysis-logs"></a>Ladda upp loggar för rumslig analys

Loggar laddas upp på begäran med `getRTCVLogs` IoT Edge-metoden i `diagnostics` modulen. 


1. Gå till IoT Hub portalsida, välj **Edge-enheter** och välj sedan din enhet och diagnostikmodulen. 
2. Gå till informationssidan för modulen och klicka på fliken ***Direktmetod.***
3. Skriv `getRTCVLogs` på Metodnamn och en json-formatsträng i nyttolasten. Du kan ange `{}` , som är en tom nyttolast. 
4. Ange tidsgränser för anslutningar och metoder och klicka på **Anropa metod**.
5. Välj målcontainern och skapa en json-sträng för nyttolasten med hjälp av parametrarna som beskrivs i **avsnittet Loggningssyntax.** Klicka **på Anropa metod** för att utföra begäran.

>[!NOTE]
> När metoden `getRTCVLogs` anropas med en tom nyttolast returneras en lista över alla containrar som distribueras på enheten. Metodnamnet är ärendekänsligt. Du får ett 501-fel om ett felaktigt metodnamn anges.

:::image type="content" source="./media/spatial-analysis/direct-log-collection.png" alt-text="Anrop av metoden getRTCVLogs ":::
![getRTCVLogs Direct-metodsida](./media/spatial-analysis/direct-log-collection.png)

 
### <a name="logging-syntax"></a>Loggningssyntax

I tabellen nedan visas de parametrar som du kan använda när du frågar loggar.

| Sökord | Beskrivning | Standardvärde |
|--|--|--|
| StartTime | Starttid för önskade loggar, i millisekunder UTC. | `-1`, början av containerns körning. När `[-1.-1]` används som ett tidsintervall returnerar API:et loggar från den senaste timmen.|
| EndTime | Sluttid för önskade loggar, i millisekunder UTC. | `-1`, den aktuella tiden. När `[-1.-1]` ett tidsintervall används returnerar API:et loggar från den senaste timmen. |
| ContainerId | Målcontainer för hämtning av loggar.| `null`, när det inte finns något container-ID. API:et returnerar all tillgänglig containerinformation med -ID:n.|
| DoPost | Utför uppladdningsåtgärden. När det här är `false` inställt på utför den begärda åtgärden och returnerar uppladdningsstorleken utan att utföra uppladdningen. När det här `true` är inställt på initierar det den asynkrona uppladdningen av de valda loggarna | `false`, ladda inte upp.|
| Begränsning | Ange hur många rader loggar som ska laddas upp per batch | `1000`, Använd den här parametern för att justera posthastigheten. |
| Filter | Filtrerar loggar som ska laddas upp | `null`, kan filter anges som nyckelvärdepar baserat på loggstrukturen för rumslig analys: `[UTC, LocalTime, LOGLEVEL,PID, CLASS, DATA]` . Exempelvis: `{"TimeFilter":[-1,1573255761112]}, {"TimeFilter":[-1,1573255761112]}, {"CLASS":["myNode"]`|

I följande tabell visas attributen i frågesvaret.

| Sökord | Description|
|--|--|
|DoPost| Antingen *sant* eller *falskt*. Anger om loggar har laddats upp eller inte. När du väljer att inte ladda upp loggar returnerar API:et information ***synkront** _. När du väljer att ladda upp loggar returnerar API:et 200, om begäran är giltig, och börjar ladda upp loggar _*_asynkront_**.|
|TimeFilter| Tidsfilter som tillämpas på loggarna.|
|ValueFilters| Nyckelordsfilter som tillämpas på loggarna. |
|TimeStamp| Starttid för metodkörning. |
|ContainerId| Målcontainer-ID. |
|FetchCounter| Totalt antal loggrader. |
|FetchSizeInByte| Total mängd loggdata i byte. |
|MatchCounter| Giltigt antal loggrader. |
|MatchSizeInByte| Giltig mängd loggdata i byte. |
|FilterCount| Totalt antal loggrader efter att filtret har tillämpats. |
|FilterSizeInByte| Total mängd loggdata i byte efter tillämpat filter. |
|FetchLogsDurationInMiliSec| Varaktighet för hämtningsåtgärd. |
|PaseLogsDurationInMiliSec| Varaktighet för filteråtgärd. |
|PostLogsDurationInMiliSec| Varaktighet efter åtgärd. |

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

Kontrollera hämtningsloggens rader, tider och storlekar. Om dessa inställningar ser bra ut ersätter du ***DoPost*** till så push-hämtas loggarna med `true` samma filter till mål. 

Du kan exportera loggar från Azure Blob Storage när du felsöker problem. 

## <a name="common-issues"></a>Vanliga problem

Om du ser följande meddelande i modulloggarna kan det betyda att din Azure-prenumeration måste godkännas: 

"Containern är inte i ett giltigt tillstånd. Prenumerationsverifieringen misslyckades med statusen "Matchningsfel". API-nyckeln är inte avsedd för den angivna containertypen."

Mer information finns i [Begär godkännande för att köra containern](spatial-analysis-container.md#request-approval-to-run-the-container).

## <a name="troubleshooting-the-azure-stack-edge-device"></a>Felsöka Azure Stack Edge enhet

Följande avsnitt innehåller hjälp med felsökning och verifiering av statusen för din Azure Stack Edge enhet.

### <a name="access-the-kubernetes-api-endpoint"></a>Åtkomst till Kubernetes API-slutpunkten. 

1. I enhetens lokala användargränssnitt går du till **sidan** Enheter. 
2. Under **Enhetsslutpunkter kopierar** du Kubernetes API-tjänstslutpunkten. Den här slutpunkten är en sträng i följande format: `https://compute..[device-IP-address]` .
3. Spara slutpunktssträngen. Du kommer att använda detta senare när du `kubectl` konfigurerar för åtkomst till Kubernetes-klustret.

### <a name="connect-to-powershell-interface"></a>Ansluta till PowerShell-gränssnittet

Fjärranslut från en Windows-klient. När Kubernetes-klustret har skapats kan du hantera programmen via det här klustret. Du måste ansluta till Enhetens PowerShell-gränssnitt. Beroende på klientens operativsystem kan procedurerna för att fjärransluta till enheten vara olika. Följande steg gäller för en Windows-klient som kör PowerShell.

> [!TIP]
> * Innan du börjar kontrollerar du att Windows-klienten kör Windows PowerShell 5.0 eller senare.
> * PowerShell är också [tillgängligt på Linux](/powershell/scripting/install/installing-powershell-core-on-linux).

1. Kör en Windows PowerShell-session som administratör. 
    1. Kontrollera att Tjänsten Windows Remote Management körs på klienten. Skriv i `winrm quickconfig` kommandotolken.

2. Tilldela en variabel för enhetens IP-adress. Till exempel `$ip = "<device-ip-address>"`.

3. Använd följande kommando för att lägga till IP-adressen för enheten i klientens lista över betrodda värdar. 

    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    ```
 
4. Starta en Windows PowerShell-session på enheten. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell
    ```

5. Ange lösenordet när du uppmanas att göra det. Använd samma lösenord som används för att logga in på det lokala webbgränssnittet. Standardlösenordet för det lokala webbgränssnittet är `Password1` . 

### <a name="access-the-kubernetes-cluster"></a>Få åtkomst till Kubernetes-klustret

När Kubernetes-klustret har skapats kan du använda `kubectl` kommandoradsverktyget för att komma åt klustret.

1. Skapa ett nytt namnområde. 

    ```powershell
    New-HcsKubernetesNamespace -Namespace
    ```

2. Skapa en användare och hämta en konfigurationsfil. Det här kommandot matar ut konfigurationsinformation för Kubernetes-klustret. Kopiera den här informationen och spara den i en fil med *namnet config*. Spara inte filen med ett filnamnstillägg.
    
    ```powershell
    New-HcsKubernetesUser -UserName
    ```

3. Lägg till *konfigurationsfilen* i *.kube-mappen* i din användarprofil på den lokala datorn.    

4. Associera namnområdet med den användare som du skapade.

    ```powershell
    Grant-HcsKubernetesNamespaceAccess -Namespace -UserName
    ```

5. Installera `kubectl` på Windows-klienten med följande kommando:
    
    ```powershell
    curl https://storage.googleapis.com/kubernetesrelease/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    ```

6. Lägg till en DNS-post i värdfilen på datorn. 
    1. Kör Anteckningar som administratör och öppna *värdfilen* som finns på `C:\windows\system32\drivers\etc\hosts` . 
    2. Skapa en post i värdfilen med enhetens IP-adress och DNS-domän som du fick **från sidan** Enhet i det lokala användargränssnittet. Slutpunkten som du bör använda ser ut ungefär så här: `https://compute.asedevice.microsoftdatabox.com/10.100.10.10` .

7. Kontrollera att du kan ansluta till Kubernetes-poddarna.

    ```powershell
    kubectl get pods -n "iotedge"
    ```

Kör följande kommando för att hämta containerloggar:

```powershell
kubectl logs <pod-name> -n <namespace> --all-containers
```

### <a name="useful-commands"></a>Användbara kommandon

|Kommando  |Beskrivning  |
|---------|---------|
|`Get-HcsKubernetesUserConfig -AseUser`     | Genererar en Kubernetes-konfigurationsfil. När du använder kommandot kopierar du informationen till en fil med namnet *config*. Spara inte filen med ett filnamnstillägg.        |
| `Get-HcsApplianceInfo` | Returnerar information om din enhet. |
| `Enable-HcsSupportAccess` | Genererar autentiseringsuppgifter för åtkomst för att starta en supportsession. |


## <a name="how-to-file-a-support-ticket-for-spatial-analysis"></a>Så här gör du för att skapa en supportbiljett för rumslig analys 

Om du behöver mer support för att hitta en lösning på ett problem som du har med containern Spatial Analysis följer du dessa steg för att fylla i och skicka en supportbiljett. Vårt team kommer tillbaka till dig med ytterligare vägledning. 

### <a name="fill-out-the-basics"></a>Fyll i grunderna 
Skapa en ny supportbegäran på sidan [Ny supportbegäran.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Följ anvisningarna för att fylla i följande parametrar:

![Grundläggande om stöd](./media/support-ticket-page-1-final.png)

1. Ange **Problemtyp** till `Technical` .
2. Välj den prenumeration som du använder för att distribuera containern för rumslig analys.
3. Välj `My services` och välj som `Cognitive Services` tjänsten.
4. Välj den resurs som du använder för att distribuera containern för rumslig analys.
5. Skriv en kort beskrivning som beskriver problemet du har. 
6. Välj `Spatial Analysis` som problemtyp.
7. Välj lämplig undertyp i listrutan.
8. Välj **Nästa: Lösningar** för att gå vidare till nästa sida.

### <a name="recommended-solutions"></a>Rekommenderade lösningar
Nästa steg erbjuder rekommenderade lösningar för den problemtyp som du har valt. Dessa lösningar löser de vanligaste problemen, men om det inte är användbart för din lösning väljer du **Nästa: Information** för att gå till nästa steg.

### <a name="details"></a>Information
På den här sidan lägger du till ytterligare information om problemet. Se till att ta med så mycket information som möjligt, eftersom det hjälper våra tekniker att bättre begränsa problemet. Ta med din önskade kontaktmetod och problemets allvarlighetsgrad så att vi kan kontakta dig på rätt sätt och välj **Nästa: Granska +** skapa för att gå vidare till nästa steg. 

### <a name="review-and-create"></a>Granska och skapa 
Granska informationen i din supportbegäran för att se till att allt är korrekt och representerar problemet på ett effektivt sätt. När du är klar väljer du **Skapa** för att skicka biljetten till vårt team! Du får en e-postbekräftelse när din biljett har tagits emot och vårt team kommer att arbeta för att komma tillbaka till dig så snart som möjligt. Du kan visa status för din biljett i Azure Portal.

## <a name="next-steps"></a>Nästa steg

* [Distribuera en webbapp för att räkna personer](spatial-analysis-web-app.md)
* [Konfigurera åtgärder för rumslig analys](./spatial-analysis-operations.md)
* [Guide för kameraplacering](spatial-analysis-camera-placement.md)
* [Guide för zon- och radplacering](spatial-analysis-zone-line-placement.md)
