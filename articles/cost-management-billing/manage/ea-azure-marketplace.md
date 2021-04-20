---
title: Azure Marketplace
description: Beskriver hur EA-kunder kan använda Azure Marketplace
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: banders
ms.openlocfilehash: ce9dff017a796e420586ad191a59c149bed07190
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726834"
---
# <a name="azure-marketplace"></a>Azure Marketplace

I den här artikeln förklarar vi hur EA-kunder och -partner kan se Marketplace-avgifter och aktivera Azure Marketplace-köp.

## <a name="azure-marketplace-for-ea-customers"></a>Azure Marketplace för EA-kunder

För direktkunder visas Azure Marketplace-avgifter i Azure Enterprise-portalen. Azure Marketplace-köp och -förbrukning debiteras utanför Azure-förskottsbetalningar och debiteras kvartalsvis eller månadsvis och i efterskott.

Indirekta kunder kan se sina Azure Marketplace-prenumerationer på sidan **Hantera prenumerationer** i Azure Enterprise-portalen, men priserna är dolda. Kunderna bör kontakta sin LSP (leverantör av licenslösningar) för information om Azure Marketplace-avgifter.

Nya månatliga eller årligen återkommande Azure Marketplace-köp debiteras hela beloppet inom den period då Azure Marketplace-objekten köptes. Dessa objekt förnyas automatiskt i nästa period på samma dag då det ursprungliga köpet gjordes.

Befintliga månatliga återkommande avgifter fortsätter att förnyas den första i varje kalendermånad. Årsavgifter förnyas på årsdagen för inköpet.

Nu förbrukar vissa tjänster från tredjepartsåterförsäljare på Azure Marketplace ditt saldo för Azure EA-förskottsbetalningar. Tidigare debiterades dessa tjänster utanför Azure EA-förskottsbetalningar och fakturerades separat. Azure EA-förskottsbetalningar för dessa tjänster på Azure Marketplace förenklar hanteringen av köp och betalningar från kunder. En fullständig lista över tjänster som förbrukar Azure-förskottsbetalningar finns i [uppdateringen från 6 mars 2018 på Azure-webbplatsen](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/).

### <a name="partners"></a>Partner

LSP:er kan ladda ned en Azure Marketplace-prislista från prisdokumentsidan i Azure Enterprise-portalen. Välj länken **Marketplace-prislistan** i det övre högra hörnet. Azure Marketplace-prislistan visar alla tillgängliga tjänster och motsvarande priser.

Så här laddar du ned prislistan:

1. I Azure Enterprise-portalen går du till **Rapporter** > **Prisdokument**.
1. Leta reda på länken Azure Marketplace-prislistan under ditt användarnamn i det övre högra hörnet.
1. Högerklicka på länken och välj **Spara mål som**.
1. I fönstret **Spara** ändrar du dokumentets titel till `AzureMarketplacePricelist.zip` så att filen ändras från en XLSX-fil till en ZIP-fil.
1. När hämtningen är klar har du en ZIP-fil med landsspecifika prislistor.
1. LSP:er kan se priser för respektive land i motsvarande fil. På fliken **Notifications** (Meddelanden) kan LSP:er se vilka SKU:er som är nya eller som har tagits bort.
1. Prisändringar är ovanliga. LSP:er meddelas via e-post om prisökningar och valutakursändringar 30 dagar i förväg.
1. LSP:er får en faktura per registrering, ISV och kvartal.

### <a name="enabling-azure-marketplace-purchases"></a>Aktivera Azure Marketplace-inköp

Företagsadministratörer kan inaktivera eller aktivera Marketplace-inköp för alla Azure-prenumerationer som ingår i registreringen. Om företagsadministratören inaktiverar köp och det finns Azure-prenumerationer som redan har Azure Marketplace-prenumerationer, avbryts eller påverkas inte dessa Azure Marketplace-prenumerationer.

Även om kunderna kan konvertera sina Azure-direktprenumerationer till Azure EA genom att koppla dem till registreringen i Azure Enterprise-portalen konverteras inte de underordnade prenumerationerna automatiskt med den åtgärden.

Så här aktiverar du Azure Marketplace-inköp:

1. Logga in på Azure Enterprise-portalen som företagsadministratör.
1. Gå till **Hantera**.
1. Under **Enrollment Detail** (Registreringsinformation) väljer du pennikonen bredvid **Azure Marketplace**.
1. Växla **Aktiverad/inaktiverad** eller **Endast kostnadsfria/BYOL SKU:er** efter behov.
1. Välj **Spara**.

> [!NOTE]
> BYOL (Bring your own license) och alternativet för endast kostnadsfritt begränsar inköp och förvärv av Azure Marketplace-SKU:er till endast BYOL och kostnadsfria SKU:er.

### <a name="services-billed-hourly-for-azure-ea"></a>Tjänster debiteras per timme för Azure EA

Följande tjänster debiteras per timme enligt Enterprise-avtalet i stället för priset per månad i MOSP:

- Application Delivery Network
- Brandvägg för webbaserade program

### <a name="azure-remoteapp"></a>Azure RemoteApp

Om du har ett Enterprise-avtal betalar du för Azure RemoteApp baserat på prisnivån för ditt Enterprise-avtal. Det finns inga ytterligare avgifter. Standardprisnivån innefattar de första 40 timmarna. Den obegränsade prisnivån innefattar de första 80 timmarna. Remote App slutar generera användning efter dessa 80 timmar.

## <a name="next-steps"></a>Nästa steg

- Få mer information om [prissättning](ea-pricing-overview.md).
- Läs vanliga [Cost Management + Billing om du](../cost-management-billing-faq.yml) vill se en lista över frågor och svar om Azure Marketplace-tjänster och Azure EA-förskottsbetalningar.