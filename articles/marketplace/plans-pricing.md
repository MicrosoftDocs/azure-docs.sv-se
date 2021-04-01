---
title: Planer och priser för kommersiella Marketplace-erbjudanden
description: Lär dig mer om planer för Microsofts kommersiella Marketplace-erbjudanden i Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: mingshen
ms.author: mingshen
ms.date: 09/02/2020
ms.openlocfilehash: e97171e61814b832e78f7bd815d8d6f341a90840
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91858180"
---
# <a name="plans-and-pricing-for-commercial-marketplace-offers"></a>Planer och priser för kommersiella Marketplace-erbjudanden

En plan definierar ett erbjudandes omfattning och begränsningar samt tillhör ande priser om det är tillämpligt. Beroende på typen av erbjudande kan du till exempel välja regionala marknader och välja om en plan ska vara synlig för allmänheten eller bara för en privat mål grupp. Vissa erbjudande typer har stöd för återkommande prenumerationer, vissa support användnings priser och några gör att kunden kan köpa erbjudandet med en licens som de har köpt direkt från utgivaren. Detta ger dig flexibiliteten att förse dina kunder med olika tekniska och pris alternativ, i förekommande fall.

Du kan skapa upp till 100-planer för varje erbjudande som stöder planer, och upp till 45 av dessa planer kan vara [privata](#plan-visibility). Du kan bara skapa betalda planer för virtuella Azure-datorer, erbjudanden för Azure-program (hanterade program) och SaaS-erbjudanden (Software as a Service). När du väljer att sälja någon av dessa erbjudanden via Microsoft (transactable-erbjudanden) måste du skapa minst en plan. Du kan skapa planer för några av de andra erbjudande typerna, men planerna för dessa erbjudande typer omfattar inte pris alternativ.

> [!TIP]
> Ett transactable-erbjudande är ett där Microsoft fören klar utbytet av pengar för en program varu licens för utgivarens räkning.

## <a name="plans-by-offer-type"></a>Abonnemang efter erbjudande typ

I följande tabell visas plan alternativen för varje erbjudande typ. I följande avsnitt förklaras mer om de här alternativen. 

| Erbjudandetyp | Abonnemang med pris alternativ | Planer utan pris alternativ | Alternativ för privat publik |
| ------------ | ------------- | ------------- | ------------- |
| Azure-hanterat program | &#10004; | | &#10004; |
| Mall för Azure-lösning | | &#10004; | &#10004; |
| Azure-behållare | | &#10004; (BYOL) | |
| IoT Edge modul |  | &#10004; |  |
| Hanterad tjänst |  | &#10004; (BYOL) | &#10004; |
| Programvara som en tjänst | &#10004; |  | &#10004; |
| Virtuell Azure-dator | &#10004; |  | &#10004; |
|||||

Planer stöds inte för följande erbjudande typer:

- Konsult tjänst
- Dynamics 365 Business Central
- Dynamics 365 kund engagemang & PowerApps
- Dynamics 365 for Operations
- Power BI-app

## <a name="plan-information"></a>Planera information

Varje erbjudande typ kräver annan information när du skapar en ny plan. Du hittar länkar till erbjudanden om Special artiklar i [publicerings guiden efter erbjudande typ](publisher-guide-by-offer-type.md). När du skapar en ny plan på sidan **plan översikt** kan du se olika flikar, till exempel **Planera lista** eller **prissättning och tillgänglighet** för att konfigurera annan information för din prenumeration. På varje flik visas statusen ofullständig eller slutförd när du arbetar med de här fälten.

![Visar sidan med plan listan i Partner Center. Fälten planera namn, plan Sammanfattning och plan beskrivning är markerade.](./media/commercial-marketplace-plans/plan-listing-tab.png)

Det finns några vanliga uppgifter att slutföra för en ny plan:

- **Plans-ID**: skapa ett unikt ID för varje plan i det här erbjudandet. Använd högst 50 tecken: endast gemener, alfanumeriska tecken, bindestreck och under streck. Detta ID visas för kunder i produkt-URL och Azure Resource Manager mallar (om tillämpligt). Du kan inte ändra detta ID när du har publicerat erbjudandet.
- **Plan namn**: (bild text 1 i bilden ovan) Skapa ett unikt namn för varje plan i det här erbjudandet. Använd högst 50 tecken. Plan namnet används för att särskilja program varu planer som kan vara en del av samma erbjudande (till exempel erbjudande namn: standard plan och företags plan). Kunder ser det här namnet när du bestämmer vilken plan du vill välja i erbjudandet.
- **Plan Sammanfattning**: (bild text 2 i bilden ovan) Den här sammanfattningen visas i Sök resultaten i Azure Marketplace och kan innehålla upp till 100 tecken.
   > [!NOTE]
   > Det här fältet gäller inte för SaaS-erbjudanden.
- **Plan Beskrivning**: (bild text 3 i bilden ovan) Lägg till en plan beskrivning som förklarar vad som gör den här planen unik från andra prenumerationer för ditt erbjudande. Använd högst 500 tecken. Det här innehållet kommer att visas för dina kunder på sidan med erbjudanden för erbjudanden när de bläddrar igenom och väljer en plan för ditt erbjudande.

Plan namnet och beskrivningen visas på sidan för erbjudande listan i de kommersiella Marketplace Online Store (ar). Följande skärm bild visar tre planer för SaaS-erbjudanden på Azure Marketplace.

![Visar en lista med erbjudande sidor i Partner Center. Tre planer visas.](./media/commercial-marketplace-plans/offer-listing-page.png)

När du har skapat dina planer visar sidan **plan översikt** en lista över dina prenumerationers namn, ID, annan information, aktuell publicerings status och alla tillgängliga åtgärder. Vilka åtgärder som är tillgängliga beror på planens status och kan vara:

- Om plan status är **Draft** – ta bort utkast.
- Om plan status är **Live** – avsluta Sälj plan eller synkronisera privat mål grupp.

Följande skärm bild visar två utkast erbjudanden.

![Visar sidan plan översikt i Partner Center. Två planer visas.](./media/commercial-marketplace-plans/plan-overview-tab.png)

## <a name="pricing-and-availability"></a>Priser och tillgänglighet

Den kommersiella marknads platsen fungerar på en byrå modell, där utgivare anger priser, Microsoft fakturerar kunder och Microsoft betalar intäkterna till utgivare samtidigt som en Agen tur avgift debiteras. Du definierar ditt erbjudandes marknader, synlighet och priser (om tillämpligt) på fliken **priser och tillgänglighet** eller **tillgänglighet** .

- **Marknader**: varje plan måste vara tillgänglig på minst en marknad.  Du kan välja att bara välja "skatte mottagare", där Microsoft betalar och använder moms för din räkning.
- **Priser**: pris modeller gäller endast för planer för Azure-hanterade program, SaaS och erbjudanden för virtuella Azure-datorer. Alla planer för samma erbjudande måste använda samma pris modell.  
- **Plan synlighet**: beroende på erbjudande typen kan du definiera en privat mål grupp eller dölja erbjudandet eller planen från Azure Marketplace. Detta beskrivs mer detaljerat i [Plans visning](#plan-visibility) senare i den här artikeln.

> [!TIP]
> Vi rekommenderar att du skapar planer som är bäst lämpade för användnings mönstren för mål kund basen. Detta reducerar användare från att ofta byta plan utifrån deras användnings ändringar. Ett exempel på ett SaaS-erbjudande med tre avgiftsbelagda fakturerings planer finns i [exempel erbjudande](./partner-center-portal/saas-metered-billing.md#sample-offer).

### <a name="pricing-models"></a>Prissättningsmodeller

Du måste koppla en pris modell till varje plan för följande typer av erbjudanden. Var och en av dessa erbjudande typer har olika tillgängliga pris modeller:

- **Azure-hanterat program**: fast pris (månatlig) och användnings priser (mätnings tjänst dimensioner).
- **Program vara som en tjänst**: fast pris (månatlig eller årlig), per användare och användnings priser (mätnings tjänst dimensioner). 
- **Virtuell Azure-dator**: Bring Your Own License (BYOL) och användnings baserat pris. För en användnings-baserad pris modell kan du debitera per kärna, per kärn storlek eller per marknad och kärn storlek. En BYOL-licens modell tillåter inte ytterligare, användnings avgifter.   (BYOL Virtual Machine-erbjudanden kräver ingen pris sättnings modell.)

Alla planer för samma erbjudande måste använda samma pris modell. Ett SaaS-erbjudande kan till exempel inte ha ett plan som är fast pris och en annan plan per användare. Mer information finns i dokumentationen för den särskilda erbjudandet.

Om du redan har angett priser för din plan i USA dollar (USD) och lagt till en annan marknads plats kommer priset för den nya marknaden att beräknas enligt de aktuella växelkurserna. När du har sparat ändringarna visas en länk för **export priser (xlsx)** som du kan använda för att granska och ändra priset för varje marknad innan du publicerar.

> [!IMPORTANT]
> Valet av pris modell kan inte ändras när erbjudandet har publicerats.

Fasta SaaS-erbjudanden och hanterade program erbjuder stöd för fakturering av priser med hjälp av Marketplace-avläsning. Det här är en användnings-baserad fakturerings modell som gör att du kan definiera enheter som inte är standard, t. ex. bandbredd eller e-post, som kunderna betalar per användning. Se relaterad dokumentation för att lära dig mer om avgiftsbelagd fakturering för [hanterade program](./partner-center-portal/azure-app-metered-billing.md) och [SaaS-appar](./partner-center-portal/saas-metered-billing.md).

## <a name="custom-prices"></a>Anpassade priser

Om du vill ange anpassade priser på en enskild marknad, exportera, ändra och importera sedan pris kalkyl bladet. Du ansvarar för att verifiera priset och äga dessa inställningar.

Granska dina priser noggrant innan du publicerar, eftersom det finns vissa begränsningar för vad som kan ändras när en plan publiceras:

- När en plan har publicerats går det inte att ändra pris sättnings modellen.
- När en fakturerings period har publicerats för en plan kan du inte ta bort den senare.
- När du har publicerat ett pris för en marknad i planen kan du inte ändra det senare.

Priserna som anges i USA dollar (USD) konverteras till den lokala valutan för alla valda marknader med de aktuella växelkurserna när de sparas. Validera dessa priser innan du publicerar genom att exportera pris kalkyl bladet och granska priset på varje marknad som du har valt.

1. Du måste först spara dina pris ändringar för att möjliggöra export av pris information. Nästan längst ned på fliken **priser och tillgänglighet** väljer du **Spara utkast**.
1. Under **prissättning** väljer du länken **Exportera pris data** .
1. Öppna exportedPrice.xlsx-filen i Microsoft Excel.
1. I kalkyl bladet gör du de uppdateringar som du vill ha på din pris information och sparar sedan. CSV-fil.
    > [!NOTE]
    > Du kan behöva aktivera redigering i Excel innan du kan uppdatera filen.
1. På fliken **priser och tillgänglighet** under **prissättning** väljer du länken **Importera prissättnings data** .
1. I dialog rutan som visas väljer du **Ja**.
1. Välj den exportedPrice.xlsx filen som du uppdaterade och välj sedan **Öppna**.

## <a name="plan-visibility"></a>Plan synlighet

Du kan göra planer för vissa erbjudande typer offentligt tillgängliga eller tillgängliga enbart för en viss (privat) publik. Erbjudanden med privata planer publiceras till Azure Portal. Lär dig mer om privata planer i [privata erbjudanden på Microsofts kommersiella marknads platser](private-offers.md).

Du definierar din privata mål grupp genom att använda Azures klient-ID eller ID för Azure-prenumeration, beroende på typen av erbjudande. Du kan ange upp till 10 ID: n manuellt eller importera upp till 10, 00 prenumerations-ID: n eller 20 000 klient-ID: n (om tillämpligt) med en. csv-fil. Du kan också definiera privata mål grupper för konsult tjänster och Dynamics 365-erbjudanden som inte har några planer.

När ditt erbjudande har publicerats med en privat plan kan du uppdatera mål gruppen eller välja att göra planen tillgänglig för alla. När en plan har publicerats som synlig för alla måste den förbli synlig för alla och kan inte konfigureras som en privat plan igen.

> [!NOTE]
> En privat mål grupp skiljer sig från en förhands gransknings publik. På **tillgänglighets** sidan för vissa erbjudande typer kan du definiera en mål grupp som kan förhandsgranska ditt erbjudande innan erbjudandet publiceras Live på den kommersiella marknads platsen. Även om den privata mål grupps beteckningen bara gäller för en viss plan, kan förhands gransknings publiken Visa alla planer (privata eller inte), men endast under den begränsade för hands perioden medan planen testas och verifieras.

Du kan också välja att dölja planen helt från Azure Marketplace om ditt erbjudande ska distribueras som en del av en hanterad program-eller lösnings mall.

## <a name="free-trials"></a>Kostnadsfria utvärderingsversioner

Du kan aktivera en kostnads fri utvärderings version av planerna för den virtuella Azure-datorn och SaaS-erbjudanden.

> [!NOTE]
> I det här avsnittet beskrivs kostnads fria utvärderingar av betalda planer för erbjudanden som säljs via Microsoft. Detta skiljer sig från de kostnads fria utvärderings listorna från partner som väljer att bearbeta transaktionerna oberoende av varandra. Du kan skapa en lista över kostnads fria utvärderings listor (som inte är associerade med en plan) för följande erbjudande typer:
> - Virtuell Azure-dator 
> - SaaS 
> - Dynamics 365 Business Central
> - Dynamics 365 för kund engagemang & PowerApps
> - Dynamics 365 for Operations
>
> Mer information om List alternativ finns i [bestämma publicerings](determine-your-listing-type.md)alternativ.

Kostnads fria utvärderings versioner stöds för alla fakturerings modeller utom för avgiftsbelagda planer. SaaS-prenumerationer tillåter en kostnads fri utvärderings version på en månad. Med prenumerationer på virtuella Azure-datorer kan du använda en kostnads fri utvärderings version på 1, 3 eller 6 månader.

När en kund väljer en kostnads fri utvärderings version samlar vi in fakturerings informationen, men vi påbörjar inte att fakturera kunden förrän utvärderings versionen har konverterats till en betald prenumeration. Kostnads fria utvärderings versioner omvandlas automatiskt till en betald prenumeration i slutet av utvärderings versionen, om inte kunden avbryter prenumerationen innan utvärderings perioden är slut.

Under utvärderings perioden kan kunderna utvärdera vilka planer som stöds inom samma erbjudande som har en kostnads fri utvärderings version aktive rad. Om de växlar till en annan utvärderings version inom samma erbjudande startar inte utvärderings perioden om. Om en kund till exempel använder en kostnads fri utvärderings version i 15 dagar och sedan växlar till en annan kostnads fri utvärderings version av samma erbjudande, kommer den nya utvärderings perioden att redovisa 15 dagar. Den kostnads fria utvärderings versionen som kunden utvärderade när utvärderings perioden upphör är den som konverteras automatiskt till en betald prenumeration.

När en kund väljer en kostnads fri utvärderings version av en plan kan de inte omvandlas till en betald prenumeration för planen förrän utvärderings perioden har upphört. Om en kund väljer att konvertera till en annan plan inom erbjudandet som inte har en kostnads fri utvärderings version sker konverteringen, men den kostnads fria utvärderings versionen avslutas omedelbart och alla data går förlorade.

> [!NOTE]
> När en kund börjar betala för en plan kan de inte få en kostnads fri utvärderings version på samma erbjudande igen, även om de byter till ett annat plan som stöder kostnads fria utvärderings versioner.

För att få information om kund prenumerationer som för närvarande ingår i en kostnads fri utvärderings version använder du den nya API `isFreeTrial` -egenskapen som kommer att markeras som sant eller falskt. Mer information finns i API för [SaaS get-prenumeration](./partner-center-portal/pc-saas-fulfillment-api-v2.md#get-subscription).

## <a name="next-steps"></a>Nästa steg

- Information om hur du lägger till eller uppdaterar planer på ett befintligt erbjudande finns i [Uppdatera ett befintligt erbjudande i den kommersiella Marketplace](./partner-center-portal/update-existing-offer.md).
- Om du vill veta mer om transaktions alternativ och tillhör ande pris modeller kan du läsa om [Transact-funktioner för kommersiella Marketplace](./marketplace-commercial-transaction-capabilities-and-considerations.md).
