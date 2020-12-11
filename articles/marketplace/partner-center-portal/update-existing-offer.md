---
title: Uppdatera ett befintligt erbjudande – Microsofts kommersiella marknads platser
description: Hur du gör uppdateringar av ett befintligt erbjudande för handels platser eller planerar, synkroniserar privata mål grupper och tar bort ett erbjudande.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: trkeya
ms.author: trkeya
ms.date: 10/27/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 26ceee828bae7ea3fe2d78ec1393f308556b8401
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033365"
---
# <a name="update-existing-offers-in-the-commercial-marketplace"></a>Uppdatera befintliga erbjudanden på den kommersiella Marketplace

Den här artikeln förklarar hur du gör uppdateringar av befintliga erbjudanden och planer, samt hur du tar bort ett erbjudande från den kommersiella Marketplace. Du kan visa dina erbjudanden på fliken **Översikt** i den [kommersiella Marketplace-portalen](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) i Partner Center.

## <a name="update-a-published-offer"></a>Uppdatera ett publicerat erbjudande

Följ dessa steg om du vill uppdatera ett erbjudande som har publicerats i för hands versionen eller i Live-tillstånd.

1. Välj namnet på erbjudandet som du vill uppdatera. Status för erbjudandet kan visas som för **hands version**, **Live**, **publicering** pågår, **utkast**, **åtgärd krävs** eller **inte tillgängligt** (om du tidigare har valt att sluta sälja erbjudandet). När du har valt så öppnas sidan **erbjudande översikt** för det erbjudandet.
1. Välj den erbjudande sida som du vill uppdatera, till exempel **Egenskaper**, **erbjudande lista** eller för **hands version** (eller Välj **Uppdatera** från lämpligt kort på sidan **erbjudande översikt** ).
1. Gör dina ändringar och välj **Spara utkast**. Upprepa processen tills alla ändringar har slutförts.
1. Granska ändringarna på sidan **[Jämför](#compare-changes-to-your-offer)** .
1. När du är redo att publicera ditt uppdaterade erbjudande väljer du **Granska och publicera** från vilken sida som helst. Sidan **Granska och publicera** öppnas. På den här sidan visas slut för ande status för avsnitten i erbjudandet som du uppdaterade: 
    - **Opublicerade ändringar**: avsnittet har uppdaterats och är klart. Alla begärda data har angetts och inga fel infördes i uppdateringarna.
    - **Ofullständig**: uppdateringar som görs i avsnittet introducerade fel som måste åtgärdas eller som kräver mer information.
2. Välj **publicera** för att skicka det uppdaterade publicerings erbjudandet. Ditt erbjudande kommer sedan att gå igenom standard [validerings-och publicerings stegen](../review-publish-offer.md#validation-and-publishing-steps).

> [!IMPORTANT]
> Du måste granska din för hands version av erbjudandet när den är tillgänglig och välja **Go-Live** för att publicera ditt uppdaterade erbjudande till din avsedda mål grupp (offentlig eller privat).

## <a name="add-a-plan-to-an-existing-offer"></a>Lägg till en plan i ett befintligt erbjudande

Slutför de här stegen för att lägga till en ny plan till ett erbjudande som du redan har publicerat.

1. På sidan **erbjudande översikt** för det befintliga erbjudandet öppnar du sidan **plan översikt** och väljer sedan **Skapa ny plan**.
1. Skapa en ny plan enligt [rikt linjerna](../plans-pricing.md) genom att använda **pris modellen befintliga planer**.
1. Välj **Spara utkast** när du har ändrat namnet på planen.
1. Välj **publicera** när du är redo att publicera dina uppdateringar. Sidan **[Granska och publicera](../review-publish-offer.md)** öppnas och ger en slut för ande status för dina uppdateringar.

## <a name="update-a-plan-for-an-existing-offer"></a>Uppdatera en plan för ett befintligt erbjudande

Utför de här stegen för att göra ändringar i en plan för ett erbjudande som du redan har publicerat.

1. På sidan **erbjudande översikt** för det befintliga erbjudandet väljer du den plan som du vill ändra. Om planen inte är tillgänglig från listan **plan översikt** väljer du **Se alla planer**.
1. Välj plan **namn**, **pris modell** eller **tillgänglighet**. *För närvarande är planer bara tillgängliga på engelska (USA)*.
1. Välj **Spara utkast** när du har ändrat plan namn, beskrivning eller tillgänglighet för mål gruppen.
1. Välj **Granska och publicera** när du är redo att publicera dina uppdateringar. Sidan **[Granska och publicera](../review-publish-offer.md)** öppnas och ger en slut för ande status för dina uppdateringar.
1. Välj **publicera** för att skicka den uppdaterade planen för publicering. Vi skickar e-post till dig när en för hands version av det uppdaterade erbjudandet kan granskas och godkännas.

## <a name="offer-a-virtual-machine-plan-at-a-new-price"></a>Erbjud en virtuell dator plan till ett nytt pris

När en virtuell dator plan har publicerats går det inte att ändra dess pris. Om du vill erbjuda samma plan till ett annat pris måste du dölja planen och skapa en ny med det uppdaterade priset. Börja med att dölja planen med det pris som du vill ändra:

1. På sidan **erbjudande översikt** för det befintliga erbjudandet väljer du den plan som du vill ändra. Om planen inte är tillgänglig från listan **plan översikt** väljer du **Se alla planer**.
1. Markera kryss rutan **Dölj plan** . Spara utkastet innan du fortsätter.

Nu när du har dolt planen med det gamla priset skapar du en kopia av planen med det uppdaterade priset:

1. Gå tillbaka till **plan översikt** i Partner Center.
2. Välj **Skapa ny plan**. Ange ett **plan-ID** och ett **plan namn** och välj sedan **skapa**.
1. Om du vill återanvända den tekniska konfigurationen från den plan som du har dolt, markerar du kryss rutan **Återanvänd teknisk konfiguration** . Läs [skapa planer för ett virtuellt dator erbjudande](../azure-vm-create-plans.md) om du vill veta mer.
    > [!IMPORTANT]
    > Om du väljer **den här planen återanvänder teknisk konfiguration från en annan plan** kan du inte sluta sälja den överordnade planen senare. Använd inte det här alternativet om du inte vill sluta att sälja den överordnade planen.
3. Slutför alla nödvändiga avsnitt för den nya planen, inklusive det nya priset.
1. Välj **Spara utkast**.
1. När du har slutfört alla nödvändiga avsnitt för den nya planen väljer du **Granska och publicera**. Detta skickar ditt erbjudande om granskning och publicering. Läs [Granska och publicera ett erbjudande på den kommersiella Marketplace](../review-publish-offer.md) för mer information.

## <a name="sync-private-plan-audiences"></a>Synkronisera privat planens mål grupper

Om erbjudandet innehåller en eller flera planer som är konfigurerade för att endast vara tillgängliga för en privat begränsad publik, är det möjligt att endast uppdatera den mål grupp som har åtkomst till det privata schemat utan att publicera andra ändringar i erbjudandet. 

Så här uppdaterar och synkroniserar du den privata mål gruppen för dina abonnemang:

- Ändra mål gruppen i ett eller flera privata planer med knappen + **Lägg till ID** eller **Importera kunder (CSV)** och spara sedan ändringarna.
- Välj **Synkronisera privat publik** på sidan **plan översikt** .

**Synkronisera privat publik** publicerar bara ändringar i dina privata mål grupper, utan att publicera några andra uppdateringar som du har gjort i utkastet till erbjudandet.

## <a name="compare-changes-to-your-offer"></a>Jämför ändringar i erbjudandet

Innan du publicerar uppdateringar i Live-eller [Preview](#compare-changes-to-a-preview-offer) -erbjudandet kan du granska de sparade ändringarna på sidan **Jämför** . Du kan komma åt sidan **Jämför** i det övre högra hörnet på alla erbjudande sidor, till exempel sidan **Egenskaper** eller **erbjudande lista** . På sidan **Jämför** visas sida-vid-sida-versioner av de sparade ändringarna i erbjudandet och det publicerade Marketplace-erbjudandet.

- Du kan använda **Jämför** när som helst under redigerings processen.
- Välj ett fält på sidan **Jämför** för att navigera till det värde som du vill ändra.
- Om du vill se värdena för alla fält, till och med fält som inte har uppdaterats, väljer du filtret **alla fält** . Du kan ändra filtren i dessa fält genom att välja **ändrade fält** och sedan välja ett av filtren nedan:
    - Filter för **borttagna värden** visar fält som du har publicerat och som du nu tar bort helt.
    - Filtret för **tillagda värden** visar fält som du inte ursprungligen publicerade och som nu läggs till.
    - Filtret för **redigerade värden** visar fält som har publicerats men nu har uppdaterat innehållet.

      >[!NOTE]
      > Om något av dessa filter inte är tillgängligt indikerar det att du inte har gjort en uppdatering av den typen.

- Om du bara vill se värden som inte har uppdaterats väljer du filtret **oförändrade fält** . Fältvärdena som visas för den publicerade och utkast versionen är samma.

  ![Filter för att jämföra uppdateringar med ditt publicerade eller för hands versions erbjudande](./media/compare-changes-marketplace.png)

>[!NOTE]
> Följande sidor stöder för närvarande inte **jämförelse**:
>- CSP åter försäljarens publik
>- Teknisk konfiguration för test enhet
>- Testa enhetens Marketplace-lista
>- Co-försäljning
>- Kompletterande filer

Kom ihåg att publicera om erbjudandet igen när du har gjort uppdateringar för att ändringarna ska börja gälla.

### <a name="compare-changes-to-a-preview-offer"></a>Jämför ändringar i ett för hands erbjudande

Om du har ändringar i för hands versionen som inte är Live kan du jämföra nya ändringar med erbjudandet för hands versions Marketplace.

1. Välj **Jämför** i kommando fältet på sidan.
2. Välj List rutan **med** och ändra den från **Live-erbjudandet** till för **hands versions erbjudandet**. Om ditt erbjudande inte har varit aktivt än kan du inte se alternativet **Live-erbjudande** .
3. Sidan **Jämför** innehåller versioner sida vid sida som visar ändringarna.

Kom ihåg att publicera om erbjudandet igen när du har gjort uppdateringar för att ändringarna ska börja gälla.

## <a name="stop-selling-an-offer-or-plan"></a>Sluta sälja ett erbjudande eller en plan

Du kan ta bort erbjudande listor och planer från Microsofts kommersiella marknads plats, vilket gör det möjligt för nya kunder att hitta och köpa dem. Kunder som tidigare förvärvade erbjudandet eller abonnemanget kan fortfarande använda det, och de kan hämta det igen om det behövs. De får dock inga uppdateringar om du bestämmer dig för att publicera om erbjudandet eller planen vid ett senare tillfälle.

- Om du vill sluta att sälja ett erbjudande när du har publicerat det väljer du **sluta sälja** från sidan **erbjudande översikt** . Inom några timmar från din bekräftelse kommer erbjudandet inte längre att synas på den kommersiella Marketplace.

- Om du vill sluta sälja en plan väljer du **sluta sälja** från sidan **plan översikt** . Alternativet att sluta sälja en plan är bara tillgängligt om du har mer än en prenumeration på erbjudandet. Du kan välja att sluta sälja en plan utan att påverka andra planer i erbjudandet.
     >[!NOTE]
     > När du har bekräftat att du vill sluta sälja planen måste du publicera om erbjudandet för att ändringen ska börja gälla.

När du har slutat sälja ett erbjudande eller en plan ser du fortfarande det i Partner Center med statusen **inte tillgänglig** . Om du väljer att lista eller sälja erbjudandet eller planeraren igen följer du anvisningarna för att [Uppdatera ett publicerat erbjudande](#update-a-published-offer). Glöm inte att du behöver **publicera** erbjudandet eller planera igen när du har gjort några ändringar.

## <a name="remove-offers-from-existing-customers"></a>Ta bort erbjudanden från befintliga kunder

[Logga en supportbegäran](https://aka.ms/marketplacepublishersupport)om du vill ta bort erbjudanden från befintliga kunder. I listan support ämne väljer du erbjudande för **extern Marketplace**  >  **eller app-delistor, borttagning eller uppsägning** och skicka begäran. Support teamet leder dig genom borttagnings processen för erbjudandet.

## <a name="next-steps"></a>Nästa steg

- [Kontrol lera publicerings statusen för ditt kommersiella Marketplace-erbjudande](../review-publish-offer.md)
