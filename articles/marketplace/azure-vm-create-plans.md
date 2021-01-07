---
title: Skapa planer för ett erbjudande för virtuella datorer på Azure Marketplace
description: Lär dig hur du skapar planer för ett erbjudande för virtuella datorer på Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: e5fb425afdd4b212a0b28ce91418eb0ee9e3632f
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964441"
---
# <a name="how-to-create-plans-for-a-virtual-machine-offer"></a>Skapa planer för ett erbjudande för virtuell dator

På sidan **plan översikt** (Välj från vänster-navigerings meny i Partner Center) kan du ange olika plan alternativ inom samma erbjudande. Ett erbjudande kräver minst en plan (tidigare kallat SKU), som kan variera av försäljande mål, Azure-region,-funktioner eller VM-avbildningar.

Du kan skapa upp till 100-planer för varje erbjudande: upp till 45 av dessa kan vara privata. Lär dig mer om privata planer i [privata erbjudanden på Microsofts kommersiella marknads platser](private-offers.md).

När du har skapat dina planer väljer du fliken **plan översikt** för att visa:

- Planera namn
- Licens modeller
- Mål grupp (offentlig eller privat)
- Aktuell publicerings status
- Tillgängliga åtgärder

Vilka åtgärder som är tillgängliga i fönstret **plan översikt** varierar beroende på planens aktuella status.

- Om plan status är ett utkast väljer du **ta bort utkast**.
- Om plan status är publicerad Live väljer du **stoppa Sälj plan** eller **Synkronisera privat publik**.

## <a name="create-a-new-plan"></a>Skapa en ny plan

Välj **+ Skapa ny plan** längst upp.

I dialog rutan **nytt plan** anger du ett unikt **plan-ID** för varje plan i det här erbjudandet. Detta ID visas för kunder i produktens webb adress. Använd bara gemena bokstäver och siffror, bindestreck eller under streck och högst 50 tecken.

> [!NOTE]
> Det går inte att ändra plan-ID när du har valt **skapa**.

Ange ett **namn på prenumerationen**. Kunderna ser det här namnet när de bestämmer vilken plan som ska väljas i erbjudandet. Skapa ett unikt namn som tydligt pekar på skillnaderna mellan planerna. Du kan till exempel ange **Windows Server** med " *betala per* användning"-, *BYOL*-, *Advanced*-och *Enterprise* -planer.

Välj **Skapa**. Då öppnas sidan **Planera installationen** .

## <a name="plan-setup"></a>Planera installationen

Ange den avancerade konfigurationen för typen av plan, ange om den återanvänder en teknisk konfiguration från ett annat abonnemang och identifiera de Azure-regioner där planen ska vara tillgänglig. Dina val här avgör vilka fält som visas i andra fönster för samma plan.

### <a name="reuse-technical-configuration"></a>Återanvänd teknisk konfiguration

Om du har mer än en plan av samma typ och paketen är identiska mellan dem, kan du välja **den här prenumerationen Återanvänd teknisk konfiguration från ett annat abonnemang**. Med det här alternativet kan du välja något av de andra planerna av samma typ för det här erbjudandet och du kan återanvända den tekniska konfigurationen.

> [!NOTE]
> När du återanvänder den tekniska konfigurationen från ett annat abonnemang försvinner fliken **teknisk konfiguration** från den här planen. Den tekniska konfigurations informationen från den andra planen, inklusive eventuella uppdateringar som du gör i framtiden, kommer även att användas för den här planen. Det går inte att ändra den här inställningen när planen har publicerats.

### <a name="azure-regions"></a>Azure-regioner

Din plan måste göras tillgänglig i minst en Azure-region.

Välj **Azure Global** för att göra ditt abonnemang tillgängligt för kunder i alla globala Azure-regioner med marknads plats integrering. Mer information finns i [stöd för geografisk tillgänglighet och valuta](marketplace-geo-availability-currencies.md).

Välj **Azure Government** för att göra ditt abonnemang tillgängligt i [Azure Government](../azure-government/documentation-government-welcome.md) region. Den här regionen tillhandahåller kontrollerad åtkomst för kunder från amerikanska federala, statliga, lokala eller stambaserad entiteter, samt för partner som är berättigade att betjäna dem. Du, som utgivare, är ansvariga för alla kontroller, säkerhets åtgärder och bästa praxis. Azure Government använder fysiskt isolerade Data Center och nätverk (som finns i USA).

Innan du publicerar till [Azure Government](../azure-government/documentation-government-manage-marketplace-partners.md)ska du testa och validera planen i miljön, eftersom vissa slut punkter kan skilja sig åt. Ställ in och testa planen genom att begära ett utvärderings konto på sidan [Microsoft Azure Government utvärderings version](https://azure.microsoft.com/global-infrastructure/government/request/) .

> [!NOTE]
> När planen har publicerats och är tillgänglig i en speciell Azure-region kan du inte ta bort den regionen.

### <a name="azure-government-certifications"></a>Azure Government certifieringar

Det här alternativet visas bara om du har valt **Azure Government** som Azure-region i föregående avsnitt.

Azure Government Services hanterar data som omfattas av vissa myndighets bestämmelser och krav. Till exempel FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD L4 och CJIS. För att få kännedom om dina certifieringar för dessa program kan du ge upp till 100 länkar som beskriver dem. De kan antingen vara länkar till din lista över programmet direkt eller länkar till beskrivningar av dina efterlevnad med dem på dina egna webbplatser. Dessa länkar är bara synliga för Azure Government kunder.

Välj **Spara utkast** innan du fortsätter till nästa flik i menyn till vänster-navigerings plan, **Planera lista**.

## <a name="plan-listing"></a>Plan lista

Konfigurera registrerings informationen för planen. I det här fönstret visas detaljerad information som kan skilja sig från andra planer i samma erbjudande.

### <a name="plan-name"></a>Plan namn

Det här fältet fylls automatiskt med det namn som du gav din plan när du skapade det. Det här namnet visas på Azure Marketplace som rubrik för den här planen. Den är begränsad till 100 tecken.

### <a name="plan-summary"></a>Plan Sammanfattning

Ge en kort sammanfattning av din plan, inte erbjudandet. Den här sammanfattningen är begränsad till 100 tecken.

### <a name="plan-description"></a>Beskrivning av plan

Beskriv vad som gör den här program varu planen unik och Beskriv eventuella skillnader mellan planer i erbjudandet. Beskriv endast planen, inte erbjudandet. Plan beskrivningen kan innehålla upp till 2 000 tecken.

Välj **Spara utkast** innan du fortsätter till nästa flik i menyn till vänster-navigerings plan, **prissättning och tillgänglighet**.

## <a name="pricing-and-availability"></a>Priser och tillgänglighet

I det här fönstret konfigurerar du:

- Marknader där denna plan är tillgänglig. Varje plan måste vara tillgänglig på minst en [marknad](marketplace-geo-availability-currencies.md).
- Priset per timme.
- Om planen ska vara synlig för alla eller bara för vissa kunder (en privat mål grupp).

### <a name="markets"></a>Marknaden

Varje plan måste vara tillgänglig på minst en marknad. De flesta marknader är markerade som standard. Om du vill redigera listan väljer du **Redigera marknader** och markerar eller avmarkerar kryss rutor för varje marknads plats där den här planen ska (eller inte) vara tillgänglig för köp. Användare på valda marknader kan fortfarande distribuera erbjudandet till alla Azure-regioner som valts i avsnittet ["planera konfiguration"](#plan-setup) .

Välj **Välj endast Microsoft-moms remitterad** om du bara vill välja de länder/regioner där Microsoft förbrukar försäljning och använder skatt för din räkning. Publicering till Kina är begränsad till planer som antingen är *kostnads fria* eller har *en egen licens* (BYOL).

Om du redan har angett priser för din plan i valutan US dollar (USD) och lägger till en annan marknads plats, beräknas priset för den nya marknaden enligt aktuella växelkurser. Granska alltid priset för varje marknad innan du publicerar. Granska din prissättning genom att välja **export priser (xlsx)** när du har sparat ändringarna.

När du tar bort en marknad kommer kunder från den marknaden som använder aktiva distributioner inte att kunna skapa nya distributioner eller skala upp sina befintliga distributioner. Befintliga distributioner påverkas inte.

Fortsätt genom att välja **Spara** .

### <a name="pricing"></a>Prissättning

För **licens modellen** väljer du Använd **månatlig månatlig fakturerings plan** för att konfigurera prissättningen för den här planen eller **Bring Your Own License** så att kunderna kan använda den här planen med sin befintliga licens.

Använd en av följande tre pris poster för en användnings-baserad månatlig fakturerings plan:

- **Per kärna** – ange priser per kärna i USD. Microsoft beräknar priset per kärn storlek och omvandlar det till lokala valutor med hjälp av den aktuella växelkursen.
- **Per kärn storlek** – ange priser per kärn storlek i USD. Microsoft beräknar priset och konverterar det till lokala valutor med hjälp av den aktuella växelkursen.
- **Per marknad och kärn storlek** – ange priser för varje kärn storlek för alla marknader. Du kan importera priserna från ett kalkyl blad.

Ange ett **pris per kärna** och välj sedan **pris per kärn storlek** för att se en tabell med pris-och Tim beräkningar.

> [!NOTE]
> Spara pris ändringar så att du kan exportera pris information. När du har publicerat ett pris för en marknad i planen kan du inte ändra det senare. För att se till att priserna är rätt innan du publicerar dem, exportera pris kalkyl bladet och granska priserna på varje marknad.

### <a name="free-trial"></a>Kostnadsfri utvärdering

Du kan erbjuda en en-, tre-eller sex månaders **kostnads fri utvärderings version** till dina kunder.

### <a name="plan-visibility"></a>Plan synlighet

Du kan utforma varje plan som ska vara synlig för alla eller endast till en förvaltad mål grupp. Tilldela medlemskap i den här begränsade mål gruppen med hjälp av ID för Azure-prenumeration.

**Offentlig**: din prenumeration kan ses av alla.

**Privat**: se till att ditt plan endast visas i förvalt mål grupp. När den har publicerats som en privat plan kan du uppdatera mål gruppen eller ändra den till offentlig. När du har gjort en plan offentlig måste den vara offentlig. Det går inte att ändra tillbaka till en privat plan.

Tilldela den mål grupp som ska ha åtkomst till den här privata planen med **prenumerations-ID: t för Azure**. Du kan också ta med en **Beskrivning** av varje Azure-prenumerations-ID som du tilldelar. Lägg till upp till 10 prenumerations-ID: n manuellt eller upp till 20 000 om du importerar ett CSV-kalkylblad. ID: n för Azure-prenumerationen visas som GUID och alla bokstäver måste vara gemena.

> [!NOTE]
> En privat eller begränsad mål grupp skiljer sig från den förhands visnings mål som du definierade i **förhands gransknings** fönstret. En förhands gransknings grupp kan komma åt ditt erbjudande *innan* det publiceras Live till Azure Marketplace. Även om valet för den privata publiken bara gäller för en speciell plan, kan förhands gransknings publiken Visa alla privata och offentliga planer i validerings syfte.

Privata erbjudanden stöds inte med Azure-prenumerationer som upprättats via en åter försäljare av Cloud Solution Provider-programmet (CSP).

### <a name="hide-plan"></a>Dölj plan

Om den virtuella datorn är avsedd att användas endast indirekt när den refereras till via en annan lösnings mall eller ett hanterat program, markerar du den här kryss rutan för att publicera den virtuella datorn, men döljer den från kunder som kan söka efter eller bläddra efter den direkt.

Dolda planer stöder inte för hands versions länkar.

Välj **Spara utkast** innan du fortsätter till nästa flik i menyn till vänster-navigerings plan, **teknisk konfiguration**.

## <a name="technical-configuration"></a>Teknisk konfiguration

Ange de bilder och andra tekniska egenskaper som är associerade med den här planen.

> [!NOTE]
> Den här fliken visas inte om du har konfigurerat den här planen för att återanvända paket från en annan på fliken **Planera konfiguration** .

### <a name="operating-system"></a>Operativsystem

Välj operativ Systems familj för **Windows** eller **Linux** .

Välj Windows- **versionen** eller Linux- **leverantören**.

Ange ett **eget operativ system namn** för operativ systemet. Det här namnet är synligt för kunderna.

### <a name="recommended-vm-sizes"></a>Rekommenderade VM-storlekar

Välj länken för att välja upp till sex rekommenderade storlekar för virtuella datorer som ska visas på Azure Marketplace.

### <a name="open-ports"></a>Öppna portar

Lägg till öppna offentliga eller privata portar på en distribuerad virtuell dator.

### <a name="properties"></a>Egenskaper

Välj om din virtuella dator **har stöd för accelererat nätverk**. Mer information finns i [accelererat nätverk](https://go.microsoft.com/fwlink/?linkid=2124513).

### <a name="generations"></a>Generationer

När en virtuell dator skapas definieras den virtuella maskin varan som används. Baserat på kundens behov kan du publicera en virtuell dator i generation 1, generation 2 VM eller både och.

1. När du skapar ett nytt erbjudande väljer du en **typ av generation** och anger den information som krävs:

    :::image type="content" source="./media/create-vm/azure-vm-generations-image-details-1.png" alt-text="En vy av avsnittet om generations information i Partner Center.":::

2. Om du vill lägga till en annan generation i en plan väljer du **Lägg till generation**...

    :::image type="content" source="./media/create-vm/azure-vm-generations-add.png" alt-text="En vy av länken Lägg till generation.":::

    ... och ange den begärda informationen:

    :::image type="content" source="./media/create-vm/azure-vm-generations-image-details-3.png" alt-text="En vy av fönstret för rapportgenerering.":::

<!--    The **Generation ID** you choose will be visible to customers in places such as product URLs and ARM templates (if applicable). Use only lowercase, alphanumeric characters, dashes, or underscores; it cannot be modified once published.
-->
3. Om du vill uppdatera en befintlig virtuell dator som har en generation 1 redan publicerad, redigerar du information på sidan **teknisk konfiguration** .

Mer information om skillnaderna mellan funktioner i generation 1 och generation 2 finns i [stöd för virtuella datorer i generation 2 på Azure](../virtual-machines/generation-2.md).

### <a name="vm-images"></a>VM-avbildningar

Ange en disk version och URL: en för signaturen för delad åtkomst (SAS) för de virtuella dator avbildningarna. Lägg till upp till 16 data diskar för varje VM-avbildning. Ange endast en ny avbildnings version per plan i ett angivet överförings objekt. När en avbildning har publicerats kan du inte redigera den, men du kan ta bort den. Om du tar bort en version förhindrar det att både nya och befintliga användare distribuerar en ny instans av den borttagna versionen.

Dessa två obligatoriska fält visas i föregående bild ovan:

- **Disk version**: den version av avbildningen som du tillhandahåller.
- **OS-VHD-länk**: platsen i ditt Azure Storage-konto för operativ systemets virtuella hård disk. Information om hur du hämtar en SAS-URI finns i [Hämta signatur-URI för delad åtkomst för din virtuella dator avbildning](azure-vm-get-sas-uri.md).

Data diskar (Välj **Lägg till data disk (max 16)**) är också URI: er för delad åtkomst för virtuella hård diskar som lagras i deras Azure Storage-konton. Lägg endast till en bild per överföring i en plan.

Oavsett vilket operativ system du använder lägger du endast till det minsta antalet data diskar som krävs för lösningen. Under distributionen kan kunderna inte ta bort diskar som ingår i en avbildning, men de kan alltid lägga till diskar under eller efter distributionen.

Välj **Spara utkast** och välj sedan **← plan Overview** överst till vänster för att se den plan som du nyss skapade.

## <a name="next-steps"></a>Nästa steg

- [Sälja via CSP-partner](azure-vm-create-resell-csp.md)
