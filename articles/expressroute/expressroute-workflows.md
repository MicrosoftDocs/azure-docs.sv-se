---
title: 'Azure-ExpressRoute: arbets flöde för krets konfiguration'
description: På den här sidan visas arbets flödet för konfiguration av ExpressRoute-kretsar och peering
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: duau
ms.custom: contperf-fy21q1
ms.openlocfilehash: 24ad325cae2ee71ad49ee8ee055a83ceb8fa7ef2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721743"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Arbetsflöden i ExpressRoute för kretsetablering och kretstillstånd

Den här artikeln vägleder dig genom tjänst etablering och konfigurations arbets flöden för routning på en hög nivå. I följande avsnitt beskrivs aktiviteterna för att etablera en ExpressRoute-krets från slut punkt till slut punkt.

## <a name="workflow-steps"></a>Arbets flödes steg

### <a name="1-prerequisites"></a>1. krav

Se till att kraven är uppfyllda. En fullständig lista finns i [krav och check lista](expressroute-prerequisites.md).

* En Azure-prenumeration har skapats.
* Fysisk anslutning har upprättats med ExpressRoute-partnern eller kon figurer ATS via ExpressRoute Direct. Gransknings plats, se [platser och partners](expressroute-locations-providers.md#partners) för att Visa ExpressRoute-partner och ExpressRoute direkt anslutning mellan peering-platser.

### <a name="2-order-connectivity-or-configure-expressroute-direct"></a>2. Beställ anslutning eller konfigurera ExpressRoute Direct

Beställ anslutningar från tjänst leverantören eller konfigurera ExpressRoute Direct.

#### <a name="expressroute-partner-model"></a>ExpressRoute-partner modell

Beställ anslutningar från tjänst leverantören. Den här processen varierar. Kontakta din anslutnings leverantör om du vill ha mer information om hur du beställer anslutningar.

* Välj ExpressRoute-partnern
* Välj peering-plats
* Välj bandbredd
* Välj fakturerings modell
* Välj standard-eller Premium-tillägg

#### <a name="expressroute-direct-model"></a>ExpressRoute Direct-modell

* Visa tillgänglig ExpressRoute Direct-kapacitet mellan peering-platser.
* Reservera portar genom att skapa ExpressRoute Direct-resursen i din Azure-prenumeration.
* Begär och ta emot rem bur tillståndet och Beställ de fysiska kors anslutningarna från peering-providern.
* Aktivera admin-tillstånd och Visa ljus nivåer och fysisk länk med [Azure Monitor](expressroute-monitoring-metrics-alerts.md#expressroute-direct-metrics).

### <a name="3-create-an-expressroute-circuit"></a>3. skapa en ExpressRoute-krets

#### <a name="expressroute-partner-model"></a>ExpressRoute-partner modell

Kontrol lera att ExpressRoute-partnern är redo att etablera anslutningen. Din ExpressRoute-krets debiteras från den tidpunkt då en tjänst nyckel utfärdas. Följ anvisningarna i [skapa en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md) för att skapa din krets.

#### <a name="expressroute-direct-model"></a>ExpressRoute Direct-modell

Se till att den fysiska länken och administratörs statusen är aktiverade på båda länkarna. Läs om [hur du konfigurerar ExpressRoute Direct](how-to-expressroute-direct-portal.md) för vägledning. Din ExpressRoute-krets debiteras från den tidpunkt då en tjänst nyckel utfärdas. Följ anvisningarna i [skapa en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md) för att skapa din krets.

### <a name="4-service-provider-provisions-connectivity"></a>4. tjänst leverantör etablerar anslutningar

Det här avsnittet gäller endast för ExpressRoute-partnerns anslutnings modell:

* Ange tjänst nyckeln (s-Key) för anslutnings leverantören.
* Ange ytterligare information som anslutnings leverantören behöver (t. ex. VPN-ID).
* Om providern hanterar konfigurationen av routningen anger du den information som krävs.

Du kan se till att kretsen har etablerats genom att verifiera ExpressRoute-kretsens etablerings status med PowerShell, Azure Portal eller CLI.

### <a name="5-configure-routing-domains"></a>5. Konfigurera routningsdomäner

Konfigurera routningsdomäner. Om din anslutnings leverantör hanterar Layer 3-konfiguration, konfigureras routning för din krets. Om din anslutnings leverantör bara erbjuder Layer 2-tjänster eller om du använder ExpressRoute Direct, måste du konfigurera routning enligt de rikt linjer som beskrivs i artiklarna [routnings krav](expressroute-routing.md) och [konfiguration av routning](expressroute-howto-routing-classic.md) .

#### <a name="for-azure-private-peering"></a>För privat Azure-peering

Aktivera privat peering för att ansluta till virtuella datorer och moln tjänster som distribueras i det virtuella Azure-nätverket.

* IPv4-undernät:
    * Peering-undernät för sökväg 1 (/30)
    * Peering-undernät för sökväg 2 (/30)
* IPv6-undernät (valfritt):
    * Peering-undernät för sökväg 1 (/126)
    * Peering-undernät för sökväg 2 (/126)
* VLAN-ID för peering
* ASN för peering
* ExpressRoute ASN = 12076
* MD5-hash (valfritt)

#### <a name="for-microsoft-peering"></a>För Microsoft-peering

Aktivera detta för att få åtkomst till Microsoft onlinetjänster, till exempel Microsoft 365. Dessutom är alla Azure PaaS-tjänster tillgängliga via Microsoft-peering. Du måste se till att du använder en separat proxy/Edge för att ansluta till Microsoft än den som du använder för Internet. Att använda samma gräns för både ExpressRoute och Internet kommer att orsaka asymmetrisk Routning och orsaka anslutnings avbrott för nätverket.

* IPv4-undernät:
    * Peering-undernät för sökväg 1 (/30) – måste vara offentlig IP
    * Peering-undernät för sökväg 2 (/30) – måste vara offentlig IP
* IPv6-undernät (valfritt):
    * Peering-undernät för sökväg 1 (/126) – måste vara offentlig IP
    * Peering-undernät för sökväg 2 (/126) – måste vara offentlig IP
* VLAN-ID för peering
* ASN för peering
* Annonserade prefix – måste vara offentliga IP-prefix
* Kund-ASN (valfritt om det skiljer sig från peering ASN)
* RIR/IR för IP-och ASN-verifiering
* ExpressRoute ASN = 12076
* MD5-hash (valfritt)

### <a name="6-start-using-the-expressroute-circuit"></a>6. börja använda ExpressRoute-kretsen

* Du kan länka virtuella Azure-nätverk till din ExpressRoute-krets för att möjliggöra anslutning från lokala nätverk till det virtuella Azure-nätverket. I artikeln [Länka ett VNet till en krets kan](expressroute-howto-linkvnet-arm.md) du läsa mer om vägledning. Dessa virtuella nätverk kan antingen finnas i samma Azure-prenumeration som ExpressRoute-kretsen eller vara i en annan prenumeration.
* Anslut till Azure-tjänster och Microsofts moln tjänster via Microsoft-peering.

##  <a name="expressroute-partner-circuit-provisioning-states"></a><a name="expressroute-circuit-provisioning-states"></a>Etablerings tillstånd för ExpressRoute-partner-kretsen

I följande avsnitt beskrivs olika ExpressRoute-krets lägen för ExpressRoute-partnerns anslutnings modell.
Varje ExpressRoute partner krets har två tillstånd:

* **ServiceProviderProvisioningState** representerar statusen på anslutnings leverantörens sida. Det kan antingen vara *NotProvisioned*, *etablering* eller *etablerad*. ExpressRoute-kretsen måste vara i ett etablerings tillstånd för att du ska kunna konfigurera peering. **Det här läget gäller bara för ExpressRoute-partner kretsar och visas inte i egenskaperna för en ExpressRoute Direct-krets**.

* **Status** representerar Microsofts etablerings status. Den här egenskapen anges till aktive rad när du skapar en ExpressRoute-krets

### <a name="possible-states-of-an-expressroute-circuit"></a>Möjliga tillstånd för en ExpressRoute-krets

I det här avsnittet beskrivs möjliga tillstånd för en ExpressRoute-krets som skapats under ExpressRoute partner Connectivity-modellen.

**Vid skapande tillfället**

ExpressRoute-kretsen rapporterar följande tillstånd när en resurs skapas.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

**När anslutnings leverantören håller på att allokera kretsen**

ExpressRoute-kretsen kommer att rapportera följande tillstånd när anslutnings leverantören arbetar för att etablera kretsen.

```output
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

**När anslutnings leverantören har slutfört etablerings processen**

ExpressRoute-kretsen rapporterar följande tillstånd när anslutnings leverantören har kunnat allokera kretsen.

```output
ServiceProviderProvisioningState : Provisioned
Status                           : Enabled
```

**När anslutnings leverantören avetablerar kretsen**

Om ExpressRoute-kretsen behöver avetableras, kommer kretsen att rapportera följande tillstånd när tjänste leverantören har slutfört avetablerings processen.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

Du kan välja att återaktivera den om det behövs eller köra PowerShell-cmdletar för att ta bort kretsen.  

> [!IMPORTANT]
> Det går inte att ta bort en krets när ServiceProviderProvisioningState är etablerad eller etablerad. Anslutnings leverantören måste avetablera kretsen innan den kan tas bort. Microsoft fortsätter att debitera kretsen tills ExpressRoute-krets-resursen tas bort i Azure.
> 

## <a name="routing-session-configuration-state"></a>Konfigurations tillstånd för routningsdomän

Tillstånds rapporter för BGP-etablering om BGP-sessionen har Aktiver ATS på Microsoft Edge. Status måste vara aktive rad för att använda privat eller Microsoft-peering.

Det är viktigt att kontrol lera BGP-sessionstillstånd särskilt för Microsoft-peering. Förutom BGP-etablerings status är det ett annat tillstånd som kallas *annonserat offentlig prefix*. Det annonserade offentliga prefixet måste vara i det *konfigurerade* läget, båda för att BGP-sessionen ska vara igång och för att routningen ska fungera från slut punkt till slut punkt. 

Om det annonserade offentliga prefixet är inställt på ett tillstånd som *krävs för validering* , aktive ras inte BGP-sessionen eftersom de annonserade prefixen inte MATCHAde som-antalet i någon av vägvals registren.

> [!IMPORTANT]
> Om det annonserade offentliga prefixet är i *manuellt validerings* tillstånd måste du öppna ett support ärende med [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) och tillhandahålla bevis på att du äger IP-adresserna som annonseras tillsammans med det tillhör ande autonoma system numret.
> 
> 

## <a name="next-steps"></a>Nästa steg

* Konfigurera ExpressRoute-anslutningen.
  
  * [Skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md)
  * [Konfigurera routning](expressroute-howto-routing-arm.md)
  * [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)
