---
title: Vanliga frågor och svar om inbyggda behållare
description: Få svar på vanliga frågor om de inbyggda Linux-behållarna i Azure App Service.
keywords: Azure App Service, webbapp, vanliga frågor, Linux, oss, Web App for Containers, multi-container, multibehållare
author: msangapu-msft
ms.topic: article
ms.date: 10/30/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: fb5203629915914ab9af22d89e5f2865078a8e44
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012615"
---
# <a name="azure-app-service-on-linux-faq"></a>Vanliga frågor och svar om Azure App Service on Linux

I och med lanseringen av App Service på Linux arbetar vi med att lägga till funktioner och förbättra vår plattform. Den här artikeln innehåller svar på frågor som våra kunder har tillfrågat oss nyligen.

Om du har en fråga kan du kommentera den här artikeln.

## <a name="built-in-images"></a>Inbyggda avbildningar

**Jag vill förgrena de inbyggda Docker-behållare som plattformen tillhandahåller. Var hittar jag filerna?**

Du kan hitta alla Docker-filer på [GitHub](https://github.com/azure-app-service). Du kan hitta alla Docker-behållare på [Docker Hub](https://hub.docker.com/u/appsvc/).

<a id="#startup-file"></a>

**Vilka är de förväntade värdena för start filen i avsnittet när jag konfigurerar körnings stacken?**

| Stack           | Förväntat värde                                                                         |
|-----------------|----------------------------------------------------------------------------------------|
| Java SE         | kommandot för att starta JAR-appen (till exempel `java -jar /home/site/wwwroot/app.jar --server.port=80` ) |
| Tomcat          | platsen för ett skript för att utföra nödvändiga konfigurationer (till exempel `/home/site/deployments/tools/startup_script.sh` )          |
| Node.js         | konfigurations filen för PM2 eller skript filen                                |
| .NET Core       | det kompilerade DLL-namnet som `dotnet <myapp>.dll`                                 |
| Ruby            | det ruby-skript som du vill initiera din app med                     |

De här kommandona eller skripten körs när den inbyggda Docker-behållaren har startats, men innan program koden startas.

## <a name="management"></a>Hantering

**Vad händer när jag trycker på knappen starta om i Azure Portal?**

Den här åtgärden är samma som en Docker-omstart.

**Kan jag använda SSH (Secure Shell) för att ansluta till den virtuella datorns app container (VM)?**

Ja, du kan göra det via platsen för käll kontroll hantering (SCM).

> [!NOTE]
> Du kan också ansluta till appcontainern direkt från din lokala utvecklingsdator med SSH, SFTP eller Visual Studio Code (för livefelsökning av Node.js-appar). Mer information finns i [Remote debugging and SSH in App Service on Linux](https://azure.github.io/AppService/2018/05/07/New-SSH-Experience-and-Remote-Debugging-for-Linux-Web-Apps.html) (Fjärrfelsökning och SSH i App Service på Linux).
>

**Hur kan jag skapa en Linux-App Service plan via en SDK eller en Azure Resource Manager mall?**

Ange det **reserverade** fältet för App Service som *Sant*.

## <a name="continuous-integration-and-deployment"></a>Kontinuerlig integrering och distribution

**Min webbapp använder fortfarande en gammal Docker-behållar avbildning efter att jag har uppdaterat avbildningen på Docker Hub. Stöder du kontinuerlig integrering och distribution av anpassade behållare?**

Ja, om du vill konfigurera kontinuerlig integrering/distribution för Azure Container Registry eller DockerHub, genom att följa [kontinuerlig distribution med Web App for containers](./deploy-ci-cd-custom-container.md). För privata register kan du uppdatera behållaren genom att stoppa och sedan starta din webbapp. Eller så kan du ändra eller lägga till en dummy-programinställning för att tvinga fram en uppdatering av din behållare.

**Har du stöd för mellanlagrings miljöer?**

Ja.

**Kan jag använda *WebDeploy/MSDeploy* för att distribuera min webbapp?**

Ja, du måste ange en app-inställning `WEBSITE_WEBDEPLOY_USE_SCM` som kallas *false*.

**Git-distribution av mitt program Miss lyckas när Linux-webbappen används. Hur kan jag lösa problemet?**

Om git-distributionen Miss lyckas med din Linux-webbapp väljer du något av följande alternativ för att distribuera program koden:

- Använd funktionen för kontinuerlig leverans (för hands version): du kan lagra appens källkod i en Azure DevOps git lagrings platsen-eller GitHub-lagrings platsen för att använda Azures kontinuerliga leverans. Mer information finns i [så här konfigurerar du kontinuerlig leverans för Linux-webbappar](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/).

- Använd [zip Deploy API](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file): för att använda detta API, [ssh i din webbapp](configure-linux-open-ssh-session.md) och gå till den mapp där du vill distribuera din kod. Kör följande kod:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Om du får ett fel meddelande om att `curl` kommandot inte hittas kontrollerar du att du installerar en sväng genom att använda `apt-get install curl` innan du kör föregående `curl` kommando.

## <a name="language-support"></a>Stöd för språk

**Jag vill använda Web Sockets i mitt Node.js-program, särskilda inställningar eller konfigurationer att ställa in?**

Ja, inaktivera `perMessageDeflate` i Node.js koden på Server sidan. Om du till exempel använder socket.io använder du följande kod:

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Stöder du kompilerade .NET Core-appar?**

Ja.

**Stöder du Composer som beroende hanterare för PHP-appar?**

Ja, under en Git-distribution ska kudu identifiera att du distribuerar ett PHP-program (tack vare förekomsten av en Composer. lock-fil) och kudu sedan utlöser en Composer-installation.

## <a name="custom-containers"></a>Anpassade containrar

**Jag använder min egen anpassade behållare. Jag vill att plattformen ska montera en SMB-resurs till `/home/` katalogen.**

Om `WEBSITES_ENABLE_APP_SERVICE_STORAGE` inställningen är **ospecificerad** eller är inställd på *falskt*, `/home/` **delas katalogen inte** mellan skalnings instanser och filer som skrivs **behålls inte** i omstarter. Om `WEBSITES_ENABLE_APP_SERVICE_STORAGE` du anger *True* aktive ras monteringen.

**Min anpassade behållare tar lång tid att starta och plattformen startar om behållaren innan den har startats.**

Du kan konfigurera hur lång tid som plattformen ska vänta innan den startar om din behållare. Det gör du genom att ställa in `WEBSITES_CONTAINER_START_TIME_LIMIT` appens inställningar på det värde som du vill använda. Standardvärdet är 230 sekunder och det högsta värdet är 1800 sekunder.

**Vad är formatet för den privata register serverns URL?**

Ange fullständig registrerings-URL, inklusive `http://` eller `https://` .

**Vad är formatet för avbildnings namnet i det privata register alternativet?**

Lägg till det fullständiga avbildnings namnet, inklusive URL: en för den privata registret (till exempel myacr.azurecr.io/dotnet:latest). Avbildnings namn som använder en anpassad port [kan inte anges via portalen](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). `docker-custom-image-name`Använd [ `az` kommando rads verktyget](/cli/azure/webapp/config/container#az-webapp-config-container-set)för att ange.

**Kan jag exponera fler än en port i min anpassade behållar avbildning?**

Vi har inte stöd för att exponera mer än en port.

**Kan jag ta min egen lagring?**

Ja, [ta med din egen lagrings enhet](./configure-connect-to-azure-storage.md) i för hands versionen.

**Varför kan jag inte bläddra i min anpassade behållares fil system eller köra processer från SCM-platsen?**

SCM-platsen körs i en separat behållare. Du kan inte kontrol lera fil systemet eller körnings processerna för app-behållaren.

**Min anpassade behållare lyssnar till en annan port än port 80. Hur konfigurerar jag min app för att dirigera begär anden till den porten?**

Vi har automatisk port identifiering. Du kan också ange en app-inställning som kallas *WEBSITES_PORT* och ge den värdet för det förväntade port numret. Tidigare använde plattformen *porten* app-inställningen. Vi planerar att använda den här appens inställning och använda *WEBSITES_PORT* exklusivt.

**Måste jag implementera HTTPS i min anpassade behållare?**

Nej, plattformen hanterar HTTPS-avslutning på delade klient delar.

**Måste jag använda PORT variabel i kod för inbyggda behållare?**

Nej, PORT variabel är inte nödvändig på grund av automatisk port identifiering. Om ingen port identifieras är den som standard 80.
Om du vill konfigurera en anpassad port manuellt använder du instruktionen Visa i Dockerfile och appens inställning WEBSITES_PORT, med ett port värde som ska bindas i behållaren.

**Behöver jag använda WEBSITES_PORT för anpassade behållare?**

Ja, detta krävs för anpassade behållare. Om du vill konfigurera en anpassad port manuellt använder du instruktionen Visa i Dockerfile och appens inställning WEBSITES_PORT, med ett port värde som ska bindas i behållaren.

**Kan jag använda ASPNETCORE_URLS i Docker-avbildningen?**

Ja, Skriv över miljö variabeln innan .NET Core-appen startas.
T.ex. I init.sh-skriptet: exportera ASPNETCORE_URLS = {ditt värde}

## <a name="multi-container-with-docker-compose"></a>Multi-container med Docker Compose

**Hur gör jag för att konfigurera Azure Container Registry (ACR) för användning med flera behållare?**

För att kunna använda ACR med flera behållare måste **alla behållar avbildningar** vara värdbaserade på samma ACR-register Server. När de finns på samma register Server måste du skapa program inställningar och sedan uppdatera Docker-bildningens konfigurations fil för att inkludera avbildnings namnet för ACR.

Skapa följande program inställningar:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (fullständig URL, ex: `https://<server-name>.azurecr.io` )
- DOCKER_REGISTRY_SERVER_PASSWORD (aktivera administratörs åtkomst i ACR-inställningar)

I konfigurations filen refererar du till ACR-avbildningen som i följande exempel:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Hur gör jag för att vet du vilken behållare som är tillgänglig för Internet?**

- Endast en behållare kan vara öppen för åtkomst
- Endast port 80 och 8080 kan nås (exponerade portar)

Här följer reglerna för att avgöra vilken behållare som är tillgänglig – i prioritetsordning:

- Program inställningen `WEBSITES_WEB_CONTAINER_NAME` har angetts till behållar namnet
- Den första behållaren för att definiera port 80 eller 8080
- Om inget av ovanstående är sant kommer den första behållaren som definieras i filen att vara tillgänglig (exponeras)


## <a name="web-sockets"></a>Web Sockets

Web Sockets stöds i Linux-appar.

> [!IMPORTANT]
> Web Sockets stöds för närvarande inte för Linux-appar på App Service – kostnadsfri planer. Vi arbetar på att ta bort den här begränsningen och planera för att stödja upp till 5 WebSocket-anslutningar på App Service – kostnadsfri planer.

## <a name="pricing-and-sla"></a>Priser och service nivå avtal

**Vad är prissättningen? nu är tjänsten allmänt tillgänglig?**

Prissättningen varierar beroende på SKU och region, men du kan se mer information på vår prissättnings sida: [App Service prissättning](https://azure.microsoft.com/pricing/details/app-service/linux/).

## <a name="other-questions"></a>Andra frågor

**Vad betyder "den begärda funktionen är inte tillgänglig i resurs gruppen"?**

Du kan se det här meddelandet när du skapar en webbapp med Azure Resource Manager (ARM). I samma resurs grupp kan du inte blanda Windows-och Linux-appar i samma region, baserat på en aktuell begränsning.

**Vad är de tecken som stöds i program inställnings namn?**

Du kan bara använda bokstäver (A-Z, A-z), siffror (0-9) och under strecks tecknet (_) för program inställningar.

**Var kan jag begära nya funktioner?**

Du kan skicka in din idé i [forumen Web Apps feedback](https://aka.ms/webapps-uservoice). Lägg till "[Linux]" i titeln på din idé.

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure App Service på Linux?](overview.md#app-service-on-linux)
- [Konfigurera mellanlagringsmiljöer i Azure App Service](deploy-staging-slots.md)
- [Kontinuerlig distribution med Web App for Containers](./deploy-ci-cd-custom-container.md)
- [Saker du bör känna till: Web Apps och Linux](https://techcommunity.microsoft.com/t5/apps-on-azure/things-you-should-know-web-apps-and-linux/ba-p/392472)
