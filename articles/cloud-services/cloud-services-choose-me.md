---
title: Vad är Azure Cloud Services (klassisk) | Microsoft Docs
description: Lär dig mer om vad Azure Cloud Services är, särskilt att det är utformat för att stödja program som är skalbara, tillförlitliga och billiga att fungera.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: cbb9aae57b952c05aa722f81309158a11aef826b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98742819"
---
# <a name="overview-of-azure-cloud-services-classic"></a>Översikt över Azure Cloud Services (klassisk)

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Azure Cloud Services är ett exempel på en [plattform som en tjänst](https://azure.microsoft.com/overview/what-is-paas/) (PaaS). Precis som [Azure App Service](../app-service/overview.md)är den här tekniken utformad för att stödja program som är skalbara, tillförlitliga och billiga att fungera. På samma sätt som App Service finns på virtuella datorer (VM: ar) så är det också för Azure Cloud Services. Men du har större kontroll över de virtuella datorerna. Du kan installera din egen program vara på virtuella datorer som använder Azure Cloud Services och du kan komma åt dem via en fjärr anslutning.

![Azure Cloud Services-diagram](./media/cloud-services-choose-me/diagram.png)

Mer kontroll innebär också mindre enkel användning. Om du inte behöver de ytterligare kontroll alternativen är det vanligt vis snabbare och enklare att komma igång med ett webb program i Web Apps funktionen i App Service jämfört med Azure Cloud Services.

Det finns två typer av Azure Cloud Services-roller. Den enda skillnaden mellan två är hur din roll finns på de virtuella datorerna:

* **Webb roll**: distribuerar och är värd för din app automatiskt via IIS.

* **Arbets roll**: använder inte IIS och kör fristående app.

Ett enkelt program kan till exempel bara använda en enda webb roll som betjänar en webbplats. Ett mer komplext program kan använda en webb roll för att hantera inkommande begär Anden från användare och sedan skicka dessa förfrågningar till en arbets roll för bearbetning. (Den här kommunikationen kan använda [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) eller [Azure Queue Storage](../storage/common/storage-introduction.md).)

Som föregående bild föreslår alla virtuella datorer i ett enda program i samma moln tjänst. Användare får åtkomst till programmet via en enskild offentlig IP-adress, med begär Anden som automatiskt belastnings utjämning i programmets virtuella datorer. Plattformen [skalar och distribuerar](cloud-services-how-to-scale-portal.md) de virtuella datorerna i ett Azure Cloud Services-program på ett sätt som gör det möjligt att undvika en enskild maskin varu krasch.

Även om program körs på virtuella datorer är det viktigt att förstå att Azure Cloud Services tillhandahåller PaaS, inte infrastruktur som en tjänst (IaaS). Här är ett sätt att tänka på det. Med IaaS, till exempel Azure Virtual Machines, skapar du först och konfigurerar miljön som programmet körs i. Sedan distribuerar du ditt program till den här miljön. Du ansvarar för att hantera mycket av den här världen, genom att göra saker som att distribuera nya versioner av operativ systemet i varje virtuell dator. I PaaS är det däremot som om miljön redan finns. Allt du behöver göra är att distribuera ditt program. Hantering av den plattform som den körs på, inklusive distribution av nya versioner av operativ systemet, hanteras åt dig.

## <a name="scaling-and-management"></a>Skalning och hantering
Med Azure Cloud Services skapar du inte virtuella datorer. I stället kan du ange en konfigurations fil som talar om för Azure hur många av vart som helst, till exempel "tre webb Rolls instanser" och "två arbets Rolls instanser". Plattformen skapar sedan dem åt dig. Du kan fortfarande välja [vilken storlek](cloud-services-sizes-specs.md) som de virtuella datorerna ska vara, men du behöver inte uttryckligen skapa dem själv. Om ditt program behöver hantera en större belastning kan du be om fler virtuella datorer och Azure skapar dessa instanser. Om belastningen minskar kan du stänga av instanserna och sluta betala för dem.

Ett Azure Cloud Services-program görs vanligt vis tillgängligt för användare via en två stegs process. En utvecklare [överför först programmet](cloud-services-how-to-create-deploy-portal.md) till plattformens mellanlagringsområde. När utvecklaren är redo att göra programmet Live använder de Azure Portal för att växla mellan mellanlagring med produktion. Den här [växeln mellan mellanlagring och produktion](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) kan göras utan avbrott, vilket gör att ett program som körs kan uppgraderas till en ny version utan att användarna störs.

## <a name="monitoring"></a>Övervakning
Azure Cloud Services tillhandahåller också övervakning. Precis som Virtual Machines identifierar den en felaktig fysisk server och startar om de virtuella datorer som kördes på den servern på en ny dator. Men Azure Cloud Services identifierar även misslyckade virtuella datorer och program, inte bara maskin varu fel. Till skillnad från Virtual Machines har den en agent i varje webb-och arbets roll och kan därför starta nya virtuella datorer och program instanser när fel inträffar.

PaaS-karaktären hos Azure Cloud Services har också andra konsekvenser. En av de viktigaste är att program som bygger på den här tekniken skrivs för att köras korrekt när en webb-eller arbets roll instans Miss lyckas. För att uppnå detta bör ett Azure Cloud Services-program inte behålla tillstånd i fil systemet på sina egna virtuella datorer. Till skillnad från virtuella datorer som har skapats med Virtual Machines, är skrivningar som gjorts till Azure Cloud Services VM: ar inte permanenta. Det finns inget som Virtual Machines data disk. I stället bör ett Azure Cloud Services-program explicit skriva alla tillstånd till Azure SQL Database, blobbar, tabeller eller någon annan extern lagrings plats. Genom att skapa program på det här sättet blir det enklare att skala och mer motstånds kraft mot haverier, som är både viktiga mål för Azure Cloud Services.

## <a name="next-steps"></a>Nästa steg
* [Skapa en Cloud Service-app i .NET](cloud-services-dotnet-get-started.md) 
* [Skapa en Cloud Service-app i Node.js](cloud-services-nodejs-develop-deploy-app.md) 
* [Skapa en Cloud Service-app i PHP](../cloud-services-php-create-web-role.md) 
* [Skapa en Cloud Service-app i python](cloud-services-python-ptvs.md)






