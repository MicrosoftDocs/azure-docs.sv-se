---
title: Lokal cache
description: Lär dig hur lokal cache fungerar i Azure App Service och hur du aktiverar, ändrar storlek på och frågar statusen för appens lokala cacheminne.
tags: optional
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.topic: article
ms.date: 03/04/2016
ms.custom: seodec18
ms.openlocfilehash: 462f37730d61a674edd463e57a98a1228c8ed611
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967089"
---
# <a name="azure-app-service-local-cache-overview"></a>Översikt över Azure App Service lokal cache

> [!NOTE]
> Local cache stöds inte i Function Apps eller i behållare App Service appar, t. ex. i [Windows-behållare](quickstart-custom-container.md?pivots=container-windows) eller i [app service på Linux](overview.md#app-service-on-linux). En version av lokalt cacheminne som är tillgänglig för dessa typer av appar är [app-cache](https://github.com/Azure-App-Service/KuduLite/wiki/App-Cache).


Azure App Service innehåll lagras på Azure Storage och finns på ett hållbart sätt som en innehålls resurs. Den här designen är avsedd att användas med en mängd olika appar och har följande attribut:  

* Innehållet delas mellan flera virtuella dator instanser (VM) i appen.
* Innehållet är hållbart och kan ändras genom att appar körs.
* Loggfiler och diagnostikdata är tillgängliga under samma delade innehålls-mapp.
* Om du publicerar nytt innehåll uppdateras mappen innehåll direkt. Du kan direkt Visa samma innehåll via SCM-webbplatsen och den app som körs (vanligt vis vissa tekniker, t. ex. ASP.NET, initierar en omstart för vissa fil ändringar för att hämta det senaste innehållet).

Många appar använder en eller flera av dessa funktioner, men vissa appar behöver bara ett skrivskyddat innehålls lager med hög prestanda som de kan köra från med hög tillgänglighet. De här apparna kan dra nytta av en virtuell dator instans av ett angivet lokalt cacheminne.

Den Azure App Service Local cache-funktionen tillhandahåller en webbrolls visning av ditt innehåll. Det här innehållet är en Write-to-Discard-cache med ditt lagrings innehåll som skapas asynkront på plats starten. När cachen är klar växlar platsen till att köras mot det cachelagrade innehållet. Appar som körs i lokalt cacheminne har följande fördelar:

* De är immunitet mot fördröjningar som inträffar när de får åtkomst till innehåll på Azure Storage.
* De är immunitet mot planerade uppgraderingar eller oplanerade drift stopp och andra avbrott med Azure Storage som inträffar på servrar som hanterar innehålls resursen.
* De har färre appar som startas om på grund av ändringar i lagrings resurserna.

## <a name="how-the-local-cache-changes-the-behavior-of-app-service"></a>Hur den lokala cachen ändrar beteendet för App Service
* _D:\home_ pekar på det lokala cacheminnet som skapas på den virtuella dator instansen när appen startas. _D:\Local_ fortsätter att peka på den temporära VM-angivna lagringen.
* Den lokala cachen innehåller en engångs kopia av _/installation_ -och _/siteextensions_ -mapparna i det delade innehålls arkivet, på _D:\home\site_ respektive _D:\home\siteextensions_. Filerna kopieras till den lokala cachen när appen startar. Storleken på de två mapparna för varje app är begränsad till 1 GB som standard, men den kan ökas till 2 GB. Observera att om cachestorleken ökar, tar det längre tid att läsa in cacheminnet. Om du har ökat gränsen för den lokala cachen till 2 GB och de kopierade filerna överskrider den maximala storleken på 2 GB, ignorerar App Service tyst den lokala cachen och läser från den fjärranslutna fil resursen. Om det inte finns någon gräns definierad eller om gränsen har angetts till något lägre än 2 GB och de kopierade filerna överskrider gränsen, kan distributionen eller växlingen Miss lyckas med ett fel.
* Den lokala cachen har Läs-och Skriv behörighet. Alla ändringar tas dock bort när appen flyttar virtuella datorer eller startas om. Använd inte det lokala cacheminnet för appar som lagrar verksamhets kritiska data i innehålls lagringen.
* _D:\home\LogFiles_ och _D:\home\Data_ innehåller loggfiler och appdata. De två undermapparna lagras lokalt på den virtuella dator instansen och kopieras till den delade innehålls lagringen med jämna mellanrum. Appar kan spara loggfiler och data genom att skriva dem till dessa mappar. Kopieringen till den delade innehålls lagringen är dock bästa möjliga, så det är möjligt att loggfiler och data går förlorade på grund av en plötslig krasch i en VM-instans.
* [Logg strömning](troubleshoot-diagnostic-logs.md#stream-logs) påverkas av den bästa kopieringen. Du kan se en fördröjning på en minut i strömmarna loggar.
* I det delade innehålls arkivet finns en ändring i mappstrukturen för _loggfiler_ och datamappar för  appar som använder den lokala cachen. Det finns nu undermappar som följer namngivnings mönstret för "unik identifierare" + tidsstämpel. Var och en av undermapparna motsvarar en VM-instans där appen körs eller har körts.
* Andra mappar i _D:\home_ finns kvar i det lokala cacheminnet och kopieras inte till den delade innehålls lagringen.
* Program distribution via en metod som stöds publicerar direkt till den varaktigt delade innehålls lagringen. Om du vill uppdatera _D:\home\site_ -och _D:\home\siteextensions_ -mapparna i det lokala cacheminnet måste appen startas om. Om du vill göra livs cykeln sömlös kan du läsa mer i informationen längre fram i den här artikeln.
* Standard innehålls visningen av SCM-platsen fortsätter att vara den som finns i det delade innehålls arkivet.

## <a name="enable-local-cache-in-app-service"></a>Aktivera lokal cache i App Service 

> [!NOTE]
> Lokalt cacheminne stöds inte på **F1** -eller **D1** -nivån. 

Du konfigurerar lokal cache genom att använda en kombination av reserverade appinställningar. Du kan konfigurera dessa inställningar för appar med hjälp av följande metoder:

* [Azure-portalen](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Konfigurera lokal cache med hjälp av Azure Portal
<a name="Configure-Local-Cache-Portal"></a>

Du aktiverar lokal cache per webb-app genom att använda den här inställningen för appen: `WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Azure Portal app-inställningar: lokalt cacheminne](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Konfigurera lokal cache med hjälp av Azure Resource Manager
<a name="Configure-Local-Cache-ARM"></a>

```json

...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],

    "properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "1000"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>Ändra storleks inställningen i lokalt cacheminne
Som standard är den lokala cachestorleken **1 GB**. Detta omfattar de/installation-och/siteextensions-mappar som kopieras från innehålls arkivet, samt alla lokalt skapade loggar och datamappar. Använd appens inställning för att öka den här gränsen `WEBSITE_LOCAL_CACHE_SIZEINMB` . Du kan öka storleken upp till **2 GB** (2000 MB) per app. Observera att det tar längre tid att läsa in den lokala cachen när storleken ökar.

## <a name="best-practices-for-using-app-service-local-cache"></a>Metod tips för att använda App Service lokal cache
Vi rekommenderar att du använder lokal cache tillsammans med funktionen för [mellanlagrings miljöer](../app-service/deploy-staging-slots.md) .

* Lägg till inställningen för *tröga* appar `WEBSITE_LOCAL_CACHE_OPTION` med värdet `Always` till **produktions** platsen. Om du använder `WEBSITE_LOCAL_CACHE_SIZEINMB` , kan du även lägga till den som en trög inställning för din produktions plats.
* Skapa en **mellanlagringsplats** och publicera på mellanlagrings platsen. Normalt anger du inte mellanlagringsplatsen för att använda lokal cache för att möjliggöra en sömlös bygge-distribution-test-livscykel för mellanlagring om du får fördelarna med lokal cache för produktions platsen.
* Testa din webbplats mot mellanlagrings platsen.  
* När du är klar utfärdar du en [växlings åtgärd](../app-service/deploy-staging-slots.md#Swap) mellan dina mellanlagrings-och produktions platser.  
* Tröga inställningar är namn och fästis till en plats. Så när mellanlagringsplatsen har växlats till produktion, ärver den de lokala cache-apparna. Den nyligen utbytta produktions platsen kommer att köras mot den lokala cachen efter några minuter och kommer att värmas upp som en del av plats uppvärmnings efter växling. När plats växlingen är klar körs din produktions plats mot det lokala cacheminnet.

## <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ)

### <a name="how-can-i-tell-if-local-cache-applies-to-my-app"></a>Hur kan jag se om det lokala cacheminnet gäller för min app?
Om din app behöver ett högpresterande, tillförlitligt innehålls lager, använder inte innehålls lagringen för att skriva kritiska data vid körning och är mindre än 2 GB i Total storlek, och svaret är "Ja"! Om du vill få Total storlek på dina/installation-och/siteextensions-mappar kan du använda webbplats tillägget "Azure Web Apps disk Usage".

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Hur vet jag om min webbplats har växlat till att använda det lokala cacheminnet?
Om du använder funktionen Local cache med mellanlagrings miljöer slutförs inte växlings åtgärden förrän den lokala cachen har värmts upp. Om du vill kontrol lera om platsen körs mot lokal cache kan du kontrol lera variabeln för arbets process miljön `WEBSITE_LOCALCACHE_READY` . Använd instruktionerna på sidan [arbets process miljö variabel](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) för att komma åt variabeln för arbets process miljön på flera instanser.  

### <a name="i-just-published-new-changes-but-my-app-does-not-seem-to-have-them-why"></a>Jag publicerade bara nya ändringar, men appen verkar inte ha dem. Varför?
Om din app använder lokal cache måste du starta om platsen för att få de senaste ändringarna. Vill du inte publicera ändringar på en produktions plats? Se plats alternativen i avsnittet tidigare metod tips.

> [!NOTE]
> Distributions alternativet [Kör från paket](deploy-run-package.md) är inte kompatibelt med Local cache.

### <a name="where-are-my-logs"></a>Var finns mina loggar?
Med lokal cache ser dina loggar och datamapparna lite annorlunda ut. Strukturen i undermapparna förblir dock densamma, förutom att undermapparna är Nestled under en undermapp med formatet "unik VM-identifierare" + tidsstämpel.

### <a name="i-have-local-cache-enabled-but-my--app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Lokal cache är aktive rad, men appen startas fortfarande om. Vad beror det på? Jag trodde lokal cache som hjälpte med att starta om appar.
Lokalt cacheminne bidrar till att förhindra att Storage-relaterade appar startas om. Din app kan dock fortfarande genomgå omstart under uppgraderingen av planerade infrastrukturer på den virtuella datorn. Den övergripande appen startas om och du får färre lokala cache-aktiverade.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>Utesluter lokal cache eventuella kataloger från att kopieras till den snabbare lokala enheten?
Som en del av det steg som kopierar lagrings innehållet undantas alla mappar som heter lagrings plats. Detta hjälper till med scenarier där ditt webbplats innehåll kan innehålla en lagrings plats för käll kontroll som kanske inte behövs under den dagliga driften av appen. 

### <a name="how-to-flush-the-local-cache-logs-after-a-site-management-operation"></a>Hur tömmer du de lokala cache-loggarna efter en plats hanterings åtgärd?
Stoppa och starta om appen om du vill tömma de lokala cache-loggarna. Den här åtgärden rensar det gamla cacheminnet. 
