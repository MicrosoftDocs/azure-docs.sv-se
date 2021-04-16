---
title: Datavisualisering i realtid för dina IoT Hub-data i en webbapp
description: Använd en webbapp för att visualisera temperatur- och luftfuktighetsdata som samlas in från en sensor och skickas till din IoT-hubb.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: 4f2f0678b421ac6965b2848cc25564b4e95c7c6b
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567072"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Visualisera sensordata i realtid från din Azure IoT-hubb i en webbapp

![Diagram från end-to-end](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

I den här artikeln får du lära dig hur du visualiserar sensordata i realtid som din IoT-hubb tar emot med en node.js-webbapp som körs på din lokala dator. När du har kört webbappen lokalt kan du följa stegen för att lagra webbappen i Azure App Service. Om du vill försöka visualisera data i din IoT-hubb med hjälp av Power BI kan du se Använda Power BI för att visualisera [sensordata](iot-hub-live-data-visualization-in-power-bi.md)i realtid från Azure IoT Hub .

## <a name="prerequisites"></a>Förutsättningar

* Slutför [självstudien om Raspberry Pi-onlinesimulatorn](iot-hub-raspberry-pi-web-simulator-get-started.md) eller någon av självstudierna för enheten. Du kan till exempel gå till [Raspberry Pi med node.js](iot-hub-raspberry-pi-kit-node-get-started.md) eller till någon av snabbstarterna för att skicka [telemetri.](quickstart-send-telemetry-dotnet.md) De här artiklarna omfattar följande krav:

  * En aktiv Azure-prenumeration
  * En IoT-hubb under din prenumeration
  * Ett klientprogram som skickar meddelanden till din IoT-hubb

* [Ladda ned Git](https://www.git-scm.com/downloads)

* Stegen i den här artikeln förutsätter en Windows-utvecklingsdator. Du kan dock enkelt utföra de här stegen på ett Linux-system i önskat gränssnitt.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Lägga till en konsumentgrupp i din IoT-hubb

[Konsumentgrupper](../event-hubs/event-hubs-features.md#event-consumers) tillhandahåller oberoende vyer i händelseströmmen som gör att appar och Azure-tjänster kan använda data oberoende av varandra från samma händelsehubbslutpunkt. I det här avsnittet lägger du till en konsumentgrupp i din IoT-hubbs inbyggda slutpunkt som webbappen ska använda för att läsa data från.

Kör följande kommando för att lägga till en konsumentgrupp i den inbyggda slutpunkten för din IoT-hubb:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Anteckna det namn du väljer. Du behöver det senare i den här självstudien.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Hämta en tjänstanslutningssträng för din IoT-hubb

IoT-hubbar skapas med flera standardåtkomstprinciper. En sådan princip är **tjänstprincipen,** som ger tillräcklig behörighet för att en tjänst ska kunna läsa och skriva IoT-hubbens slutpunkter. Kör följande kommando för att hämta en anslutningssträng för din IoT-hubb som följer tjänstprincipen:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

Anslutningssträngen bör se ut ungefär så här:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Anteckna tjänstanslutningssträngen. Du behöver den senare i den här självstudien.

## <a name="download-the-web-app-from-github"></a>Ladda ned webbappen från GitHub

Öppna ett kommandofönster och ange följande kommandon för att ladda ned exemplet från GitHub och ändra till exempelkatalogen:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>Granska koden för webbappen

Från katalogen web-apps-node-iot-hub-data-visualization öppnar du webbappen i din favoritredigerare. Följande visar filstrukturen som visas i VS Code:

![Filstruktur för webbapp](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Undersök följande filer en stund:

* **Server.js** är ett skript på tjänstsidan som initierar webbsocketen och händelsehubbens wrapper-klass. Den ger ett återanrop till klassen Event Hub wrapper som klassen använder för att sända inkommande meddelanden till webbsocketen.

* **Event-hub-reader.js** är ett skript på tjänstsidan som ansluter till IoT-hubbens inbyggda slutpunkt med hjälp av den angivna anslutningssträngen och konsumentgruppen. Den extraherar DeviceId och EnqueuedTimeUtc från metadata på inkommande meddelanden och vidarebefordrar sedan meddelandet med hjälp av återanropsmetoden som registrerats av server.js.

* **Chart-device-data.js** är ett skript på klientsidan som lyssnar på webbsocketen, håller reda på varje DeviceId och lagrar de sista 50 punkterna med inkommande data för varje enhet. Den binder sedan valda enhetsdata till diagramobjektet.

* **Index.htmhanterar** gränssnittslayouten för webbsidan och refererar till de skript som krävs för logik på klientsidan.

## <a name="configure-environment-variables-for-the-web-app"></a>Konfigurera miljövariabler för webbappen

För att kunna läsa data från din IoT-hubb behöver webbappen din IoT-hubbs anslutningssträng och namnet på konsumentgruppen som den ska läsa igenom. Den hämtar dessa strängar från processmiljön på följande rader i server.js:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Ange miljövariablerna i kommandofönstret med följande kommandon. Ersätt platshållarvärdena med tjänstanslutningssträngen för din IoT-hubb och namnet på konsumentgruppen som du skapade tidigare. Ange inte strängarna.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>Kör webbappen

1. Kontrollera att enheten körs och skickar data.

2. I kommandofönstret kör du följande rader för att ladda ned och installera refererade paket och starta webbplatsen:

   ```cmd
   npm install
   npm start
   ```

3. Du bör se utdata i konsolen som anger att webbappen har anslutits till din IoT-hubb och lyssnar på port 3000:

   ![Webbappen startades i konsolen](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Öppna en webbsida för att se data från din IoT-hubb

Öppna en webbläsare till `http://localhost:3000` .

I listan **Välj en enhet** väljer du din enhet för att se ett körningsdiagram över de senaste 50 temperatur- och luftfuktighetsdatapunkterna som skickats av enheten till din IoT-hubb.

![Webbappssida som visar temperatur och luftfuktighet i realtid](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

Du bör också se utdata i konsolen som visar meddelanden som din webbapp sänder till webbläsarklienten:  

![Utdata från sändning av webbapp i konsolen](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>Vara värd för webbappen i App Service

Den [Web Apps funktionen i Azure App Service](../app-service/overview.md) tillhandahåller en paas (plattform som en tjänst) som är värd för webbappar. Webbprogram som finns i Azure App Service kan dra nytta av kraftfulla Azure-funktioner som ytterligare säkerhet, belastningsutjämning och skalbarhet samt Azure- och partner-DevOps-lösningar som kontinuerlig distribution, pakethantering och så vidare. Azure App Service har stöd för webbprogram som utvecklats på många populära språk och distribuerats i Windows- eller Linux-infrastruktur.

I det här avsnittet etablerar du en webbapp i App Service distribuerar koden till den med hjälp av Azure CLI-kommandon. Du hittar information om de kommandon som används i [az webapp-dokumentationen.](/cli/azure/webapp) Innan du börjar kontrollerar du att du har slutfört stegen för att lägga till en resursgrupp i [din IoT-hubb,](#add-a-consumer-group-to-your-iot-hub)hämta en tjänstanslutningssträng för [din IoT-hubb](#get-a-service-connection-string-for-your-iot-hub)och ladda ned webbappen [från GitHub.](#download-the-web-app-from-github)

1. En [App Service plan](../app-service/overview-hosting-plans.md) definierar en uppsättning beräkningsresurser för en app som finns i App Service ska köras. I den här självstudien använder vi nivån Utvecklare/Kostnadsfri som värd för webbappen. Med den kostnadsfria nivån körs din webbapp på delade Windows-resurser med andra App Service, inklusive appar från andra kunder. Azure erbjuder även App Service för distribution av webbappar på Linux-beräkningsresurser. Du kan hoppa över det här steget om du redan har App Service plan som du vill använda.

   Kör följande kommando App Service plan skapa ett konto med den kostnadsfria Windows-nivån. Använd samma resursgrupp som din IoT-hubb finns i. Namnet på tjänstplanen kan innehålla versaler och gemener, siffror och bindestreck.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Etablera nu en webbapp i din App Service plan. Parametern `--deployment-local-git` gör att webbappkoden kan laddas upp och distribueras från en Git-lagringsplats på den lokala datorn. Webbappens namn måste vara globalt unikt och får innehålla versaler och gemener, siffror och bindestreck. Se till att ange Node version 10.6 eller senare för parametern, beroende på vilken `--runtime` version Node.js körtid som du använder. Du kan använda kommandot `az webapp list-runtimes` för att hämta en lista över körningar som stöds.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. Lägg nu till Programinställningar för miljövariablerna som anger IoT Hub-anslutningssträngen och konsumentgruppen Händelsehubb. Enskilda inställningar är blanksteg avgränsade i `-settings` parametern . Använd tjänstanslutningssträngen för din IoT-hubb och konsumentgruppen som du skapade tidigare i den här självstudien. Ange inte värdena.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString="<your IoT hub connection string>"
   ```

4. Aktivera Web Sockets-protokollet för webbappen och ange att webbappen endast ska ta emot HTTPS-begäranden (HTTP-begäranden omdirigeras till HTTPS).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. Du distribuerar koden till App Service genom att använda dina autentiseringsuppgifter [för distribution på användarnivå.](../app-service/deploy-configure-credentials.md) Dina autentiseringsuppgifter för distribution på användarnivå skiljer sig från dina autentiseringsuppgifter för Azure och används för lokala Git- och FTP-distributioner till en webbapp. När den har angetts är de giltiga för alla dina App Service i alla prenumerationer i ditt Azure-konto. Om du tidigare har angett autentiseringsuppgifter för distribution på användarnivå kan du använda dem.

   Om du inte tidigare har angett autentiseringsuppgifter för distribution på användarnivå eller om du inte kommer ihåg lösenordet kör du följande kommando. Ditt distributionsanvändarnamn måste vara unikt i Azure och får inte innehålla symbolen \@ för lokala Git-pushar. När du uppmanas till det anger du och bekräftar det nya lösenordet. Lösenordet måste innehålla minst åtta tecken, med två av följande tre element: bokstäver, siffror och symboler.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Hämta Git-URL:en som ska användas för att skicka koden till App Service.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Lägg till en fjärrplats till klonen som refererar till Git-lagringsplatsen för webbappen i App Service. För \<Git clone URL\> använder du den URL som returnerades i föregående steg. Kör följande kommando i kommandofönstret.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. Distribuera koden till App Service genom att ange följande kommando i kommandofönstret. Om du uppmanas att ange autentiseringsuppgifter anger du autentiseringsuppgifterna för distribution på användarnivå som du skapade i steg 5. Se till att du push-meddelandena till main-grenen App Service fjärrgrenen.

    ```cmd
    git push webapp main:main
    ```

9. Distributionsförloppet uppdateras i kommandofönstret. En lyckad distribution avslutas med rader som liknar följande utdata:

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  main -> main
    ```

10. Kör följande kommando för att fråga efter webbappens tillstånd och kontrollera att den körs:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. Gå till `https://<your web app name>.azurewebsites.net` i en webbläsare. En webbsida som liknar den du såg när du körde webbappen visas lokalt. Förutsatt att enheten kör och skickar data bör du se ett körningsdiagram över de 50 senaste temperatur- och fuktighetsavläsningarna som skickas av enheten.

## <a name="troubleshooting"></a>Felsökning

Om du får problem med det här exemplet kan du prova stegen i följande avsnitt. Om du fortfarande har problem kan du skicka feedback till oss längst ned i det här avsnittet.

### <a name="client-issues"></a>Klientproblem

* Om en enhet inte visas i listan eller om ingen graf ritas kontrollerar du att enhetskoden körs på enheten.

* Öppna utvecklarverktygen i webbläsaren (I många webbläsare öppnar F12-nyckeln den) och hittar konsolen. Leta efter eventuella varningar eller fel som skrivs ut där.

* Du kan felsöka skript på klientsidan i /js/chat-device-data.js.

### <a name="local-website-issues"></a>Problem med lokal webbplats

* Titta på utdata i fönstret där du startade noden för konsolutdata.

* Felsök serverkoden, särskilt server.js /scripts/event-hub-reader.js.

### <a name="azure-app-service-issues"></a>Azure App Service problem

* I Azure Portal du till din webbapp. Under **Övervakning** i det vänstra fönstret väljer du **App Service loggar**. Sätt **Application Logging (filsystem)** till på, **ange Nivå** till Fel och välj sedan **Spara.** Öppna sedan **Loggström** (under **Övervakning).**

* Från webbappen i Azure Portal under **Utvecklingsverktyg**  väljer du Konsol och  **validerar** nod- och npm-versioner med `node -v` och `npm -v` .

* Om du ser ett fel om att du inte hittar ett paket kan du ha kört stegen i fel ordning. När webbplatsen distribueras (med ) kör App Service , som körs baserat på den aktuella versionen av `git push` `npm install` noden som den har konfigurerat. Om detta ändras i konfigurationen senare måste du göra en betydelselös ändring i koden och push-skicka igen.

## <a name="next-steps"></a>Nästa steg

Du har använt din webbapp för att visualisera sensordata i realtid från din IoT-hubb.

Ett annat sätt att visualisera data från Azure IoT Hub finns i Använda Power BI för att visualisera sensordata i realtid från [din IoT Hub.](iot-hub-live-data-visualization-in-power-bi.md)

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
