---
title: Krav för samförsäljning | Azure Marketplace
description: Lär dig mer om kraven som ett erbjudande i Microsofts kommersiella marknads plats måste uppfylla för att kunna ta del av en färdig eller samförsäljnings motiverade status.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.reviewer: stmummer
ms.date: 3/17/2021
ms.openlocfilehash: e67219e0b029ce401ffc05c009a5cc4a96680a9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593398"
---
# <a name="co-sell-requirements"></a>Krav för samförsäljning

Den här artikeln innehåller kraven för de olika nivåerna av samförsäljnings status. Den senaste listan med erbjudande typer som stöder samförsäljning finns i  [Konfigurera samförsäljning för ett erbjudande för en kommersiell Marketplace](co-sell-configure.md). En översikt över samförsäljningen finns i [samförsäljning med Microsoft Sales Teams and Partners Overview](co-sell-overview.md).

I den här tabellen visas alla möjliga status för samma försäljning:

| Status | Kommentar |
| ------------ | ------------- |
| Är inte färdig att sälja | Minimi [kraven för status för medförsäljnings klar](#requirements-for-co-sell-ready-status) har inte uppfyllts. |
| Färdiga försäljnings produkter | Alla [krav för klar status för medförsäljning](#requirements-for-co-sell-ready-status) har uppfyllts. |
| Azure IP Co-Sälj motiverade | Medförsäljnings klara krav har uppfyllts utöver [dessa ytterligare krav](#requirements-for-azure-ip-co-sell-incentivized-status). |
| Business Applications Co-Sälj motiverade | Den här statusen gäller för Dynamics 365 och Power Apps-erbjudanden i [Microsoft Business Applications ISV Connect-programmet](business-applications-isv-program.md) och indikerar att alla [krav för den här statusen](#requirements-for-business-applications-co-sell-incentivized-status) har uppfyllts. |
|||

## <a name="requirements-for-co-sell-ready-status"></a>Krav för klar status för medförsäljning

För ett erbjudande om att uppnå en färdig status för medförsäljning måste du uppfylla följande krav:

**Alla partner**:

- Ha ett MPN-ID och ett aktivt [kommersiellt marknads plats konto i Partner Center](./partner-center-portal/create-account.md).
- Se till att du har en fullständig [företags profil](/partner-center/create-a-marketing-profile) i Partner Center. Som kvalificerad Microsoft-partner hjälper din företags profil dig att presentera din verksamhet för kunder som letar efter dina unika lösningar och expertis för att lösa sina affärs behov, vilket resulterar i [hänvisningar](/partner-center/referrals).
- Slutför fliken **Co-Sälj med Microsoft** och publicera erbjudandet på den kommersiella Marketplace.
- Ange en försäljnings kontakt för varje samsäljande geografisk region och nödvändig material struktur.

**Tjänste partner**:

- För erbjudanden av typen av _tjänst lösning_ måste du ha en aktiv guld-kompetens i alla kompetens områden.

**Business Applications-ISV**: ar:

- Dynamics 365 & PowerApps (utom Dynamics 365 Business Central) lösningar kräver registrering av ISV Connect.

### <a name="complete-the-co-sell-with-microsoft-tab"></a>Slutför fliken Co-Sälj med Microsoft

När du publicerar eller uppdaterar ditt erbjudande ska du ange all information som krävs på sidan **samförsäljning med Microsoft** , som beskrivs i [Konfigurera samförsäljning för ett erbjudande från en kommersiell Marketplace](commercial-marketplace-co-sell.md). Detta omfattar att tillhandahålla följande dokument:

- Lösning/erbjuda en-pager
- Lösning/erbjud avstånds kort

Vi tillhandahåller mallar för att hjälpa dig att skapa dessa dokument. Mer information om den obligatoriska och valfria informationen för samförsäljning med Microsoft-fliken finns i [Konfigurera samförsäljning för ett erbjudande för ett kommersiellt Marketplace](commercial-marketplace-co-sell.md).

### <a name="publish-your-offer-live"></a>Publicera ditt erbjudande Live

Om du vill kvalificera sig för medförsäljnings status måste ditt erbjudande eller din lösning publiceras Live till minst ett av de kommersiella online butikerna för Marketplace: Azure Marketplace eller Microsoft AppSource. Information om hur du publicerar erbjudanden till den kommersiella marknads platsen finns i [publicerings guide efter erbjudande typ](publisher-guide-by-offer-type.md). Om du inte har publicerat något erbjudande på den kommersiella Marketplace tidigare, se till att du har ett [kommersiellt marknads plats konto](./partner-center-portal/create-account.md).

## <a name="requirements-for-azure-ip-co-sell-incentivized-status"></a>Krav för Azure IP Co-Sälj motiverade-status

Azure IP Co-Sälj motiverade-status gäller för följande erbjudande typer:

- Azure Application
- Azure-behållare
- Virtuell Azure-dator
- IoT Edge modul
- Programvara som en tjänst (SaaS)

Efter att ha slutfört statusen medförsäljning finns det tre ytterligare krav för att uppnå Azure IP-motiverade status:

Krav 1 – uppnå följande:

- På _organisations nivå_ genererar du minst $100 000 USD av Azures förbrukade intäkts tröskel under den efterföljande 12-månaders perioden. Detta kan fås genom en kombination av Azure-lösningar. Om erbjudandet är i ett särskilt spektrum i den kommersiella marknads platsen kan du uppfylla det här kravet genom att uppfylla $100 000 USD för en fakturerings kvot.

Krav 2 – skicka Microsofts tekniska validering för en Azure-baserad lösning:
- Den tekniska valideringen måste bekräfta att mer än 50% av ditt erbjudandes infrastruktur använder upprepnings bar IP-kod på Azure. Observera att virtuella datorer i transactable och Azure Application lösningar på den kommersiella marknads platsen uppfyller detta krav som standard.

Krav 3 – Ange ett referens arkitektur diagram:
- Ladda upp ett referens arkitektur diagram med dina samsäljande dokument i Partner Center för granskning. Vägledning om hur du skapar diagrammet finns i [referens arkitektur diagram](reference-architecture-diagram.md). Information om hur du laddar upp diagrammet finns i [Konfigurera samförsäljning för ett erbjudande för handels platser](commercial-marketplace-co-sell.md).

## <a name="requirements-for-business-applications-co-sell-incentivized-status"></a>Krav för Business Applications medförsäljning motiverade-status

Den här statusen gäller för IP-baserade lösningar som bygger på Dynamics 365 eller Power Apps (förutom för Dynamics 365 Business Central) som har registrerats i ISV Connect-programmet. Erbjudandena måste dock också uppfylla kraven för klar ande status för medförsäljning (beskrivs ovan) för att Microsoft-säljarna ska kunna samsälja erbjudandet med dig.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera samförsäljning för ett erbjudande för handels platser](commercial-marketplace-co-sell.md)
