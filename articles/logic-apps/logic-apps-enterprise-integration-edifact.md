---
title: EDIFACT-meddelanden för B2B-integrering
description: Exchange EDIFACT-meddelanden i EDI-format för B2B Enterprise-integration i Azure Logic Apps med Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: b0df55e59bd519a816c4022f2434edfcd4460780
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96006531"
---
# <a name="exchange-edifact-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Utbyta EDIFACT-meddelanden för B2B-företagsintegrering i Azure Logic Apps med Enterprise-integrationspaket

Innan du kan utbyta EDIFACT-meddelanden för Azure Logic Apps måste du skapa ett EDIFACT-avtal och lagra avtalet i integrations kontot. Här följer stegen för att skapa ett EDIFACT-avtal.

> [!NOTE]
> Den här sidan täcker EDIFACT-funktionerna för Azure Logic Apps. Mer information finns i [X12](logic-apps-enterprise-integration-x12.md).

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt du behöver:

* Ett [integrations konto](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierats och associerats med din Azure-prenumeration  
* Minst två [partner](logic-apps-enterprise-integration-partners.md) som redan har definierats i ditt integrations konto

> [!NOTE]
> När du skapar ett avtal måste innehållet i de meddelanden som du tar emot eller skicka till och från partnern matcha avtals typen.

När du har [skapat ett integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) och [lagt till partner](logic-apps-enterprise-integration-partners.md)kan du skapa ett EDIFACT-avtal genom att följa dessa steg.

## <a name="create-an-edifact-agreement"></a>Skapa ett EDIFACT-avtal 

1. Logga in på [Azure-portalen](https://portal.azure.com "Azure Portal"). 

2. På huvud menyn i Azure väljer du **alla tjänster**. I rutan Sök anger du "integration" och väljer sedan **integrations konton**.

   ![Hitta ditt integrations konto](./media/logic-apps-enterprise-integration-edifact/edifact-0.png)

   > [!TIP]
   > Om **alla tjänster** inte visas kan du behöva expandera menyn först. Överst på menyn komprimerad väljer du **Visa text etiketter**.

3. Under **integrations konton** väljer du det integrations konto där du vill skapa avtalet.

   ![Välj integrations konto där du vill skapa avtalet](./media/logic-apps-enterprise-integration-edifact/edifact-1-4.png)

4. Välj **avtal**. Om du inte har någon avtals panel måste du först lägga till panelen.   

   ![Välj avtals panelen](./media/logic-apps-enterprise-integration-edifact/edifact-1-5.png)

5. På sidan avtal väljer du **Lägg till**.

   ![Välj "Lägg till"](./media/logic-apps-enterprise-integration-edifact/edifact-agreement-2.png)

6. Under **Lägg till** anger du ett **namn** för ditt avtal. För **avtals typ** väljer du **EDIFACT**. Välj **värd partner**, **värd identitet**, **gäst partner** och **gäst identitet** för ditt avtal.

   ![Ange avtals information](./media/logic-apps-enterprise-integration-edifact/edifact-1.png)

   | Egenskap | Beskrivning |
   | --- | --- |
   | Name |Avtalets namn |
   | Avtals typ | Ska vara EDIFACT |
   | Värd partner |Ett avtal måste både vara värd-och gäst partner. Värd partnern representerar den organisation som konfigurerar avtalet. |
   | Värd identitet |En identifierare för värd partnern |
   | Gäst partner |Ett avtal måste både vara värd-och gäst partner. Gäst partnern representerar den organisation som gör affärer med värd partnern. |
   | Gäst identitet |En identifierare för gäst partnern |
   | Ta emot inställningar |Dessa egenskaper gäller för alla meddelanden som tas emot av ett avtal. |
   | Skicka inställningar |Dessa egenskaper gäller för alla meddelanden som skickas av ett avtal. |
   ||| 

## <a name="configure-how-your-agreement-handles-received-messages"></a>Konfigurera hur ditt avtal hanterar mottagna meddelanden

Nu när du har angett avtals egenskaperna kan du konfigurera hur det här avtalet ska identifiera och hantera inkommande meddelanden som tas emot från din partner genom detta avtal.

> [!IMPORTANT]
> EDIFACT-anslutningen har endast stöd för UTF-8-tecken.
> Om utdata innehåller oväntade tecken, kontrol lera att EDIFACT-meddelanden använder UTF-8-teckenuppsättningen.

1. Under **Lägg till** väljer du **ta emot inställningar**.
Konfigurera dessa egenskaper utifrån ditt avtal med den partner som utbyter meddelanden med dig. För egenskaps beskrivningar, se tabellerna i det här avsnittet.

   **Ta emot inställningar** är indelat i följande avsnitt: identifierare, bekräftelse, scheman, kontroll nummer, verifiering och interna inställningar.

   ![Konfigurera "ta emot inställningar"](./media/logic-apps-enterprise-integration-edifact/edifact-2.png)  

2. När du är klar bör du spara inställningarna genom att välja **OK**.

Nu är ditt avtal redo att hantera inkommande meddelanden som överensstämmer med dina valda inställningar.

### <a name="identifiers"></a>Identifierare

| Egenskap | Beskrivning |
| --- | --- |
| UNB 6.1 (referens lösen ord för mottagare) |Ange ett alfanumeriskt värde mellan 1 och 14 tecken. |
| UNB 6.2 (kvalificerare för mottagar referens) |Ange ett alfanumeriskt värde med minst ett tecken och högst två tecken. |

### <a name="acknowledgments"></a>Tack till

| Egenskap | Beskrivning |
| --- | --- |
| Mottagning av meddelandet (CONTRL) |Markera den här kryss rutan om du vill returnera en teknisk (CONTRL) bekräftelse till Interchange-avsändaren. Bekräftelsen skickas till Interchange-avsändaren baserat på sändnings inställningarna för avtalet. |
| Bekräftelse (CONTRL) |Markera den här kryss rutan om du vill returnera en funktionell (CONTRL) bekräftelse till Interchange-avsändaren skickas bekräftelsen till Interchange-avsändaren baserat på sändnings inställningarna för avtalet. |

### <a name="schemas"></a>Scheman

| Egenskap | Beskrivning |
| --- | --- |
| UNH 2.1 (TYP) |Välj en transaktions uppsättnings typ. |
| UNH 2.2 (VERSION) |Ange versions numret för meddelandet. (Minst ett tecken, högst tre tecken). |
| UNH 2.3 (VERSION) |Ange meddelandets versions nummer. (Minst ett tecken, högst tre tecken). |
| UNH 2.5 (ASSOCIERAD TILLDELAD KOD) |Ange tilldelad kod. (Högst sex tecken. Måste vara alfanumeriskt). |
| UNG 2.1 (APP-AVSÄNDAR-ID) |Ange ett alfanumeriskt värde med minst ett tecken och högst 35 tecken. |
| UNG 2.2 (PROGRAMSÄNDARENS KOD KVALIFICERARE) |Ange ett alfanumeriskt värde med högst fyra tecken. |
| SCHEMA |Välj det tidigare laddade schemat som du vill använda från det associerade integrations kontot. |

### <a name="control-numbers"></a>Kontroll nummer

| Egenskap | Beskrivning |
| --- | --- |
| Tillåt inte dubbletter av utbytes kontroll nummer |Om du vill blockera dubbla ändringar väljer du den här egenskapen. Om det här alternativet markeras kontrollerar EDIFACT avkoda att Interchange Control Number (UNB5) för mottagen Interchange inte matchar ett tidigare bearbetat utbytes kontroll nummer. Om en matchning identifieras bearbetas inte växlingen. |
| Sök efter dubbletter av UNB5 var (dagar) |Om du väljer att inte tillåta dubbla utbytes kontroll nummer kan du ange antalet dagar när du vill utföra kontrollen genom att ange lämpligt värde för den här inställningen. |
| Tillåt inte dubbletter av grupp kontroll nummer |Om du vill blockera ändringar med duplicerade grupp kontroll nummer (UNG5) väljer du den här egenskapen. |
| Tillåt inte dubbletter av transaktions uppsättnings nummer |Om du vill blockera ändringar med dubbletter av transaktions uppsättnings nummer (UNH1) väljer du den här egenskapen. |
| Kontroll nummer för EDIFACT-bekräftelse |Ange ett värde för prefixet, ett intervall med referens nummer och ett suffix för att ange transaktions referens numren som ska användas i en bekräftelse. |

### <a name="validation"></a>Validering

När du Slutför varje validerings rad läggs en ny automatiskt till. Om du inte anger några regler använder valideringen raden "standard".

| Egenskap | Beskrivning |
| --- | --- |
| Meddelande typ |Välj typ av EDI-meddelande. |
| EDI-verifiering |Utföra EDI-verifiering på data typer som definieras av schemats EDI-egenskaper, längd begränsningar, tomma data element och avslutande avgränsare. |
| Utökad verifiering |Om data typen inte är EDI, är verifiering av data elementets krav och tillåten upprepning, uppräkningar och verifiering av data element längd (min/max). |
| Tillåt inledande/avslutande nollor |Behåll eventuella ytterligare inledande eller avslutande noll och blank stegs tecken. Ta inte bort de här tecknen. |
| Trimma inledande/avslutande nollor |Ta bort inledande eller avslutande noll och blank stegs tecken. |
| Avslutande avgränsnings princip |Generera avslutande avgränsare. <p>Välj **tillåts inte** för att förhindra avslutande avgränsare och avgränsare i mottaget utbyte. Om Interchange har avslutande avgränsare och avgränsare, deklareras inte Interchange som giltig. <p>Välj **valfritt** om du vill acceptera ändringar med eller utan efterföljande avgränsare och avgränsare. <p>Välj **obligatoriskt** när mottaget utbyte måste ha avslutande avgränsare och avgränsare. |

### <a name="internal-settings"></a>Interna inställningar

| Egenskap | Beskrivning |
| --- | --- |
| Skapa tomma XML-taggar om avslutande avgränsare tillåts |Markera den här kryss rutan om du vill att Interchange Sender ska innehålla tomma XML-taggar för efterföljande avgränsare. |
| Dela upp utbyte som transaktions uppsättningar – inaktivera transaktions uppsättningar vid fel|Parsar varje transaktion som angetts i ett utbyte till ett separat XML-dokument genom att använda lämpligt kuvert i transaktions uppsättningen. Pausa bara de transaktions uppsättningar som inte har verifierats. |
| Dela upp utbyte som transaktions uppsättningar – pausa utbyte vid fel|Parsar varje transaktion som anges i ett utbyte till ett separat XML-dokument genom att använda rätt kuvert. Pausa hela utbytet när en eller flera transaktions uppsättningar i överförings testet inte kunde verifieras. | 
| Bevara Interchange – pausa transaktions uppsättningar vid fel |Lämnar utväxlingen intakt, skapar ett XML-dokument för hela det batchade utbytet. Pausa bara de transaktions uppsättningar som inte kan verifieras, samtidigt som du fortsätter att bearbeta alla andra transaktions uppsättningar. |
| Bevara Interchange – pausa utbyte vid fel |Lämnar utväxlingen intakt, skapar ett XML-dokument för hela det batchade utbytet. Pausa hela utbytet när en eller flera transaktions uppsättningar i överförings testet inte kunde verifieras. |

## <a name="configure-how-your-agreement-sends-messages"></a>Konfigurera hur ditt avtal skickar meddelanden

Du kan konfigurera hur det här avtalet identifierar och hanterar utgående meddelanden som du skickar till dina partner genom detta avtal.

1.  Under **Lägg till** väljer du **Skicka inställningar**.
Konfigurera dessa egenskaper utifrån ditt avtal med din partner som utbyter meddelanden med dig. För egenskaps beskrivningar, se tabellerna i det här avsnittet.

    **Sändnings inställningarna** är indelade i följande avsnitt: identifierare, bekräftelse, scheman, kuvert, teckenuppsättningar och avgränsare, kontroll nummer och valideringar.

    ![Konfigurera "Skicka inställningar"](./media/logic-apps-enterprise-integration-edifact/edifact-3.png)    

2. När du är klar bör du spara inställningarna genom att välja **OK**.

Nu är ditt avtal redo att hantera utgående meddelanden som överensstämmer med dina valda inställningar.

### <a name="identifiers"></a>Identifierare

| Egenskap | Beskrivning |
| --- | --- |
| UNB 1.2 (syntax-version) |Välj ett värde mellan **1** och **4**. |
| UNB 2.3 (avsändarens adress för omvänd routning) |Ange ett alfanumeriskt värde med minst ett tecken och högst 14 tecken. |
| UNB 3.3 (mottagarens adress för omvänd routning) |Ange ett alfanumeriskt värde med minst ett tecken och högst 14 tecken. |
| UNB 6.1 (referens lösen ord för mottagare) |Ange ett alfanumeriskt värde med minst ett och högst 14 tecken. |
| UNB 6.2 (kvalificerare för mottagar referens) |Ange ett alfanumeriskt värde med minst ett tecken och högst två tecken. |
| UNB7 (program referens-ID) |Ange ett alfanumeriskt värde med minst ett tecken och högst 14 tecken |

### <a name="acknowledgment"></a>Godkänd

| Egenskap | Beskrivning |
| --- | --- |
| Mottagning av meddelandet (CONTRL) |Markera den här kryss rutan om den värdbaserade partnern förväntar sig att ta emot en teknisk (CONTRL) bekräftelse. Den här inställningen anger att den värd partner som skickar meddelandet, begär en bekräftelse från gäst partnern. |
| Bekräftelse (CONTRL) |Markera den här kryss rutan om en värd partner förväntar sig att ta emot en fungerande (CONTRL) bekräftelse. Den här inställningen anger att den värd partner som skickar meddelandet, begär en bekräftelse från gäst partnern. |
| Generera SG1/SG4-slinga för godkända transaktions uppsättningar |Om du väljer att begära en funktionell bekräftelse markerar du den här kryss rutan om du vill framtvinga generering av SG1/SG4-slingor i funktionella CONTRL-bekräftelser för godkända transaktions uppsättningar. |

### <a name="schemas"></a>Scheman

| Egenskap | Beskrivning |
| --- | --- |
| UNH 2.1 (TYP) |Välj en transaktions uppsättnings typ. |
| UNH 2.2 (VERSION) |Ange versions numret för meddelandet. |
| UNH 2.3 (VERSION) |Ange meddelandets versions nummer. |
| SCHEMA |Välj det schema som ska användas. Scheman finns i integrations kontot. För att få åtkomst till dina scheman länkar du först ditt integrations konto till din Logic app. |

### <a name="envelopes"></a>Kuvert

| Egenskap | Beskrivning |
| --- | --- |
| UNB8 (bearbetar prioritets kod) |Ange ett alfabetiskt värde som inte är längre än ett tecken. |
| UNB10 (kommunikations avtal) |Ange ett alfanumeriskt värde med minst ett tecken och högst 40 tecken. |
| UNB11 (test indikator) |Markera den här kryss rutan om du vill ange att den genererade Interchange-filen är test data |
| Tillämpa UNA-segment (tjänst Strängs råd) |Markera den här kryss rutan om du vill generera ett UNA-segment för det utbyte som ska skickas. |
| Använda UNG segment (funktions grupps huvud) |Markera den här kryss rutan om du vill skapa grupperade segment i funktions grupps huvudet i meddelanden som skickas till gäst partnern. Följande värden används för att skapa UNG-segment: <p>För **UNG1** anger du ett alfanumeriskt värde med minst ett tecken och högst sex tecken. <p>För **ung 2.1** anger du ett alfanumeriskt värde med minst ett tecken och högst 35 tecken. <p>För **ung 2.2** anger du ett alfanumeriskt värde med högst fyra tecken. <p>För **ung 3.1** anger du ett alfanumeriskt värde med minst ett tecken och högst 35 tecken. <p>För **ung 3.2** anger du ett alfanumeriskt värde med högst fyra tecken. <p>För **UNG6** anger du ett alfanumeriskt värde med minst en och högst tre tecken. <p>För **ung 7.1** anger du ett alfanumeriskt värde med minst ett tecken och högst tre tecken. <p>För **ung 7,2** anger du ett alfanumeriskt värde med minst ett tecken och högst tre tecken. <p>För **ung 7.3** anger du ett alfanumeriskt värde med minst 1 tecken och högst 6 tecken. <p>För **UNG8** anger du ett alfanumeriskt värde med minst ett tecken och högst 14 tecken. |

### <a name="character-sets-and-separators"></a>Teckenuppsättningar och avgränsare

Förutom teckenuppsättningen kan du ange en annan uppsättning avgränsare som ska användas för varje meddelande typ. Om ingen teckenuppsättning har angetts för ett visst meddelande schema används standard teckenuppsättningen.

| Egenskap | Beskrivning |
| --- | --- |
| UNB 1.1 (system identifierare) |Välj den EDIFACT som ska tillämpas på utgående Interchange. |
| Schema |Välj ett schema från den nedrullningsbara listan. När du har slutfört varje rad läggs en ny rad till automatiskt. För det valda schemat väljer du de avgränsnings uppsättningar som du vill använda, baserat på avgräns beskrivningarna nedan. |
| Indatatyp |Välj en indatatyp i list rutan. |
| Komponent avgränsare |Om du vill separera sammansatta data element anger du ett enda-Character. |
| Data Elements avgränsare |Om du vill separera enkla data element i sammansatta data element anger du ett enskilt. |
| Segment begränsare |Om du vill ange ett EDI-segments slut anger du ett enskilt tecken. |
| Huvudnamnssuffix |Välj det tangent som används med segment identifieraren. Om du anger ett suffix, kan data elementet för segment begränsas vara tomt. Om avgränsarna för segment lämnas tomma måste du ange ett suffix. |

### <a name="control-numbers"></a>Kontroll nummer

| Egenskap | Beskrivning |
| --- | --- |
| UNB5 (Interchange kontroll nummer) |Ange ett prefix, ett värde intervall för utbytes kontroll numret och ett suffix. Dessa värden används för att generera ett utgående utbyte. Prefixet och suffixet är valfritt, medan kontroll numret måste anges. Kontroll numret ökar för varje nytt meddelande. prefixet och suffixet är oförändrat. |
| UNG5 (grupp kontroll nummer) |Ange ett prefix, ett värde intervall för utbytes kontroll numret och ett suffix. Dessa värden används för att generera grupp kontroll numret. Prefixet och suffixet är valfritt, medan kontroll numret måste anges. Kontroll numret ökar för varje nytt meddelande tills det maximala värdet har uppnåtts. prefixet och suffixet är oförändrat. |
| UNH1 (referens nummer för meddelande rubrik) |Ange ett prefix, ett värde intervall för utbytes kontroll numret och ett suffix. Dessa värden används för att generera referens numret för meddelande rubriken. Prefixet och suffixet är valfritt, medan referens numret är obligatoriskt. Referens numret ökar för varje nytt meddelande. prefixet och suffixet är oförändrat. |

### <a name="validation"></a>Validering

När du Slutför varje validerings rad läggs en ny automatiskt till. Om du inte anger några regler använder valideringen raden "standard".

| Egenskap | Beskrivning |
| --- | --- |
| Meddelande typ |Välj typ av EDI-meddelande. |
| EDI-verifiering |Utföra EDI-verifiering på data typer som definieras av EDI-egenskaperna i schemat, med längd begränsningar, tomma data element och avslutande avgränsare. |
| Utökad verifiering |Om data typen inte är EDI, är verifiering av data elementets krav och tillåten upprepning, uppräkningar och verifiering av data element längd (min/max). |
| Tillåt inledande/avslutande nollor |Behåll eventuella ytterligare inledande eller avslutande noll och blank stegs tecken. Ta inte bort de här tecknen. |
| Trimma inledande/avslutande nollor |Ta bort inledande eller avslutande noll tecken. |
| Avslutande avgränsnings princip |Generera avslutande avgränsare. <p>Välj **tillåts inte** för att förhindra avslutande avgränsare och avgränsare i skickade Interchange. Om Interchange har avslutande avgränsare och avgränsare, deklareras inte Interchange som giltig. <p>Välj **valfritt** om du vill skicka ändringar med eller utan efterföljande avgränsare och avgränsare. <p>Välj **obligatoriskt** om den skickade Interchange måste ha efterföljande avgränsare och avgränsare. |

## <a name="find-your-created-agreement"></a>Hitta ditt skapade avtal

1.  När du är klar med att ange alla avtals egenskaper går du till sidan **Lägg till** och väljer **OK** för att slutföra skapandet av ditt avtal och återgår till ditt integrations konto.

    Ditt nyligen tillagda avtal visas nu i **avtals** listan.

2.  Du kan också visa dina avtal i Översikt över integrations kontot. På integrations konto menyn väljer du **Översikt** och väljer sedan **avtals** panelen. 

    ![Skärm bild som visar avtals panelen.](./media/logic-apps-enterprise-integration-edifact/edifact-4.png)   

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer teknisk information om den här anslutningen, till exempel åtgärder och begränsningar som beskrivs av kopplingens Swagger-fil, finns på [kopplingens referens sida](/connectors/edifact/).

> [!NOTE]
> För logi Kap par i en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ISE-märkta version de [B2B-meddelande gränserna för ISE](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
