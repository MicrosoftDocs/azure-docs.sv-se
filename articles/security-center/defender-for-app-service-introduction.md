---
title: Azure Defender för App Service – fördelar och funktioner
description: Lär dig mer om funktionerna i Azure Defender för App Service och hur du aktiverar det i din prenumeration
author: memildin
ms.author: memildin
ms.date: 01/25/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 2a3253d1ed8b0814fc20b3256a0f98d3aa0949f6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393317"
---
# <a name="introduction-to-azure-defender-for-app-service"></a>Introduktion till Azure Defender för App Service

Azure App Service är en helt hanterad plattform för att bygga och vara värd för dina webbappar och API: er. Eftersom plattformen är fullständigt hanterad behöver du inte oroa dig över infrastrukturen. Den ger hantering, övervakning och Operational Insights för att uppfylla prestanda-, säkerhets-och efterlevnads krav i företags klass. Mer information finns i [Azure App Service](https://azure.microsoft.com/services/app-service/).

**Azure Defender för App Service** använder molnets skala för att identifiera angrepps mål program som körs över App Service. Angripare avsöker webb program för att hitta och utnyttja svagheter. Innan de dirigeras till vissa miljöer går förfrågningar till program som körs i Azure genom flera gatewayer, där de inspekteras och loggas. Dessa data används sedan för att identifiera sårbarheter och angripare och för att lära dig nya mönster som ska användas senare.


## <a name="availability"></a>Tillgänglighet

| Aspekt                       | Information                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
|------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Versions tillstånd:               | Allmän tillgänglighet (GA)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Priset                     | [Azure Defender för App Service](azure-defender.md) faktureras så som visas på [sidan med priser](security-center-pricing.md)<br>Faktureringen sker enligt de totala beräknings instanserna i alla planer|
| App Services planer som stöds: | Alla App Services planer stöds (med ett undantag, se nedan). [Läs mer om app Services planer](https://azure.microsoft.com/pricing/details/app-service/plans/).<br>Azure Functions i förbruknings planen stöds inte. [Läs mer om Azure Functions värd alternativ](../azure-functions/functions-scale.md).                                                                                                                                                                                                                                                                   |
| Moln                      | ![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Inga](./media/icons/no-icon.png) National/suverän (US Gov, Kina gov, andra gov)                                                                                                                                                                                                                                                                                                                                                                                 |
|                              |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |

## <a name="what-are-the-benefits-of-azure-defender-for-app-service"></a>Vilka är fördelarna med Azure Defender för App Service?

När du aktiverar Azure Defender för App Service drar du omedelbart nytta av följande tjänster som erbjuds av den här Azure Defender-planen:

- **Secure** -Security Center utvärderar de resurser som täcks av din app service plan och genererar säkerhets rekommendationer baserat på dess resultat. Använd de detaljerade anvisningarna i dessa rekommendationer för att förstärka dina App Service-resurser.

- **Identifiera** – Azure Defender identifierar en mängd hot för dina App Service resurser genom övervakning:
    - den virtuella dator instans som din App Service körs i och dess hanterings gränssnitt
    - begär Anden och svar som skickas till och från dina App Service-appar
    - underliggande sand boxar och virtuella datorer
    - App Service interna loggar – tillgänglig tack vare den synlighet som Azure har som en moln leverantör

Som en molnbaserad lösning kan Azure Defender identifiera angrepps metoder som tillämpas på flera mål. Till exempel kan det vara svårt att identifiera ett distribuerat angrepp från en liten delmängd IP-adresser från en enda värd och crawla till liknande slut punkter på flera värdar.

Loggdata och infrastrukturen tillsammans kan meddela artikeln: från ett nytt angrepp som cirkulerar på vilda djur att kompromissa med kund maskiner. Det innebär att även om Security Center distribueras efter att en webbapp har utnyttjats, kan det vara möjligt att identifiera pågående attacker.


## <a name="what-threats-can-azure-defender-for-app-service-detect"></a>Vilka hot kan Azure Defender för App Service identifiera?

### <a name="threats-by-mitre-attck-tactics"></a>Hot från MITRE att&CK taktiker

Azure Defender övervakar för många hot om dina App Service-resurser. Aviseringarna behandlar nästan den fullständiga listan över MITRE att&tilldela taktiker från för angrepp till kommando och kontroll. Azure Defender kan identifiera:

- **För hands angrepp** – Defender kan identifiera körningen av flera typer av sårbarhets skannrar som angripare ofta använder för att avsöka program efter svagheter.

- **Första åtkomst till hot**  -  [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684) ger till gång till de här aviseringarna som inkluderar utlöser en avisering när en känd skadlig IP-adress ansluts till ditt Azure App Service FTP-gränssnitt.

- **Körnings hot** – Defender kan identifiera försök att köra kommandon med hög behörighet, Linux-kommandon på en Windows-app service, fil lös angrepp, verktyg för digital valuta utvinning och många andra misstänkta och skadliga kod körnings aktiviteter.

### <a name="dangling-dns-detection"></a>Dangling DNS-identifiering

Azure Defender för App Service identifierar också eventuella DNS-poster som återstår i din DNS-registrator när en App Service webbplats inaktive ras – dessa kallas Dangling DNS-poster. När du tar bort en webbplats och inte tar bort den anpassade domänen från din DNS-registrator, pekar DNS-posten på en resurs som inte finns och under domänen är utsatt för en övertag Ande. Azure Defender söker inte igenom din DNS-registrator efter *befintliga* Dangling DNS-poster; den varnar dig när en App Service webbplats tas ur bruk och dess anpassade domän (DNS-post) inte tas bort.

Under domänens övertag Ande är ett vanligt hot mot hög allvarlighets grad för organisationer. När en Threat aktör identifierar en Dangling DNS-post, skapar de sin egen webbplats på mål adressen. Trafiken som är avsedd för organisationens domän dirigeras sedan till hot aktörens webbplats, och de kan använda trafiken för en mängd olika skadliga aktiviteter.

Dangling DNS-skydd är tillgängligt oavsett om dina domäner hanteras med Azure DNS eller en extern domän registrator och gäller för App Service på både Windows och Linux.

:::image type="content" source="media/defender-for-app-service-introduction/dangling-dns-alert.png" alt-text="Ett exempel på en Azure Defender-avisering om en identifierad Dangling DNS-post. Aktivera Azure Defender för App Service att ta emot dessa och andra aviseringar för din miljö." lightbox="media/defender-for-app-service-introduction/dangling-dns-alert.png":::

Läs mer om Dangling DNS och hotet om under domän Övertagning, i [förhindra Dangling DNS-poster och undvika under domän övertag](../security/fundamentals/subdomain-takeover.md)Ande.

En fullständig lista över Azure App Service aviseringar finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-azureappserv).

> [!NOTE]
> Defender kanske inte utlöser Dangling DNS-aviseringar om din anpassade domän inte pekar direkt till en App Service resurs, eller om Defender inte har övervakat trafik till webbplatsen sedan Dangling-DNS-skyddet aktiverades (eftersom det inte finns några loggar för att hjälpa till att identifiera den anpassade domänen).

## <a name="how-to-protect-your-azure-app-service-web-apps-and-apis"></a>Skydda dina Azure App Service-webbappar och API: er

För att skydda din Azure App Service-plan med Azure Defender för App Service:

1. Se till att du har ett App Service plan som är associerat med dedikerade datorer. De planer som stöds anges ovan i [tillgänglighet](#availability).

2. Aktivera **Azure Defender** på din prenumeration enligt beskrivningen i [prissättningen av Azure Security Center](security-center-pricing.md).

    Du kan välja att aktivera enskilda planer i Azure Defender (till exempel Azure Defender för App Service).

    Security Center är internt integrerat med App Service, vilket eliminerar behovet av distribution och onboarding – integreringen är transparent.


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om Azure Defender för App Service. 

Information om relaterade material finns i följande artiklar: 

- Om du vill exportera aviseringar till Azure Sentinel, SIEM eller andra externa verktyg, följer du anvisningarna i [Stream-aviseringar till en Siem, Soar eller IT-Tjänstehanterings lösning](export-to-siem.md).
- En lista över Azure Defender för App Service-aviseringar finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-azureappserv).
- Mer information om App Service-planer finns i [App Service planer](https://azure.microsoft.com/pricing/details/app-service/plans/).
> [!div class="nextstepaction"]
> [Aktivera Azure Defender](security-center-pricing.md#enable-azure-defender)