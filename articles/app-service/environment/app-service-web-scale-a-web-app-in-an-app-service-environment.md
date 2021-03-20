---
title: Skala en app i ASE v1
description: Skala en app i en App Service-miljön. Detta dokument tillhandahålls endast för kunder som använder den äldre v1-ASE.
author: ccompy
ms.assetid: 78eb1e49-4fcd-49e7-b3c7-f1906f0f22e3
ms.topic: article
ms.date: 10/17/2016
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0e665ec27da0a898e754817f946b965ac7360fda
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86220566"
---
# <a name="scaling-apps-in-an-app-service-environment-v1"></a>Skala appar i en App Service-miljön v1
I Azure App Service finns det vanligt vis tre saker du kan skala:

* pris plan
* arbets storlek 
* antal instanser.

I en ASE behöver du inte välja eller ändra pris planen.  Vad gäller funktioner finns det redan på pris nivån Premium.  

Med hänsyn till arbetarnas storlekar kan ASE-administratören tilldela storleken på den beräknings resurs som ska användas för varje arbets grupp.  Det innebär att du kan ha Worker pool 1 med P4 Compute-resurser och Worker-pool 2 med P1-beräknings resurser, om så önskas.  De behöver inte vara i storleks ordning.  Mer information kring storlekarna och deras priser finns i dokumentet här [Azure App Service prissättning][AppServicePricing].  Detta gör att skalnings alternativen för webbappar och App Service-planer i en App Service-miljön:

* Val av arbets grupp
* antal instanser

Att ändra något av objekten görs via det gränssnitt som visas för dina ASE-App Service planer.  

![Skärm bild som visar var du kan se information om Scale service-planen och service planen för arbets gruppen.][1]

Du kan inte skala upp ditt ASP-minne utöver antalet tillgängliga beräknings resurser i den arbets grupp som din ASP är i.  Om du behöver beräknings resurser i den arbets gruppen måste du be ASE-administratören att lägga till dem.  Information om hur du konfigurerar om ASE finns i informationen här: [så här konfigurerar du en app service miljö][HowtoConfigureASE].  Du kanske också vill dra nytta av funktionerna för autoskalning i ASE för att lägga till kapacitet utifrån schema eller mått.  Mer information om hur du konfigurerar autoskalning för ASE-miljön finns i [så här konfigurerar du autoskalning för en app service-miljön][ASEAutoscale].

Du kan skapa flera App Service-planer med hjälp av beräknings resurser från olika arbetspooler, eller så kan du använda samma arbets grupp.  Om du till exempel har (10) tillgängliga beräknings resurser i Worker-pool 1 kan du välja att skapa en app service-plan med hjälp av (6) beräknings resurser och en andra App Service-plan som använder (4) beräknings resurser.

### <a name="scaling-the-number-of-instances"></a>Skala antalet instanser
Första gången du skapar en webbapp i en App Service-miljön börjar den med 1 instans.  Du kan sedan skala ut till ytterligare instanser för att tillhandahålla ytterligare beräknings resurser för din app.   

Om din ASE har tillräckligt med kapacitet är detta ganska enkelt.  Du går till App Services plan som innehåller de platser som du vill skala upp och välja skala.  Detta öppnar användar gränssnittet där du kan ställa in skalan manuellt för ASP eller konfigurera regler för autoskalning för ASP.  Om du vill skala din app manuellt anger du ***skala enligt** _ till _ *_ett instans antal som jag anger manuellt_* *.  Härifrån kan du antingen dra skjutreglaget till önskad kvantitet eller ange det i rutan bredvid skjutreglaget.  

![Skärm bild som visar var du kan ställa in skalningen för ASP eller konfigurera regler för autoskalning för din ASP.][2] 

Reglerna för autoskalning för en ASP-ASE fungerar på samma sätt som de gör normalt.  Du kan välja ***CPU-procent** _ under _*_skala efter_*_ och skapa regler för autoskalning för din ASP baserat på processor procent, eller så kan du skapa mer komplexa regler med _ *_schema-och prestanda regler_* *.  Om du vill se mer information om hur du konfigurerar autoskalning använder du guiden här för att [skala en app i Azure App Service][AppScale]. 

### <a name="worker-pool-selection"></a>Val av arbets grupp
Som tidigare nämnts kan valet av arbets grupp nås från ASP-ANVÄNDARGRÄNSSNITTET.  Öppna bladet för den ASP-server som du vill skala och välj arbets grupp.  Du ser alla Worker-pooler som du har konfigurerat i App Service-miljön.  Om du bara har en pool visas bara den enda poolen.  Om du vill ändra vilken resurspool din ASP är i väljer du bara den arbets grupp som du vill att App Service planen ska flyttas till.  

![Skärm bild som visar var du kan ändra vilken resurspool din ASP är i.][3]

Innan du flyttar ASP från en arbets grupp till en annan är det viktigt att se till att du har tillräcklig kapacitet för din ASP.  I listan över Worker-pooler är inte bara namnet på arbets gruppen i listan, men du kan också se hur många arbetare som är tillgängliga i den arbets gruppen.  Kontrol lera att det finns tillräckligt många instanser för att rymma App Services planen.  Om du behöver fler beräknings resurser i den arbets grupp som du vill flytta till kan du be ASE-administratören att lägga till dem.  

> [!NOTE]
> Om du flyttar en ASP-modul från en arbets grupp leder det till att apparna i ASP startas.  Detta kan göra att förfrågningar körs långsamt när appen är kall igång på de nya beräknings resurserna.  Kall start kan undvikas genom att använda [appens värme funktion][AppWarmup] i Azure App Service.  Modulen för programinitiering som beskrivs i artikeln fungerar även för kall start eftersom initierings processen också anropas när appar är kall igång med nya beräknings resurser. 
> 
> 

## <a name="getting-started"></a>Komma igång
Information om hur du kommer igång med App Service miljöer finns i [så här skapar du en app service-miljön][HowtoCreateASE]

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ScaleWebapp]: ../manage-scale-up.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[CreateWebappinASE]: app-service-web-how-to-create-a-web-app-in-an-ase.md
[Appserviceplans]: ../overview-hosting-plans.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[AppScale]: ../manage-scale-up.md
[AppWarmup]: https://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
