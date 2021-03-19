---
title: Hantera artefakt metadata för integrations kontot
description: Lägg till eller hämta artefakt-metadata från integrations konton i Azure Logic Apps med Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/17/2019
ms.openlocfilehash: bc119f1ce8efb821781dabfb9dd259cc5c8d9c23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "74792466"
---
# <a name="manage-artifact-metadata-in-integration-accounts-with-azure-logic-apps-and-enterprise-integration-pack"></a>Hantera metadata för artefakter i integrationskonton med Azure Logic Apps och Enterprise-integrationspaket

Du kan definiera anpassade metadata för artefakter på integrationskonton och hämta dessa metadata under körningen för din logikapp att använda. Du kan till exempel tillhandahålla metadata för artefakter, till exempel partner, avtal, scheman och kartor – alla lagra metadata med nyckel/värde-par. 

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>.

* Ett grundläggande [integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) som har artefakter där du vill lägga till metadata, till exempel: 

  * [Partner](logic-apps-enterprise-integration-partners.md)
  * [Avtal](logic-apps-enterprise-integration-agreements.md)
  * [Schema](logic-apps-enterprise-integration-schemas.md)
  * [Karta](logic-apps-enterprise-integration-maps.md)

* En Logic-app som är länkad till det integrerings konto och de artefakt-metadata som du vill använda. Om din Logi Kap par inte redan är länkad, lär du dig att länka Logi Kap par [till integrations konton](logic-apps-enterprise-integration-create-integration-account.md#link-account). 

  Om du inte redan har en Logic app kan du läsa om [hur du skapar](../logic-apps/quickstart-create-first-logic-app-workflow.md)Logi Kap par. 
  Lägg till den utlösare och de åtgärder som du vill använda för att hantera artefakt-metadata. Eller, om du bara vill testa saker, lägger du till en utlösare som **Request** eller **http** i din Logic app.

## <a name="add-metadata-to-artifacts"></a>Lägg till metadata i artefakter

1. Logga in på <a href="https://portal.azure.com" target="_blank">Azure Portal</a> med autentiseringsuppgifterna för ditt Azure-konto. Hitta och öppna ditt integrations konto.

1. Välj den artefakt där du vill lägga till metadata och välj **Redigera**. Ange information om metadata för artefakten, till exempel:

   ![Ange metadata](media/logic-apps-enterprise-integration-metadata/add-partner-metadata.png)

1. När du är klar väljer du **OK**.

1. Om du vill visa dessa metadata i JavaScript Object Notation (JSON)-definitionen för integrations kontot väljer du **Redigera som JSON** så att JSON-redigeraren öppnas: 

   ![JSON för partner-metadata](media/logic-apps-enterprise-integration-metadata/partner-metadata.png)

## <a name="get-artifact-metadata"></a>Hämta artefakt-metadata

1. I Azure Portal öppnar du den Logic-app som är länkad till det integrations konto som du vill använda. 

1. Om du lägger till steget för att hämta metadata under utlösaren eller den senaste åtgärden i arbets flödet i Logic App Designer väljer du **nytt steg**  >  **Lägg till en åtgärd**. 

1. I rutan Sök anger du "integrations konto". Under sökrutan väljer du **Alla**. I listan åtgärder väljer du den här åtgärden: **integrations kontots artefakt sökning – integrations konto**

   ![Välj "integrations kontots artefakt sökning"](media/logic-apps-enterprise-integration-metadata/integration-account-artifact-lookup.png)

1. Ange den här informationen för den artefakt du vill hitta:

   | Egenskap | Krävs | Värde | Beskrivning | 
   |----------|---------|-------|-------------| 
   | **Artefakt typ** | Ja | **Schema**, **kartor**, **partner**, **avtal** eller en anpassad typ | Typen för artefakten som du vill använda | 
   | **Artefakt namn** | Ja | <*artefakt-namn*> | Namnet på artefakten som du vill använda | 
   ||| 

   Anta till exempel att du vill hämta metadata för en handels partners artefakt:

   ![Välj artefakt typ och ange artefakt namn](media/logic-apps-enterprise-integration-metadata/artifact-lookup-information.png)

1. Lägg till den åtgärd som du vill använda för att hantera dessa metadata, till exempel:

   1. Under sökåtgärden **integrations konto artefakt** väljer du **Nästa steg** och väljer **Lägg till en åtgärd**. 

   1. Skriv "http" i rutan Sök. Välj **inbyggda moduler** under sökrutan och välj den här åtgärden: **http-http**

      ![Lägg till HTTP-åtgärd](media/logic-apps-enterprise-integration-metadata/http-action.png)

   1. Ange information om de artefakt-metadata som du vill hantera. 

      Anta till exempel att du vill hämta de `routingUrl` metadata som har lagts till tidigare i det här avsnittet. Här är de egenskaps värden som du kan ange: 

      | Egenskap | Krävs | Värde | Beskrivning | 
      |----------|----------|-------|-------------| 
      | **Metod** | Ja | <*åtgärd att köra*> | HTTP-åtgärden som ska köras på artefakten. Den här HTTP-åtgärden använder till exempel metoden **Get** . | 
      | **URI** | Ja | <*metadata-plats*> | Om du vill komma åt `routingUrl` metadata-värdet från den artefakt du hämtade kan du använda ett uttryck, till exempel: <p>`@{outputs('Integration_Account_Artifact_Lookup')['properties']['metadata']['routingUrl']}` | 
      | **Sidhuvuden** | Inga | <*rubrik – värden*> | Eventuella sidhuvuds utdata från den utlösare som du vill skicka till HTTP-åtgärden. Om du till exempel vill skicka utlösarens `headers` egenskaps värde: du kan använda ett uttryck, till exempel: <p>`@triggeroutputs()['headers']` | 
      | **Brödtext** | Inga | <*brödtext – innehåll*> | Annat innehåll som du vill skicka genom HTTP-åtgärdens `body` egenskap. I det här exemplet överförs artefaktens `properties` värden till HTTP-åtgärden: <p>1. Klicka inuti egenskapen **brödtext** så att listan med dynamiskt innehåll visas. Om inga egenskaper visas väljer du **Visa fler**. <br>2. gå till listan med dynamiskt innehåll och välj **Egenskaper** under **integrations kontots artefakt sökning**. | 
      |||| 

      Exempel:

      ![Ange värden och uttryck för HTTP-åtgärd](media/logic-apps-enterprise-integration-metadata/add-http-action-values.png)

   1. Du kan kontrol lera den information du angav för HTTP-åtgärden genom att visa din Logic Apps JSON-definition. I verktygsfältet Logic App Designer väljer du **kodvyn** så att appens JSON-definition visas, till exempel:

      ![JSON-definition för Logic app](media/logic-apps-enterprise-integration-metadata/finished-logic-app-definition.png)

      När du växlar tillbaka till Logic Apps Designer visas alla uttryck som du använde nu lösta, till exempel:

      ![Matchade uttryck i Logic App Designer](media/logic-apps-enterprise-integration-metadata/resolved-expressions.png)

## <a name="next-steps"></a>Nästa steg

* [Läs mer om avtal](logic-apps-enterprise-integration-agreements.md)
