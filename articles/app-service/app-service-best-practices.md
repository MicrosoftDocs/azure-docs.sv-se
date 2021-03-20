---
title: Metodtips
description: Lär dig metod tips och vanliga fel söknings scenarier för din app som körs i Azure App Service.
author: dariagrigoriu
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.topic: article
ms.date: 07/01/2016
ms.author: dariac
ms.custom: seodec18
ms.openlocfilehash: 91fd974c730037907258cb4a670f6fa836bfda6c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92144879"
---
# <a name="best-practices-for-azure-app-service"></a>Metodtips för Azure App Service
I den här artikeln sammanfattas metod tips för hur du använder [Azure App Service](./overview.md). 

## <a name="colocation"></a><a name="colocation"></a>Samordning
När Azure-resurser skriver en lösning som en webbapp och en databas finns i olika regioner, kan den ha följande effekter:

* Ökad svars tid i kommunikationen mellan resurser
* Penning kostnader för utgående data överföring mellan regioner enligt vad som anges på [sidan med priser för Azure](https://azure.microsoft.com/pricing/details/data-transfers).

Samplacering i samma region är bäst för Azure-resurser som skriver en lösning, till exempel en webbapp och en databas eller ett lagrings konto som används för att lagra innehåll eller data. När du skapar resurser måste du kontrol lera att de finns i samma Azure-region, om du inte har specifika affärs-eller design skäl för att de inte ska vara det. Du kan flytta en App Service-app till samma region som din databas genom att använda den [App Service kloning-funktionen](app-service-web-app-cloning.md) som för närvarande är tillgänglig för Premium App Service plan-appar.   

## <a name="when-apps-consume-more-memory-than-expected"></a><a name="memoryresources"></a>När appar förbrukar mer minne än förväntat
När du märker att en app förbrukar mer minne än vad som förväntas via övervakning eller tjänst rekommendationer, bör du tänka på [App Service funktionen för automatisk återställning](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Ett av alternativen för funktionen automatisk återställning tar anpassade åtgärder baserat på ett minnes tröskelvärde. Åtgärder sträcker sig från e-postaviseringar till undersökning via minnes dumpning till skydd på plats genom att återvinna arbets processen. Automatisk återställning kan konfigureras via web.config och via ett användarvänligt användar gränssnitt som beskrivs i det här blogg inlägget för [webbplats tillägget app service support](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="when-apps-consume-more-cpu-than-expected"></a><a name="CPUresources"></a>När appar förbrukar mer processor än förväntat
När du märker att en app förbrukar mer processor än förväntat eller upplever upprepade CPU-toppar som anges via övervakning eller tjänst rekommendationer, kan du överväga att skala upp eller skala ut App Service plan. Om ditt program är tillstånds känsligt är skala upp det enda alternativet, men om ditt program är tillstånds löst ger du större flexibilitet och bättre skalnings möjligheter. 

För mer information om "tillstånds känsliga" vs "tillstånds lösa" program kan du titta på den här videon: [planera ett skalbart program på flera nivåer på Azure App Service](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). Mer information om alternativ för App Service skalning och automatisk skalning finns i [skala en webbapp i Azure App Service](manage-scale-up.md).  

## <a name="when-socket-resources-are-exhausted"></a><a name="socketresources"></a>När socket-resurserna är uttömda
En vanlig orsak till att använda utgående TCP-anslutningar är användningen av klient bibliotek, som inte implementeras för att återanvända TCP-anslutningar eller när ett protokoll på högre nivå, till exempel HTTP-Keep-Alive inte används. Läs dokumentationen för varje bibliotek som refereras av apparna i App Services plan för att säkerställa att de konfigureras eller nås i koden för effektiv åter användning av utgående anslutningar. Följ även vägledningen för biblioteks dokumentation för att skapa och släppa eller rensa för att undvika läckage av anslutningar. Även om granskning av sådana klient bibliotek pågår, kan påverkan minskas genom att skala ut till flera instanser.

### <a name="nodejs-and-outgoing-http-requests"></a>Node.js och utgående HTTP-begäranden
När du arbetar med Node.js och många utgående HTTP-begäranden är det viktigt att hantera HTTP-Keep-Alive. Du kan använda [agentkeepalive](https://www.npmjs.com/package/agentkeepalive) - `npm` paketet för att göra det enklare i din kod.

Hantera alltid `http` svaret, även om du inte gör något i hanteraren. Om du inte hanterar svaret korrekt kommer ditt program att fastna så småningom, eftersom det inte finns några fler socketar tillgängliga.

Till exempel när du arbetar med- `http` eller- `https` paketet:

```javascript
const request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Om du kör på App Service på Linux på en dator med flera kärnor, är det en annan metod att använda PM2 för att starta flera Node.js processer för att köra programmet. Du kan göra det genom att ange ett start kommando till din behållare.

Till exempel för att starta fyra instanser:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="when-your-app-backup-starts-failing"></a><a name="appbackup"></a>När säkerhets kopieringen av appen inte startar
De två vanligaste orsakerna till varför det inte går att säkerhetskopiera appar: ogiltiga lagrings inställningar och ogiltig databas konfiguration. Dessa fel inträffar vanligt vis när det finns ändringar i lagrings-eller databas resurser, eller ändringar för att få åtkomst till dessa resurser (till exempel uppdaterade autentiseringsuppgifter för den databas som valts i säkerhets kopierings inställningarna). Säkerhets kopieringar körs vanligt vis enligt ett schema och kräver åtkomst till lagring (för att placera de säkerhetskopierade filerna) och databaser (för att kopiera och läsa innehåll som ska ingå i säkerhets kopian). Resultatet av att det inte går att få åtkomst till någon av dessa resurser är konsekventa säkerhets kopierings problem. 

När säkerhets kopierings felen inträffar granskar du de senaste resultaten för att förstå vilken typ av haveri som inträffar. Granska och uppdatera lagrings inställningarna som används i säkerhets kopierings konfigurationen för att få åtkomst till lagrings problem. Granska och uppdatera anslutnings strängarna som en del av appens inställningar för databas åtkomst problem. Fortsätt sedan att uppdatera säkerhets kopierings konfigurationen för att lägga till nödvändiga databaser på rätt sätt. Mer information om säkerhets kopiering av appar finns [i Säkerhetskopiera en webbapp i Azure App Service](manage-backup.md).

## <a name="when-new-nodejs-apps-are-deployed-to-azure-app-service"></a><a name="nodejs"></a>När nya Node.js-appar distribueras till Azure App Service
Azure App Service standard konfigurationen för Node.js appar är avsedd att bäst passa behoven hos de vanligaste apparna. Om konfigurationen för din Node.js-appen skulle dra nytta av anpassad justering för att förbättra prestandan eller optimera resursanvändningen för processor-/minnes-/nätverks resurser, se [metod tips och fel söknings guide för Node-program på Azure App Service](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md). Den här artikeln beskriver de iisnode-inställningar som du kan behöva konfigurera för din Node.js-app, beskriver de olika scenarier eller problem som din app kan vara riktade till och visar hur du kan åtgärda problemen.


## <a name="next-steps"></a>Nästa steg
Mer information om bästa praxis finns i [App Service Diagnostics](./overview-diagnostics.md) för att ta reda på vilka metod tips som är lämpliga för din resurs.

- Navigera till din webbapp i [Azure Portal](https://portal.azure.com).
- Klicka på **diagnostisera och lös problem** i det vänstra navigerings fönstret, som öppnar App Service Diagnostics.
- Välj Start sida för **bästa praxis** .
- Klicka på **metod tips för tillgänglighets & prestanda** eller **bästa praxis för optimal konfiguration** för att visa appens aktuella tillstånd med avseende på dessa metod tips.

Du kan också använda den här länken för att direkt öppna App Service diagnostik för din resurs: `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot` .