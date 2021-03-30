---
title: Avkoda X12-meddelanden
description: Verifiera EDI och generera bekräftelser med X12 Message decoder i Azure Logic Apps med Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 918516a5629f8570d54c641ffc29f2367937266f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "74792366"
---
# <a name="decode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Avkoda X12-meddelanden i Azure Logic Apps med Enterprise-integrationspaket

Med anslutningsappen för avkodning av X12-meddelanden kan du verifiera kuvertet mot ett handelspartneravtal, verifiera EDI- och partnerspecifika egenskaper, dela upp utbyten i transaktionsuppsättningar eller bevara hela utbyten, samt generera bekräftelser för bearbetade transaktioner. För att kunna använda den här anslutningsappen måste du lägga till den till en befintlig utlösare i din logikapp.

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt du behöver:

* Ett Azure-konto; Du kan skapa ett [kostnads fritt konto](https://azure.microsoft.com/free)
* Ett [integrations konto](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierats och associerats med din Azure-prenumeration. Du måste ha ett integrations konto för att kunna använda meddelandet avkoda X12 Message Connector.
* Minst två [partner](logic-apps-enterprise-integration-partners.md) som redan har definierats i ditt integrations konto
* Ett [X12-avtal](logic-apps-enterprise-integration-x12.md) som redan har definierats i ditt integrations konto

## <a name="decode-x12-messages"></a>Avkoda X12-meddelanden

1. [Skapa en Logic-app](quickstart-create-first-logic-app-workflow.md).

2. X12 Message Connector har inte utlösare, så du måste lägga till en utlösare för att starta din Logic app, t. ex. en begäran-utlösare. Lägg till en utlösare i Logic Apps designer och Lägg sedan till en åtgärd i din Logic app.

3.  I rutan Sök anger du "x12" för ditt filter. Välj **X12-avkoda X12-meddelande**.
   
    ![Sök efter "x12"](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Om du inte tidigare skapade några anslutningar till ditt integrations konto uppmanas du att skapa anslutningen nu. Ge anslutningen ett namn och välj det integrations konto som du vill ansluta. 

    ![Ange anslutnings information för integrations kontot](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    Egenskaper med en asterisk måste anges.

    | Egenskap | Information |
    | --- | --- |
    | Anslutnings namn * |Ange ett namn för anslutningen. |
    | Integrations konto * |Ange ett namn för ditt integrations konto. Se till att ditt integrations konto och din Logic app finns på samma Azure-plats. |

5.  När du är klar bör din anslutnings information se ut ungefär som i det här exemplet. Klicka på **skapa** om du vill slutföra skapandet av anslutningen.
   
    ![anslutnings information för integrations konto](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. När anslutningen har skapats, som du ser i det här exemplet, väljer du det X12 platt fil meddelande som ska avkodas.

    ![integrerings konto anslutningen har skapats](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Exempel:

    ![Välj X12 platt fil meddelande för avkodning](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > Det faktiska innehållet eller nytto lasten för meddelande mat ris, bra eller dåligt är Base64-kodat. Så du måste ange ett uttryck som bearbetar det här innehållet.
   > Här är ett exempel som bearbetar innehållet som XML-kod som du kan ange i kodvyn eller med uttrycks verktyget i designern.
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![Innehålls exempel](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>Information om X12-avkodning

X12 avkodnings koppling utför följande uppgifter:

* Validerar kuvertet mot handels partner avtal
* Validerar EDI-och partner-/regionsspecifika egenskaper
  * Strukturell validering av EDI och utökad schema validering
  * Validering av strukturen för Interchange-kuvertet.
  * Schema validering av kuvertet mot kontroll schema.
  * Schema validering av transaktions uppsättningens data element mot meddelande schemat.
  * EDI-verifiering utförs på transaktions uppsättnings data element 
* Verifierar att kontroll numren för växling, grupp och transaktions uppsättning inte är dubbletter
  * Kontrollerar växlings kontroll numret mot tidigare mottagna ändringar.
  * Kontrollerar grupp kontroll numret mot andra grupp kontroll nummer i Interchange.
  * Kontrollerar transaktions uppsättningens kontroll nummer mot andra kontroll nummer för transaktions uppsättningar i gruppen.
* Delar upp Interchange i transaktions uppsättningar eller bevarar hela utbytet:
  * Dela upp utbyte som transaktions uppsättningar – inaktivera transaktions uppsättningar vid fel: delar upp Interchange i transaktions uppsättningar och parsar varje transaktions uppsättning. 
  X12-avkodnings åtgärden matar bara ut de transaktions uppsättningar som inte kan verifieras till `badMessages` , och de återstående transaktions uppsättningarna matas ut till `goodMessages` .
  * Dela upp utbyte som transaktions uppsättningar – pausa utbyte vid fel: delar upp Interchange i transaktions uppsättningar och parsar varje transaktions uppsättning. 
  Om en eller flera transaktions uppsättningar i växlingen inte kan verifieras, kommer X12-avkodnings åtgärden att mata ut alla transaktions uppsättningar i som Interchange till `badMessages` .
  * Bevara Interchange – pausa transaktions uppsättningar vid fel: bevara Interchange och bearbeta hela det batchade utbytet. 
  X12-avkodnings åtgärden matar bara ut de transaktions uppsättningar som inte kan verifieras till `badMessages` , och de återstående transaktions uppsättningarna matas ut till `goodMessages` .
  * Bevara Interchange – pausa utbyte vid fel: bevara Interchange och bearbeta hela det batchade utbytet. 
  Om en eller flera transaktions uppsättningar i växlingen inte kan verifieras, kommer X12-avkodnings åtgärden att mata ut alla transaktions uppsättningar i som Interchange till `badMessages` . 
* Genererar en teknisk och/eller funktionell bekräftelse (om den är konfigurerad).
  * En teknisk bekräftelse skapas till följd av huvud verifiering. Den tekniska bekräftelsen rapporterar statusen för bearbetningen av ett utbytes huvud och en släpvagn från mottagar adressen.
  * En funktionell bekräftelse skapas till följd av verifiering av brödtext. Funktions bekräftelsen rapporterar varje fel som påträffades vid bearbetning av det mottagna dokumentet

## <a name="view-the-swagger"></a>Visa Swagger
Se [Swagger-informationen](/connectors/x12/). 

## <a name="next-steps"></a>Nästa steg
[Läs mer om Enterprise-integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md "Läs mer om Enterprise-integrationspaket") 

