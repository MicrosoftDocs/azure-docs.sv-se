---
title: Skapa en webbapp i ASE v1
description: Lär dig hur du skapar webbappar i en App Service-miljön v1. Detta dokument tillhandahålls endast för kunder som använder den äldre v1-ASE.
author: ccompy
ms.assetid: 983ba055-e9e4-495a-9342-fd3708dcc9ac
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: fa92002cfef070218902aa7919900dde9fe5a9b5
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102505417"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>Skapa en webbapp i en App Service-miljön v1

> [!NOTE]
> Den här artikeln gäller App Service-miljön v1.  Det finns en nyare version av App Service-miljön som är enklare att använda och som körs på en kraftfullare infrastruktur. Om du vill veta mer om den nya versionen börjar [du med introduktionen till App Service-miljön](intro.md).
> 

## <a name="overview"></a>Översikt
I den här självstudien visas hur du skapar webbappar och App Service-planer i en [App Service-miljön v1](app-service-app-service-environment-intro.md) (ASE). 

> [!NOTE]
> Om du vill lära dig hur du skapar en webbapp men inte behöver göra det i en App Service-miljön, se [skapa en .NET-webbapp](../quickstart-dotnetcore.md) eller någon av de relaterade självstudierna för andra språk och ramverk.
> 
> 

## <a name="prerequisites"></a>Förutsättningar
I den här självstudien förutsätter vi att du har skapat en App Service-miljön. Om du inte har gjort det ännu kan du läsa [skapa en app service-miljön](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Skapa en webbapp
1. I [Azure-portalen](https://portal.azure.com/)klickar du på **skapa en resurs > webb och mobilt >-webbapp**. 
   
    ![Skärm bild som visar var du kan skapa en webbapp i Azure Portal.][1]
2. Välj din prenumeration.  
   
    Om du har flera prenumerationer som är medvetna om att du ska skapa en app i App Service-miljön måste du använda samma prenumeration som du använde när du skapade miljön. 
3. Välj eller skapa en Resursgrupp.
   
    Med *resurs grupper* kan du hantera relaterade Azure-resurser som en enhet och är användbara när du etablerar *Azure-rollbaserad åtkomst kontroll (Azure RBAC)* regler för dina appar. Mer information finns i [Översikt över Azure Resource Manager][ResourceGroups]. 
4. Välj eller skapa en App Service plan.
   
    *App Service planer* är hanterade uppsättningar av Web Apps.  Normalt när du väljer prissättning tillämpas det pris som debiteras för App Service plan snarare än till de enskilda apparna. I en ASE betalar du för de beräknings instanser som allokeras till ASE i stället för det som du har listat med din ASP.  För att skala upp antalet instanser av en webbapp skalar du upp instanserna av din App Service plan och påverkar alla webbappar i planen.  Vissa funktioner som webbplats platser eller VNET-integration också har begränsningar i planen.  Mer information finns i [Översikt över Azure App Service planer](../overview-hosting-plans.md)
   
    Du kan identifiera App Service planer i din ASE genom att titta på den plats som anges under prenumerations namnet.  
   
    ![Skärm bild som visar var att Visa App Service planer i din ASE.][5]
   
    Om du vill använda en App Service plan som redan finns i App Service-miljön väljer du den planen. Om du vill skapa en ny App Service plan går du till följande avsnitt i den här självstudien och [skapar en app service plan i en app service-miljön](#createplan).
5. Ange namnet på din webbapp och klicka sedan på **skapa**. 
   
    Om din ASE använder en extern VIP är URL: en för en app i en ASE: [*webbplats* namn]. [*namnet på din app service-miljön*]. p.azurewebsites.net i stället för [*webbplats* namn]. azurewebsites.net
   
    Om din ASE använder en intern VIP-adress är URL: en för en app i den ASE: [*webbplats* namn]. [*under domän angavs vid skapande av ASE*]   
    När du har valt ASP när du har skapat ASE visas under domän uppdateringen nedan **namn**

## <a name="create-an-app-service-plan"></a><a name="createplan"></a> Skapa en App Service plan
När du skapar en App Service plan i en App Service-miljön skiljer sig dina arbets val åt eftersom det inte finns några delade arbetare i en ASE.  De anställda som du måste använda är de som har allokerats till ASE av administratören.  Det innebär att du måste ha fler medarbetare som har allokerats till din ASE-pool för att skapa en ny plan än det totala antalet instanser i alla planer som redan finns i arbets gruppen.  Om du inte har tillräckligt många arbetare i ASE Worker-poolen för att skapa din plan måste du arbeta med ASE-administratören för att få dem tillagda.

En annan skillnad med App Service planer som är värd för en App Service-miljön är brist på pris urval.  När du har ett App Service-miljön du betala för beräknings resurser som används av systemet och inte har extra avgifter för planerna i den miljön.  Normalt när du skapar en App Service plan väljer du en pris plan som avgör din fakturering.  En App Service-miljön är i princip en privat plats där du kan skapa innehåll.  Du betalar för miljön och inte är värd för ditt innehåll.

Följande instruktioner visar hur du skapar en App Service plan när du skapar en webbapp enligt beskrivningen i föregående avsnitt i självstudien.

1. Klicka på **Skapa nytt** i användar gränssnittet för plan val och ange ett namn för din plan precis som du normalt skulle hamna utanför en ASE.
2. Välj den ASE som du vill använda från din plats väljare.
   
    Eftersom en App Service-miljön är i princip en privat distributions plats visas den under plats. 
   
    ![Skärm bild som visar plats väljaren för att välja de ASE som du vill använda.][2]
   
    När du har valt en ASE i plats väljaren, uppdateras de App Service plan att skapa användar gränssnitt.  Platsen visar nu namnet på ASE-systemet och regionen som det finns i och pris avtals väljaren ersätts med en väljare för arbetare.  
   
    ![Skärm bild som visar ASE system information när du har valt ASE i plats väljaren.][3]

### <a name="selecting-a-worker-pool"></a>Välja en arbets grupp
Normalt i Azure App Service och utanför en App Service-miljön, finns det 3 beräknings storlekar som är tillgängliga med valet av en dedikerad pris plan.  På samma sätt kan du för en ASE definiera upp till 3 pooler med arbetare och ange den beräknings storlek som används för den arbets gruppen.  Vad det innebär för klienter i ASE är att i stället för att välja en pris plan med beräknings storlek för din App Service plan väljer du vad som kallas för en *arbets grupp*.  

I användar gränssnittet för anslutningspoolen visas den beräknings storlek som används för den arbets gruppen under namnet.  Den tillgängliga kvantiteten avser hur många beräknings instanser som är tillgängliga för användning i poolen.  Den totala poolen kan faktiskt ha fler instanser än det här antalet, men det här värdet avser bara hur många som inte används.  Om du behöver justera App Service-miljön för att lägga till fler beräknings resurser, se [Konfigurera din app service-miljön](app-service-web-configure-an-app-service-environment.md).

![Skärm bild som visar fönstret för arbets gruppen där du kan välja Worker-pooler för ASE.][4]

I det här exemplet ser du bara två tillgängliga arbets grupper. Det beror på att ASE-administratören endast allokerade värdar till de två arbetspoolerna.  Den tredje visas när virtuella datorer allokeras till den.  

## <a name="after-web-app-creation"></a>När webb program har skapats
Det finns några överväganden för att köra webbappar och hantera App Service-planer i en ASE som måste beaktas.  

Som tidigare nämnts är ägaren av ASE ansvarig för systemets storlek och därför ansvarar de också för att se till att det finns tillräckligt med kapacitet för att vara värd för önskade App Services planer. Om det inte finns några tillgängliga arbetare kommer du inte att kunna skapa din App Service plan.  Detta gäller även för att skala upp din webbapp.  Om du behöver fler instanser måste du få App Service-miljön administratören att lägga till fler arbetare.

När du har skapat din webbapp och App Service plan det är en bra idé att skala upp den.  I en ASE måste du alltid ha minst 2 instanser av din App Service plan för att tillhandahålla fel tolerans för dina appar.  Skalning av en App Service plan i en ASE är detsamma som normalt via App Service plan gränssnittet.  Mer information om skalning, [hur du skalar en webbapp i en app service-miljön](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../overview-hosting-plans.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[ResourceGroups]: ../../azure-resource-manager/management/overview.md
[AzurePowershell]: /powershell/azure/