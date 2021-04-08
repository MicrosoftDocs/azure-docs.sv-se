---
title: Principer för tjänstslutpunkter för virtuellt nätverk i Azure | Microsoft Docs
description: Lär dig hur du filtrerar virtuell nätverkstrafik till Azure-tjänstresurser med hjälp av tjänstslutpunktsprinciper
services: virtual-network
documentationcenter: na
author: RDhillon
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: 633d0b9e454fe2906d98624ac2cdbcc9a8b6c7fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105022270"
---
# <a name="virtual-network-service-endpoint-policies-for-azure-storage"></a>Principer för tjänstslutpunkt för virtuellt nätverk för Azure Storage

Med tjänst slut punkts principerna för Virtual Network (VNet) kan du filtrera utgående virtuell nätverks trafik till Azure Storage konton över tjänstens slut punkt och bara tillåta att data exfiltrering till vissa Azure Storage-konton. Slut punkts principer ger detaljerad åtkomst kontroll för virtuell nätverks trafik till Azure Storage vid anslutning via tjänst slut punkt.

![Skydda utgående trafik för virtuella nätverk till Azure Storage konton](./media/virtual-network-service-endpoint-policies-overview/vnet-service-endpoint-policies-overview.png)

Den här funktionen är allmänt tillgänglig för __Azure Storage__ i __alla globala Azure-regioner__.

## <a name="key-benefits"></a>Viktiga fördelar

Principer för tjänstslutpunkt för virtuellt nätverk ger följande fördelar:

- __Förbättrad säkerhet för Virtual Network trafik till Azure Storage__

  Med [Azure Service-taggar för nätverks säkerhets grupper](./network-security-groups-overview.md) kan du begränsa utgående trafik för virtuella nätverk till vissa Azure Storage regioner. Detta tillåter dock trafik till alla konton inom vald Azure Storage region.
  
  Med slut punkts principer kan du ange de Azure Storage konton som tillåts utgående åtkomst till virtuella nätverk och begränsa åtkomsten till alla andra lagrings konton. Detta ger mycket mer detaljerad säkerhets kontroll för att skydda data exfiltrering från ditt virtuella nätverk.

- __Skalbara principer med hög tillgänglighet för att filtrera Azure-tjänsttrafik__

   Slutpunktsprinciperna ger en horisontellt skalbar lösning med hög tillgänglighet för att filtrera Azure-tjänsttrafik från virtuella nätverk över tjänstslutpunkter. Det krävs inget ytterligare arbete för att upprätthålla centrala nätverksinstallationer för den här trafiken i ditt virtuella nätverk.

## <a name="json-object-for-service-endpoint-policies"></a>JSON-objekt för tjänst slut punkts principer
Låt oss ta en titt på objektet tjänst slut punkts princip.

```json
"serviceEndpointPolicyDefinitions": [
    {
            "description": null,
            "name": "MySEP-Definition",
            "resourceGroup": "MySEPDeployment",
            "service": "Microsoft.Storage",
            "serviceResources": [ 
                    "/subscriptions/subscriptionID/resourceGroups/MySEPDeployment/providers/Microsoft.Storage/storageAccounts/mystgacc"
            ],
            "type": "Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions"
    }
]
```

## <a name="configuration"></a>Konfiguration

-   Du kan konfigurera slut punkts principerna för att begränsa virtuell nätverks trafik till vissa Azure Storage-konton.
-   Slutpunktsprinciper konfigureras i ett undernät i ett virtuellt nätverk. Tjänst slut punkter för Azure Storage ska vara aktiverade på under nätet för att tillämpa principen.
-   Med hjälp av slut punkts principen kan du lägga till vissa Azure Storage konton i listan med hjälp av resourceID-formatet. Du kan begränsa åtkomsten till
    - alla lagrings konton i en prenumeration<br>
      `E.g. /subscriptions/subscriptionId`

    - alla lagrings konton i en resurs grupp<br>
      `E.g. subscriptions/subscriptionId/resourceGroups/resourceGroupName`
     
    - ett enskilt lagrings konto genom att lista motsvarande Azure Resource Manager resourceId. Detta omfattar trafik till blobar, tabeller, köer, filer och Azure Data Lake Storage Gen2. <br>
    `E.g. /subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/storageAccountName`
-   Om inga principer är kopplade till ett undernät med slut punkter kan du som standard komma åt alla lagrings konton i tjänsten. När en princip har konfigurerats i undernätet kan endast de resurser som anges i principen nås från beräkningsinstanser i det undernätet. Åtkomst till alla andra lagrings konton kommer att nekas.
-   När du tillämpar tjänst slut punkts principer på ett undernät, uppgraderas Azure Storage *tjänstens slut punkts omfång* från regional till **Global**. Det innebär att all trafik som Azure Storage skyddas av tjänst slut punkten därefter. Tjänst slut punkts principerna är också tillämpliga globalt, så alla lagrings konton som inte uttryckligen tillåts kommer att nekas åtkomst. 
-   Du kan tillämpa flera principer på ett undernät. När flera principer är kopplade till under nätet kommer den virtuella nätverks trafiken till resurser som anges i någon av dessa principer att tillåtas. Åtkomst till alla andra tjänstresurser som inte specificerats i någon av principerna nekas.

    > [!NOTE]  
    > Principer för tjänst slut punkter är **att tillåta principer**, så utöver de angivna resurserna är alla andra resurser begränsade. Kontrol lera att alla tjänst resurs beroenden för dina program identifieras och visas i principen.

- Det är bara lagringskonton som använder Azure Resource Model kan anges i slutpunktsprincipen. Dina klassiska Azure Storage-konton har inte stöd för tjänst slut punkts principer för Azure.
- Sekundär RA-GRS-åtkomst tillåts automatiskt om det primära kontot anges.
- Lagringskonton kan finnas i samma prenumeration, en annan prenumeration eller i Azure Active Directory-klientorganisationen som det virtuella nätverket.

## <a name="scenarios"></a>Scenarier

- **Peer-kopplade, anslutna eller flera virtuella nätverk**: Om du vill filtrera trafik i peer-kopplade virtuella nätverk ska slutpunktsprinciper tillämpas individuellt på dessa virtuella nätverk.
- **Filtrera Internet trafik med nätverks enheter eller Azure-brandvägg**: filtrera Azure-tjänstetrafik med principer, överanvändnings slut punkter och filtrera rest av Internet-eller Azure-trafik via apparater eller Azure-brandväggen.
- **Filtrera trafik på Azure-tjänster som distribuerats till virtuella nätverk**: för tillfället stöds inte Azure Service Endpoint-principer för några hanterade Azure-tjänster som distribueras i det virtuella nätverket. 
- **Filtrera trafik till Azure-tjänster från lokala**: tjänstslutpunktsprinciper tillämpas endast på trafiken från undernät som är associerade med principerna. För att tillåta åtkomst till specifika Azure-tjänstresurser från en lokal plats ska trafik filtreras med hjälp av virtuella nätverksinstallationer eller brandväggar.

## <a name="logging-and-troubleshooting"></a>Loggning och felsökning
Det finns ingen centraliserad loggning för tjänstslutpunktsprinciper. För tjänst resurs loggar, se [tjänst slut punkter loggning](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting).

### <a name="troubleshooting-scenarios"></a>Felsökningsscenarier
- Åtkomst nekad till lagrings konton som fungerade i förhands granskning (inte i Geo-parad region)
  - Med Azure Storage att uppgradera för att använda globala tjänst taggar, är omfånget för tjänst slut punkten och därmed tjänst slut punkts principerna globalt. Trafik till Azure Storage krypteras via tjänst slut punkter och endast lagrings konton som uttryckligen listas i principen är tillåtna.
  - Ange uttryckligen alla lagrings konton som krävs för att återställa åtkomsten.  
  - Kontakta Azure-supporten.
- Åtkomst nekas för konton som anges i slutpunktsprinciperna
  - Nätverkssäkerhetsgrupper och brandväggsfiltrering blockerar kanske åtkomst
  - Om borttagning/återtillämpning av principen leder till anslutningsförlust:
    - Kontrol lera om Azure-tjänsten är konfigurerad för att tillåta åtkomst från det virtuella nätverket över slut punkter, eller att standard principen för resursen är inställd på *Tillåt alla*.
    - Verifiera att tjänstdiagnostiken visar trafiken över slutpunkter.
    - Kontrollera om flödesloggarna för nätverkssäkerhetsgruppen visar åtkomsten och att lagringsloggarna visar åtkomsten som förväntat över tjänstslutpunkter.
    - Kontakta Azure-supporten.
- Åtkomst nekas för konton som inte anges i tjänstslutpunktsprinciperna
  - Verifiera om Azure Storage har kon figurer ATS för att tillåta åtkomst från det virtuella nätverket över slut punkter, eller om standard principen för resursen är inställd på *Tillåt alla*.
  - Se till att kontona inte är **klassiska lagrings konton** med tjänst slut punkts principer på under nätet.
- En hanterad Azure-tjänst slutade fungera efter att ha tillämpat en tjänst slut punkts princip över under nätet
  - Hanterade tjänster stöds inte med tjänst slut punkts principer för tillfället. *Titta på det här utrymmet för uppdateringar*.
- Åtkomst till hanterade lagrings konton slutade fungera efter att ha tillämpat en tjänst slut punkts princip över under nätet
  - Hanterade lagrings konton stöds inte med tjänst slut punkts principer. Om den är konfigurerad nekar principer åtkomst till alla hanterade lagrings konton som standard. Om ditt program behöver åtkomst till hanterade lagrings konton bör slut punkts principerna inte användas för den här trafiken.

## <a name="provisioning"></a>Etablering

Tjänstslutpunktsprinciper kan konfigureras i virtuella nätverk av en användare med skrivbehörighet för ett virtuellt nätverk. Lär dig mer om [inbyggda roller](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i Azure och att tilldela specifika behörigheter till [anpassade roller](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Virtuella nätverk och Azure Storage konton kan vara i samma eller olika prenumerationer eller Azure Active Directory klienter.

## <a name="limitations"></a>Begränsningar

- Du kan endast distribuera tjänstslutpunktsprinciper på virtuella nätverk som distribuerats med Azure Resource Manager-distributionsmodellen.
- Virtuella nätverk måste finnas i samma region som tjänstslutpunktsprincipen.
- Du kan bara använda tjänstslutpunktsprincipen i ett undernät om tjänstslutpunkterna har konfigurerats för de Azure-tjänster som anges i principen.
- Du kan inte använda tjänstslutpunktsprinciper för trafik från ditt lokala nätverk till Azure-tjänster.
- Azure Managed Services stöder för närvarande inte slut punkts principer. Detta inkluderar hanterade tjänster som distribuerats till delade undernät (t. ex. *Azure Batch, Azure lägger till Azure Application Gateway, Azure-VPN gateway, Azure-brandvägg*) eller till dedikerade undernät (t. ex. *Azure App Service Environment, Azure Redis Cache, Azure API Management, Azure SQL mi, klassiska hanterade tjänster*).

 > [!WARNING]
 > Azure-tjänster som distribueras till ditt virtuella nätverk, till exempel Azure HDInsight, kommer åt andra Azure-tjänster såsom Azure Storage för infrastrukturkrav. Om slutpunktsprincipen begränsas till specifika resurser kan det stoppa åtkomsten till dessa infrastrukturresurser för de Azure-tjänster som distribueras i ditt virtuella nätverk.

- Klassiska lagringskonton stöds inte i slutpunktsprinciper. Principer nekar åtkomst till alla klassiska lagringskonton som standard. Om ditt program behöver åtkomst till Azure Resource Manager och klassiska lagringskonton bör slutpunktsprinciper inte användas för den här trafiken.

## <a name="pricing-and-limits"></a>Priser och begränsningar

Det tillkommer inga extra avgifter för att använda tjänstslutpunktsprinciper. Den aktuella prismodellen för Azure-tjänster (till exempel Azure Storage) tillämpas som den gäller i dag över tjänstslutpunkter.

Följande begränsningar tillämpas på tjänstslutpunktsprinciper: 

 |Resurs | Standardgräns |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpointPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>Nästa steg

- Lär dig [hur du konfigurerar principer för tjänstslutpunkt för virtuellt nätverk](virtual-network-service-endpoint-policies-portal.md)
- Läs mer om [tjänst slut punkter för virtuella nätverk](virtual-network-service-endpoints-overview.md)
