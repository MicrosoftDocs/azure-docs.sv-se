---
title: Så här klonar du en Azure IoT Hub
description: Så här klonar du en Azure IoT Hub
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: robinsh
ms.openlocfilehash: 370ea2f16632ae18142f0770742e5a52d3cabae0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92151656"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>Så här klonar du en Azure IoT-hubb till en annan region

I den här artikeln lär du dig hur du kan klona en IoT Hub och innehåller några frågor som du behöver svara innan du börjar. Här följer flera orsaker till att du kanske vill klona en IoT-hubb:
 
* Du flyttar ditt företag från en region till en annan, t. ex. från Europa till Nordamerika (eller vice versa) och du vill att dina resurser och data ska ligga geografiskt nära din nya plats, så du måste flytta hubben.

* Du ställer in en hubb för utveckling respektive produktions miljö.

* Du vill göra en anpassad implementering av hög tillgänglighet för flera hubbar. Mer information finns i [avsnittet så här gör du för att uppnå flera regioner med IoT Hub hög tillgänglighet och haveri beredskap](iot-hub-ha-dr.md#achieve-cross-region-ha).

* Du vill öka antalet [partitioner](iot-hub-scaling.md#partitions) som har kon figurer ATS för hubben. Detta anges när du först skapar din hubb och inte kan ändras. Du kan använda informationen i den här artikeln för att klona din hubb och när klonen skapas, öka antalet partitioner.

Om du vill klona en hubb behöver du en prenumeration med administrativ åtkomst till den ursprungliga hubben. Du kan ställa in den nya hubben i en ny resurs grupp och region, i samma prenumeration som den ursprungliga hubben eller till och med i en ny prenumeration. Du kan bara använda samma namn eftersom hubbens namn måste vara globalt unikt.

> [!NOTE]
> För närvarande finns det ingen funktion tillgänglig för kloning av en IoT Hub automatiskt. Den är i första hand en manuell process och är därmed ganska fel känslig. Komplexiteten vid kloning av en hubb är direkt proportionell mot hubbens komplexitet. Till exempel är det ganska enkelt att klona en IoT-hubb utan meddelanderoutning. Om du lägger till meddelanderoutning som bara en komplexitet blir det mer komplicerat att klona hubben. Om du också flyttar resurserna som används för routning av slut punkter, är det en annan ordning för magniture mer komplicerad. 

## <a name="things-to-consider"></a>Saker att tänka på

Det finns flera saker att tänka på innan du klonar en IoT-hubb.

* Se till att alla funktioner som är tillgängliga på den ursprungliga platsen också är tillgängliga på den nya platsen. Vissa tjänster är i för hands version och alla funktioner är inte tillgängliga överallt.

* Ta inte bort de ursprungliga resurserna innan du skapar och verifierar den klonade versionen. När du tar bort en hubb är den borta för alltid och det finns inget sätt att återställa det för att kontrol lera inställningarna eller data för att se till att hubben replikeras korrekt.

* Många resurser kräver globalt unika namn, så du måste använda olika namn för klonade versioner. Du bör också använda ett annat namn på resurs gruppen som den klonade hubben tillhör. 

* Data för den ursprungliga IoT Hub har inte migrerats. Detta omfattar telemetri-meddelanden, C2D-kommandon (Cloud-to-Device) och projektrelaterad information, till exempel scheman och historik. Mått och loggnings resultat migreras inte heller. 

* För att data eller meddelanden ska dirigeras till Azure Storage kan du lämna data i det ursprungliga lagrings kontot, överföra dessa data till ett nytt lagrings konto i den nya regionen eller lämna gamla data på plats och skapa ett nytt lagrings konto på den nya platsen för nya data. Mer information om hur du flyttar data i Blob Storage finns i [Kom igång med AZCopy](../storage/common/storage-use-azcopy-v10.md).

* Data för Event Hubs och för Service Bus ämnen och köer kan inte migreras. Detta är tidpunkts data och lagras inte när meddelandena har bearbetats.

* Du måste schemalägga nedtid för migreringen. Det tar tid att klona enheterna till den nya hubben. Om du använder metoden import/export har benchmark-testning visat att det kan ta cirka två timmar att flytta 500 000 enheter och fyra timmar för att flytta en miljon enheter. 

* Du kan kopiera enheterna till den nya hubben utan att stänga av eller ändra enheterna. 

    * Om enheterna ursprungligen etablerades med DPS uppdaterar de anslutnings informationen som lagras på varje enhet. 
    
    * Annars måste du använda metoden import/export för att flytta enheterna och enheterna måste ändras för att använda den nya hubben. Du kan till exempel konfigurera enheten så att den använder IoT Hub värd namnet från de dubbla önskade egenskaperna. Enheten tar IoT Hub värdnamn, kopplar bort enheten från den gamla hubben och ansluter den igen till den nya.
    
* Du måste uppdatera alla certifikat som du använder så att du kan använda dem med de nya resurserna. Du har förmodligen även navet som definierats i en DNS-tabell någonstans – du måste uppdatera DNS-informationen.

## <a name="methodology"></a>Metodik

Detta är den allmänna metod som vi rekommenderar för att flytta en IoT-hubb från en region till en annan. För meddelanderoutning, förutsätter detta att resurserna inte flyttas till den nya regionen. Mer information finns i [avsnittet om meddelanderoutning](#how-to-handle-message-routing).

   1. Exportera hubben och dess inställningar till en Resource Manager-mall. 
   
   1. Gör nödvändiga ändringar i mallen, till exempel uppdatera alla förekomster av namnet och platsen för den klonade hubben. För alla resurser i mallen som används för slut punkter för meddelanderoutning uppdaterar du nyckeln i mallen för den resursen.
   
   1. Importera mallen till en ny resurs grupp på den nya platsen. Detta skapar klonen.

   1. Felsök vid behov. 
   
   1. Lägg till något som inte exporterades till mallen. 
   
       Konsument grupper exporteras till exempel inte till mallen. Du måste lägga till konsument grupperna i mallen manuellt eller använda [Azure Portal](https://portal.azure.com) när hubben har skapats. Det finns ett exempel på att lägga till en konsument grupp till en mall i artikeln [Använd en Azure Resource Manager-mall för att konfigurera IoT Hub meddelanderoutning](tutorial-routing-config-message-routing-rm-template.md).
       
   1. Kopiera enheterna från den ursprungliga hubben till klonen. Detta beskrivs i avsnittet [Hantera enheter som är registrerade i IoT Hub](#managing-the-devices-registered-to-the-iot-hub).

## <a name="how-to-handle-message-routing"></a>Så här hanterar du meddelanderoutning

Om din hubb använder [anpassad routning](iot-hub-devguide-messages-read-custom.md), tar exporten av mallen för hubben med Routningsprincipen, men den innehåller inte själva resurserna. Du måste välja om du vill flytta vägvals resurserna till den nya platsen eller lämna dem på plats och fortsätta att använda dem som de är. 

Anta till exempel att du har en hubb i västra USA som dirigerar meddelanden till ett lagrings konto (även i västra USA) och du vill flytta hubben till USA, östra. Du kan flytta hubben och dirigera meddelanden till lagrings kontot i västra USA, eller så kan du flytta hubben och även flytta lagrings kontot. Det kan finnas en liten prestanda träff från routning av meddelanden till slut punkts resurser i en annan region.

Du kan flytta en hubb som använder meddelanderoutning på ett säkert sätt om du inte också flyttar resurserna som används för Dirigerings slut punkterna. 

Om navet använder meddelanderoutning, har du två alternativ. 

1. Flytta resurserna som används för Dirigerings slut punkterna till den nya platsen.

    * Du måste skapa de nya resurserna själv antingen manuellt i [Azure Portal](https://portal.azure.com) eller genom att använda Resource Manager-mallar. 

    * Du måste byta namn på alla resurser när du skapar dem på den nya platsen, eftersom de har globalt unika namn. 
     
    * Du måste uppdatera resurs namnen och resurs nycklarna i den nya hubbens mall innan du skapar den nya hubben. Resurserna bör finnas när den nya hubben skapas.

1. Flytta inte resurserna som används för Dirigerings slut punkterna. Använd dem på plats.

   * I det steg där du redigerar mallen måste du hämta nycklarna för varje vägvals resurs och lägga dem i mallen innan du skapar den nya hubben. 

   * Hubben refererar fortfarande till de ursprungliga Dirigerings resurserna och dirigerar meddelanden till dem enligt konfigurationen.

   * Du får en liten prestanda träff eftersom hubben och slut punkts resurserna inte finns på samma plats.

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>Förbered för att migrera hubben till en annan region

Det här avsnittet innehåller information om hur du migrerar hubben.

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>Hitta den ursprungliga hubben och exportera den till en resurs mal len.

1. Logga in på [Azure-portalen](https://portal.azure.com). 

1. Gå till **resurs grupper** och välj den resurs grupp som innehåller den hubb som du vill flytta. Du kan också gå till **resurser** och hitta hubben på det sättet. Välj hubben.

1. Välj **Exportera mall** i listan över egenskaper och inställningar för hubben. 

   ![Skärm bild som visar kommandot för att exportera mallen för IoT Hub.](./media/iot-hub-how-to-clone/iot-hub-export-template.png)

1. Välj **Ladda ned** för att ladda ned mallen. Spara filen någonstans där du hittar den igen. 

   ![Skärm bild som visar kommandot för att ladda ned mallen för IoT Hub.](./media/iot-hub-how-to-clone/iot-hub-download-template.png)

### <a name="view-the-template"></a>Visa mallen 

1. Gå till mappen Hämtade filer (eller till den mapp som du använde när du exporterade mallen) och hitta zip-filen. Öppna zip-filen och leta upp filen som heter `template.json` . Markera den och välj sedan Ctrl + C för att kopiera mallen. Gå till en annan mapp som inte finns i zip-filen och klistra in filen (Ctrl + V). Nu kan du redigera det.
 
    Följande exempel gäller för ett generiskt nav utan konfiguration av routning. Det är en hubb på S1-nivå (med 1 enhet) som kallas **ContosoTestHub29358** i region, **väst**. Här är den exporterade mallen.

    ``` json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Devices/IotHubs",
                "apiVersion": "2018-04-01",
                "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
                "location": "westus",
                "sku": {
                    "name": "S1",
                    "tier": "Standard",
                    "capacity": 1
                },
                "properties": {
                    "operationsMonitoringProperties": {
                        "events": {
                            "None": "None",
                            "Connections": "None",
                            "DeviceTelemetry": "None",
                            "C2DCommands": "None",
                            "DeviceIdentityOperations": "None",
                            "FileUploadOperations": "None",
                            "Routes": "None"
                        }
                    },
                    "ipFilterRules": [],
                    "eventHubEndpoints": {
                        "events": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        },
                        "operationsMonitoringEvents": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358-operationmonitoring",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        }
                    },
                    "routing": {
                        "endpoints": {
                            "serviceBusQueues": [],
                            "serviceBusTopics": [],
                            "eventHubs": [],
                            "storageContainers": []
                        },
                        "routes": [],
                        "fallbackRoute": {
                            "name": "$fallback",
                            "source": "DeviceMessages",
                            "condition": "true",
                            "endpointNames": [
                                "events"
                            ],
                            "isEnabled": true
                        }
                    },
                    "storageEndpoints": {
                        "$default": {
                            "sasTtlAsIso8601": "PT1H",
                            "connectionString": "",
                            "containerName": ""
                        }
                    },
                    "messagingEndpoints": {
                        "fileNotifications": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "enableFileUploadNotifications": false,
                    "cloudToDevice": {
                        "maxDeliveryCount": 10,
                        "defaultTtlAsIso8601": "PT1H",
                        "feedback": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "features": "None"
                }
            }
        ]
    }
    ```

### <a name="edit-the-template"></a>Redigera mallen 

Du måste göra några ändringar innan du kan använda mallen för att skapa den nya hubben i den nya regionen. Använd [vs Code](https://code.visualstudio.com) eller en text redigerare för att redigera mallen.

#### <a name="edit-the-hub-name-and-location"></a>Redigera hubbens namn och plats

1. Ta bort parameter avsnittet överst – det är mycket enklare att bara använda hubben eftersom vi inte ska ha flera parametrar. 

    ``` json
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
    ```

1. Ändra namnet till att använda det faktiska namnet (nytt) i stället för att hämta det från en parameter (som du tog bort i föregående steg). 

    För den nya hubben använder du namnet på den ursprungliga hubben och den *klonade* strängen för att skapa det nya namnet. Börja med att rensa hubbens namn och plats.
    
    Tidigare version:

    ``` json 
    "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
    "location": "westus",
    ```
    
    Ny version: 

    ``` json 
    "name": "ContosoTestHub29358clone",
    "location": "eastus",
    ```

    Härnäst kommer du att se att värdena för **Path** innehåller det gamla hubbens namn. Ändra dem så att de använder den nya. Detta är Sök vägs värden under **eventHubEndpoints** som kallas **händelser** och **OperationsMonitoringEvents**.

    När du är klar ska avsnittet för Event Hub-slutpunkter se ut så här:

    ``` json
    "eventHubEndpoints": {
        "events": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        },
        "operationsMonitoringEvents": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone-operationmonitoring",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        }
    ```

#### <a name="update-the-keys-for-the-routing-resources-that-are-not-being-moved"></a>Uppdatera nycklarna för de vägvals resurser som inte flyttas

När du exporterar Resource Manager-mallen för en hubb som har konfigurerat routning, ser du att nycklarna för dessa resurser inte finns i den exporterade mallen – deras placering anges av asterisker. Du måste fylla i dem genom att gå till dessa resurser i portalen och hämta nycklarna **innan** du importerar den nya hubbens mall och skapar hubben. 

1. Hämta de nycklar som krävs för någon av vägvals resurserna och Lägg dem i mallen. Du kan hämta nyckel (er) från resursen i [Azure Portal](https://portal.azure.com). 

   Om du till exempel dirigerar meddelanden till en lagrings behållare söker du efter lagrings kontot i portalen. Under avsnittet Inställningar väljer du **åtkomst nycklar** och kopierar sedan en av nycklarna. Så här ser nyckeln ut när du först exporterar mallen:

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. När du har hämtat konto nyckeln för lagrings kontot placerar du den i mallen i-satsen `AccountKey=****` i stället för asteriskerna. 

1. För Service Bus-köer hämtar du den delade åtkomst nyckeln som matchar SharedAccessKeyName. Här är nyckeln och `SharedAccessKeyName` i JSON:

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. Samma gäller för Service Bus ämnen och Event Hub-anslutningar.

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>Skapa de nya Dirigerings resurserna på den nya platsen

Det här avsnittet gäller endast om du flyttar resurserna som används av hubben för routningens slut punkter.

Om du vill flytta vägvals resurserna måste du manuellt konfigurera resurserna på den nya platsen. Du kan skapa vägvals resurser med hjälp av [Azure Portal](https://portal.azure.com)eller genom att exportera Resource Manager-mallen för var och en av de resurser som används av meddelanderoutning, redigera dem och importera dem. När resurserna har kon figurer ATS kan du importera hubbens mall (som innehåller konfigureringen av routningen).

1. Skapa varje resurs som används av operationsföljden. Du kan göra detta manuellt med hjälp av [Azure Portal](https://portal.azure.com)eller skapa resurserna med Resource Manager-mallar. Om du vill använda mallar följer du stegen nedan:

    1. Exportera den till en Resource Manager-mall för varje resurs som används av routningen.
    
    1. Uppdatera resursens namn och plats. 

    1. Uppdatera eventuella kors referenser mellan resurserna. Om du till exempel skapar en mall för ett nytt lagrings konto måste du uppdatera lagrings kontots namn i mallen och andra mallar som refererar till den. I de flesta fall är routing-avsnittet i mallen för hubben den enda andra mallen som refererar till resursen. 

    1. Importera varje mall, som distribuerar varje resurs.

    När resurserna som används av routningen har kon figurer ATS och körs kan du fortsätta.

1. I mallen för IoT Hub ändrar du namnet på varje vägvals resurs till det nya namnet och uppdaterar platsen om det behövs. 

Nu har du en mall som kommer att skapa en ny hubb som ser nästan precis likadan som den gamla hubben, beroende på hur du har valt att hantera routningen.

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>Flytta – skapa den nya hubben i den nya regionen genom att läsa in mallen

Skapa den nya hubben på den nya platsen med hjälp av mallen. Om du har cirkulations resurser som ska flyttas bör resurserna konfigureras på den nya platsen och referenserna i mallen har uppdaterats för att matcha. Om du inte flyttar cirkulations resurserna bör de finnas i mallen med de uppdaterade nycklarna.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Skapa en resurs**. 

1. I sökrutan skriver du i "mall distribution" och väljer RETUR.

1. Välj **mall distribution (distribuera med anpassade mallar)**. Då går du till en skärm för Malldistribution. Välj **Skapa**. Följande skärm visas:

   ![Skärm bild som visar kommandot för att skapa en egen mall](./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png)

1. Välj **Bygg en egen mall i redigeraren**, så att du kan överföra mallen från en fil. 

1. Välj **Läs in fil**. 

   ![Skärm bild som visar kommandot för att ladda upp en mallfil](./media/iot-hub-how-to-clone/iot-hub-upload-file.png)

1. Bläddra efter den nya mall som du redigerade och markera den och välj sedan **Öppna**. Mallen läses in i fönstret Redigera. Välj **Spara**. 

   ![Skärm bild som visar inläsning av mallen](./media/iot-hub-how-to-clone/iot-hub-loading-template.png)

1. Fyll i följande fält.

   **Prenumeration**: Välj den prenumeration som ska användas.

   **Resurs grupp**: skapa en ny resurs grupp på en ny plats. Om du redan har en ny konfiguration kan du välja den i stället för att skapa en ny.

   **Plats**: om du har valt en befintlig resurs grupp fylls detta i så att du matchar resurs gruppens plats. Om du har skapat en ny resurs grupp kommer detta att vara dess plats.

   **Jag accepterar CheckBox**: det här innebär att du samtycker till att betala för resursen/resurserna som du skapar.

1. Välj knappen **köp** .

Portalen verifierar nu mallen och distribuerar den klonade hubben. Om du har konfigurations data för routning kommer den att ingå i den nya hubben, men kommer att peka på resurserna på den tidigare platsen.

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>Hantera enheter som är registrerade i IoT Hub

Nu när du har skapat och kört måste du kopiera alla enheter från den ursprungliga hubben till klonen. 

Det finns flera sätt att göra detta på. Du har antingen använt [DPS (Device Provisioning service)](../iot-dps/about-iot-dps.md)för att etablera enheterna, eller så har du inte gjort det. Om du har gjort det är det inte svårt. Om du inte gjorde det kan det vara mycket komplicerat. 

Om du inte använde DPS för att etablera dina enheter kan du hoppa över nästa avsnitt och börja med [att använda importera/exportera för att flytta enheterna till den nya hubben](#using-import-export-to-move-the-devices-to-the-new-hub).

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>Använda DPS för att etablera om enheterna i den nya hubben

Information om hur du använder DPS för att flytta enheterna till den nya platsen finns i [så här etablerar du om enheter](../iot-dps/how-to-reprovision.md). När du är klar kan du Visa enheterna i [Azure Portal](https://portal.azure.com) och kontrol lera att de finns på den nya platsen.

Gå till den nya hubben med hjälp av [Azure Portal](https://portal.azure.com). Välj hubben och välj **IoT-enheter**. De enheter som har allokerats om till den klonade hubben visas. Du kan också visa egenskaperna för den klonade hubben. 

Om du har implementerat routning, testa och kontrol lera att dina meddelanden dirigeras till resurserna korrekt.

### <a name="committing-the-changes-after-using-dps"></a>Genomför ändringarna efter att du har använt DPS

Den här ändringen har genomförts av DPS-tjänsten.

### <a name="rolling-back-the-changes-after-using-dps"></a>Återställa ändringarna när du har använt DPS. 

Om du vill återställa ändringarna kan du etablera om enheterna från den nya hubben till den gamla.

Du har nu slutfört migreringen av hubben och dess enheter. Du kan hoppa över att [Rensa](#clean-up).

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>Använda Import-Export för att flytta enheterna till den nya hubben

Programmet är riktat mot .NET Core, så du kan köra det på antingen Windows eller Linux. Du kan ladda ned exemplet, hämta anslutnings strängarna, ange flaggor för vilka bitar du vill köra och köra det. Du kan göra detta utan att behöva öppna koden.

### <a name="downloading-the-sample"></a>Hämtar exemplet

1. Använd IoT C#-exempel från den här sidan: [Azure IoT-exempel för C#](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/). Hämta zip-filen och zippa upp den på din dator. 

1. Relevant kod är i./iot-hub/Samples/service/ImportExportDevicesSample. Du behöver inte Visa eller redigera koden för att kunna köra programmet.

1. Ange tre anslutnings strängar och fem alternativ för att köra programmet. Du skickar dessa data i som kommando rads argument eller använder miljövariabler eller använder en kombination av de två. Vi ska skicka alternativen i som kommando rads argument och anslutnings strängarna som miljövariabler. 

   Orsaken till detta beror på att anslutnings strängarna är långa och inte längre, och troligen inte ändras, men du kanske vill ändra alternativen och köra programmet mer än en gång. Om du vill ändra värdet för en miljö variabel måste du stänga kommando fönstret och Visual Studio eller VS Code, beroende på vilket du använder. 

### <a name="options"></a>Alternativ

Här är de fem alternativ som du anger när du kör programmet. Vi kommer att lägga dessa på kommando raden på en minut.

*   **addDevices** (argument 1) – ange värdet sant om du vill lägga till virtuella enheter som har genererats åt dig. De läggs till i käll navet. Ange också **numToAdd** (argument 2) för att ange hur många enheter du vill lägga till. Det maximala antalet enheter som du kan registrera till en hubb är 1 000 000. Syftet med det här alternativet är för testning – du kan generera ett specifikt antal enheter och sedan kopiera dem till en annan hubb.

*   **copyDevices** (argument 3) – ange värdet true för att kopiera enheterna från en hubb till en annan. 

*   **deleteSourceDevices** (argument 4)-– ange värdet true för att ta bort alla enheter som är registrerade i käll navet. Vi rekommenderar att vänta tills du är säker på att alla enheter har överförts innan du kör det. När du har tagit bort enheterna kan du inte få tillbaka dem.

*   **deleteDestDevices** (argument 5) – ange värdet sant om du vill ta bort alla enheter som är registrerade i mål navet (klonen). Du kanske vill göra detta om du vill kopiera enheterna mer än en gång. 

Det grundläggande kommandot är *dotNet-körning* – detta meddelar .net att skapa den lokala CSPROJ-filen och sedan köra den. Du lägger till kommando rads argument i slutet innan du kör det. 

Kommando raden kommer att se ut som i följande exempel:

``` console 
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub, or delete them. 
    // The first argument is true, numToAdd is 50, and the other arguments are false.
    dotnet run true 1000 false false false 

    // Copy the devices you just added to the other hub; don't delete anything.
    // The first argument is false, numToAdd is 0, copy-devices is true, and the delete arguments are both false
    dotnet run false 0 true false false 
```

### <a name="using-environment-variables-for-the-connection-strings"></a>Använda miljövariabler för anslutnings strängarna

1. Om du vill köra exemplet måste du ha anslutnings strängarna till de gamla och nya IoT-hubbarna och till ett lagrings konto som du kan använda för temporära arbetsfiler. Vi kommer att lagra värdena för dessa i miljövariabler.

1. Logga in på [Azure Portal](https://portal.azure.com)för att hämta värden för anslutnings strängen. 

1. Lägg till anslutnings strängarna någonstans som du kan hämta dem, till exempel Anteckningar. Om du kopierar följande kan du klistra in anslutnings strängarna direkt där de finns. Lägg inte till blank steg runt likhets tecknet eller ändra variabelns namn. Du behöver inte heller dubbla citat tecken runt anslutnings strängarna. Om du sätter citat runt lagrings kontots anslutnings sträng fungerar den inte.

   För Windows är detta hur du ställer in miljövariablerna:

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   För Linux är det här du definierar miljövariablerna:

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. För IoT Hub-anslutningssträngarna går du till varje hubb i portalen. Du kan söka i **resurser** för hubben. Om du känner till resurs gruppen kan du gå till **resurs grupper**, välja din resurs grupp och sedan välja hubben i listan över resurser i resurs gruppen. 

1. Välj **principer för delad åtkomst** från inställningarna för hubben och välj sedan **iothubowner** och kopiera en av anslutnings strängarna. Gör samma sak för mål navet. Lägg till dem i lämpliga UPPSÄTTNINGs kommandon.

1. För anslutnings strängen för lagrings kontot letar du reda på lagrings kontot i **resurser** eller under **resurs gruppen** och öppnar det. 
   
1. Under avsnittet Inställningar väljer du **åtkomst nycklar** och kopierar en av anslutnings strängarna. Lägg till anslutnings strängen i text filen för lämpligt SET-kommando. 

Nu har du miljövariabler i en fil med SET-kommandon och du vet vad kommando rads argumenten är. Vi ska köra exemplet.

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>Köra exempel programmet och använda kommando rads argument

1. Öppna ett kommandotolksfönster. Välj Windows och skriv in `command prompt` för att hämta kommando tolks fönstret.

1. Kopiera de kommandon som anger miljövariablerna, en i taget, och klistra in dem i kommando tolkens fönster och välj RETUR. När du är klar skriver du `SET` i kommando tolkens fönster för att se miljövariablerna och deras värden. När du har kopierat dessa till kommando tolks fönstret behöver du inte kopiera dem igen, om du inte öppnar ett nytt kommando tolks fönster.

1. I kommando tolkens fönster ändrar du kataloger tills du är i./ImportExportDevicesSample (där filen ImportExportDevicesSample. CSPROJ finns). Skriv sedan följande och inkludera kommando rads argumenten.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    Kommandot dotNet skapar och kör programmet. Eftersom du skickar in alternativen när du kör programmet kan du ändra värdena för dem varje gång du kör programmet. Du kanske exempelvis vill köra den en gång och skapa nya enheter, sedan köra den igen och kopiera enheterna till en ny hubb, och så vidare. Du kan också utföra alla steg i samma körning, men vi rekommenderar att du inte tar bort några enheter förrän du är säker på att du är färdig med kloningen. Här är ett exempel som skapar 1000 enheter och sedan kopierar dem till den andra hubben.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    När du har kontrollerat att enheterna har kopierats kan du ta bort enheterna från käll navet så här:

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>Köra exempel programmet med Visual Studio

1. Om du vill köra programmet i Visual Studio ändrar du din aktuella katalog till den mapp där filen IoTHubServiceSamples. SLN finns. Kör sedan det här kommandot i kommando tolks fönstret för att öppna lösningen i Visual Studio. Du måste göra detta i samma kommando fönster där du anger miljövariablerna, så att dessa variabler är kända.

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. Högerklicka på projektet *ImportExportDevicesSample* och välj **Ange som start projekt**.    
    
1. Ange variablerna överst i program. cs i mappen ImportExportDevicesSample för de fem alternativen.

   ``` csharp
   // Add randomly created devices to the source hub.
   private static bool addDevices = true;
   //If you ask to add devices, this will be the number added.
   private static int numToAdd = 0; 
   // Copy the devices from the source hub to the destination hub.
   private static bool copyDevices = false;
   // Delete all of the devices from the source hub. (It uses the IoTHubConnectionString).
   private static bool deleteSourceDevices = false;
   // Delete all of the devices from the destination hub. (Uses the DestIotHubConnectionString).
   private static bool deleteDestDevices = false;
   ```

1. Välj F5 för att köra programmet. När körningen är klar kan du visa resultatet.

### <a name="view-the-results"></a>Visa resultatet 

Du kan visa enheterna i [Azure Portal](https://portal.azure.com) och kontrol lera att de finns på den nya platsen.

1. Gå till den nya hubben med hjälp av [Azure Portal](https://portal.azure.com). Välj hubben och välj **IoT-enheter**. Du ser de enheter som du precis kopierade från den gamla hubben till den klonade hubben. Du kan också visa egenskaperna för den klonade hubben. 

1. Sök efter import/export-fel genom att gå till Azure Storage-kontot i [Azure Portal](https://portal.azure.com) och leta i `devicefiles` behållaren för `ImportErrors.log` . Om filen är tom (storleken är 0) fanns det inga fel. Om du försöker importera samma enhet mer än en gång, avvisar den enheten den andra gången och lägger till ett fel meddelande i logg filen.

### <a name="committing-the-changes"></a>Genomför ändringarna 

Nu har du kopierat hubben till den nya platsen och migrerat enheterna till den nya klonen. Nu måste du göra ändringar så att enheterna fungerar med den klonade hubben.

Följande steg måste utföras för att genomföra ändringarna: 

* Uppdatera varje enhet för att ändra IoT Hub värdnamn för att peka IoT Hub värd namnet till den nya hubben. Du bör göra detta med samma metod som du använde när du först etablerade enheten.

* Ändra de program som du har som refererar till den gamla hubben så att den pekar på den nya hubben.

* När du är klar ska den nya hubben vara igång. Den gamla hubben ska inte ha några aktiva enheter och vara i frånkopplat läge. 

### <a name="rolling-back-the-changes"></a>Återställa ändringarna

Om du bestämmer dig för att återställa ändringarna kan du göra följande:

* Uppdatera varje enhet för att ändra IoT Hub värdnamn för att peka IoT Hub hostname för den gamla hubben. Du bör göra detta med samma metod som du använde när du först etablerade enheten.

* Ändra de program som du har som refererar till den nya hubben så att den pekar på den gamla hubben. Om du till exempel använder Azure Analytics kan du behöva konfigurera om [Azure Stream Analytics-inflödet](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).

* Ta bort den nya hubben. 

* Om du har routning av resurser ska konfigurationen på den gamla hubben fortfarande peka på korrekt konfigurering av Routning och bör arbeta med dessa resurser när hubben har startats om.

### <a name="checking-the-results"></a>Kontrollerar resultaten 

Du kan kontrol lera resultaten genom att ändra din IoT-lösning så att den pekar mot hubben på den nya platsen och köra den. Du kan med andra ord utföra samma åtgärder med det nya hubb som du utförde med föregående hubb och se till att de fungerar som de ska. 

Om du har implementerat routning, testa och kontrol lera att dina meddelanden dirigeras till resurserna korrekt.

## <a name="clean-up"></a>Rensa

Rensa inte upp förrän du är säker på att den nya hubben är igång och att enheterna fungerar som de ska. Se också till att testa routningen om du använder funktionen. När du är klar kan du rensa de gamla resurserna genom att utföra följande steg:

* Ta bort den gamla hubben om du inte redan gjort det. Detta tar bort alla aktiva enheter från hubben.

* Om du har vägvals resurser som du har flyttat till den nya platsen kan du ta bort de gamla vägvals resurserna.

## <a name="next-steps"></a>Nästa steg

Du har klonat en IoT-hubb till en ny hubb i en ny region, komplett med enheterna. Mer information om hur du utför Mass åtgärder mot identitets registret i en IoT Hub finns i [Importera och exportera IoT Hub enhets identiteter i bulk](iot-hub-bulk-identity-mgmt.md).

Mer information om IoT Hub och utveckling för hubben finns i följande artiklar.

* [Guide för IoT Hub utvecklare](iot-hub-devguide.md)

* [Självstudie för IoT Hub routning](tutorial-routing.md)

* [Översikt över IoT Hub enhets hantering](iot-hub-device-management-overview.md)

* Om du vill distribuera exempel programmet går du till [.net Core program distribution](/dotnet/core/deploying/index).