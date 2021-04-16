---
title: Komma igång med Cloud Foundry på Microsoft Azure
description: Kör OSS- eller Pivotal-Cloud Foundry på Microsoft Azure
author: seanmck
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: 2f80f07cad0e82ee35fe71223e1282ea24f791bb
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530932"
---
# <a name="cloud-foundry-on-azure"></a>Cloud Foundry på Azure

Cloud Foundry är en plattform som en tjänst (PaaS) med öppen källkod som finns för att skapa, distribuera och använda 12-faktorsprogram som utvecklats i olika språk och ramverk. Det här dokumentet beskriver de alternativ du har för att Cloud Foundry i Azure och hur du kommer igång.

## <a name="cloud-foundry-offerings"></a>Cloud Foundry erbjudanden

Det finns två typer av Cloud Foundry som kan köras i Azure: Cloud Foundry (OSS CF) och Pivotal Cloud Foundry (PCF). OSS CF är en helt [öppen källkodsversion](https://github.com/cloudfoundry) av Cloud Foundry som hanteras av Cloud Foundry Foundation. Pivotal Cloud Foundry är en företagsdistribution av Cloud Foundry pivotal Software Inc. Vi tittar på några av skillnaderna mellan de två erbjudandena.

### <a name="open-source-cloud-foundry"></a>Öppen källkod Cloud Foundry

Du kan distribuera OSS Cloud Foundry på Azure genom att först distribuera en BOSH-chef och sedan distribuera Cloud Foundry, med hjälp av anvisningarna [på GitHub.](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md) Mer information om hur du använder OSS CF finns [i dokumentationen](https://docs.cloudfoundry.org/) från Cloud Foundry Foundation.

Microsoft tillhandahåller bästa möjliga support för OSS CF via följande communitykanaler:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slack"></a>kanalen bosh-azure-cpi på [Cloud Foundry Slack](https://slack.cloudfoundry.org/)
- [cf-bosh-distributionslista](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- GitHub-problem för [CPI och](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) [Service Broker](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> Supportnivån för dina Azure-resurser, till exempel de virtuella datorer där du kör Cloud Foundry, baseras på ditt Azure-supportavtal. Support för communityn för bästa möjliga arbete gäller Cloud Foundry specifika komponenter.

### <a name="pivotal-cloud-foundry"></a>Pivotal Cloud Foundry

Pivotal Cloud Foundry innehåller samma kärnplattform som OSS-distributionen, tillsammans med en uppsättning egna hanteringsverktyg och företagsstöd. Om du vill köra PCF på Azure måste du skaffa en licens från Pivotal. PCF-erbjudandet från Azure Marketplace innehåller en 90-dagars utvärderingslicens.

Verktygen omfattar [Pivotal Operations Manager,](https://docs.pivotal.io/ops-manager/2-10/install/)ett webbprogram som förenklar distributionen och hanteringen av en Cloud Foundry Foundation och [Pivotal Apps Manager](https://docs.pivotal.io/pivotalcf/console/), ett webbprogram för hantering av användare och program.

Förutom de supportkanaler som anges för OSS CF ovan ger en PCF-licens dig rätt att kontakta Pivotal för support. Microsoft och Pivotal har också aktiverat supportarbetsflöden som gör att du kan kontakta endera part för att få hjälp och få din förfrågan dirigerad på rätt sätt beroende på var problemet finns.

## <a name="azure-service-broker"></a>Azure Service Broker

Cloud Foundry uppmuntrar [till metoden "tolvfaktorsapp",](https://12factor.net/) som främjar en ren separation av tillståndslösa programprocesser och tillståndsfulla stödtjänster. [Service Brokers](https://docs.cloudfoundry.org/services/api.html) erbjuder ett konsekvent sätt att etablera och binda stödtjänster till program. Azure [Service Broker tillhandahåller](https://github.com/Azure/meta-azure-service-broker) några av de viktigaste Azure-tjänsterna via den här kanalen, inklusive Azure Storage och Azure SQL.

Om du använder Pivotal Cloud Foundry service broker också [tillgänglig som en panel](https://docs.pivotal.io/azure-sb/installing.html) från Pivotal Network.

## <a name="related-resources"></a>Relaterade resurser

### <a name="azure-devops-services-plugin"></a>Plugin-program för Azure DevOps Services

Cloud Foundry passar bra för agil programvaruutveckling, inklusive användning av kontinuerlig integrering (CI) och kontinuerlig leverans (CD). Om du använder Azure DevOps Services för att hantera dina projekt och vill konfigurera en CI/CD-pipeline för Cloud Foundry kan du använda [Azure DevOps Services Cloud Foundry build-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension). Plugin-programmet gör det enkelt att konfigurera och automatisera distributioner till Cloud Foundry, oavsett om de körs i Azure eller någon annan miljö.

## <a name="next-steps"></a>Nästa steg

- [Distribuera pivotal Cloud Foundry från Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)
- [Distribuera en app till Cloud Foundry i Azure](./cloudfoundry-deploy-your-first-app.md)
