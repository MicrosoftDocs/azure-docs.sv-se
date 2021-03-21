---
title: Optimera Azure CDN för typen av innehålls leverans
description: Lär dig hur Azure Content Delivery Network kan optimera leverans baserat på typ av innehåll. Bästa metoder för optimering ger bättre prestanda och ursprunglig avlastning.
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 03/25/2019
ms.author: allensu
ms.openlocfilehash: 3d207ee09a76509a65a2645515b182f8d92753b0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387928"
---
# <a name="optimize-azure-cdn-for-the-type-of-content-delivery"></a>Optimera Azure CDN för typen av innehålls leverans

När du levererar innehåll till en stor global publik är det viktigt att säkerställa optimerad leverans av ditt innehåll. [Azure Content Delivery Network (CDN)](cdn-overview.md) kan optimera leverans upplevelsen baserat på den typ av innehåll som du har. Innehållet kan vara en webbplats, en real tids ström, en video eller en stor fil för hämtning. När du skapar en CDN-slutpunkt anger du ett scenario i alternativet **optimerad för** . Ditt val bestämmer vilken optimering som ska användas för det innehåll som levereras från CDN-slutpunkten.

Optimerings alternativ är utformade för att förbättra prestanda för innehåll och få bättre ursprungs avlastning. Dina scenario alternativ påverkar prestanda genom att ändra konfigurationer för partiell cachelagring, objekt segment och återförsök för ursprungs fel. 

Den här artikeln innehåller en översikt över olika optimerings funktioner och när du ska använda dem. Mer information om funktioner och begränsningar finns i respektive artiklar om varje enskild optimerings typ.

> [!NOTE]
> När du skapar en CDN-slutpunkt kan de **optimerade** alternativen variera beroende på vilken typ av profil som slut punkten skapas i. Azure CDN leverantörer tillämpar förbättringar på olika sätt, beroende på scenariot. 

## <a name="provider-options"></a>Leverantörs alternativ

**Azure CDN Standard från Microsoft** -profiler stöder följande optimeringar:

* [Allmän webb leverans](#general-web-delivery). Den här optimeringen används också för medie direkt uppspelning och stor fil hämtning.

> [!NOTE]
> Den dynamiska webbplats accelerationen från Microsoft erbjuds via [Azures frontend-tjänst](../frontdoor/front-door-overview.md).

**Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon** -profiler stöder följande optimeringar:

* [Allmän webb leverans](#general-web-delivery). Den här optimeringen används också för medie direkt uppspelning och stor fil hämtning.

* [Acceleration av dynamisk webbplats](#dynamic-site-acceleration) 


**Azure CDN Standard från Akamai** -profiler stöder följande optimeringar:

* [Allmän webb leverans](#general-web-delivery) 

* [Allmän medie direkt uppspelning](#general-media-streaming)

* [Video-on-demand-mediedirektuppspelning](#video-on-demand-media-streaming)

* [Hämtning av stora filer](#large-file-download)

* [Acceleration av dynamisk webbplats](#dynamic-site-acceleration) 

Microsoft rekommenderar att du testar prestanda variationer mellan olika leverantörer för att välja den optimala leverantören för leveransen.

## <a name="select-and-configure-optimization-types"></a>Välj och konfigurera optimerings typer

När du skapar en CDN-slutpunkt väljer du en optimerings typ som bäst matchar det scenario och den typ av innehåll som du vill att slut punkten ska leverera. 

**Allmän webb leverans** är standard valet. Du kan bara uppdatera **Azure CDN Standard från optimering av Akamai** -slutpunkter när som helst. 

För **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon**, kan du inte.

1. Välj en slut punkt i **Azure CDns profil för standard från Akamai** .

    ![Slut punkts val](./media/cdn-optimization-overview/01_Akamai.png)

2. Under Inställningar väljer du **optimering**. Välj sedan en typ från List rutan **optimerad för** .

    ![Optimering och typ val](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Optimering för vissa scenarier

Du kan optimera CDN-slutpunkten för något av dessa scenarier. 

### <a name="general-web-delivery"></a>Allmän webb leverans

Allmän webb leverans är det vanligaste optimerings alternativet. Den är utformad för allmän optimering av webb innehåll, till exempel webb sidor och webb program. Den här optimeringen kan också användas för hämtning av filer och filer.

En typisk webbplats innehåller statiskt och dynamiskt innehåll. Statiskt innehåll inkluderar bilder, JavaScript-bibliotek och formatmallar som kan cachelagras och levereras till olika användare. Dynamiskt innehåll anpassas för en enskild användare, till exempel nyhets objekt som är skräddarsydda för en användar profil. Dynamiskt innehåll, till exempel shopping vagns innehåll, cachelagras inte eftersom det är unikt för varje användare. Allmän webb leverans kan optimera hela webbplatsen. 

> [!NOTE]
> Om du använder en **Azure CDN Standard från Akamai** profil väljer du den här optimerings typen om den genomsnittliga fil storleken är mindre än 10 MB. Annars, om den genomsnittliga fil storleken är större än 10 MB, väljer du **stor fil nedladdning** från List rutan **optimerad för** .

### <a name="general-media-streaming"></a>Allmän medie direkt uppspelning

Om du behöver använda slut punkten för direkt uppspelning och video på begäran, väljer du optimerings typen allmän medie uppspelning.

Medie direkt uppspelning är tids känsligt, eftersom paket som kommer sent på klienten, till exempel frekvent buffring av video innehåll, kan orsaka en försämrad visnings upplevelse. Optimering av medie direkt uppspelning minskar svars tiden för leverans av medie innehåll och ger användarna en smidig strömmande upplevelse. 

Det här scenariot är vanligt för Azure Media service-kunder. När du använder Azure Media Services får du en enda slut punkt för direkt uppspelning som kan användas för både Live-och on-demand-direktuppspelning. I det här scenariot behöver kunderna inte växla till en annan slut punkt när de ändras från Live till strömning på begäran. Optimering av generell medie direkt uppspelning stöder den här typen av scenario.

För **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon**, använder du den allmänna optimerings typen för webb leverans för att leverera allmänt direktuppspelande medie innehåll.

Mer information om optimering av medie direkt uppspelning finns i [optimering av medie direkt uppspelning](cdn-media-streaming-optimization.md).

### <a name="video-on-demand-media-streaming"></a>Video-on-demand-mediedirektuppspelning

Video-on-demand-optimering av medie direkt uppspelning förbättrar innehåll på begäran. Använd det här alternativet om du använder en slut punkt för direkt uppspelning av video på begäran.

För **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon** -profiler, använder du den allmänna webb leverans optimerings typen för att leverera innehåll på begäran strömmande media.

Mer information om optimering av medie direkt uppspelning finns i [optimering av medie direkt uppspelning](cdn-media-streaming-optimization.md).

> [!NOTE]
> Om CDN-slutpunkten huvudsakligen hanterar video på begäran, använder du den här optimerings typen. Den största skillnaden mellan den här optimerings typen och den allmänna optimerings typen för medie uppspelning är timeout för anslutnings försök. Timeout-tiden är mycket kortare för att fungera med direkt uppspelnings scenarier.
>

### <a name="large-file-download"></a>Hämtning av stora filer

För **Azure CDN Standard från Akamai** -profiler optimeras stora fil hämtningar för innehåll som är större än 10 MB. Om den genomsnittliga fil storleken är mindre än 10 MB använder du allmän webb leverans. Om dina genomsnittliga fil storlekar är konsekvent större än 10 MB, kan det vara mer effektivt att skapa en separat slut punkt för stora filer. Till exempel är inbyggd program vara eller program uppdateringar vanligt vis stora filer. För att leverera filer som är större än 1,8 GB krävs den stora optimeringen av fil hämtning.

För **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon** -profiler, använder du den allmänna optimerings typen för webb leverans för att leverera innehåll med stor fil hämtning. Det finns ingen begränsning för fil hämtnings storlek.

Mer information om optimering av stora filer finns i [optimering av stora filer](cdn-large-file-optimization.md).

### <a name="dynamic-site-acceleration"></a>Acceleration av dynamisk webbplats

 Tjänsten för dynamisk webbplats acceleration (DSA) är tillgänglig för **Azure CDN Standard från Akamai**, **Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon** -profiler. Den här optimeringen innebär en ytterligare avgift att använda. Mer information finns i [Content Delivery Network prissättning](https://azure.microsoft.com/pricing/details/cdn/).

> [!NOTE]
> Den dynamiska webbplats accelerationen från Microsoft erbjuds via [Azures frontend-tjänst](../frontdoor/front-door-overview.md) , som är en global [anycast](https://en.wikipedia.org/wiki/Anycast) -tjänst som utnyttjar Microsofts privata globala nätverk för att leverera dina app-arbetsbelastningar.

DSA innehåller olika metoder som drar svars tid och prestanda för dynamiskt innehåll. Tekniker inkluderar Routning och nätverks optimering, TCP-optimering med mera. 

Du kan använda den här optimeringen för att påskynda en webbapp som innehåller många svar som inte kan cachelagras. Exempel är Sök resultat, check transaktioner eller real tids data. Du kan fortsätta att använda Core Azure CDN caching-funktioner för statiska data. 

Mer information om acceleration för dynamiska platser finns i [acceleration av dynamisk webbplats](cdn-dynamic-site-acceleration.md).