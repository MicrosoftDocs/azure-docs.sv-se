---
title: B2B-företagsintegration
description: Lär dig mer om att skapa automatiserade B2B-arbetsflöden för företags integrering med hjälp av Azure Logic Apps och Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: overview
ms.date: 08/01/2019
ms.openlocfilehash: adf15904d4490647074d6e2b46ac7e9e871dd388
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86536410"
---
# <a name="b2b-enterprise-integration-solutions-with-azure-logic-apps-and-enterprise-integration-pack"></a>B2B-lösningar för företags integrering med Azure Logic Apps och Enterprise-integrationspaket

För Business-to-Business-lösningar (B2B) och sömlös kommunikation mellan organisationer kan du bygga automatiserade arbets flöden för företags integrering med hjälp av Enterprise-integrationspaket (EIP) med [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Även om organisationer använder olika protokoll och format kan de utbyta meddelanden elektroniskt. EIP omvandlar olika format till ett format som organisationens system kan bearbeta och stöder bransch standard protokoll, inklusive [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md)och [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md). Du kan också förbättra säkerheten för meddelanden med både kryptering och digitala signaturer. EIP stöder dessa [företags integrerings anslutningar](../connectors/apis-list.md#integration-account-connectors) och dessa bransch standarder:

* Electronic Data Interchange (EDI)
* Enterprise Application Integration (EAI)

Om du är bekant med Microsoft BizTalk Server eller Azure BizTalk Services, följer EIP följande koncept som gör det enkelt att använda dem. En stor skillnad är dock att EIP baseras på "integrations konton" för att förenkla lagring och hantering av artefakter som används i B2B-kommunikation. Dessa konton är molnbaserade behållare som lagrar alla artefakter, till exempel partner, avtal, scheman, kartor och certifikat. 

## <a name="why-use-the-enterprise-integration-pack"></a>Varför ska jag använda Enterprise-integrationspaket?

* Med EIP kan du lagra alla artefakter på en plats – ditt integrations konto.

* Du kan bygga B2B-arbetsflöden och integrera med SaaS-appar (program vara som tjänst) från tredje part, lokala appar och anpassade appar med hjälp av Azure Logic Apps och anslutningar.

* Du kan skapa anpassad kod för dina Logic Apps med Azure Functions.

## <a name="how-do-i-get-started"></a>Hur kommer jag igång?

Innan du kan börja skapa B2B-Logic app-arbetsflöden med EIP behöver du följande objekt:

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett [integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) med de artefakter som du vill använda

* Om du vill skapa kartor och scheman kan du använda [Microsoft Azure Logic Apps Enterprise-integration verktyg för Visual studio 2015 2,0](https://aka.ms/vsmapsandschemas) och Visual Studio 2015.

När du har skapat ett integrations konto och lagt till dina artefakter kan du börja skapa B2B-arbetsflöden med dessa artefakter genom att skapa en Logic app i Azure Portal. Om du inte har använt Logic Apps igen kan du försöka [skapa en grundläggande Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill arbeta med dessa artefakter måste du först länka ditt integrations konto till din Logic app. Sedan kan din Logic-app komma åt ditt integrations konto. Du kan också skapa, hantera och distribuera Logi Kap par med Visual Studio eller [PowerShell](/powershell/module/az.logicapp).

Här följer de övergripande stegen för att komma igång med att skapa B2B-Logic Apps:

![Krav för att skapa B2B Logic Apps](./media/logic-apps-enterprise-integration-overview/overview.png)  

## <a name="try-now"></a>Testa nu

[Distribuera en app Logic-app med fullt drift som skickar och tar emot AS2-meddelanden](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)

## <a name="next-steps"></a>Nästa steg

* [Skapa handels partner](logic-apps-enterprise-integration-partners.md)
* [Skapa avtal](../logic-apps/logic-apps-enterprise-integration-agreements.md)
* [Lägga till scheman](logic-apps-enterprise-integration-schemas.md)
* [Lägg till kartor](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Migrera från BizTalk Services](../logic-apps/logic-apps-move-from-mabs.md)
