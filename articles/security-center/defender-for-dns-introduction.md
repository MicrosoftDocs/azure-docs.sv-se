---
title: Azure Defender för DNS – fördelar och funktioner
description: Lär dig mer om fördelarna och funktionerna i Azure Defender för DNS
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: dffb505719e6778adfdd8e99f62790df9ebd615a
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100703"
---
# <a name="introduction-to-azure-defender-for-dns"></a>Introduktion till Azure Defender för DNS

[Azure DNS](../dns/dns-overview.md) är en värd tjänst för DNS-domäner som tillhandahåller namn matchning med hjälp av Microsoft Azure-infrastrukturen. Genom att använda Azure som värd för dina domäner kan du hantera dina DNS-poster med hjälp av samma autentiseringsuppgifter, API:er, verktyg och fakturering som för dina andra Azure-tjänster.

Azure Defender för DNS ger ett ytterligare skydds lager för dina moln resurser genom att:

- kontinuerligt övervakning av alla DNS-frågor från dina Azure-resurser
- köra avancerad säkerhets analys för att varna dig om misstänkt aktivitet

## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Förhandsgranskning<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Priset|**Azure Defender för DNS** faktureras enligt [Security Center priser](https://azure.microsoft.com/pricing/details/security-center/)|
|Moln|![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Inga](./media/icons/no-icon.png) National/suverän (US Gov, Kina gov, andra gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-dns"></a>Vilka är fördelarna med Azure Defender för DNS?

Azure Defender för DNS skyddar mot problem, inklusive:

- Data exfiltrering från dina Azure-resurser med DNS-tunnel
- Kommunikation mot skadlig kod med C&C-Server
- Kommunikation med skadliga domäner som nätfiske och krypto-utvinning
- DNS-attacker – kommunikation med skadliga DNS-matchare 

En fullständig lista över de aviseringar som tillhandahålls av Azure Defender för DNS finns på [referens sidan aviseringar](alerts-reference.md#alerts-dns).

## <a name="dependencies"></a>Beroenden

Azure Defender för DNS använder inte några agenter. 

Om du vill skydda ditt DNS-lager aktiverar du Azure Defender för DNS för var och en av dina prenumerationer enligt beskrivningen i [Aktivera Azure Defender](enable-azure-defender.md).


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om Azure Defender för DNS. Information om relaterade material finns i följande artikel: 

- Säkerhets aviseringar kan genereras av Security Center eller tas emot av Security Center från olika säkerhets produkter. Om du vill exportera alla dessa aviseringar till Azure Sentinel, SIEM eller andra externa verktyg, följer du anvisningarna i [Exportera aviseringar till en Siem](continuous-export.md).

- > [!div class="nextstepaction"]
    > [Aktivera Azure Defender](enable-azure-defender.md)