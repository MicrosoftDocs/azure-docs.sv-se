---
title: CI/CD för Azure API Management med ARM-mallar
description: Introduktion till API-DevOps med Azure API Management med Azure Resource Manager mallar för att hantera API-distributioner i en CI/CD-pipeline
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: apimpm
ms.openlocfilehash: 62f163b9ce649cd5ddb52b4325682570633dfb92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96183166"
---
# <a name="cicd-for-api-management-using-azure-resource-manager-templates"></a>CI/CD för API Management med Azure Resource Manager-mallar

Den här artikeln visar hur du använder API-DevOps med Azure API Management, via Azure Resource Manager mallar. Med det strategiska värdet för API: er har en pipeline för kontinuerlig integrering och kontinuerlig distribution (CI/CD) blivit en viktig aspekt av API-utveckling. Det gör det möjligt för organisationer att automatisera distribution av API-ändringar utan fel känsliga manuella steg, identifiera problem tidigare och leverera värdet till användarna snabbare. 

Mer information, verktyg och kod exempel för att implementera DevOps-metoden som beskrivs i den här artikeln finns i [Azure API Management DevOps Resource Kit](https://github.com/Azure/azure-api-management-devops-resource-kit) med öppen källkod i GitHub. Eftersom kunderna använder en mängd olika teknik kulturer och befintliga automatiserings lösningar, är metoden inte en enda stor lösning.

## <a name="the-problem"></a>Problemet

Organisationer idag har normalt flera distributions miljöer (till exempel utveckling, testning och produktion) och använder separata API Management-instanser för varje miljö. Vissa instanser delas av flera utvecklings grupper som är ansvariga för olika API: er med olika versions cadences.

Kunderna har därför följande utmaningar:

* Automatisera distribution av API: er till API Management
* Så här migrerar du konfigurationer från en miljö till en annan
* Undvik störningar mellan olika utvecklings team som delar samma API Management-instans

## <a name="manage-configurations-in-resource-manager-templates"></a>Hantera konfigurationer i Resource Manager-mallar

Följande bild illustrerar den föreslagna metoden. 

:::image type="content" source="media/devops-api-development-templates/apim-devops.png" alt-text="Diagram som illustrerar DevOps med API Management.":::

I det här exemplet finns det två distributions miljöer: *utveckling* och *produktion*. Var och en har sin egen API Management instans. 

* API-utvecklare har åtkomst till utvecklings instansen och kan använda den för att utveckla och testa API: er. 
* Ett särskilt team som kallas för *API-utgivare* hanterar produktions instansen.

Nyckeln i den här föreslagna metoden är att behålla alla API Management konfigurationer i [Azure Resource Manager mallar](../azure-resource-manager/templates/template-syntax.md). Organisationen bör behålla dessa mallar i ett käll kontroll system, t. ex. git. Som illustreras i bilden innehåller en publicerings databas alla konfigurationer av produktions API Managements instansen i en samling mallar:

|Mall  |Beskrivning  |
|---------|---------|
|Tjänstmall     | Konfigurationer på tjänst nivå för API Management-instansen, till exempel pris nivå och anpassade domäner.         |
|Delade mallar     |  Delade resurser i en API Management instans, till exempel grupper, produkter och loggar.    |
|API-mallar     |  Konfigurationer av API: er och deras under resurser: åtgärder, principer, diagnostikinställningar.        |
|Huvud-mall (Main)     |   Länkar allt samman genom att [Länka](../azure-resource-manager/templates/linked-templates.md) till alla mallar och distribuera dem i rätt ordning. Distribuera huvud mal len för att distribuera alla konfigurationer till en API Management-instans. Du kan också distribuera varje mall individuellt.       |

API-utvecklare delar in utgivar databasen till ett utvecklings lager och arbetar med ändringarna för deras API: er. I de flesta fall fokuserar de på API-mallarna för sina API: er och behöver inte ändra de delade mallarna eller tjänst mallarna.

## <a name="migrate-configurations-to-templates"></a>Migrera konfigurationer till mallar
API-utvecklare möter utmaningarna när de arbetar med Resource Manager-mallar:

* API-utvecklare arbetar ofta med [openapi-specifikationen](https://github.com/OAI/OpenAPI-Specification) och kanske inte är bekant med Resource Manager-scheman. Att redigera mallar manuellt kan vara fel känsligt. 

   Ett verktyg som kallas [skapare](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#Creator) i Resource Kit kan hjälpa dig att automatisera skapandet av API-mallar baserat på en öppen API-Specifikations fil. Dessutom kan utvecklare tillhandahålla API Management principer för ett API i XML-format. 

* För kunder som redan använder API Management är det en annan utmaning att extrahera befintliga konfigurationer till Resource Manager-mallar. För dessa kunder kan ett verktyg som heter [Extractor](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#extractor) i Resource Kit hjälpa dig att skapa mallar genom att extrahera konfigurationer från sina API Management instanser.  

## <a name="workflow"></a>Arbetsflöde

* När API-utvecklare har slutfört utvecklingen och testat ett API och har genererat API-mallarna kan de skicka in en pull-begäran för att sammanfoga ändringarna till utgivar databasen. 

* API-utgivare kan verifiera pull-begäran och se till att ändringarna är säkra och kompatibla. De kan till exempel kontrol lera om endast HTTPS tillåts kommunicera med API: et. De flesta verifieringar kan automatiseras som ett steg i CI/CD-pipeline.

* När ändringarna har godkänts och sammanfogats kan API-utgivare välja att distribuera dem till produktions instansen antingen enligt schema eller på begäran. Distributionen av mallarna kan automatiseras med hjälp av [GitHub-åtgärder](https://github.com/Azure/apimanagement-devops-samples), Azure- [pipelines](/azure/devops/pipelines), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)eller andra verktyg.


Med den här metoden kan en organisation automatisera distributionen av API-ändringar till API Management instanser, och det är enkelt att befordra ändringar från en miljö till en annan. Eftersom olika API-utvecklings team kommer att arbeta med olika uppsättningar API-mallar och filer, förhindrar det störningar mellan olika team.

## <a name="video"></a>Video

> [!VIDEO https://www.youtube.com/embed/4Sp2Qvmg6j8]

## <a name="next-steps"></a>Nästa steg

- Se [Azure API Management DevOps Resource Kit](https://github.com/Azure/azure-api-management-devops-resource-kit) med öppen källkod för ytterligare information, verktyg och exempel mallar.