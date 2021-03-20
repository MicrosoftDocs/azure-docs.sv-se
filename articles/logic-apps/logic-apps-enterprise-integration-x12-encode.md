---
title: Koda X12-meddelanden
description: Verifiera EDI och konvertera XML-kodade meddelanden med X12 Message Encoder i Azure Logic Apps med Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: c87a61dc77e656a1cfe667ce87f852303a0cc486
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "74792360"
---
# <a name="encode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Koda X12-meddelanden i Azure Logic Apps med Enterprise-integrationspaket

Med anslutningsappen för X12-meddelanden kan du verifiera EDI-egenskaper och partnerspecifika egenskaper, konvertera XML-kodade meddelanden i EDI-transaktionsuppsättningar och begära en teknisk bekräftelse, funktionsbekräftelse eller både och.
För att kunna använda den här anslutningsappen måste du lägga till den till en befintlig utlösare i din logikapp.

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt du behöver:

* Ett Azure-konto; Du kan skapa ett [kostnads fritt konto](https://azure.microsoft.com/free)
* Ett [integrations konto](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierats och associerats med din Azure-prenumeration. Du måste ha ett integrations konto för att kunna använda kodningen X12 Message Connector.
* Minst två [partner](logic-apps-enterprise-integration-partners.md) som redan har definierats i ditt integrations konto
* Ett [X12-avtal](logic-apps-enterprise-integration-x12.md) som redan har definierats i ditt integrations konto

## <a name="encode-x12-messages"></a>Koda X12-meddelanden

1. [Skapa en Logic-app](quickstart-create-first-logic-app-workflow.md).

2. X12 Message Connector har inte utlösare, så du måste lägga till en utlösare för att starta din Logic app, t. ex. en begäran-utlösare. Lägg till en utlösare i Logic Apps designer och Lägg sedan till en åtgärd i din Logic app.

3.  I rutan Sök anger du "x12" för ditt filter. Välj antingen **X12-koda till X12-meddelande efter avtals namn** eller **X12-koda till X12-meddelande med identiteter**.
   
    ![Sök efter "x12"](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. Om du inte tidigare skapade några anslutningar till ditt integrations konto uppmanas du att skapa anslutningen nu. Ge anslutningen ett namn och välj det integrations konto som du vill ansluta. 
   
    ![integrations konto anslutning](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    Egenskaper med en asterisk måste anges.

    | Egenskap | Information |
    | --- | --- |
    | Anslutnings namn * |Ange ett namn för anslutningen. |
    | Integrations konto * |Ange ett namn för ditt integrations konto. Se till att ditt integrations konto och din Logic app finns på samma Azure-plats. |

5.  När du är klar bör din anslutnings information se ut ungefär som i det här exemplet. Klicka på **skapa** om du vill slutföra skapandet av anslutningen.

    ![integrerings konto anslutningen har skapats](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    Nu skapas din anslutning.

    ![anslutnings information för integrations konto](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>Koda X12-meddelanden efter avtals namn

Om du har valt att koda X12-meddelanden efter avtals namn öppnar du listan **namn på X12-avtal** , anger eller väljer ditt befintliga X12-avtal. Ange det XML-meddelande som ska kodas.

![Ange X12 avtals namn och XML-meddelande som ska kodas](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>Koda X12-meddelanden med identiteter

Om du väljer att koda X12-meddelanden efter identiteter anger du avsändar-ID, avsändarens kvalificerare, mottagar identifierare och mottagarens kvalificerare enligt konfigurationen i ditt X12-avtal. Välj det XML-meddelande som ska kodas.
   
![Ange identiteter för avsändare och mottagare, Välj XML-meddelande för att koda](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>Information om X12-kodning

X12 kodnings koppling utför följande uppgifter:

* Avtals lösning genom att matcha egenskaperna för avsändare och mottagare.
* Serialiserar EDI Interchange och konverterar XML-kodade meddelanden till EDI-transaktions uppsättningar i Interchange.
* Använder transaktions uppsättnings huvud och trailer-segment
* Genererar ett utbytes kontroll nummer, ett grupp kontroll nummer och ett kontroll nummer för transaktions uppsättning för varje utgående utbyte
* Ersätter avgränsare i nytto Last data
* Validerar EDI-och partner-/regionsspecifika egenskaper
  * Schema validering av transaktions uppsättningens data element mot meddelande schemat
  * EDI-verifiering utförs på transaktions uppsättnings data element.
  * Utökad verifiering utförs på transaktions uppsättnings data element
* Begär en teknisk och/eller funktionell bekräftelse (om den är konfigurerad).
  * En teknisk bekräftelse skapas till följd av huvud verifiering. Den tekniska bekräftelsen rapporterar statusen för bearbetningen av ett utbytes huvud och en släpvagn från mottagar adressen
  * En funktionell bekräftelse skapas till följd av verifiering av brödtext. Funktions bekräftelsen rapporterar varje fel som påträffades vid bearbetning av det mottagna dokumentet

## <a name="view-the-swagger"></a>Visa Swagger
Se [Swagger-informationen](/connectors/x12/). 

## <a name="next-steps"></a>Nästa steg
[Läs mer om Enterprise-integrationspaket](logic-apps-enterprise-integration-overview.md "Läs mer om Enterprise-integrationspaket") 

