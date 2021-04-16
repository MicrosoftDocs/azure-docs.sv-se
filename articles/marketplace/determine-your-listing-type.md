---
title: Introduktion till listalternativ – Microsofts kommersiella marknadsplats
description: Den här artikeln beskriver alternativ för erbjudanden som publicerats för Microsoft AppSource och Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 01/14/2021
ms.openlocfilehash: 467cc4a62cd044cb4ad264ec8643728e6db0b10c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479822"
---
# <a name="introduction-to-listing-options"></a>Introduktion till listalternativ

När du skapar en erbjudandetyp väljer du ett eller flera listalternativ. De här alternativen avgör vilka knappar kunderna ser på sidan med erbjudandelistor i onlinebutikerna. Listalternativen är **Kostnadsfri utvärderingsversion,** Test **Drive,** **Kontakta** mig och Hämta **nu.**

Den här tabellen visar vilka listalternativ som är tillgängliga för varje erbjudandetyp:

| Erbjudandetyp | Kostnadsfri utvärdering | Test Drive | Kontakta mig | Hämta nu `*` |
| ------------ | ------------- | ------------- | ------------- | ------------- |
| Azure Application (hanterad app) |   | &#10004; |   | &#10004; |
| Azure Application (lösningsmall) |  |  |  | &#10004; |
| Konsulttjänst |  |  | &#10004; |  |
| Azure Container |  |  |  | &#10004; |
| Dynamics 365 Business Central | &#10004; | &#10004; | &#10004; | &#10004; |
| Dynamics 365 Customer Engagement & PowerApps | &#10004; | &#10004; | &#10004; | &#10004; |
| Dynamics 365 for operations | &#10004; | &#10004; | &#10004; | &#10004; |
| IoT Edge modul |  |  |  | &#10004; |
| Hanterad tjänst |  |  |  | &#10004; |
| Power BI-app |  |  |  | &#10004; |
| Virtuell Azure-dator | &#10004; | &#10004; |  | &#10004; |
| Programvara som en tjänst | &#10004; | &#10004; | &#10004; | &#10004; |
||||||

&#42; **Listalternativet** Hämta nu innehåller Get It Now (Kostnadsfri), BYOL (Bring Your Own License), Prenumeration och Användningsbaserad prissättning. Mer information finns i [Hämta nu.](#get-it-now)

## <a name="change-the-offer-type"></a>Ändra erbjudandetyp

[!INCLUDE [change-offer-type](./includes/change-offer-type.md)]

## <a name="free-trial"></a>Kostnadsfri utvärdering

Använd den kommersiella marknadsplatsen för att förbättra identifieringen och automatisera etableringen av lösningens utvärderingsversion. Detta gör det möjligt för potentiella kunder att använda din programvara som en tjänst (SaaS), infrastruktur som en tjänst (IaaS) eller Microsofts upplevelse i appen utan kostnad från 30 dagar till sex månader, beroende på erbjudandetyp.

Kunder använder knappen **Kostnadsfri utvärderingsversion** på erbjudandelistan för att prova erbjudandet. Om du tillhandahåller en kostnadsfri utvärderingsversion för flera planer i samma erbjudande kan kunderna byta till en kostnadsfri utvärderingsversion i ett annat abonnemang, men utvärderingsperioden startar inte om.

För erbjudanden om virtuella datorer debiteras kunderna kostnader för Azure-infrastruktur för att använda erbjudandet under en utvärderingsperiod. När utvärderingsperioden har löpt ut debiteras kunderna automatiskt för den senaste plan som de försökte med, baserat på standardpriser, såvida de inte avbryter innan utvärderingsperiodens slut.

## <a name="test-drive"></a>Test Drive

Kunder använder **knappen Test Drive** på erbjudandelistan för att få åtkomst till en förkonfigurerad miljö under ett fast antal timmar. Mer information om testenheter finns i [Vad är en provkörning?](what-is-test-drive.md).

> [!TIP]
> Alternativet Test Drive skiljer sig från den kostnadsfria utvärderingsversionen. Du kan erbjuda Test Drive, kostnadsfri utvärderingsversion eller både och. Båda ger dina kunder din lösning under en fast tidsperiod. Provkörningen innehåller dock även en praktisk, guidad rundtur i produktens viktiga funktioner och fördelar som visas i ett verkligt implementeringsscenario.

## <a name="contact-me"></a>Kontakta mig

Det här alternativet är en enkel lista över ditt program eller din tjänst. Kunder använder knappen **Kontakta mig** på ditt erbjudandes listsida för att begära att du ansluter till dem om ditt erbjudande.

## <a name="get-it-now"></a>Hämta nu

Det här listningsalternativet innehåller köpbara erbjudanden (prenumerationer eller användarbaserade priser), BYOL-erbjudanden (Bring Your Own License) och **Get It Now (kostnadsfri).** Transactable-erbjudanden säljs via den kommersiella marknadsplatsen. Microsoft ansvarar för fakturering och samlingar. Kunder använder knappen **Hämta nu** för att få erbjudandet.

Den här tabellen visar vilka erbjudandetyper som stöder de prisalternativ som ingår i **listalternativet** Hämta nu.

| Erbjudandetyp | Hämta nu (kostnadsfritt) | BYOL | Prenumeration | Användningsbaserad prissättning |
| ------------ | ------------- | ------------- | ------------- | ------------- |
| Azure Application (hanterad app) |   |   | &#10004; | &#10004; |
| Azure Application (lösningsmall) | &#10004; |   |   |   |
| Konsulttjänst |   |   |   |   |
| Azure Container | &#10004;<sup>1</sup> | &#10004;<sup>1</sup> |   |   |
| Dynamics 365 Business Central | &#10004; |   |   |   |
| Dynamics 365 Customer Engagement & PowerApps | &#10004; |   |   |   |
| Dynamics 365 for operations | &#10004; |   |   |   |
| IoT Edge modul | &#10004;<sup>1</sup> | &#10004;<sup>1</sup> |   |   |
| Hanterad tjänst |   | &#10004; |   |   |
| Power BI-app | &#10004; |   |   |   |
| Virtuell Azure-dator |   | &#10004; |   | &#10004;<sup>2</sup> |
| Programvara som en tjänst | &#10004; |   | &#10004; | &#10004; |
||||||

<sup>1</sup> Kolumnen **Prissättningsmodell** på **fliken Planöversikt** visar **Kostnadsfri** eller **BYOL,** men det går inte att välja.

<sup>2</sup> Prissätts per timme och faktureras månadsvis.

### <a name="get-it-now-free"></a>Hämta nu (kostnadsfritt)

Använd det här listalternativet för att erbjuda ditt program kostnadsfritt. Kunder använder knappen **Hämta nu för** att få ditt kostnadsfria erbjudande.

> [!NOTE]
> Get It Now-erbjudanden (kostnadsfri) är inte berättigade Marketplace Rewards för transactable-erbjudanden. Eftersom det inte finns någon transaktion via butiken kategoriseras dessa som **utvärderingsversion.** Se [Marketplace Rewards](#marketplace-rewards).

### <a name="bring-your-own-license-byol"></a>Bring Your Own License (BYOL)

Använd det här listalternativet om du vill att kunderna ska kunna distribuera ditt erbjudande med hjälp av en licens som köpts utanför den kommersiella marknadsplatsen. Det här alternativet är perfekt för migreringar lokalt till molnet. Kunder använder knappen **Hämta nu för att** köpa ditt erbjudande med hjälp av en licens som de har köpt i förväg från dig.

> [!NOTE]
> BYOL-erbjudanden är inte berättigade Marketplace Rewards för erbjudanden som kan omvandlas. Eftersom en kund måste skaffa licensen från partnern och det inte finns någon transaktion via den kommersiella marknadsplatsen kategoriseras dessa som **Lista**. Se [Marketplace Rewards](#marketplace-rewards).

### <a name="subscription"></a>Prenumeration

Du kan debitera en fast avgift för dessa erbjudandetyper:

- Azure Application (hanterad app) har stöd för månatliga prenumerationer.
- SaaS har stöd för både månatliga och årliga prenumerationer.

### <a name="usage-based-pricing"></a>Användningsbaserad prissättning

Följande erbjudandetyper stöder användningsbaserad prissättning:

- Azure Application (hanterad app) har stöd för förbrukningsfakturering. Mer information finns i Fakturering [med dataförbrukning för hanterade program.](partner-center-portal/azure-app-metered-billing.md)
- SaaS har stöd för priser för förbrukningsbaserad fakturering och per användare (per plats). Mer information om förbrukningsfakturering finns i [Mätarfakturering för SaaS med hjälp av den kommersiella marknadsplatsens mätningstjänst.](partner-center-portal/saas-metered-billing.md)
- Den virtuella Azure-datorn har stöd **för** **priser per kärna,** per kärnstorlek och **per marknad och kärnstorlek.** Dessa alternativ prissätts per timme och faktureras månadsvis.

När du skapar ett erbjudande som kan omvandlas är det viktigt att du förstår prissättning, fakturering, fakturering och utbetalningsöverväganden innan du väljer en erbjudandetyp och skapar ditt erbjudande. Mer information finns i Kommersiella [marknadsplatsens onlinebutiker.](overview.md#commercial-marketplace-online-stores)

## <a name="sample-offer"></a>Exempelerbjudande

När ditt erbjudande har publicerats visas listalternativen som knappar i det övre vänstra hörnet på listsidan i onlinebutiken. Följande bild visar till exempel en sida med erbjudanden i Microsoft AppSource onlinebutiken med knapparna **Hämta nu** och **Test Drive:**

:::image type="content" source="media/listing-options.png" alt-text="Skärmbild som visar listsidan för ett erbjudande med knapparna Hämta nu och Test Drive.":::

## <a name="listing-and-pricing-options-by-online-store"></a>Listning och prissättningsalternativ efter onlinebutik

Baserat på olika kriterier avgör vi om ditt erbjudande visas i Azure Marketplace, Microsoft AppSource eller båda onlinebutikerna. Mer information om skillnaderna mellan de två onlinebutikerna finns i [Commercial Marketplace online stores](overview.md#commercial-marketplace-online-stores).

I följande tabell visas de alternativ som är tillgängliga för olika typer av erbjudanden och tillägg och vilka onlinebutiker som ditt erbjudande kan visas på.

| Erbjudandetyper och tillägg | Kontakta mig | Kostnadsfri utvärdering | Hämta nu (kostnadsfritt) | BYOL | Hämta nu (Transact) |
| ------------ | ------------- | ------------- | ------------- | ------------- | ------------- |
| Virtuell Azure-dator |   |   |   | Azure Marketplace | Azure Marketplace |
| Azure Application |   |   | Azure Marketplace | Azure Marketplace | Azure Marketplace |
| Azure Container  |   |   | Azure Marketplace | Azure Marketplace |   |
| IoT Edge modul |   |   | Azure Marketplace | Azure Marketplace |   |
| Hanterad tjänst |   |   |   | Azure Marketplace |   |
| Konsulttjänst | Båda onlinebutikerna |   |   |   |   |
| SaaS  | Båda onlinebutikerna | Båda onlinebutikerna | Båda onlinebutikerna |   | Båda onlinebutikerna &#42; |
| Microsoft 365 App | AppSource | AppSource |   |   | AppSource- &#42;&#42; |
| Dynamics 365 Business Central | AppSource | AppSource |   |   |   |
| Dynamics 365 for Customer Engagements & PowerApps | AppSource | AppSource |   |   |   |
| Dynamics 365-åtgärder | AppSource | AppSource |   |   |   |
| Power BI-app |   |   | AppSource |   |   |
|||||||

&#42; SaaS-köpbara erbjudanden i AppSource accepterar endast kreditkort just nu.

&#42;&#42; Microsoft 365 tillägg är kostnadsfria att installera och kan generera intäkter med hjälp av ett SaaS-erbjudande. Mer information finns i Tjäna [pengar på din app via den kommersiella marknadsplatsen.](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace)

## <a name="marketplace-rewards"></a>Marketplace Rewards

Dina Marketplace Rewards beror på vilket listalternativ du väljer. Mer information finns i Förmåner [på den kommersiella marknadsplatsen.](gtm-your-marketplace-benefits.md)

Om ditt erbjudande är köpbart får du förmåner när du ökar din fakturerade försäljning.

Icke-transactable-erbjudanden får förmåner baserat på om en kostnadsfri utvärderingsversion är kopplad eller inte.

## <a name="next-steps"></a>Nästa steg

Om du vill välja en erbjudandetyp att skapa kan du [gå till Publiceringsguide efter erbjudandetyp.](publisher-guide-by-offer-type.md)
