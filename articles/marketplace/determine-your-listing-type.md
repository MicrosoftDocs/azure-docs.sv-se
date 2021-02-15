---
title: Introduktion till List alternativ – Microsoft Commercial Marketplace
description: I den här artikeln beskrivs List alternativ för erbjudanden som publiceras till Microsoft AppSource och Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 01/14/2021
ms.openlocfilehash: 2433e62fd1dbf35748483b40958fc3367532102a
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100099242"
---
# <a name="introduction-to-listing-options"></a>Introduktion till List alternativ

När du skapar en erbjudande typ väljer du ett eller flera List alternativ. De här alternativen avgör vilka knappar kunderna ser på sidan för erbjudande listan i onlinebutiker. I list alternativen ingår _kostnads fri utvärderings version_, _test enhet_, _kontakta mig_ och _Hämta den nu_.

Den här tabellen visar vilka List alternativ som är tillgängliga för varje erbjudande typ.

| Erbjudandetyp | Kostnadsfri utvärdering | Test Drive | Kontakta mig | Hämta nu `*` |
| ------------ | ------------- | ------------- | ------------- | ------------- |
| Azure Application (hanterad app) |   | &#10004; |   | &#10004; |
| Azure Application (lösnings mal len) |  |  |  | &#10004; |
| Konsult tjänst |  |  | &#10004; |  |
| Azure-behållare |  |  |  | &#10004; |
| Dynamics 365 Business Central | &#10004; | &#10004; | &#10004; | &#10004; |
| Dynamics 365 kund engagemang & PowerApps | &#10004; | &#10004; | &#10004; | &#10004; |
| Dynamics 365 för åtgärder | &#10004; | &#10004; | &#10004; | &#10004; |
| IoT Edge modul |  |  |  | &#10004; |
| Hanterad tjänst |  |  |  | &#10004; |
| Power BI-app |  |  |  | &#10004; |
| Virtuell Azure-dator | &#10004; | &#10004; |  | &#10004; |
| Programvara som en tjänst | &#10004; | &#10004; | &#10004; | &#10004; |
||||||

&#42; hämtnings alternativet Hämta nu finns det nu (kostnads fritt), ta med din egen licens (BYOL), prenumeration och användnings baserat pris. Mer information finns i [Hämta nu](#get-it-now).

## <a name="changing-offer-type"></a>Ändrar erbjudande typ

[!INCLUDE [change-offer-type](./includes/change-offer-type.md)]

## <a name="free-trial"></a>Kostnadsfri utvärdering

Använd den kommersiella Marketplace för att förbättra identifieringen och automatisera etableringen av din lösnings utvärderings upplevelse. Detta gör det möjligt för potentiella kunder att använda din program vara som en tjänst (SaaS), IaaS eller Microsoft app-upplevelse utan kostnad från 30 dagar till sex månader, beroende på typen av erbjudande.

Kunderna använder den _kostnads fria utvärderings_ knappen på ditt erbjudandes List sida för att testa ditt erbjudande. Om du tillhandahåller en kostnads fri utvärderings version av flera planer inom samma erbjudande kan kunderna byta till en kostnads fri utvärderings version på ett annat avtal, men utvärderings perioden startar inte om.

För erbjudanden om virtuella datorer debiteras kunderna Azures infrastruktur kostnader för användning av erbjudandet under en utvärderings period. När utvärderings perioden har löpt ut debiteras kunderna automatiskt för den senaste planen de försökte baseras på standardpriser, såvida de inte annulleras före utvärderings periodens slut.

## <a name="test-drive"></a>Test Drive

Kunderna använder knappen _testa enhet_ på ditt erbjudandes List sida för att få åtkomst till en förkonfigurerad miljö för ett fast antal timmar. Mer information om test enheter finns i [Vad är en test enhet?](what-is-test-drive.md)

> [!TIP]
> En testen het skiljer sig från en kostnads fri utvärderings version. Du kan erbjuda en testenhet, en kostnads fri utvärderings version eller både och. De förser båda kunderna med din lösning under en fast period. Men en testenhet innehåller även en praktisk guidad rundtur i produktens viktiga funktioner och fördelar som visas i ett verkligt implementerings scenario.

## <a name="contact-me"></a>Kontakta mig

Enkel lista över ditt program eller din tjänst. Kunder använder knappen _kontakta mig_ på ditt erbjudandes List sida för att begära att du ansluter till dem om ditt erbjudande.

## <a name="get-it-now"></a>Hämta nu

Det här List alternativet innehåller transactable-erbjudanden (prenumerationer och användarbaserade priser), framförar egna licens erbjudanden och skaffar det nu (kostnads fritt). Transactable-erbjudanden säljs via den kommersiella marknads platsen. Microsoft ansvarar för fakturering och samlingar. Kunderna använder _knappen Hämta nu_ för att hämta erbjudandet.

Alternativet Hämta IT-lista kan innehålla följande pris alternativ, beroende på erbjudande typen:

- Hämta nu (kostnads fritt)
- Bring your own license (BYOL)
- Prenumeration
- Användnings-baserade priser

Den här tabellen visar vilka erbjudande typer som har stöd för ytterligare pris alternativ som ingår i alternativet Hämta IT-lista.

| Erbjudandetyp | Hämta nu (kostnads fritt) | BYOL | Prenumeration | Användnings-baserade priser |
| ------------ | ------------- | ------------- | ------------- | ------------- |
| Azure Application (hanterad app) |   |   | &#10004; | &#10004; |
| Azure Application (lösnings mal len) | &#10004; |   |   |   |
| Konsult tjänst |   |   |   |   |
| Azure-behållare | &#10004;<sup>1</sup> | &#10004;<sup>1</sup> |   |   |
| Dynamics 365 Business Central | &#10004; |   |   |   |
| Dynamics 365 kund engagemang & PowerApps | &#10004; |   |   |   |
| Dynamics 365 för åtgärder | &#10004; |   |   |   |
| IoT Edge modul | &#10004;<sup>1</sup> | &#10004;<sup>1</sup> |   |   |
| Hanterad tjänst |   | &#10004; |   |   |
| Power BI-app | &#10004; |   |   |   |
| Virtuell Azure-dator |   | &#10004; |   | &#10004;<sup>2</sup> |
| Programvara som en tjänst | &#10004; |   | &#10004; | &#10004; |
||||||

**Förklaring**

<sup>1</sup> kolumnen **pris modell** i fliken **plan översikt** visar _kostnads fria_ eller _BYOL_ , men det går inte att välja.

<sup>2</sup> pris per timme och faktureras per månad.

### <a name="get-it-now-free"></a>Hämta nu (kostnads fritt)

Använd det här alternativet om du vill erbjuda ditt program kostnads fritt. Kunderna använder knappen _Hämta nu_ för att få ditt kostnads fria erbjudande.

> [!NOTE]
> Hämta nu (kostnads fria) erbjudanden är inte berättigade till förmåner för marknads plats förmåner för transactable-erbjudanden. Eftersom det inte finns någon transaktion via butik kategoriseras dessa som "utvärderings version". Se [marknads förmånerna](#marketplace-rewards) nedan.

### <a name="bring-your-own-license-byol"></a>Bring Your Own License (BYOL)

Använd det här alternativet för att låta kunderna distribuera ditt erbjudande med en licens som köpts utanför den kommersiella marknads platsen. Det här alternativet är idealiskt för migrering lokalt till molnet. Kunder använder knappen _Hämta nu_ för att köpa ditt erbjudande med en licens som de förhandlat från dig.

> [!NOTE]
> BYOL-erbjudanden är inte berättigade till förmåner för Marketplace-förmåner för vissa erbjudanden. Eftersom dessa kräver att en kund förvärvar licensen från partnern och det inte finns någon transaktion via de kommersiella Marketplace-butik, kategoriseras dessa som "lista". Se [marknads förmånerna](#marketplace-rewards) nedan.

### <a name="subscription"></a>Prenumeration

Du kan debitera en fast avgift för dessa erbjudande typer:

- Azure Application (hanterad app) erbjuder support månads prenumerationer.
- SaaS erbjuder stöd för både månatliga och årliga prenumerationer.

### <a name="usage-based-pricing"></a>Användnings-baserade priser

Följande erbjudande typer stöder användnings-baserade priser:

- Azure Application (hanterad app) ger stöd för fakturering av mätare. Mer information finns i [hanterad fakturering för program](partner-center-portal/azure-app-metered-billing.md).
- SaaS erbjuder stöd för priser för fakturering och per användare (per klient). Mer information om avgiftsbelagd fakturering finns i [mäta fakturering för SaaS med hjälp av den kommersiella tjänsten för avläsning av marknads platser](partner-center-portal/saas-metered-billing.md).
- Virtuella Azure-datorer ger support per kärna, per kärn storlek och priser per marknad och kärn storlek. Pris alternativen kostar per timme och faktureras per månad.

När du skapar ett transactable-erbjudande är det viktigt att förstå pris-, fakturerings-, fakturerings-och utbetalnings aspekter innan du väljer en erbjudande typ och skapar ditt erbjudande. Mer information finns i [onlinebutiker för kommersiella Marketplace](overview.md#commercial-marketplace-online-stores).

## <a name="sample-offer"></a>Exempel erbjudande

När ditt erbjudande har publicerats visas det eller de List alternativ som du valde som en knapp i det övre vänstra hörnet på registrerings sidan i onlinebutiken. Följande skärm visar till exempel sidan för erbjudande listan i Microsoft AppSource onlinebutiken med knapparna **Hämta nu** och **testa enhet** :

:::image type="content" source="media/listing-options.png" alt-text="Visar en lista med ett erbjudande med knapparna kontakta mig och testa enhet.":::

## <a name="listing-and-pricing-options-by-online-store"></a>Lista och pris alternativ per onlinebutik

Baserat på en rad olika kriterier avgör vi om ditt erbjudande visas på Azure Marketplace, Microsoft AppSource eller i båda onlinebutiker. Mer information om skillnaderna mellan de två onlinebutiker finns i [onlinebutiker för kommersiella Marketplace](overview.md#commercial-marketplace-online-stores).

I följande tabell visas de alternativ som är tillgängliga för olika erbjudande typer och tillägg och vilka onlinebutiker som ditt erbjudande kan visas på.

| Erbjudande typer och tillägg | Kontakta mig | Kostnadsfri utvärdering | Hämta nu (kostnads fritt) | BYOL | Hämta nu (Transact) |
| ------------ | ------------- | ------------- | ------------- | ------------- | ------------- |
| Virtuell Azure-dator |   |   |   | Azure Marketplace | Azure Marketplace |
| Azure Application |   |   | Azure Marketplace | Azure Marketplace | Azure Marketplace |
| Azure-behållare  |   |   | Azure Marketplace | Azure Marketplace |   |
| IoT Edge modul |   |   | Azure Marketplace | Azure Marketplace |   |
| Hanterad tjänst |   |   |   | Azure Marketplace |   |
| Konsult tjänst | Båda onlinebutiker |   |   |   |   |
| SaaS  | Båda onlinebutiker | Båda onlinebutiker | Båda onlinebutiker |   | Både onlinebutiker &#42; |
| Microsoft 365 app | AppSource | AppSource |   |   | AppSource &#42;&#42; |
| Dynamics 365 Business Central | AppSource | AppSource |   |   |   |
| Dynamics 365 för kund engagemang & PowerApps | AppSource | AppSource |   |   |   |
| Dynamics 365 för åtgärder | AppSource | AppSource |   |   |   |
| Power BI-app |   |   | AppSource |   |   |
|||||||

&#42; SaaS transactable erbjudanden i AppSource är för närvarande endast kredit kort.

&#42;&#42; Microsoft 365 tillägg är kostnads fria att installeras och kan försäljas med hjälp av ett SaaS-erbjudande. Mer information finns i köpa [ditt Office 365-tillägg via Microsofts kommersiella Marketplace](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace).

## <a name="marketplace-rewards"></a>Marketplace Rewards

Dina marknads förmåner särskiljs baserat på det List alternativ som du väljer. Mer information finns i [dina kommersiella marknads förmåner](gtm-your-marketplace-benefits.md).

Om ditt erbjudande är transactable får du fördelar när du ökar din fakturerade försäljning.

Icke-transactable-erbjudanden ger förmåner baserat på om en kostnads fri utvärderings version är ansluten eller inte.

## <a name="next-steps"></a>Nästa steg

- Om du vill välja en erbjudande typ som ska skapas, se [publicerings guide efter erbjudande typ](publisher-guide-by-offer-type.md).
