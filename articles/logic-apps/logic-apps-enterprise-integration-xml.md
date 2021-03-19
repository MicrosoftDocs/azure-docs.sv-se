---
title: XML-meddelanden och flata filer
description: Bearbeta, validera och transformera XML-meddelanden i Azure Logic Apps med Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 3cb3472572abbd891f8d36ea036b371c1224f38c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "74792147"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>XML-meddelanden och flata filer i Azure Logic Apps med Enterprise-integrationspaket

I [Azure Logic Apps](logic-apps-overview.md)kan du bearbeta XML-meddelanden som du skickar och tar emot med hjälp av Enterprise-integrationspaket. Om du har använt BizTalk Server tillhandahåller Enterprise-integrationspaket liknande funktioner för att transformera och validera meddelanden, arbeta med flata filer och till och med använda XPath för att utöka eller extrahera vissa egenskaper från ett meddelande. Om du är nybörjare på det här utrymmet expanderar de här funktionerna hur du bearbetar meddelanden i din Logic app-arbetsflöde. Om du till exempel har ett scenario för affärs-till-företag (B2B) och arbetar med specifika XML-scheman kan du använda Enterprise-integrationspaket för att förbättra hur ditt företag bearbetar dessa meddelanden.

Enterprise-integrationspaket innehåller till exempel följande funktioner:

* [XML-verifiering](logic-apps-enterprise-integration-xml-validation.md): verifiera ett inkommande eller utgående XML-meddelande mot ett angivet schema.

* [XML-transformering](logic-apps-enterprise-integration-transform.md): konvertera eller anpassa ett XML-meddelande baserat på dina krav eller krav på en partner med hjälp av Maps.

* [Flat fil kodning och flat fil-avkodning](logic-apps-enterprise-integration-flatfile.md): koda eller avkoda en flat fil.

  Till exempel accepterar SAP och skickar IDOC-filer i platt fil format. Många integrerings plattformar skapar XML-meddelanden, inklusive Logic Apps. Det innebär att du kan skapa en Logic-app som använder Flat File Encoder för att konvertera XML-filer till flata filer.

* [XPath](workflow-definition-language-functions-reference.md#xpath): utöka ett meddelande och extrahera vissa egenskaper från meddelandet. Du kan sedan använda de extraherade egenskaperna för att dirigera meddelandet till ett mål eller en mellanliggande slut punkt.

## <a name="sample"></a>Exempel

[Distribuera en fullständigt fungerande Logic app](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (GitHub-exempel) med hjälp av XML-funktionerna i Azure Logic Apps.

## <a name="next-steps"></a>Nästa steg

Läs mer om [Enterprise-integrationspaket](logic-apps-enterprise-integration-overview.md)
