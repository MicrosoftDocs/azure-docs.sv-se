---
title: Vanliga frågor och svar om lösningen för ansluten fabrik – Azure | Microsoft Docs
description: I den här artikeln besvaras vanliga frågor om den anslutna fabriks lösnings acceleratorn. Den innehåller länkar till GitHub-lagringsplatsen.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: c6a695030d399cb1609c63ef5652419a9bef1bbf
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646219"
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>Vanliga frågor och svar om ansluten fabriks lösnings Accelerator

Se även [vanliga frågor och svar](iot-accelerators-faq.md) för IoT Solution-acceleratorer.

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>Var hittar jag käll koden för Solution Accelerator?

Käll koden lagras i följande GitHub-lagringsplats:

* [Lösnings Accelerator för ansluten fabrik](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>Vad är OPC UA?

OPC Unified Architecture (UA), som lanserades i 2008, är en plattforms oberoende, service-orienterad interoperabilitet. OPC UA används av olika industriella system och enheter som bransch datorer, PLCs och sensorer. OPC UA integrerar funktionerna i de klassiska OPC-specifikationerna i ett utöknings Bart ramverk med inbyggd säkerhet. Det är en standard som drivs av OPC Foundation. [OPC Foundation](https://opcfoundation.org/) är en ideell organisation med fler än 440 medlemmar. Målet med organisationen är att använda OPC-specifikationer för att under lätta för flera leverantörer, multi-platform, säkra och tillförlitliga interoperabilitet genom:

* Infrastruktur
* Specifikationer
* Teknik
* Processer

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Varför valde Microsoft OPC UA för den anslutna fabriks lösnings acceleratorn?

Microsoft valde OPC UA eftersom det är en öppen, icke-patentskyddad, plattforms oberoende, bransch erkänd och beprövad standard. Det är ett krav för industri 4,0 (RAMI 4.0)-referens arkitektur lösningar som garanterar samverkan mellan en bred uppsättning tillverknings processer och utrustning. Microsoft ser efter frågan från sina kunder att bygga industri 4,0-lösningar. Stöd för OPC UA bidrar till att minska barriären för kunderna att uppnå sina mål och ger dem omedelbara affärs värden.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Hur gör jag för att lägger du till en offentlig IP-adress till den virtuella simulerings datorn?

Det finns två alternativ för att lägga till IP-adressen:

* Använd PowerShell-skriptet `Simulation/Factory/Add-SimulationPublicIp.ps1` i [lagrings platsen](https://github.com/Azure/azure-iot-connected-factory). Skicka in distributions namnet som en parameter. Använd för en lokal distribution `<your username>ConnFactoryLocal` . Skriptet skriver ut IP-adressen för den virtuella datorn.

* Leta upp resurs gruppen för din distribution i Azure Portal. Förutom för en lokal distribution har resurs gruppen det namn som du har angett som lösning eller distributions namn. För en lokal distribution med build-skriptet är namnet på resurs gruppen `<your username>ConnFactoryLocal` . Lägg nu till en ny **offentlig IP-** adressresurs i resurs gruppen.

> [!NOTE]
> I båda fallen bör du se till att du installerar de senaste korrigeringarna genom att följa anvisningarna på [Ubuntu-webbplatsen](https://wiki.ubuntu.com/Security/Upgrades). Håll installationen uppdaterad så länge den virtuella datorn är tillgänglig via en offentlig IP-adress.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Hur gör jag för att bort den offentliga IP-adressen till den virtuella simulerings datorn?

Det finns två alternativ för att ta bort IP-adressen:

* Använd PowerShell-skript simulering/fabrik/Remove-SimulationPublicIp.ps1 av [lagrings platsen](https://github.com/Azure/azure-iot-connected-factory). Skicka in distributions namnet som en parameter. Använd för en lokal distribution `<your username>ConnFactoryLocal` . Skriptet skriver ut IP-adressen för den virtuella datorn.

* Leta upp resurs gruppen för din distribution i Azure Portal. Förutom för en lokal distribution har resurs gruppen det namn som du har angett som lösning eller distributions namn. För en lokal distribution med build-skriptet är namnet på resurs gruppen `<your username>ConnFactoryLocal` . Ta nu bort den **offentliga IP** -adressresursen från resurs gruppen.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Hur gör jag för att loggar du in på den virtuella simuleringen?

Det finns bara stöd för att logga in på den virtuella dator simuleringen om du har distribuerat din lösning med PowerShell-skriptet `build.ps1` i [lagrings platsen](https://github.com/Azure/azure-iot-connected-factory).

Om du har distribuerat lösningen från www.azureiotsolutions.com kan du inte logga in på den virtuella datorn. Du kan inte logga in eftersom lösen ordet genereras slumpmässigt och du inte kan återställa det.

1. Lägg till en offentlig IP-adress till den virtuella datorn. Se [Hur gör jag för att lägga till en offentlig IP-adress till den virtuella simulerings datorn?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Skapa en SSH-session med den virtuella datorn med hjälp av IP-adressen för den virtuella datorn.
1. Användar namnet som ska användas är: `docker` .
1. Vilket lösen ord som ska användas beror på vilken version du använde för att distribuera:
    * För lösningar som distribueras med hjälp av build.ps1 skriptet före 1 juni 2017 är lösen ordet: `Passw0rd` .
    * För lösningar som distribueras med hjälp av build.ps1 skriptet efter 1 juni 2017 kan du hitta lösen ordet i `<name of your deployment>.config.user` filen. Lösen ordet lagras i **VmAdminPassword** -inställningen. Lösen ordet genereras slumpmässigt vid distributions tiden om du inte anger det med hjälp av `build.ps1` skript parametern `-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Hur gör jag för att stoppa och starta alla Docker-processer i den virtuella simuleringen av simuleringen?

1. Logga in på den virtuella simulerings datorn. Finns [Hur gör jag för att logga in på den virtuella simulerings datorn?](#how-do-i-sign-in-to-the-simulation-vm)
1. Du kan kontrol lera vilka behållare som är aktiva genom att köra: `docker ps` .
1. Stoppa alla simulerings behållare genom att köra: `./stopsimulation` .
1. Starta alla behållare för simulering:
    * Exportera en Shell-variabel med namnet **IOTHUB_CONNECTIONSTRING**. Använd värdet för **IotHubOwnerConnectionString** -inställningen i `<name of your deployment>.config.user` filen. Exempel:

        ```sh
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Kör `./startsimulation`.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Hur gör jag för att uppdaterar du simuleringen på den virtuella datorn?

Om du har gjort ändringar i simuleringen kan du använda PowerShell-skriptet `build.ps1` i [lagrings platsen](https://github.com/Azure/azure-iot-connected-factory) med hjälp av `updatedimulation` kommandot. Det här skriptet skapar alla simulerings komponenter, stoppar simuleringen på den virtuella datorn, laddar upp, installerar och startar dem.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Hur gör jag för att ta reda på anslutnings strängen för IoT Hub som används av min lösning?

Om du har distribuerat din lösning med `build.ps1` skriptet i [lagrings platsen](https://github.com/Azure/azure-iot-connected-factory)är anslutnings strängen värdet för **IotHubOwnerConnectionString** i `<name of your deployment>.config.user` filen.

Du kan också hitta anslutnings strängen med hjälp av Azure Portal. Leta upp inställningarna för anslutnings strängen i den IoT Hub resursen i resurs gruppen för din distribution.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Vilka IoT Hub enheter använder den anslutna fabriks simuleringen?

Simuleringen registrerar följande enheter:

* proxy. Beijing. Corp. contoso
* proxy. Capetown. Corp. contoso
* proxy. Mumbai. Corp. contoso
* proxy. munich0. Corp. contoso
* proxy. Rio. Corp. contoso
* proxy. Seattle. Corp. contoso
* Publisher. Beijing. Corp. contoso
* Publisher. Capetown. Corp. contoso
* Publisher. Mumbai. Corp. contoso
* Publisher. munich0. Corp. contoso
* Publisher. Rio. Corp. contoso
* Publisher. Seattle. Corp. contoso

Med hjälp av [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/) eller [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension) -verktyget kan du kontrol lera vilka enheter som är registrerade med IoT Hub som din lösning använder. Om du vill använda Device Explorer behöver du anslutnings strängen för IoT-hubben i distributionen. Om du vill använda IoT-tillägget för Azure CLI behöver du ditt IoT Hub namn.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Hur kan jag hämta logg data från simulerings komponenterna?

Alla komponenter i simulerings logg information i loggfiler. De här filerna finns i den virtuella datorn i mappen `home/docker/Logs` . Du kan hämta loggarna genom att använda PowerShell-skriptet `Simulation/Factory/Get-SimulationLogs.ps1` i [lagrings platsen](https://github.com/Azure/azure-iot-connected-factory).

Det här skriptet måste logga in på den virtuella datorn. Du kan behöva ange autentiseringsuppgifter för inloggningen. Finns [Hur gör jag för att logga in på den virtuella simulerings datorn?](#how-do-i-sign-in-to-the-simulation-vm) för att hitta autentiseringsuppgifterna.

Skriptet lägger till/tar bort en offentlig IP-adress till den virtuella datorn, om den inte redan har en och tar bort den. Skriptet placerar alla loggfiler i ett arkiv och laddar ned arkivet till din utvecklings arbets Station.

Du kan också logga in på den virtuella datorn via SSH och kontrol lera loggfilerna vid körning.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Hur kan jag kontrol lera om simuleringen skickar data till molnet?

Med [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer) eller [Azure IoT CLI-tillägget Monitor-Events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) kan du granska data som skickas till IoT Hub från vissa enheter. Om du vill använda dessa verktyg måste du känna till anslutnings strängen för IoT-hubben i distributionen. Se [Hur gör jag för att ta reda på anslutnings strängen för IoT Hub som används av min lösning?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Granska data som skickats av en av utgivar enheterna:

* Publisher. Beijing. Corp. contoso
* Publisher. Capetown. Corp. contoso
* Publisher. Mumbai. Corp. contoso
* Publisher. munich0. Corp. contoso
* Publisher. Rio. Corp. contoso
* Publisher. Seattle. Corp. contoso

Om du inte ser några data som skickats till IoT Hub, är det ett problem med simuleringen. Som ett första analys steg bör du analysera loggfilerna för simulerings komponenterna. Se [Hur kan jag hämta logg data från simulerings komponenterna?](#how-can-i-get-log-data-from-the-simulation-components) Försök sedan att stoppa och starta simuleringen och om det fortfarande inte finns några data som skickas uppdaterar du simuleringen helt. Se [Hur gör jag för att uppdatera simuleringen på den virtuella datorn?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Hur gör jag för att aktivera en interaktiv karta i min anslutna fabriks lösning?

Om du vill aktivera en interaktiv karta i din anslutna fabriks lösning måste du ha ett Azure Maps-konto.

När du distribuerar från [www.azureiotsolutions.com](https://www.azureiotsolutions.com)lägger distributions processen till ett Azure Maps-konto i resurs gruppen som innehåller Solution Accelerator-tjänsterna.

När du distribuerar med hjälp av `build.ps1` skriptet i den anslutna fabrikens GitHub-lagringsplatsen anger du miljövariabeln `$env:MapApiQueryKey` i build-fönstret till [nyckeln för ditt Azure Maps-konto](../azure-maps/how-to-manage-account-keys.md). Den interaktiva kartan aktive ras sedan automatiskt.

Du kan också lägga till en Azure Maps konto nyckel till din lösnings Accelerator efter distributionen. Navigera till Azure Portal och få åtkomst till App Service resursen i din anslutna fabriks distribution. Navigera till **program inställningar**, där du hittar ett avsnitts **program inställningar**. Ange **MapApiQueryKey** till [nyckeln för ditt Azure Maps-konto](../azure-maps/how-to-manage-account-keys.md). Spara inställningarna och gå sedan till **Översikt** och starta om App Service.

### <a name="how-do-i-create-an-azure-maps-account"></a>Hur gör jag för att skapar du ett Azure Maps konto?

Se [hur du hanterar ditt Azure Maps konto och nycklar](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-to-obtain-your-azure-maps-account-key"></a>Så här hämtar du din Azure Maps konto nyckel

Se [hur du hanterar ditt Azure Maps konto och nycklar](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Hur aktiverar den interaktiva kartan vid fel sökning lokalt?

Om du vill aktivera den interaktiva kartan medan du felsöker lokalt, ställer du in värdet för inställningen `MapApiQueryKey` i filerna `local.user.config` och `<yourdeploymentname>.user.config` i roten för distributionen till värdet för **QueryKey** som du kopierade tidigare.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Hur gör jag för att använda en annan bild på Start sidan för min instrument panel?

Om du vill ändra den statiska avbildningen som visas i/o start sidan på instrument panelen ersätter du bilden `WebApp\Content\img\world.jpg` . Bygg sedan om och distribuera om WebApp.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Hur gör jag för att använda icke-OPC UA-enheter med ansluten fabrik?

Skicka telemetridata från icke-OPC UA-enheter till ansluten fabrik:

1. [Konfigurera en ny station i den anslutna fabriks miljön](iot-accelerators-connected-factory-configure.md) i `ContosoTopologyDescription.json` filen.

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

1. Formatet `<timestamp>` är: `2017-12-08T19:24:51.886753Z`

1. Starta om den anslutna fabriken App Service.

### <a name="next-steps"></a>Nästa steg

Du kan även utforska andra funktioner och möjligheter i IoT-lösningsacceleratorerna:

* [Distribuera lösnings Accelerator för ansluten fabrik](quickstart-connected-factory-deploy.md)
* [IoT-säkerhet från grunden](../iot-fundamentals/iot-security-ground-up.md)