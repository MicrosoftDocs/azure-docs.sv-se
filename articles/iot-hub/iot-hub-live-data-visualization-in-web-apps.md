---
title: Data visualisering i real tid av dina IoT Hub-data i en webbapp
description: Använd ett webb program för att visualisera temperatur-och fuktighets data som samlas in från en sensor och skickas till din IoT-hubb.
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
ms.openlocfilehash: 53b5add7526b0c20487e8fe3adb0b8ebe207a2ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102200030"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Visualisera real tids sensor data från din Azure IoT Hub i ett webb program

![Diagram från slut punkt till slut punkt](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Detta får du får lära dig

I den här självstudien får du lära dig att visualisera real tids sensor data som IoT-hubben tar emot med en node.js webbapp som körs på den lokala datorn. När du har kört webbappen lokalt kan du också följa stegen för att vara värd för webbappen i Azure App Service. Om du vill försöka visualisera data i IoT-hubben med hjälp av Power BI kan du läsa mer i [använda Power BI för att visualisera sensor data i real tid från Azure IoT Hub](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Vad du gör

* Lägg till en konsument grupp i IoT-hubben som webb programmet ska använda för att läsa sensor data
* Ladda ned webb program koden från GitHub
* Granska koden för webb program
* Konfigurera miljövariabler för att lagra de IoT Hub artefakter som krävs av din webbapp
* Kör webb programmet på utvecklings datorn
* Öppna en webb sida om du vill se temperatur-och fuktighets data i real tid från din IoT Hub
* Valfritt Använd Azure CLI för att vara värd för din webbapp i Azure App Service

## <a name="what-you-need"></a>Detta behöver du

* Slutför själv studie kursen om [Raspberry Pi online Simulator](iot-hub-raspberry-pi-web-simulator-get-started.md) eller någon av enhets självstudierna. till exempel [Raspberry Pi med node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Detta beskriver följande krav:

  * En aktiv Azure-prenumeration
  * En IoT-hubb under din prenumeration
  * Ett klient program som skickar meddelanden till din IoT-hubb

* [Hämta git](https://www.git-scm.com/downloads)

* I den här artikeln förutsätter vi en Windows-utvecklings dator. Du kan dock enkelt utföra dessa steg på ett Linux-system i önskat gränssnitt.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Lägga till en konsument grupp i IoT Hub

[Konsument grupper](../event-hubs/event-hubs-features.md#event-consumers) tillhandahåller oberoende vyer i händelse strömmen som gör det möjligt för appar och Azure-tjänster att oberoende använda data från samma Event Hub-slutpunkt. I det här avsnittet lägger du till en konsument grupp i IoT Hub: s inbyggda slut punkt som webbappen kommer att använda för att läsa data från.

Kör följande kommando för att lägga till en konsument grupp till den inbyggda slut punkten för din IoT Hub:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Anteckna det namn du väljer, du behöver det senare i den här självstudien.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Hämta en anslutnings sträng för din IoT-hubb

IoT Hub skapas med flera standard åtkomst principer. En sådan princip är **tjänst** principen som ger tillräckliga behörigheter för att en tjänst ska kunna läsa och skriva IoT Hub-slutpunkter. Kör följande kommando för att få en anslutnings sträng för din IoT-hubb som följer tjänst principen:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

Anslutnings strängen bör se ut ungefär så här:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Anteckna tjänst anslutnings strängen, du behöver den senare i den här självstudien.

## <a name="download-the-web-app-from-github"></a>Ladda ned webb programmet från GitHub

Öppna ett kommando fönster och ange följande kommandon för att ladda ned exemplet från GitHub och ändra till exempel katalogen:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>Granska koden för webb program

Från katalogen Web-Apps-Node-IoT-Hub-data-visualisering öppnar du webbappen i din favorit redigerare. Följande visar fil strukturen som visas i VS Code:

![Webb program fil struktur](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Ta en stund att undersöka följande filer:

* **Server.js** är ett skript på tjänst sidan som initierar en omslags klass för webbsocket och Event Hub. Det ger en motringning till den omslutnings klass för Händelsehubben som klassen använder för att skicka inkommande meddelanden till webbsocketen.

* **Event-hub-reader.js** är ett skript på tjänst sidan som ansluter till IoT Hub: s inbyggda slut punkt med den angivna anslutnings strängen och konsument gruppen. Den extraherar DeviceId och EnqueuedTimeUtc från metadata för inkommande meddelanden och vidarebefordrar sedan meddelandet med hjälp av callback-metoden som registrerats av server.js.

* **Chart-device-data.js** är ett skript på klient sidan som lyssnar på webbsocketen, håller koll på varje DeviceID och lagrar de senaste 50 punkterna för inkommande data för varje enhet. Den binder sedan de valda enhets data till diagramobjektet.

* **Index.html** hanterar UI-layouten för webb sidan och refererar till nödvändiga skript för klient sidans logik.

## <a name="configure-environment-variables-for-the-web-app"></a>Konfigurera miljövariabler för webbapp

För att läsa data från din IoT-hubb behöver webbappen din IoT Hub-anslutningssträng och namnet på den konsument grupp som den bör läsa igenom. Dessa strängar hämtas från process miljön på följande rader i server.js:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Ange miljövariabler i kommando fönstret med följande kommandon. Ersätt plats hållarnas värden med tjänst anslutnings strängen för din IoT-hubb och namnet på den konsument grupp som du skapade tidigare. Citera inte strängarna.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>Kör webbappen

1. Kontrol lera att enheten kör och skickar data.

2. I kommando fönstret kör du följande rader för att ladda ned och installera refererade paket och starta webbplatsen:

   ```cmd
   npm install
   npm start
   ```

3. Du bör se utdata i-konsolen som visar att webbappen har lyckats ansluta till din IoT-hubb och lyssnar på port 3000:

   ![Webbappen har startats i-konsolen](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Öppna en webb sida om du vill visa data från din IoT Hub

Öppna en webbläsare till `http://localhost:3000` .

I listan **Välj en enhet** väljer du din enhet för att se en arbets kurva om de senaste 50 temperatur-och fuktighets-data punkterna som skickas av enheten till din IoT-hubb.

![Webb program sida med real tids temperatur och fuktighet](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

Du bör också se utdata i-konsolen som visar de meddelanden som din webbapp skickar till webb läsar klienten:  

![Utdata från webbappens sändning i-konsolen](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>Vara värd för webbappen i App Service

[Web Apps funktionen i Azure App Service](../app-service/overview.md) tillhandahåller en PaaS (Platform as a Service) för att vara värd för webb program. Webb program som finns i Azure App Service kan dra nytta av kraftfulla Azure-funktioner som ytterligare säkerhet, belastnings utjämning och skalbarhet samt Azure-och partner DevOps-lösningar som kontinuerlig distribution, paket hantering och så vidare. Azure App Service stöder webb program som utvecklats på många populära språk och distribueras i Windows-eller Linux-infrastruktur.

I det här avsnittet etablerar du en webbapp i App Service och distribuerar din kod till den med hjälp av Azure CLI-kommandon. Du hittar information om de kommandon som används i [AZ webapp](/cli/azure/webapp) -dokumentationen. Innan du börjar ska du kontrol lera att du har slutfört stegen för att [lägga till en resurs grupp i IoT-hubben](#add-a-consumer-group-to-your-iot-hub), [Hämta en tjänst anslutnings sträng för IoT Hub](#get-a-service-connection-string-for-your-iot-hub)och [Hämta webbappen från GitHub](#download-the-web-app-from-github).

1. En [App Service plan](../app-service/overview-hosting-plans.md) definierar en uppsättning beräknings resurser för en app som är värd för App Service som ska köras. I den här självstudien använder vi den utvecklings-/kostnads fria nivån som värd för webbappen. Med den kostnads fria nivån körs din webbapp på delade Windows-resurser med andra App Service appar, inklusive appar från andra kunder. Azure erbjuder också App Service planer för att distribuera webbappar på Linux Compute-resurser. Du kan hoppa över det här steget om du redan har ett App Service plan som du vill använda.

   Om du vill skapa en App Service plan med den kostnads fria nivån av Windows kör du följande kommando. Använd samma resurs grupp som din IoT Hub. Namnet på din tjänst plan får innehålla versaler, gemener, siffror och bindestreck.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Etablera nu en webbapp i App Service plan. `--deployment-local-git`Parametern gör att webbappens kod kan laddas upp och distribueras från en git-lagringsplats på den lokala datorn. Ditt webb program namn måste vara globalt unikt och får innehålla versaler, gemener, siffror och bindestreck. Se till att ange Node version 10,6 eller senare för `--runtime` parametern, beroende på vilken version av Node.js runtime du använder. Du kan använda `az webapp list-runtimes` kommandot för att hämta en lista över körningar som stöds.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. Lägg nu till program inställningar för de miljövariabler som anger IoT Hub-anslutningssträngen och konsument gruppen för Event Hub. Enskilda inställningar är avgränsade med utrymme i `-settings` parametern. Använd tjänst anslutnings strängen för din IoT-hubb och den konsument grupp som du skapade tidigare i den här självstudien. Citera inte värdena.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString="<your IoT hub connection string>"
   ```

4. Aktivera webb-socket-protokollet för webbappen och ange att webbappen endast ska ta emot HTTPS-förfrågningar (HTTP-begäranden omdirigeras till HTTPS).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. Om du vill distribuera koden till App Service använder du autentiseringsuppgifterna för [distribution på användar nivå](../app-service/deploy-configure-credentials.md). Autentiseringsuppgifterna för distribution av användar nivå skiljer sig från dina Azure-autentiseringsuppgifter och används för lokala git-och FTP-distributioner till en webbapp. När de har angetts är de giltiga för alla dina App Service-appar i alla prenumerationer i ditt Azure-konto. Om du tidigare har angett autentiseringsuppgifter för distribution på användar nivå kan du använda dem.

   Om du inte tidigare har angett autentiseringsuppgifter för distribution på användar nivå eller om du inte kan komma ihåg ditt lösen ord kör du följande kommando. Ditt distributions användar namn måste vara unikt i Azure och det får inte innehålla \@ symbolen för lokal git-push. När du uppmanas att ange och bekräfta ditt nya lösen ord. Lösen ordet måste innehålla minst åtta tecken, med två av följande tre element: bokstäver, siffror och symboler.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Hämta git-URL: en som ska användas för att skicka koden till App Service.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Lägg till en fjärran sluten till din klon som hänvisar till git-lagringsplatsen för webbappen i App Service. För \<Git clone URL\> använder du den URL som returnerades i föregående steg. Kör följande kommando i kommando fönstret.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. Om du vill distribuera koden till App Service anger du följande kommando i kommando fönstret. Om du uppmanas att ange autentiseringsuppgifter anger du de autentiseringsuppgifter för distribution på användar nivå som du skapade i steg 5. Se till att du push-överför till huvud grenen för App Service-fjärrplatsen.

    ```cmd
    git push webapp main:main
    ```

9. Distributions förloppet uppdateras i kommando fönstret. En lyckad distribution avslutas med rader som liknar följande utdata:

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  main -> main
    ```

10. Kör följande kommando för att fråga om status för din webbapp och kontrol lera att den körs:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. Gå till `https://<your web app name>.azurewebsites.net` i en webbläsare. En webb sida som liknar den som du såg när du körde webb programmet visas lokalt. Om du antar att enheten kör och skickar data bör du se en körnings kurva om de 50 senaste temperatur-och fuktighets avläsningarna som skickas av enheten.

## <a name="troubleshooting"></a>Felsökning

Om du kommer över eventuella problem med det här exemplet kan du prova stegen i följande avsnitt. Om du fortfarande har problem kan du skicka feedback till oss längst ned i det här avsnittet.

### <a name="client-issues"></a>Klient problem

* Om en enhet inte visas i listan, eller om inget diagram ritas, kontrollerar du att enhets koden körs på enheten.

* I webbläsaren öppnar du utvecklarverktyg (i många webbläsare F12-nyckeln öppnar den) och letar upp konsolen. Sök efter eventuella varningar eller fel som har skrivits ut där.

* Du kan felsöka skript på klient sidan i/JS/chat-device-data.js.

### <a name="local-website-issues"></a>Problem med lokal webbplats

* Titta på utdata i fönstret där du startade noden för konsol utdata.

* Felsök Server koden, särskilt server.js och/scripts/event-hub-reader.js.

### <a name="azure-app-service-issues"></a>Azure App Service problem

* I Azure Portal går du till din webbapp. Under **övervakning** i den vänstra rutan väljer du **App Service loggar**. Aktivera **program loggning (fil system)** till på, ange **nivå** till fel och välj sedan **Spara**. Öppna sedan **logg strömmen** (under **övervakning**).

* Från din webbapp i Azure Portal under **utvecklingsverktyg**  väljer du  **konsol** och validerar Node-och NPM-versioner med `node -v` och `npm -v` .

* Om du ser ett fel om att det inte går att hitta ett paket kan du köra stegen i rätt ordning. När platsen distribueras (med `git push` ) körs App Service `npm install` , som körs baserat på den aktuella versionen av noden som den har kon figurer ATS. Om den ändras i konfigurationen senare, måste du göra en meningslös ändring av koden och skicka igen.

## <a name="next-steps"></a>Nästa steg

Du har använt din webbapp för att visualisera sensor data i real tid från IoT Hub.

Ett annat sätt att visualisera data från Azure IoT Hub finns i [använda Power BI för att visualisera sensor data i real tid från IoT Hub](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
