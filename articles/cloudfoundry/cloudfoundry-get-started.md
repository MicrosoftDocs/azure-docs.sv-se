---
title: Komma igång med Cloud Foundry på Microsoft Azure
description: Kör OSS eller pivotal Cloud Foundry på Microsoft Azure
author: seanmck
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: d3b13dffcf251f79a04182d32f23fd40e6ed6044
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562902"
---
# <a name="cloud-foundry-on-azure"></a>Cloud Foundry på Azure

Cloud Foundry är en plattform som en tjänst (PaaS) med öppen källkod som finns för att skapa, distribuera och använda 12-faktorsprogram som utvecklats i olika språk och ramverk. I det här dokumentet beskrivs de alternativ som du kan använda för att köra Cloud Foundry på Azure och hur du kan komma igång.

## <a name="cloud-foundry-offerings"></a>Cloud Foundry erbjudanden

Det finns två former av Cloud Foundry tillgängliga för körning på Azure: Cloud Foundry med öppen källkod (OSS CF) och pivotal Cloud Foundry (PCF). OSS CF är en helt [öppen käll](https://github.com/cloudfoundry) version av Cloud Foundry som hanteras av Cloud Foundry Foundation. Pivotal Cloud Foundry är en företags distribution av Cloud Foundry från pivoting Software Inc. Vi tittar på några av skillnaderna mellan de två erbjudandena.

### <a name="open-source-cloud-foundry"></a>Cloud Foundry med öppen källkod

Du kan distribuera OSS Cloud Foundry på Azure genom att först distribuera en BOSH-regissör och sedan distribuera Cloud Foundry med hjälp av [anvisningarna på GitHub](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md). Mer information om hur du använder OSS CF finns i [dokumentationen](https://docs.cloudfoundry.org/) från Cloud Foundry Foundation.

Microsoft tillhandahåller bästa möjliga support för OSS CF genom följande community-kanaler:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slack"></a>Bosh – Azure-CPI-kanal på [Cloud Foundry slack](https://slack.cloudfoundry.org/)
- [CF-Bosh e-postlista](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- GitHub problem för [CPI](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) och [Service Broker](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> Support nivån för dina Azure-resurser, till exempel de virtuella datorer där du kör Cloud Foundry, baseras på ditt support avtal för Azure. Support för bästa möjliga community gäller endast för de Cloud Foundry-komponenter.

### <a name="pivotal-cloud-foundry"></a>Pivotal Cloud Foundry

Pivotal Cloud Foundry innehåller samma kärn plattform som OSS-distributionen, tillsammans med en uppsättning patentskyddade hanterings verktyg och Enterprise-support. Om du vill köra PCF på Azure måste du skaffa en licens från pivotal. PCF-erbjudandet från Azure Marketplace innehåller en utvärderings licens på 90 dagar.

Verktygen inkluderar [pivotal Operations Manager](https://docs.pivotal.io/pivotalcf/customizing/), ett webb program som fören klar distributionen och hanteringen av en Cloud Foundry Foundation och en [pivoting Apps Manager](https://docs.pivotal.io/pivotalcf/console/), ett webb program för att hantera användare och program.

Förutom de Support kanaler som anges för OSS CF ovan får du en PCF-licens som du kan använda för att kontakta pivotering för support. Microsoft och pivotal har också aktiverat stöd arbets flöden som gör att du kan kontakta någon av parterna för att få hjälp och låta din förfrågan dirigeras på rätt sätt beroende på var problemet ligger.

## <a name="azure-service-broker"></a>Azure-Service Broker

Cloud Foundry uppmuntrar till ["12-Factor app"](https://12factor.net/) -metodik, som främjar en ren separation av tillstånds lösa program processer och tillstånds känsliga tjänster för säkerhets kopiering. [Service mäklare](https://docs.cloudfoundry.org/services/api.html) ger ett konsekvent sätt att etablera och binda tjänster till program. [Azure Service Broker](https://github.com/Azure/meta-azure-service-broker) innehåller några av de viktigaste Azure-tjänsterna via den här kanalen, inklusive Azure Storage och Azure SQL.

Om du använder pivotal Cloud Foundry är Service Broker också [tillgänglig som en panel](https://docs.pivotal.io/azure-sb/installing.html) från Pivot-nätet.

## <a name="related-resources"></a>Relaterade resurser

### <a name="azure-devops-services-plugin"></a>Plugin-program för Azure DevOps Services

Cloud Foundry lämpar sig väl för flexibel program utveckling, inklusive användning av kontinuerlig integrering (CI) och kontinuerlig leverans (CD). Om du använder Azure DevOps Services för att hantera dina projekt och vill ställa in en CI/CD-pipeline som mål Cloud Foundry kan du använda [Azure DevOps Services Cloud Foundry build-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension). Plugin-programmet gör det enkelt att konfigurera och automatisera distributioner till Cloud Foundry, oavsett om de körs i Azure eller någon annan miljö.

## <a name="next-steps"></a>Nästa steg

- [Distribuera pivotal Cloud Foundry från Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)
- [Distribuera en app till Cloud Foundry i Azure](./cloudfoundry-deploy-your-first-app.md)
