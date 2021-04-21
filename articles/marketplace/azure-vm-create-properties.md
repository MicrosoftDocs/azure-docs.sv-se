---
title: Konfigurera egenskaper för erbjudanden för virtuella datorer på Azure Marketplace
description: Lär dig hur du konfigurerar egenskaper för erbjudanden för virtuella datorer Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 5942368ba1709127b815a35676b716955c1bee8f
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819096"
---
# <a name="configure-virtual-machine-offer-properties"></a>Konfigurera egenskaper för erbjudande för virtuell dator

På sidan **Egenskaper** (välj från den vänstra navigeringsmenyn) definierar du de kategorier som används för att gruppera erbjudandet om den virtuella datorn (VM) på Azure Marketplace, din programversion och de juridiska kontrakt som stöder ditt erbjudande.

## <a name="select-a-category"></a>Välj en kategori

Välj kategorier och underkategorier för att placera ditt erbjudande i lämpliga Azure Marketplace sökområden. Se till att du senare i erbjudandebeskrivningen beskriver hur ditt erbjudande stöder dessa kategorier.

- Välj en Primär kategori.
- Om du vill lägga till en andra valfri kategori (sekundär) väljer du **länken +Kategorier.**
- Välj upp till två underkategorier för kategorin Primär och/eller Sekundär. Om ingen underkategori gäller för ditt erbjudande väljer du **Ej tillämpligt.** Använd Ctrl+ klicka för att välja en andra underkategori.

Se den fullständiga listan över kategorier och underkategorier i [Metodtips för erbjudandelistor.](gtm-offer-listing-best-practices.md) Erbjudanden för virtuella datorer visas alltid under **kategorin Compute** på Azure Marketplace.

## <a name="provide-terms-and-conditions"></a>Ange allmänna villkor

Under **Juridiskt** anger du villkor för ditt erbjudande. Du kan välja mellan två alternativ:

- [Använd standardavtalet med valfria ändringar](#use-the-standard-contract)
- [Använd dina egna villkor](#use-your-own-terms-and-conditions)

Mer information om standardavtalet och valfria ändringar finns i [Standardavtal för Microsofts kommersiella marknadsplats.](standard-contract.md) Du kan ladda ned [Standardavtal](https://go.microsoft.com/fwlink/?linkid=2041178) PDF-filen (kontrollera att blockeringen av popup-fönster är inaktiverad).

### <a name="use-the-standard-contract"></a>Använda standardavtalet

För att förenkla anskaffningsprocessen för kunder och minska programvaruleverantörers juridiska komplexitet erbjuder Microsoft ett standardavtal som du kan använda för dina erbjudanden på den kommersiella marknadsplatsen. När du erbjuder din programvara enligt standardavtalet behöver kunderna bara läsa och godkänna den en gång och du behöver inte skapa anpassade villkor.

1. Markera kryssrutan **Använd Standardavtal för Microsofts kommersiella marknadsplats.**

   ![Visar kryssrutan Använd Standardavtal för Microsofts kommersiella marknadsplats.](partner-center-portal/media/use-standard-contract.png)

1. I dialogrutan **Bekräftelse** väljer du **Acceptera**. Beroende på skärmens storlek kan du behöva rulla uppåt för att se den.
1. Välj **Spara utkast innan** du fortsätter.

   > [!NOTE]
   > När du har publicerat ett erbjudande Standardavtal på den kommersiella marknadsplatsen kan du inte använda dina egna anpassade villkor. Antingen erbjuder du din lösning enligt standardavtalet med valfria ändringar eller enligt dina egna villkor.

#### <a name="add-amendments-to-the-standard-contract-optional"></a>Lägga till ändringar i standardavtalet (valfritt)

Det finns två typer av ändringar: *universella* och *anpassade*.

##### <a name="add-universal-amendment-terms"></a>Lägga till villkor för universella ändringar

I rutan **Universal amendment terms to the standard contract for Microsoft's commercial marketplace (Allmänna tilläggsvillkor till standardavtalet för Microsofts kommersiella marknadsplats)** anger du dina villkor för universella ändringar. Du kan ange ett obegränsat antal tecken i den här rutan. Dessa villkor visas för kunder i AppSource, Azure Marketplace och/eller Azure Portal under identifierings- och inköpsflödet.

##### <a name="add-one-or-more-custom-amendments"></a>Lägga till en eller flera anpassade ändringar

1. Under **Villkor för anpassade ändringar i Standardavtal för Microsofts** kommersiella marknadsplats väljer du länken Lägg till anpassad **ändringsterm (max 10).**
2. Ange dina **villkor för anpassad** ändring i rutan.
3. Ange **klientorganisations-ID** i rutan. Endast kunder som är kopplade till de klient-ID:er som du anger för dessa anpassade villkor ser dem i erbjudandets inköpsflöde i Azure Portal.

   > [!TIP]
   > Ett klientorganisations-ID identifierar din kund i Azure. Du kan be kunden om detta ID och de hittar det genom att gå till [**https://portal.azure.com**](https://portal.azure.com)  >  **Azure Active Directory**  >  **Egenskaper**. Katalog-ID-värdet är klientorganisations-ID:t (till exempel `50c464d3-4930-494c-963c-1e951d15360e` ). Du kan också leta upp organisationens klientorganisations-ID för din kund med hjälp av deras domännamns-URL i Vad är mitt Microsoft Azure och [Office 365-klientorganisations-ID?](https://www.whatismytenantid.com/).

4. Du kan också ange en beskrivning av klient-ID:t.  Den här beskrivningen hjälper dig att identifiera kunden du riktar ändringen mot.
5. Om du vill lägga till ett annat klientorganisations-ID väljer du länken Lägg till en kunds **klient-ID (max 10)** och upprepar steg 3 och 4. Du kan lägga till upp till 20 klient-ID:er.
6. Upprepa steg 1 till 5 om du vill lägga till ytterligare en ändringsterm. Du kan ange upp till tio anpassade ändringsvillkor per erbjudande.
7. Välj **Spara utkast innan** du fortsätter.

### <a name="use-your-own-terms-and-conditions"></a>Använd dina egna villkor

Du kan ange egna villkor i stället för att använda standardavtalet. Kunderna måste acceptera dessa villkor innan de kan prova ditt erbjudande.

1. Under **Juridiskt** avmarkerar **du kryssrutan Använd Standardavtal för Microsofts kommersiella marknadsplats.**
1. I **rutan Allmänna villkor** anger du upp till 10 000 tecken text.

   > [!NOTE]
   > Om du behöver en längre beskrivning anger du en enda webbadress som pekar på var dina villkor finns. Den visas för kunder som en aktiv länk.

1. Välj **Spara utkast** innan du fortsätter till nästa flik i den vänstra navigeringsmenyn, **Erbjudandelista.**

## <a name="next-steps"></a>Nästa steg

- [Konfigurera visning av VM-erbjudande](azure-vm-create-listing.md)
