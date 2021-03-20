---
title: Konfigurera egenskaper för virtuell dator i Azure Marketplace
description: Lär dig hur du konfigurerar egenskaper för virtuella datorer på Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 281553782774c31ec8cfaf614542fd739c4d3dd9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94629537"
---
# <a name="how-to-configure-virtual-machine-offer-properties"></a>Så här konfigurerar du egenskaper för erbjudande för virtuell dator

På sidan **Egenskaper** (Välj från vänster-navigerings-menyn i Partner Center) definierar du de kategorier som används för att gruppera din virtuella dator erbjudandet på Azure Marketplace, din program version och de juridiska kontrakt som stöder erbjudandet.

## <a name="select-a-category"></a>Välj en kategori

Välj kategorier och under Kategorier för att placera ditt erbjudande i lämpliga sökområden för Azure Marketplace. Var noga med att beskriva senare i beskrivningen av erbjudandet om hur ditt erbjudande stöder dessa kategorier.

- Välj en primär kategori.
- Om du vill lägga till en andra valfri kategori (sekundär) väljer du länken **+ Kategorier** .
- Välj upp till två under Kategorier för den primära och/eller sekundära kategorin. Om ingen under kategori gäller ditt erbjudande väljer du **ej tillämpligt**. Använd Ctrl och klicka för att välja en andra under kategori.

Se den fullständiga listan över kategorier och under Kategorier i [erbjudande lista med bästa praxis](gtm-offer-listing-best-practices.md). Virtual Machine-erbjudanden visas alltid under **beräknings** kategorin på Azure Marketplace.

## <a name="provide-terms-and-conditions"></a>Ange villkor

Under **juridisk**, ange villkor för ditt erbjudande. Du kan välja mellan två alternativ:

- [Använd standard kontraktet med valfria ändringar](#use-the-standard-contract)
- [Använd egna villkor](#use-your-own-terms-and-conditions)

Mer information om standard kontraktet och valfria ändringar finns i [standard kontrakt för Microsoft Commercial Marketplace](standard-contract.md). Du kan ladda ned [standard kontraktet](https://go.microsoft.com/fwlink/?linkid=2041178) PDF (se till att blockering av popup-fönster är inaktiverat).

### <a name="use-the-standard-contract"></a>Använd standard kontraktet

För att förenkla inköps processen för kunder och minska den juridiska komplexiteten för program varu leverantörer erbjuder Microsoft ett standard kontrakt som du kan använda för dina erbjudanden på den kommersiella marknaden. När du erbjuder program varan under standard kontraktet behöver kunderna bara läsa och godkänna den en gång, och du behöver inte skapa anpassade allmänna villkor.

1. Markera kryss rutan **Använd standard kontraktet för Microsofts kommersiella Marketplace** .

   ![Visar kryss rutan Använd standard kontraktet för Microsofts kommersiella Marketplace.](partner-center-portal/media/use-standard-contract.png)

1. I **bekräftelse** dialog rutan väljer du **acceptera**. Beroende på skärmens storlek kan du behöva rulla upp för att se den.
1. Välj **Spara utkast** innan du fortsätter.

   > [!NOTE]
   > När du har publicerat ett erbjudande med standard kontraktet för den kommersiella marknads platsen kan du inte använda dina egna anpassade villkor. Erbjud antingen lösningen enligt standard kontraktet med valfria ändringar eller under egna villkor.

#### <a name="add-amendments-to-the-standard-contract-optional"></a>Lägg till ändringar i standard kontraktet (valfritt)

Det finns två typer av ändringar som är tillgängliga: *universell* och *anpassad*.

##### <a name="add-universal-amendment-terms"></a>Lägg till generella ändrings villkor

I **villkoren för Universal-ändringar till standard kontraktet för Microsofts handels marknads plats** anger du villkoren för den allmänna ändringen. Du kan ange ett obegränsat antal tecken i den här rutan. Dessa villkor visas för kunder i AppSource, Azure Marketplace och/eller Azure Portal under identifierings-och inköps flödet.

##### <a name="add-one-or-more-custom-amendments"></a>Lägg till en eller flera anpassade ändringar

1. Under **anpassade ändrings villkor till standard kontraktet för Microsofts kommersiella marknads plats** väljer du länken **Lägg till anpassad ändrings term (max 10)** .
2. Ange dina **anpassade ändrings villkor** i rutan.
3. Ange **klient-ID** i rutan. Endast kunder som är kopplade till klient-ID: n som du anger för de här anpassade villkoren visas i erbjudandet inköps flöde i Azure Portal.

   > [!TIP]
   > Ett klient-ID identifierar din kund i Azure. Du kan be kunden om detta ID och de kan hitta det genom att gå till [**https://portal.azure.com**](https://portal.azure.com)  >  **Azure Active Directory**  >  **Egenskaper**. Värdet för katalog-ID är klient-ID (till exempel `50c464d3-4930-494c-963c-1e951d15360e` ). Du kan också leta upp organisationens klient-ID för din kund genom att använda deras domän namns-URL i [Vad är mitt Microsoft Azure-och Office 365-klient-ID?](https://www.whatismytenantid.com/).

4. Alternativt kan du ange en egen **Beskrivning** för klient-ID: t. Den här beskrivningen hjälper dig att identifiera den kund som du är mål för med ändringen.
5. Om du vill lägga till ett annat klient-ID väljer du länken **Lägg till en kunds klient-ID (max 10)** och upprepar steg 3 och 4. Du kan lägga till upp till 20 klient-ID: n.
6. Upprepa steg 1 till 5 för att lägga till ytterligare en ändrings period. Du kan ange upp till tio anpassade ändrings villkor per erbjudande.
7. Välj **Spara utkast** innan du fortsätter.

### <a name="use-your-own-terms-and-conditions"></a>Använd egna villkor

Du kan välja att ange egna villkor, i stället för standard kontraktet. Kunderna måste acceptera dessa villkor innan de kan testa ditt erbjudande.

1. Under **Legal**, avmarkerar du kryss rutan **Använd standard kontraktet för Microsofts kommersiella Marketplace** .
1. I rutan **allmänna villkor** anger du upp till 10 000 tecken text.

   > [!NOTE]
   > Om du behöver en längre Beskrivning anger du en enskild webb adress som pekar på var du hittar dina villkor. Den kommer att visas för kunder som en aktiv länk.

1. Välj **Spara utkast** innan du fortsätter till nästa flik på vänster-navigerings-menyn och **ge listan**.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera visning av VM-erbjudande](azure-vm-create-listing.md)
