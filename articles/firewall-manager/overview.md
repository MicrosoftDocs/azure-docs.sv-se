---
title: Vad är Azure Firewall Manager?
description: Lär dig mer om Azure Firewall Manager-funktioner
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 08/18/2020
ms.author: victorh
ms.openlocfilehash: b4ef35f2892925919ca9c8eda37a9b0e0d11835e
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590412"
---
# <a name="what-is-azure-firewall-manager"></a>Vad är Azure Firewall Manager?

Azure Firewall Manager är en säkerhets hanterings tjänst som tillhandahåller Central säkerhets policy och väg hantering för molnbaserade säkerhetsperimetern. 

Brand Väggs hanteraren kan tillhandahålla säkerhets hantering för två typer av nätverks arkitektur:

- **Skyddad virtuell hubb**

   En [virtuell WAN-hubb i Azure](../virtual-wan/virtual-wan-about.md#resources) är en Microsoft-hanterad resurs som gör att du enkelt kan skapa Hubbs-och eker-arkitekturer. När säkerhets-och routningsprinciperna är associerade med en sådan hubb kallas den för en *[säker virtuell hubb](secured-virtual-hub.md)*. 
- **Virtuellt hub-nätverk**

   Det här är ett standard Azure Virtual Network som du skapar och hanterar själv. När säkerhets principer är associerade med en sådan hubb kallas det ett *nav virtuellt nätverk*. För närvarande stöds endast Azure Firewall-principen. Du kan använda peer-ekrar för virtuella nätverk som innehåller dina arbets belastnings servrar och-tjänster. Du kan också hantera brand väggar i fristående virtuella nätverk som inte är peer-kopplade till ekrar.

En detaljerad jämförelse mellan *skyddade virtuella hubbar* och *virtuella hubbar i hubben* finns i [Vad är arkitektur alternativ för Azure Firewall Manager?](vhubs-and-vnets.md).

![Firewall-Manager](media/overview/trusted-security-partners.png)

## <a name="azure-firewall-manager-features"></a>Funktioner i Azure Firewall Manager

Azure Firewall Manager erbjuder följande funktioner:

### <a name="central-azure-firewall-deployment-and-configuration"></a>Central Azure Firewall-distribution och konfiguration

Du kan distribuera och konfigurera flera Azure Firewall-instanser centralt som sträcker sig över olika Azure-regioner och prenumerationer. 

### <a name="hierarchical-policies-global-and-local"></a>Hierarkiska principer (globala och lokala)

Du kan använda Azure Firewall Manager för att centralt hantera Azure Firewall-principer över flera skyddade virtuella nav. Dina centrala IT-team kan skapa globala brand Väggs principer som upprätthåller organisationens bred brand Väggs policy över team. Lokalt skapade brand Väggs principer tillåter en DevOps-självbetjänings modell för bättre flexibilitet.

### <a name="integrated-with-third-party-security-as-a-service-for-advanced-security"></a>Integrerat med säkerhets-som-en-tjänst från tredje part för avancerad säkerhet

Förutom Azure-brandväggen kan du integrera SECaaS-providers (Security as a Service) från tredje part för att ge ytterligare nätverks skydd för ditt VNet-och filial Internet-anslutningar.

Den här funktionen är endast tillgänglig med säkra virtuella nav distributioner.

- Trafik filtrering för VNet till Internet (V2I)

   - Filtrera utgående virtuell nätverks trafik med din önskade säkerhets leverantör från tredje part.
   - Använd avancerad användar medveten Internet skydd för dina moln arbets belastningar som körs på Azure.

- Trafik filtrering för gren till Internet (B2I)

   Dra nytta av din Azure-anslutning och global distribution för att enkelt lägga till tredjeparts filtrering för förgreningar i Internet scenarier.

Mer information om leverantörer av säkerhets partner finns i [Vad är Azure Firewall Manager-leverantörer av säkerhets partner?](trusted-security-partners.md)

### <a name="centralized-route-management"></a>Centraliserad flödes hantering

Dirigera snabbt trafik till din säkra hubb för filtrering och loggning utan att behöva konfigurera användardefinierade vägar manuellt (UDR) i ekrar virtuella nätverk. 

Den här funktionen är endast tillgänglig med säkra virtuella nav distributioner.

Du kan använda tredjepartsleverantörer för B2I-trafik (Branch to Internet), sida vid sida med Azure-brandväggen för gren till VNet (B2V), VNet till VNet (V2V) och VNet till Internet (V2I). Du kan också använda tredjepartsleverantörer för V2I-trafik filtrering så länge som Azure Firewall inte krävs för B2V eller V2V. 

## <a name="region-availability"></a>Regional tillgänglighet

Azure Firewall-principer kan användas i olika regioner. Du kan till exempel skapa en princip i USA, västra och använda den i USA, östra. 

## <a name="known-issues"></a>Kända problem

Azure Firewall Manager har följande kända problem:

|Problem  |Beskrivning  |Åtgärd  |
|---------|---------|---------|
|Delning av trafik|Det finns för närvarande inte stöd för att dela Office 365 och Azure offentlig PaaS-trafik. Det innebär att om du väljer en tredjeparts-Provider för V2I eller B2I skickas även all Azures offentliga PaaS och Office 365-trafik via partner tjänsten.|Undersöker delning av trafik på hubben.
|En säker virtuell hubb per region|Du kan inte ha mer än en säker virtuell hubb per region.|Skapa flera virtuella WAN-näti en region.|
|Bas principerna måste finnas i samma region som den lokala principen|Skapa alla lokala principer i samma region som bas principen. Du kan fortfarande använda en princip som har skapats i en region på ett skyddat nav från en annan region.|Undersöker|
|Filtrera Inter-Hub-trafik i säkra virtuella nav distributioner|Skyddad virtuell hubb till skyddad virtuell hubb kommunikations filtrering stöds inte ännu. NAV-till-hubb-kommunikation fungerar dock fortfarande om privat trafik filtrering via Azure Firewall inte är aktive rad.|Undersöker|
|Ekrar i annan region än den virtuella hubben|Ekrar i annan region än det virtuella navet stöds inte.|Undersöker<br><br>Skapa en hubb per region och peer-virtuella nätverk i samma region som hubben.|
|Gren till gren trafik med privat trafik filtrering aktive rad|Trafik mellan grenar och förgreningar stöds inte när privat trafik filtrering är aktive rad. |Undersöka.<br><br>Skydda inte privat trafik om gren-till-gren-anslutningen är kritisk.|
|Alla skyddade virtuella hubbar som delar samma virtuella WAN-nätverk måste finnas i samma resurs grupp.|Det här beteendet är justerat med virtuella WAN-hubbar idag.|Skapa flera virtuella WAN-administratörer för att kunna skapa säkra virtuella hubbar i olika resurs grupper.|
|Det går inte att lägga till Mass IP-adress|Brand väggen för skyddat nav hamnar i ett felaktigt tillstånd om du lägger till flera offentliga IP-adresser.|Lägg till mindre offentliga IP-prisökningar. Du kan till exempel lägga till 10 i taget.|
|Det går inte att köra program regler i ett säkert nav med konfigurerat anpassad DNS (för hands version).|DNS-proxy/anpassad DNS (för hands version) fungerar inte i scenarier där en brand Väggs hantering NIC har kon figurer ATS. Detta inkluderar distributioner av säkra hubbar och fall där Tvingad tunnel trafik är aktive rad.|Korrigera under undersökning.|

## <a name="next-steps"></a>Nästa steg

- Läs [Översikt över Azure Firewall Manager-distribution](deployment-overview.md)
- Läs mer om [skyddade virtuella nav](secured-virtual-hub.md).
