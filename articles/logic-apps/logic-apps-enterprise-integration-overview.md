---
title: B2B-företagsintegration
description: Lär dig mer om att skapa automatiserade B2B-arbetsflöden för företagsintegrering med hjälp Azure Logic Apps och Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: overview
ms.date: 03/24/2021
ms.openlocfilehash: 09d253aae4c054db5efdc252f62986044e1d366b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771865"
---
# <a name="b2b-enterprise-integration-solutions-with-azure-logic-apps-and-enterprise-integration-pack"></a>B2B enterprise integration solutions with Azure Logic Apps and Enterprise-integrationspaket

För B2B-lösningar (business-to-business) och sömlös kommunikation mellan organisationer kan du skapa automatiserade skalbara arbetsflöden för företagsintegrering med hjälp av Enterprise-integrationspaket (EIP) [med Azure Logic Apps](../logic-apps/logic-apps-overview.md). Även om organisationer använder olika protokoll och format kan de utbyta meddelanden elektroniskt. EIP omvandlar olika format till ett format som dina organisationers system kan bearbeta och stödja branschstandardprotokoll, inklusive [AS2,](../logic-apps/logic-apps-enterprise-integration-as2.md) [X12](logic-apps-enterprise-integration-x12.md)och [EDIFACT.](../logic-apps/logic-apps-enterprise-integration-edifact.md) Du kan också förbättra säkerheten för meddelanden med hjälp av både kryptering och digitala signaturer. EIP stöder dessa [anslutningsappar för företagsintegrering](../connectors/managed.md#enterprise-connectors) och dessa branschstandarder:

* EDI (Electronic Data Interchange)
* Enterprise Application Integration (EAI)

Om du är bekant med Microsoft BizTalk Server eller Azure BizTalk Services följer EIP liknande begrepp, vilket gör funktionerna enkla att använda. En viktig skillnad är dock att EIP är arkitekturbaserat baserat på "integrationskonton" för att förenkla lagring och hantering av artefakter som används i B2B-kommunikation. Dessa konton är molnbaserade containrar som lagrar alla dina artefakter, till exempel partner, avtal, scheman, kartor och certifikat. 

## <a name="why-use-the-enterprise-integration-pack"></a>Varför ska jag använda Enterprise-integrationspaket?

* Med EIP kan du lagra alla dina artefakter på ett och samma ställe – ditt integrationskonto.

* Du kan skapa B2B-arbetsflöden och integrera med SaaS-appar (software-as-service), lokala appar och anpassade appar från tredje part med hjälp av Azure Logic Apps och anslutningsappar.

* Du kan skapa anpassad kod för dina logikappar med Azure Functions.

## <a name="how-do-i-get-started"></a>Hur kommer jag igång?

Innan du kan börja skapa B2B-logikapparbetsflöden med EIP behöver du följande:

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett [integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) med de artefakter som du vill använda

* Om du vill skapa kartor och scheman kan du använda [Microsoft Azure Logic Apps Enterprise-integration Tools för Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas) och Visual Studio 2015.

När du har skapat ett integrationskonto och lagt till dina artefakter kan du börja skapa B2B-arbetsflöden med dessa artefakter genom att skapa en logikapp i Azure Portal. Om logic apps är nytt för dig kan du prova att [skapa en grundläggande logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill arbeta med dessa artefakter måste du först länka ditt integrationskonto till logikappen. Efter det kan logikappen komma åt ditt integrationskonto. Du kan också skapa, hantera och distribuera logikappar med hjälp av Visual Studio eller [PowerShell.](/powershell/module/az.logicapp)

Här följer de avancerade stegen för att komma igång med att skapa B2B-logikappar:

![Krav för att skapa B2B-logikappar](./media/logic-apps-enterprise-integration-overview/overview.png)  

## <a name="try-now"></a>Testa nu

[Distribuera en fullt fungerande exempellogikapp som skickar och tar emot AS2-meddelanden](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)

## <a name="next-steps"></a>Nästa steg

* [Skapa handelspartner](logic-apps-enterprise-integration-partners.md)
* [Skapa avtal](../logic-apps/logic-apps-enterprise-integration-agreements.md)
* [Lägga till scheman](logic-apps-enterprise-integration-schemas.md)
* [Lägg till kartor](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Migrera från BizTalk Services](../logic-apps/logic-apps-move-from-mabs.md)
