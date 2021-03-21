---
title: Översikt över DevOps starter för Azure | Microsoft Docs
description: Förstå värdet för DevOps starter
services: devops-project
documentationcenter: ''
author: mlearned
manager: gwallace
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/24/2020
ms.author: mlearned
ms.openlocfilehash: 7c36539ef5be503b2cb7e14047e596522ef8e962
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92330656"
---
# <a name="overview-of-devops-starter"></a>Översikt över DevOps starter

 DevOps starter gör det enkelt att komma igång med Azure med hjälp av antingen GitHub-åtgärder eller Azure DevOps. Det hjälper dig att distribuera din favoritapp på den Azure-tjänst du föredrar med några snabba steg i Azure-portalen. 

 DevOps starter ställer in allt du behöver för att utveckla, distribuera och övervaka ditt program. Du kan använda instrument panelen för DevOps starter för att övervaka kod skrivningar,-versioner och-distributioner, allt från en enda vy i Azure Portal.

## <a name="advantages-of-using-devops-starter"></a>Fördelar med att använda DevOps starter

  DevOps Starter har stöd för två CI/CD-providrar för att automatisera distributioner
  * [GitHub Actions](https://github.com/features/actions)
  * [Azure DevOps](https://azure.microsoft.com/services/devops)

  DevOps starter automatiserar installationen av en hel kontinuerlig integrering (CI) och kontinuerlig leverans (CD) för ditt program till Azure.  Du kan börja med befintlig kod eller använda en av de exempelprogram som tillhandahålls. Sedan kan du snabbt distribuera programmet till olika Azure-tjänster såsom virtuella datorer, App Service, Azure Kubernetes Service (AKS), Azure SQL Database och Azure Service Fabric.  

  DevOps starter utför allt arbete för den inledande konfigurationen av en DevOps-pipeline, inklusive allt från att ställa in den första git-lagringsplatsen, konfigurera CI/CD-pipeline, skapa en Application Insights-resurs för övervakning och tillhandahålla en enda vy av hela lösningen med att skapa en DevOps starter-instrumentpanel i Azure Portal.

Du kan använda DevOps starter för att:

* Snabbt distribuera ditt program till Azure
* Automatisera konfigurationen av ett CI/CD-arbetsflöde eller en pipeline
* Visa och förstå hur du konfigurerar ett CI/CD-arbetsflöde eller en pipeline korrekt
* Ytterligare anpassa versionspipelinen baserat på dina specifika scenarier

## <a name="how-to-use-devops-starter"></a>Hur använder jag DevOps starter?

  DevOps Starter är tillgängligt från Azure Portal. Du skapar en DevOps start resurs precis som du skapar andra Azure-resurser från portalen. DevOps Projects innehåller en stegvis guide för de olika konfigurationsalternativen.  

Du kan välja flera konfigurationsalternativ som en del av den initiala installationen. Dessa alternativ är:

* Välja din att prioritera CI/CD-providern
* Använda den tillhandahållna exempel appen eller att ta med din egen kod (endast för Azure-DevOps)
* Välja ett appspråk
* Välja ett appramverk utifrån språket
* Välja en Azure-tjänst (distributionsmål)
* Välj din GitHub-eller Azure DevOps-organisation
* Välja en Azure-prenumeration
* Välja en plats för Azure-tjänsterna
* Välja bland olika prisnivåer för Azure-tjänster

När du har skapat dina DevOps-startstarter kan du:

* Anpassa ditt GitHub-arbetsflöde eller Azure DevOps-pipeline
* Använda pull-förfrågningar för att hantera ditt kodflöde och hålla en hög kvalitet
* Testa och bygga varje incheckning innan du slår samman koden för att höja kvalitetsribban

När du har använt DevOps Starter kan du också ta bort alla resurser från en enda plats från DevOps starter-instrumentpanelen på Azure Portal.

## <a name="devops-starter-and-github-integration"></a>DevOps start och GitHub-integrering

DevOps starter stöder nu GitHub-åtgärder som en CI/CD-Provider. Den automatiserar allt arbete som behövs i GitHub för att skapa ett CI/CD-arbetsflöde med GitHub-åtgärder. Den skapar en GitHub-lagringsplats i en befintlig GitHub-organisation och genomför sedan ett exempel program till den nya GitHub-lagringsplatsen.  

Automation skapar också en utlösare för arbets flödet så att varje ny kod bekräftelse initierar ett bygg-och distributions jobb i arbets flödet. Programmet distribueras till valfri Azure-tjänst. GitHub-arbetsflödet kan anpassas för ytterligare scenarier. 

## <a name="devops-starter-and-azure-devops-integration"></a>DevOps starter och Azure DevOps-integrering

DevOps starter med Azure DevOps automatiserar allt arbete som behövs i Azure-pipeliner för att konfigurera en CI/CD-pipeline. Det skapar en Git-lagringsplats i en ny eller befintlig Azure DevOps-organisation och checkar sedan in ett exempelprogram eller din befintliga kod till en ny Git-lagringsplats.  

Automatiseringen upprättar även en CI-utlösare för versionen så att varje ny kodincheckning initierar en version. DevOps starter skapar en CD-utlösare och distribuerar varje ny lyckad version till den Azure-tjänst du väljer.  

Versionspipelines kan anpassas för ytterligare scenarier. Dessutom kan du klona versions- och byggpipelines för användning i andra projekt.

## <a name="getting-started-with-devops-starter"></a>Komma igång med DevOps starter

* [Kom igång med DevOps starter](./azure-devops-project-github.md)

##  <a name="devops-starter-videos"></a>DevOps starter-videor

* [Skapa CI/CD med Azure DevOps starter](https://www.youtube.com/watch?v=NuYDAs3kNV8)
