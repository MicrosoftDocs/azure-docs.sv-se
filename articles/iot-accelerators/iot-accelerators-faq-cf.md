---
title: Vanliga frågor och svar om lösningen Ansluten fabrik – Azure | Microsoft Docs
description: Den här artikeln besvarar vanliga frågor om lösningsacceleratorn Ansluten fabrik. Den innehåller länkar till GitHub-lagringsplatsen.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 038403743caf13087655066f4acbec4dcee598c7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874215"
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>Vanliga frågor och svar om lösningsacceleratorn Ansluten fabrik

Se även allmänna vanliga frågor och [svar för](iot-accelerators-faq.md) IoT-lösningsacceleratorer.

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>Var hittar jag källkoden för lösningsacceleratorn?

Källkoden lagras på följande GitHub-lagringsplats:

* [Lösningsacceleratorn Ansluten fabrik](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>Vad är OPC UA?

OPC Unified Architecture (UA), som släpptes 2008, är en plattformsoberoende, tjänstorienterad interoperabilitetsstandard. OPC UA används av olika industriella system och enheter, till exempel branschdatorer, PLC och sensorer. OPC UA integrerar funktionerna i de klassiska OPC-specifikationerna i ett utökningsbart ramverk med inbyggd säkerhet. Det är en standard som drivs av OPC Foundation. [OPC Foundation är](https://opcfoundation.org/) en ideell organisation med fler än 440 medlemmar. Målet med organisationen är att använda OPC-specifikationer för att underlätta samverkan mellan flera leverantörer, flera plattformar, säker och tillförlitlig samverkan genom:

* Infrastruktur
* Specifikationer
* Teknik
* Processer

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Varför valde Microsoft OPC UA för lösningsacceleratorn Ansluten fabrik?

Microsoft valde OPC UA eftersom det är en öppen, icke-upphovsrättsskyddad, plattformsoberoende, branschledande och beprövad standard. Det är ett krav för Referensarkitekturlösningar för Industrie 4.0 (RAMI4.0) som säkerställer samverkan mellan en bred uppsättning tillverkningsprocesser och utrustning. Microsoft ser en efterfrågan från sina kunder på att skapa Industrie 4.0-lösningar. Stöd för OPC UA bidrar till att minska barriären för kunder för att uppnå sina mål och ge dem ett omedelbart affärsvärde.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Hur gör jag för att lägga till en offentlig IP-adress till den virtuella simuleringsdatorn?

Du har två alternativ för att lägga till IP-adressen:

* Använd PowerShell-skriptet `Simulation/Factory/Add-SimulationPublicIp.ps1` på [lagringsplatsen](https://github.com/Azure/azure-iot-connected-factory). Ange distributionsnamnet som en parameter. För en lokal distribution använder du `<your username>ConnFactoryLocal` . Skriptet skriver ut IP-adressen för den virtuella datorn.

* I Azure Portal du resursgruppen för distributionen. Förutom en lokal distribution har resursgruppen det namn som du angav som lösnings- eller distributionsnamn. För en lokal distribution med hjälp av build-skriptet är namnet på resursgruppen `<your username>ConnFactoryLocal` . Lägg nu till en **ny offentlig IP-adressresurs** i resursgruppen.

> [!NOTE]
> I båda fallen måste du installera de senaste korrigeringarna genom att följa anvisningarna på [Ubuntu-webbplatsen](https://wiki.ubuntu.com/Security/Upgrades). Håll installationen uppdaterad så länge den virtuella datorn är tillgänglig via en offentlig IP-adress.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Hur gör jag för att den offentliga IP-adressen till den virtuella simuleringsdatorn?

Det finns två alternativ för att ta bort IP-adressen:

* Använd PowerShell-skriptsimulering/Factory/Remove-SimulationPublicIp.ps1 för [lagringsplatsen](https://github.com/Azure/azure-iot-connected-factory). Ange distributionsnamnet som en parameter. För en lokal distribution använder du `<your username>ConnFactoryLocal` . Skriptet skriver ut IP-adressen för den virtuella datorn.

* I Azure Portal du resursgruppen för distributionen. Förutom en lokal distribution har resursgruppen det namn som du angav som lösnings- eller distributionsnamn. För en lokal distribution med hjälp av build-skriptet är namnet på resursgruppen `<your username>ConnFactoryLocal` . Ta nu bort **resursen Offentlig IP-adress** från resursgruppen.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Hur gör jag för att logga in på den virtuella simuleringsdatorn?

Inloggning till den virtuella simuleringsdatorn stöds endast om du har distribuerat din lösning med hjälp av PowerShell-skriptet `build.ps1` på [lagringsplatsen](https://github.com/Azure/azure-iot-connected-factory).

Om du har distribuerat lösningen från www.azureiotsolutions.com kan du inte logga in på den virtuella datorn. Du kan inte logga in eftersom lösenordet genereras slumpmässigt och du inte kan återställa det.

1. Lägg till en offentlig IP-adress till den virtuella datorn. Se [Hur gör jag för att lägga till en offentlig IP-adress till den virtuella simuleringsdatorn?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Skapa en SSH-session till den virtuella datorn med hjälp av den virtuella datorns IP-adress.
1. Användarnamnet som ska användas är: `docker` .
1. Lösenordet som ska användas beror på vilken version du använde för att distribuera:
    * För lösningar som distribueras med build.ps1-skriptet före den 1 juni 2017 är lösenordet: `Passw0rd` .
    * För lösningar som distribueras med build.ps1-skriptet efter den 1 juni 2017 hittar du lösenordet i `<name of your deployment>.config.user` filen. Lösenordet lagras i inställningen **VmAdminPassword.** Lösenordet genereras slumpmässigt vid distributionen såvida du inte anger det med hjälp av `build.ps1` skriptparametern `-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Hur gör jag för att stoppa och starta alla Docker-processer på den virtuella simuleringsdatorn?

1. Logga in på den virtuella simuleringsdatorn. Se [Hur gör jag för att logga in på den virtuella simuleringsdatorn?](#how-do-i-sign-in-to-the-simulation-vm)
1. Kontrollera vilka containrar som är aktiva genom att köra: `docker ps` .
1. Om du vill stoppa alla simuleringscontainrar kör du: `./stopsimulation` .
1. Så här startar du alla simuleringscontainrar:
    * Exportera en gränssnittsvariabel med **namnet IOTHUB_CONNECTIONSTRING**. Använd värdet för inställningen **IotHubOwnerConnectionString** i `<name of your deployment>.config.user` filen. Exempel:

        ```sh
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Kör `./startsimulation`.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Hur gör jag för att du uppdatera simuleringen på den virtuella datorn?

Om du har gjort några ändringar i simuleringen kan du använda PowerShell-skriptet på `build.ps1` [lagringsplatsen](https://github.com/Azure/azure-iot-connected-factory) med `updatedimulation` kommandot . Det här skriptet skapar alla simuleringskomponenter, stoppar simuleringen i den virtuella datorn, laddar upp, installerar och startar dem.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Hur gör jag för att du reda på anslutningssträngen för den IoT-hubb som används av min lösning?

Om du har distribuerat din lösning med skriptet i lagringsplatsen är anslutningssträngen värdet `build.ps1` **för IotHubOwnerConnectionString** i [](https://github.com/Azure/azure-iot-connected-factory) `<name of your deployment>.config.user` filen.

Du kan också hitta anslutningssträngen med hjälp av Azure Portal. I IoT Hub resurs i resursgruppen för distributionen letar du upp inställningarna för anslutningssträngen.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Vilka IoT Hub-enheter använder simuleringen Ansluten fabrik?

Simuleringen registrerar följande enheter:

* proxy.beijing.corp.contoso
* proxy.capeost.corp.contoso
* proxy.mumbai.corp.contoso
* proxy.bind0.corp.contoso
* proxy.rio.corp.contoso
* proxy.seattle.corp.contoso
* publisher.beijing.corp.contoso
* publisher.capecap.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.publisher0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Med [verktyget DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/) eller [IoT-tillägget](https://github.com/Azure/azure-iot-cli-extension) för Azure CLI kan du kontrollera vilka enheter som är registrerade med den IoT-hubb som din lösning använder. Om du vill använda Enhetsutforskaren behöver du anslutningssträngen för IoT-hubben i distributionen. Om du vill använda IoT-tillägget för Azure CLI behöver du IoT Hub namn.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Hur kan jag hämta loggdata från simuleringskomponenterna?

Alla komponenter i simuleringslogginformationen i loggfilerna. Dessa filer finns på den virtuella datorn i mappen `home/docker/Logs` . Om du vill hämta loggarna kan du använda PowerShell-skriptet `Simulation/Factory/Get-SimulationLogs.ps1` på [lagringsplatsen](https://github.com/Azure/azure-iot-connected-factory).

Det här skriptet måste logga in på den virtuella datorn. Du kan behöva ange autentiseringsuppgifter för inloggningen. Se [Hur gör jag för att logga in på den virtuella simuleringsdatorn?](#how-do-i-sign-in-to-the-simulation-vm) för att hitta autentiseringsuppgifterna.

Skriptet lägger till/tar bort en offentlig IP-adress till den virtuella datorn, om den ännu inte har en och tar bort den. Skriptet placerar alla loggfiler i ett arkiv och laddar ned arkivet till utvecklingsarbetsstationen.

Du kan också logga in på den virtuella datorn via SSH och granska loggfilerna vid körning.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Hur kan jag kontrollera om simuleringen skickar data till molnet?

Med kommandot [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer) eller [Azure IoT CLI-tillägget monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) kan du granska de data som skickas till IoT Hub från vissa enheter. Om du vill använda de här verktygen måste du känna till anslutningssträngen för IoT-hubben i distributionen. Se [Hur gör jag för att ta reda på anslutningssträngen för den IoT-hubb som används av min lösning?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Granska de data som skickas av någon av utgivarenheterna:

* publisher.beijing.corp.contoso
* publisher.capecap.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.publisher0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Om inga data skickas till IoT Hub finns det ett problem med simuleringen. Som ett första analyssteg bör du analysera loggfilerna för simuleringskomponenterna. Se [Hur kan jag hämta loggdata från simuleringskomponenterna?](#how-can-i-get-log-data-from-the-simulation-components) Försök sedan att stoppa och starta simuleringen. Om inga data fortfarande skickas uppdaterar du simuleringen helt. Se [Hur gör jag för att uppdaterar simuleringen på den virtuella datorn?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Hur gör jag för att du aktivera en interaktiv karta i lösningen Ansluten fabrik?

Om du vill aktivera en interaktiv karta i lösningen Ansluten fabrik måste du ha ett Azure Maps konto.

När du distribuerar [www.azureiotsolutions.com](https://www.azureiotsolutions.com)lägger distributionsprocessen till ett Azure Maps-konto till resursgruppen som innehåller lösningsacceleratortjänsterna.

När du distribuerar med hjälp av skriptet på GitHub-lagringsplatsen Ansluten fabrik anger du miljövariabeln i build-fönstret till nyckeln för `build.ps1` `$env:MapApiQueryKey` ditt [Azure Maps konto](../azure-maps/how-to-manage-account-keys.md). Den interaktiva kartan aktiveras sedan automatiskt.

Du kan också lägga till en Azure Maps-kontonyckel i lösningsacceleratorn efter distributionen. Gå till Azure Portal åtkomst till App Service i din Connected Factory-distribution. Gå till **Programinställningar**, där du hittar avsnittet **Programinställningar**. Ange **MapApiQueryKey till** nyckeln [för ditt Azure Maps konto](../azure-maps/how-to-manage-account-keys.md). Spara inställningarna och gå sedan till **Översikt och** starta om App Service.

### <a name="how-do-i-create-an-azure-maps-account"></a>Hur gör jag för att skapa ett Azure Maps konto?

Se Hantera [ditt Azure Maps och nycklar.](../azure-maps/how-to-manage-account-keys.md)

### <a name="how-to-obtain-your-azure-maps-account-key"></a>Så här hämtar du din Azure Maps-kontonyckel

Se Hantera [ditt Azure Maps och nycklar.](../azure-maps/how-to-manage-account-keys.md)

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Hur aktiverar jag den interaktiva kartan vid felsökning lokalt?

Om du vill aktivera den interaktiva kartan när du felsöker lokalt anger du värdet för inställningen i filerna och i roten för distributionen till värdet för `MapApiQueryKey` `local.user.config` `<yourdeploymentname>.user.config` **QueryKey** som du kopierade tidigare.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Hur gör jag för att du använda en annan bild på startsidan för min instrumentpanel?

Om du vill ändra den statiska bild som visas på io-startsidan för instrumentpanelen ersätter du bilden `WebApp\Content\img\world.jpg` . Bygg sedan om och distribuera om WebApp.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Hur gör jag för att använda icke OPC UA-enheter med ansluten fabrik?

Så här skickar du telemetridata från icke OPC UA-enheter till Ansluten fabrik:

1. [Konfigurera en ny station i den anslutna fabrikstopologin](iot-accelerators-connected-factory-configure.md) i `ContosoTopologyDescription.json` filen .

1. Mata in telemetridata i Connected Factory-kompatibelt JSON-format:

    ```json
    [
      {
        "ApplicationUri": "<the_value_of_OpcUri_of_your_station",
        "DisplayName": "<name_of_the_datapoint>",
        "NodeId": "value_of_NodeId_of_your_datapoint_in_the_station",
        "Value": {
          "Value": <datapoint_value>,
          "SourceTimestamp": "<timestamp>"
        }
      }
    ]
    ```

1. Formatet för `<timestamp>` är: `2017-12-08T19:24:51.886753Z`

1. Starta om connected factory-App Service.

### <a name="next-steps"></a>Nästa steg

Du kan även utforska andra funktioner och möjligheter i IoT-lösningsacceleratorerna:

* [Distribuera lösningsacceleratorn Ansluten fabrik](quickstart-connected-factory-deploy.md)
* [IoT-säkerhet från grunden](../iot-fundamentals/iot-security-ground-up.md)