---
title: Vanliga frågor och svar om att köra inbyggda containrar
description: Få svar på vanliga frågor om de inbyggda Linux-containrarna i Azure App Service.
keywords: azure app service, web app, faq, linux, oss, web app for containers, multi-container, multicontainer
author: msangapu-msft
ms.topic: article
ms.date: 10/30/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 82fc5707800e06e3221754bfa29d8e981ccdbd2d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782593"
---
# <a name="azure-app-service-on-linux-faq"></a>Vanliga frågor och svar om Azure App Service on Linux

Med lanseringen av App Service på Linux arbetar vi med att lägga till funktioner och göra förbättringar av vår plattform. Den här artikeln innehåller svar på frågor som våra kunder har ställt till oss nyligen.

Om du har en fråga kan du kommentera den här artikeln.

## <a name="built-in-images"></a>Inbyggda avbildningar

**Jag vill förbebygga de inbyggda Docker-containrar som plattformen tillhandahåller. Var hittar jag dessa filer?**

Du hittar alla Docker-filer på [GitHub.](https://github.com/azure-app-service) Du hittar alla Docker-containrar på [Docker Hub](https://hub.docker.com/u/appsvc/).

<a id="#startup-file"></a>

**Vilka är de förväntade värdena för avsnittet Startfil när jag konfigurerar körningsstacken?**

| Stack           | Förväntat värde                                                                         |
|-----------------|----------------------------------------------------------------------------------------|
| Java SE         | kommandot för att starta JAR-appen (till exempel `java -jar /home/site/wwwroot/app.jar --server.port=80` ) |
| Tomcat          | platsen för ett skript för att utföra nödvändiga konfigurationer (till exempel `/home/site/deployments/tools/startup_script.sh` )          |
| Node.js         | PM2-konfigurationsfilen eller skriptfilen                                |
| .NET Core       | det kompilerade DLL-namnet som `dotnet <myapp>.dll`                                 |
| Ruby            | Ruby-skriptet som du vill initiera appen med                     |

Dessa kommandon eller skript körs när den inbyggda Docker-containern har startats, men innan programkoden startas.

## <a name="management"></a>Hantering

**Vad händer när jag trycker på omstartsknappen i Azure Portal?**

Den här åtgärden är samma som en Docker-omstart.

**Kan jag använda Secure Shell (SSH) för att ansluta till den virtuella datorn för appcontainer (VM)?**

Ja, det kan du göra via källkontrollhanteringsplatsen (SCM).

> [!NOTE]
> Du kan också ansluta till appcontainern direkt från din lokala utvecklingsdator med SSH, SFTP eller Visual Studio Code (för livefelsökning av Node.js-appar). Mer information finns i [Remote debugging and SSH in App Service on Linux](https://azure.github.io/AppService/2018/05/07/New-SSH-Experience-and-Remote-Debugging-for-Linux-Web-Apps.html) (Fjärrfelsökning och SSH i App Service på Linux).
>

**Hur skapar jag en Linux-App Service plan via en SDK eller en Azure Resource Manager mall?**

Ange det **reserverade** fältet för apptjänsten till *true*.

## <a name="continuous-integration-and-deployment"></a>Kontinuerlig integrering och distribution

**Min webbapp använder fortfarande en gammal Docker-containeravbildning när jag har uppdaterat avbildningen Docker Hub. Har du stöd för kontinuerlig integrering och distribution av anpassade containrar?**

Ja, för att konfigurera kontinuerlig integrering/distribution för Azure Container Registry eller DockerHub genom att följa [Kontinuerlig distribution med Web App for Containers](./deploy-ci-cd-custom-container.md). För privata register kan du uppdatera containern genom att stoppa och sedan starta webbappen. Eller så kan du ändra eller lägga till en dummy-programinställning för att tvinga fram en uppdatering av containern.

**Har du stöd för mellanlagringsmiljöer?**

Ja.

**Kan jag använda *WebDeploy/MSDeploy för* att distribuera min webbapp?**

Ja, du måste ange en appinställning med namnet `WEBSITE_WEBDEPLOY_USE_SCM` *false*.

**Git-distributionen av mitt program misslyckas när jag använder Linux-webbappen. Hur kan jag komma runt problemet?**

Om Git-distributionen misslyckas med din Linux-webbapp väljer du något av följande alternativ för att distribuera programkoden:

- Använd funktionen Kontinuerlig leverans (förhandsversion): Du kan lagra appens källkod på en Azure DevOps Git-lagringsplatsen eller GitHub-lagringsplatsen för att använda Azure Continuous Delivery. Mer information finns i Så [här konfigurerar du kontinuerlig leverans för Linux-webbappen](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/).

- Använd [API:et för](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)ZIP-distribution: Om du vill använda det här API:et använder du [SSH](configure-linux-open-ssh-session.md) i webbappen och går till mappen där du vill distribuera koden. Kör följande kod:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Om du får ett felmeddelande om att kommandot inte hittas kontrollerar du att du installerar `curl` curl med innan du kör föregående `apt-get install curl` `curl` kommando.

## <a name="language-support"></a>Stöd för språk

**Jag vill använda webbsocketar i mitt Node.js program, eventuella särskilda inställningar eller konfigurationer som ska anges?**

Ja, inaktivera `perMessageDeflate` i koden för Node.js serversidan. Om du till exempel använder socket.io använder du följande kod:

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Har du stöd för okompilerade .NET Core-appar?**

Ja.

**Har du stöd för Composer som beroendehanterare för PHP-appar?**

Ja, under en Git-distribution bör Kudu identifiera att du distribuerar ett PHP-program (tack vare förekomsten av en composer.lock-fil) så utlöser Kudu en installation av en composer.

## <a name="custom-containers"></a>Anpassade containrar

**Jag använder min egen anpassade container. Jag vill att plattformen ska montera en SMB-resurs i `/home/` katalogen.**

Om `WEBSITES_ENABLE_APP_SERVICE_STORAGE` inställningen är **ospecificerad** eller inställd på *false* delas inte katalogen mellan skalningsinstanser och filer som skrivs bevaras inte `/home/` mellan omstarter.   Om du `WEBSITES_ENABLE_APP_SERVICE_STORAGE` uttryckligen anger till *true* aktiveras monteringen.

**Det tar lång tid att starta min anpassade container och plattformen startar om containern innan den har startats klart.**

Du kan konfigurera hur lång tid plattformen ska vänta innan den startar om containern. Det gör du genom att ange `WEBSITES_CONTAINER_START_TIME_LIMIT` appinställningen till det värde som du vill använda. Standardvärdet är 230 sekunder och det högsta värdet är 1 800 sekunder.

**Vilket format har den privata registerserverns URL?**

Ange den fullständiga register-URL:en, `http://` inklusive eller `https://` .

**Vilket format har avbildningsnamnet i det privata registeralternativet?**

Lägg till det fullständiga avbildningsnamnet, inklusive URL:en för det privata registret (till exempel myacr.azurecr.io/dotnet:latest). Avbildningsnamn som använder en [anpassad port kan inte anges via portalen](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Ange med `docker-custom-image-name` [ `az` kommandoradsverktyget](/cli/azure/webapp/config/container#az_webapp_config_container_set).

**Kan jag exponera mer än en port på min anpassade containeravbildning?**

Vi stöder inte att exponera mer än en port.

**Kan jag använda min egen lagring?**

Ja, [Bring Your Own Storage](./configure-connect-to-azure-storage.md) är en förhandsversion.

**Varför kan jag inte bläddra i min anpassade containers filsystem eller köra processer från SCM-webbplatsen?**

SCM-platsen körs i en separat container. Du kan inte kontrollera filsystemet eller köra processer för appcontainern.

**Min anpassade container lyssnar på en annan port än port 80. Hur konfigurerar jag min app för att dirigera begäranden till den porten?**

Vi har automatisk portidentifiering. Du kan också ange en appinställning *WEBSITES_PORT* och ge den värdet för det förväntade portnumret. Tidigare använde plattformen *appinställningen PORT.* Vi planerar att ta bort den här appinställningen och använda den *WEBSITES_PORT* exklusivt.

**Måste jag implementera HTTPS i min anpassade container?**

Nej, plattformen hanterar HTTPS-avslutning på den delade fronten.

**Måste jag använda PORT-variabeln i koden för inbyggda containrar?**

Nej, portvariabeln är inte nödvändig på grund av automatisk portidentifiering. Om ingen port identifieras blir standardvärdet 80.
Om du vill konfigurera en anpassad port manuellt använder du instruktionen EXPOSE i Dockerfile och appinställningen, WEBSITES_PORT, med ett portvärde som ska bindas i containern.

**Måste jag använda en WEBSITES_PORT anpassade containrar?**

Ja, detta krävs för anpassade containrar. Om du vill konfigurera en anpassad port manuellt använder du instruktionen EXPOSE i Dockerfile och appinställningen, WEBSITES_PORT, med ett portvärde som ska bindas i containern.

**Kan jag använda ASPNETCORE_URLS i Docker-avbildningen?**

Ja, skriva över miljövariabeln innan .NET Core-appen startar.
T.ex. I skriptet init.sh: export ASPNETCORE_URLS={Ditt värde}

## <a name="multi-container-with-docker-compose"></a>Flera containrar med Docker Compose

**Hur gör jag för att konfigurera Azure Container Registry (ACR) för användning med flera containrar?**

För att kunna använda ACR med flera containrar måste **alla containeravbildningar** finnas på samma ACR-registerserver. När de finns på samma registerserver måste du skapa programinställningar och sedan uppdatera Docker Compose-konfigurationsfilen så att den innehåller namnet på ACR-avbildningen.

Skapa följande programinställningar:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (fullständig URL, till exempel `https://<server-name>.azurecr.io` )
- DOCKER_REGISTRY_SERVER_PASSWORD (aktivera administratörsåtkomst i ACR-inställningar)

I konfigurationsfilen refererar du till ACR-avbildningen som i följande exempel:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Hur gör jag för att du vilken container som är tillgänglig via Internet?**

- Endast en container kan öppnas för åtkomst
- Endast port 80 och 8080 är tillgänglig (exponerade portar)

Här är reglerna för att avgöra vilken container som är tillgänglig – i prioritetsordning:

- Programinställning `WEBSITES_WEB_CONTAINER_NAME` inställd på containernamnet
- Den första containern som definierar port 80 eller 8080
- Om inget av ovanstående stämmer är den första containern som definierats i filen tillgänglig (exponerad)


## <a name="web-sockets"></a>Web Sockets

Web Sockets stöds i Linux-appar.

> [!IMPORTANT]
> Web Sockets stöds för närvarande inte för Linux-appar på kostnadsfria App Service-planer. Vi arbetar med att ta bort den här begränsningen och planerar att stödja upp till 5 webbsocketanslutningar på kostnadsfria App Service planer.

## <a name="pricing-and-sla"></a>Priser och serviceavtal

**Vad är prissättningen, nu när tjänsten är allmänt tillgänglig?**

Prissättningen varierar beroende på SKU och region, men du kan se mer information på vår [prissättningssida: App Service priser.](https://azure.microsoft.com/pricing/details/app-service/linux/)

## <a name="other-questions"></a>Andra frågor

**Vad betyder "Den begärda funktionen är inte tillgänglig i resursgruppen"?**

Du kan se det här meddelandet när du skapar en webbapp med Azure Resource Manager (ARM). Baserat på en aktuell begränsning kan du inte blanda Windows- och Linux-appar i samma region för samma resursgrupp.

**Vilka tecken stöds i namn på programinställningar?**

Du kan bara använda bokstäver (A–Z, a–z), siffror (0–9) och understreck (_) för programinställningar.

**Var kan jag begära nya funktioner?**

Du kan skicka in din idé på Web Apps [feedbackforumet](https://aka.ms/webapps-uservoice). Lägg till "[Linux]" i rubriken för din idé.

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure App Service på Linux?](overview.md#app-service-on-linux)
- [Konfigurera mellanlagringsmiljöer i Azure App Service](deploy-staging-slots.md)
- [Kontinuerlig distribution med Web App for Containers](./deploy-ci-cd-custom-container.md)
- [Saker du bör känna till: Web Apps och Linux](https://techcommunity.microsoft.com/t5/apps-on-azure/things-you-should-know-web-apps-and-linux/ba-p/392472)
