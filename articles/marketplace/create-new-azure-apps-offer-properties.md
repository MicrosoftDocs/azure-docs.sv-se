---
title: Så här konfigurerar du egenskaper för Azure Applications erbjudande
description: Lär dig hur du konfigurerar egenskaperna för ditt Azure Application-erbjudande i Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 6f92c138a02c9dbdc1d22ca04c733cfbee69dcd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94488527"
---
# <a name="how-to-configure-your-azure-application-offer-properties"></a>Så här konfigurerar du egenskaper för Azure Applications erbjudande

I den här artikeln beskrivs hur du konfigurerar egenskaperna för ett Azure Application erbjudande i den kommersiella Marketplace.

På sidan **Egenskaper** definierar du de kategorier som är tillämpliga för ditt erbjudande och juridiska kontrakt. Se till att tillhandahålla fullständig och korrekt information om ditt erbjudande på den här sidan, så att det visas på lämpligt sätt och erbjuds till rätt kund uppsättning.

## <a name="select-a-category-for-your-offer"></a>Välj en kategori för ditt erbjudande

Under **Kategorier** väljer du länken **Kategorier** och väljer sedan minst en och upp till två kategorier för att gruppera ditt erbjudande i lämpliga sökområden för kommersiella Marketplace. Välj upp till två under Kategorier för varje primär och sekundär kategori. Om ingen under kategori gäller ditt erbjudande väljer du **ej tillämpligt**.

## <a name="provide-terms-and-conditions"></a>Ange villkor

Under **juridisk**, ange villkor för ditt erbjudande. Du kan välja mellan två alternativ:

- [Använd standard kontraktet med valfria ändringar](#use-the-standard-contract)
- [Använd egna villkor](#use-your-own-terms-and-conditions)

Mer information om standard kontraktet och valfria ändringar finns i [standard kontrakt för Microsoft Commercial Marketplace](standard-contract.md). Du kan ladda ned [standard kontraktet](https://go.microsoft.com/fwlink/?linkid=2041178) PDF (se till att blockering av popup-fönster är inaktiverat).

### <a name="use-the-standard-contract"></a>Använd standard kontraktet

För att förenkla inköps processen för kunder och minska den juridiska komplexiteten för program varu leverantörer erbjuder Microsoft ett standard kontrakt som du kan använda för dina erbjudanden på den kommersiella marknaden. När du erbjuder program varan under standard kontraktet behöver kunderna bara läsa och godkänna den en gång, och du behöver inte skapa anpassade allmänna villkor.

1. Markera kryss rutan **Använd standard kontraktet för Microsofts kommersiella Marketplace** .

   ![Visar kryss rutan Använd standard kontraktet för Microsofts kommersiella Marketplace.](partner-center-portal/media/use-standard-contract.png)

1. I **bekräftelse** dialog rutan väljer du **acceptera**. Du kan behöva rulla upp för att se den.
1. Välj **Spara utkast** innan du fortsätter.

> [!NOTE]
> När du har publicerat ett erbjudande med standard kontraktet för den kommersiella marknads platsen kan du inte använda dina egna anpassade villkor. Erbjud antingen lösningen enligt standard kontraktet med valfria ändringar eller under egna villkor.

### <a name="add-amendments-to-the-standard-contract-optional"></a>Lägg till ändringar i standard kontraktet (valfritt)

Det finns två typer av ändringar som är tillgängliga: _universell_ och _anpassad_.

#### <a name="add-universal-amendment-terms"></a>Lägg till generella ändrings villkor

I **villkoren för Universal-ändringar till standard kontraktet för Microsofts handels marknads plats** anger du villkoren för den allmänna ändringen. Du kan ange ett obegränsat antal tecken i den här rutan. Dessa villkor visas för kunder i AppSource, Azure Marketplace och/eller Azure Portal under identifierings-och inköps flödet.

#### <a name="add-one-or-more-custom-amendments"></a>Lägg till en eller flera anpassade ändringar

1. Under **anpassade ändrings villkor till standard kontraktet för Microsofts kommersiella marknads plats** väljer du länken **Lägg till anpassad ändrings term (max 10)** .
1. I rutan **anpassade ändrings villkor** anger du dina ändrings villkor.
1. I rutan **klient-ID** anger du ett klient-ID. Endast kunder som är kopplade till klient-ID: n som du anger för de här anpassade villkoren visas i erbjudandet inköps flöde i Azure Portal.

   > [!TIP]
   > Ett klient-ID identifierar din kund i Azure. Du kan be kunden om detta ID och de kan hitta det genom att gå till [**https://portal.azure.com**](https://portal.azure.com)  >  **Azure Active Directory**  >  **Egenskaper**. Värdet för katalog-ID är klient-ID (till exempel `50c464d3-4930-494c-963c-1e951d15360e` ). Du kan också leta upp organisationens klient-ID för din kund genom att använda deras domän namns-URL i [Vad är mitt Microsoft Azure-och Office 365-klient-ID?](https://www.whatismytenantid.com/).

1. I rutan **Beskrivning** kan du ange en egen beskrivning för klient-ID: t. Den här beskrivningen hjälper dig att identifiera den kund som du är mål för med ändringen.
1. Om du vill lägga till ett annat klient-ID väljer du länken **Lägg till en kunds klient-ID** och upprepar steg 3 och 4. Du kan lägga till upp till 20 klient-ID: n.
1. Upprepa steg 1 till 5 för att lägga till ytterligare en ändrings period. Du kan ange upp till tio anpassade ändrings villkor per erbjudande.
1. Välj **Spara utkast** innan du fortsätter.

### <a name="use-your-own-terms-and-conditions"></a>Använd egna villkor

Du kan välja att ange egna villkor, i stället för standard kontraktet. Kunderna måste acceptera dessa villkor innan de kan testa ditt erbjudande.

1. Under **juridisk** kontrollerar du att kryss rutan **Använd standard kontraktet för Microsofts kommersiella Marketplace** är avmarkerad.
1. I rutan **allmänna villkor** anger du upp till 10 000 tecken text.
1. Välj **Spara utkast** innan du fortsätter till nästa flik: erbjudande lista.

## <a name="next-steps"></a>Nästa steg

- [Så här konfigurerar du din Azure Application lista över erbjudanden om erbjudanden](create-new-azure-apps-offer-listing.md)
