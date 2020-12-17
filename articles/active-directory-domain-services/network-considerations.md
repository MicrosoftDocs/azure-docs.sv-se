---
title: Nätverks planering och anslutningar för Azure AD Domain Services | Microsoft Docs
description: Läs om några av de utformningar och resurser för utformning av virtuella nätverk som används för anslutning när du kör Azure Active Directory Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: justinha
ms.openlocfilehash: d1a3ab5face03754bf84f442ac0fa73768b0fc80
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97615826"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-active-directory-domain-services"></a>Design överväganden för virtuellt nätverk och konfigurations alternativ för Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) tillhandahåller autentiserings-och hanterings tjänster för andra program och arbets belastningar. Nätverks anslutningen är en viktig komponent. Utan korrekt konfigurerade virtuella nätverks resurser kan program och arbets belastningar inte kommunicera med och använda de funktioner som tillhandahålls av Azure AD DS. Planera dina krav för virtuella nätverk för att kontrol lera att Azure AD DS kan hantera dina program och arbets belastningar efter behov.

Den här artikeln beskriver design överväganden och krav för ett virtuellt Azure-nätverk som stöd för Azure AD DS.

## <a name="azure-virtual-network-design"></a>Design av virtuellt nätverk i Azure

För att tillhandahålla nätverks anslutning och tillåta program och tjänster att autentisera mot en Azure AD DS-hanterad domän, använder du ett virtuellt Azure-nätverk och undernät. Vi rekommenderar att den hanterade domänen distribueras till ett eget virtuellt nätverk.

Du kan inkludera ett separat program-undernät i samma virtuella nätverk som värd för din hantering av virtuella datorer eller arbets belastningar för ljus program. Ett separat virtuellt nätverk för större eller komplexa program arbets belastningar som är peer-kopplat till det virtuella Azure AD DS-nätverket är vanligt vis den lämpligaste designen.

Andra design alternativ är giltiga, förutsatt att du uppfyller de krav som beskrivs i följande avsnitt för det virtuella nätverket och under nätet.

När du utformar det virtuella nätverket för Azure AD DS gäller följande aspekter:

* Azure AD DS måste distribueras till samma Azure-region som det virtuella nätverket.
    * För närvarande kan du bara distribuera en hanterad domän per Azure AD-klient. Den hanterade domänen distribueras till en enda region. Se till att du skapar eller väljer ett virtuellt nätverk i en [region som stöder Azure AD DS](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all).
* Överväg att använda andra Azure-regioner och de virtuella nätverk som är värdar för dina program arbets belastningar.
    * För att minimera fördröjningen bör du hålla dina kärn program nära eller i samma region som det virtuella nätverkets undernät för din hanterade domän. Du kan använda peering för virtuella nätverk eller anslutningar för virtuella privata nätverk (VPN) mellan virtuella Azure-nätverk. De här anslutnings alternativen beskrivs i följande avsnitt.
* Det virtuella nätverket kan inte förlita sig på andra DNS-tjänster än de tjänster som tillhandahålls av den hanterade domänen.
    * Azure AD DS tillhandahåller sin egen DNS-tjänst. Det virtuella nätverket måste konfigureras för att använda dessa DNS-tjänst adresser. Namn matchning för ytterligare namn områden kan utföras med hjälp av villkorliga vidarebefordrare.
    * Du kan inte använda anpassade DNS-serverinställningar för att dirigera frågor från andra DNS-servrar, inklusive virtuella datorer. Resurser i det virtuella nätverket måste använda den DNS-tjänst som tillhandahålls av den hanterade domänen.

> [!IMPORTANT]
> Du kan inte flytta Azure AD DS till ett annat virtuellt nätverk när du har aktiverat tjänsten.

En hanterad domän ansluter till ett undernät i ett virtuellt Azure-nätverk. Utforma det här under nätet för Azure AD DS med följande överväganden:

* En hanterad domän måste distribueras i sitt eget undernät. Använd inte ett befintligt undernät eller ett Gateway-undernät.
* En nätverks säkerhets grupp skapas under distributionen av en hanterad domän. Den här nätverks säkerhets gruppen innehåller de regler som krävs för korrekt tjänst kommunikation.
    * Skapa eller Använd inte en befintlig nätverks säkerhets grupp med dina egna anpassade regler.
* En hanterad domän kräver 3-5 IP-adresser. Kontrol lera att IP-adressintervallet för ditt undernät kan ange det här antalet adresser.
    * Att begränsa de tillgängliga IP-adresserna kan förhindra att den hanterade domänen underhåller två domänkontrollanter.

Följande exempel diagram beskriver en giltig design där den hanterade domänen har sitt eget undernät, det finns ett Gateway-undernät för extern anslutning och program arbets belastningar finns i ett anslutet undernät i det virtuella nätverket:

![Rekommenderad under näts design](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>Anslutningar till det virtuella Azure AD DS-nätverket

Som anges i föregående avsnitt kan du bara skapa en hanterad domän i ett enda virtuellt nätverk i Azure, och endast en hanterad domän kan skapas per Azure AD-klient. Baserat på den här arkitekturen kan du behöva ansluta ett eller flera virtuella nätverk som är värdar för dina program arbets belastningar till din hanterade domäns virtuella nätverk.

Du kan ansluta program arbets belastningar som finns i andra virtuella Azure-nätverk med någon av följande metoder:

* Virtuell nätverkspeering
* Virtuellt privat nätverk (VPN)

### <a name="virtual-network-peering"></a>Virtuell nätverkspeering

Peering för virtuella nätverk är en mekanism som ansluter två virtuella nätverk i samma region via Azures stamnät nätverk. Global peering för virtuella nätverk kan ansluta till virtuella nätverk i Azure-regioner. När de två virtuella nätverken har peer-kopplats kan resurser, till exempel virtuella datorer, kommunicera med varandra direkt med hjälp av privata IP-adresser. Med hjälp av peering för virtuella nätverk kan du distribuera en hanterad domän med dina program arbets belastningar som distribueras i andra virtuella nätverk.

![Anslutning till virtuellt nätverk med peering](./media/active-directory-domain-services-design-guide/vnet-peering.png)

Mer information finns i [Översikt över Azure Virtual Network-peering](../virtual-network/virtual-network-peering-overview.md).

### <a name="virtual-private-networking-vpn"></a>Virtuellt privat nätverk (VPN)

Du kan ansluta ett virtuellt nätverk till ett annat virtuellt nätverk (VNet-till-VNet) på samma sätt som du kan konfigurera ett virtuellt nätverk till en lokal plats. Båda anslutningarna använder en VPN-gateway för att skapa en säker tunnel med IPsec/IKE. Med den här anslutnings modellen kan du distribuera den hanterade domänen till ett virtuellt Azure-nätverk och sedan ansluta lokala platser eller andra moln.

![Anslutning till virtuellt nätverk med hjälp av en VPN Gateway](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

Mer information om hur du använder virtuella privata nätverk finns i [Konfigurera en VNet-till-VNet VPN gateway-anslutning med hjälp av Azure Portal](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

## <a name="name-resolution-when-connecting-virtual-networks"></a>Namn matchning vid anslutning av virtuella nätverk

Virtuella nätverk som är anslutna till den hanterade domänens virtuella nätverk har vanligt vis egna DNS-inställningar. När du ansluter virtuella nätverk konfigureras inte automatiskt namn matchning för det virtuella nätverket för att lösa de tjänster som tillhandahålls av den hanterade domänen. Namn matchningen på de virtuella nätverks anslutningarna måste konfigureras för att aktivera program arbets belastningar för att hitta den hanterade domänen.

Du kan aktivera namn matchning med villkorliga DNS-vidarebefordrare på den DNS-server som stöder anslutning till virtuella nätverk, eller genom att använda samma DNS IP-adresser från den hanterade domänens virtuella nätverk.

## <a name="network-resources-used-by-azure-ad-ds"></a>Nätverks resurser som används av Azure AD DS

En hanterad domän skapar vissa nätverks resurser under distributionen. De här resurserna behövs för lyckad åtgärd och hantering av den hanterade domänen och ska inte konfigureras manuellt.

| Azure-resurs                          | Beskrivning |
|:----------------------------------------|:---|
| Nätverks gränssnitts kort                  | Azure AD DS är värd för den hanterade domänen på två domänkontrollanter (DCs) som körs på Windows Server som virtuella Azure-datorer. Varje virtuell dator har ett virtuellt nätverks gränssnitt som ansluter till det virtuella nätverkets undernät. |
| Offentlig IP-adress för dynamisk standard      | Azure AD DS kommunicerar med tjänsten synkronisering och hantering med hjälp av en offentlig IP-adress för standard-SKU. Mer information om offentliga IP-adresser finns i [IP-diagramtyper och autentiseringsmetoder i Azure](../virtual-network/public-ip-addresses.md). |
| Azure standard Load Balancer            | Azure AD DS använder en standard-SKU-belastningsutjämnare för Network Address Translation (NAT) och belastnings utjämning (vid användning med säker LDAP). Mer information om Azure load Balances finns i [Vad är Azure Load Balancer?](../load-balancer/load-balancer-overview.md) |
| Regler för NAT (Network Address Translation) | Azure AD DS skapar och använder tre NAT-regler på belastningsutjämnaren – en regel för säker HTTP-trafik och två regler för säker PowerShell-fjärrkommunikation. |
| Regler för lastbalanserare                     | När en hanterad domän har kon figurer ATS för säker LDAP på TCP-port 636, skapas tre regler och används på en belastningsutjämnare för att distribuera trafiken. |

> [!WARNING]
> Ta inte bort eller ändra någon nätverks resurs som skapats av Azure AD DS, till exempel manuell konfigurering av belastningsutjämnare eller regler. Om du tar bort eller ändrar någon nätverks resurs kan det uppstå ett avbrott i Azure AD DS-tjänsten.

## <a name="network-security-groups-and-required-ports"></a>Nätverks säkerhets grupper och nödvändiga portar

En [nätverks säkerhets grupp (NSG)](../virtual-network/network-security-groups-overview.md) innehåller en lista över regler som tillåter eller nekar nätverks trafik till trafik i ett virtuellt Azure-nätverk. En nätverks säkerhets grupp skapas när du distribuerar en hanterad domän som innehåller en uppsättning regler som gör att tjänsten tillhandahåller autentiserings-och hanterings funktioner. Den här standard nätverks säkerhets gruppen är associerad med det virtuella nätverkets undernät som den hanterade domänen har distribuerats till.

Följande regler för nätverks säkerhets grupper krävs för att den hanterade domänen ska kunna tillhandahålla autentiserings-och hanterings tjänster. Redigera inte eller ta bort dessa regler för nätverks säkerhets grupper för det virtuella nätverkets undernät som din hanterade domän distribueras till.

| Portnummer | Protokoll | Källa                             | Mål | Åtgärd | Obligatorisk | Syfte |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 5986        | TCP      | AzureActiveDirectoryDomainServices | Valfri         | Tillåt  | Yes      | Hantering av din domän. |
| 3389        | TCP      | CorpNetSaw                         | Valfri         | Tillåt  | Valfritt      | Fel sökning för support. |

En Azure standard Load Balancer skapas som kräver att dessa regler placeras. Den här nätverks säkerhets gruppen säkrar Azure AD DS och krävs för att den hanterade domänen ska fungera korrekt. Ta inte bort den här nätverks säkerhets gruppen. Belastningsutjämnaren fungerar inte korrekt utan den.

Om det behövs kan du [skapa den nätverks säkerhets grupp och de regler som krävs med hjälp av Azure PowerShell](powershell-create-instance.md#create-a-network-security-group).

> [!WARNING]
> Redigera inte dessa nätverks resurser och konfigurationer manuellt. När du associerar en felkonfigurerad nätverks säkerhets grupp eller en användardefinierad routningstabell med under nätet där den hanterade domänen distribueras, kan du störa Microsofts möjlighet att underhålla och hantera domänen. Synkronisering mellan din Azure AD-klient och din hanterade domän avbryts också.
>
> Om du använder säker LDAP kan du lägga till den TCP-port 636-regel som krävs för att tillåta extern trafik om det behövs. Om du lägger till den här regeln placeras inte reglerna för nätverks säkerhets gruppen i ett tillstånd som inte stöds. Mer information finns i [låsa säker LDAP-åtkomst via Internet](tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet)
>
> Standard reglerna för *AllowVnetInBound*, *AllowAzureLoadBalancerInBound*, *DenyAllInBound*, *AllowVnetOutBound*, *AllowInternetOutBound* och *DenyAllOutBound* finns också för nätverks säkerhets gruppen. Redigera inte eller ta bort dessa standard regler.
>
> Azure service avtal gäller inte för distributioner där en felaktigt konfigurerad nätverks säkerhets grupp och/eller användardefinierade väg tabeller har tillämpats som blockerar Azure AD DS från att uppdatera och hantera din domän.

### <a name="port-5986---management-using-powershell-remoting"></a>Port 5986 – hantering med PowerShell-fjärrkommunikation

* Används för att utföra hanterings uppgifter med PowerShell-fjärrkommunikation i din hanterade domän.
* Utan åtkomst till den här porten kan din hanterade domän inte uppdateras, konfigureras, säkerhets kopie ras eller övervakas.
* För hanterade domäner som använder ett Resource Manager-baserat virtuellt nätverk kan du begränsa inkommande åtkomst till den här porten till *AzureActiveDirectoryDomainServices* -tjänst tag gen.
    * För äldre hanterade domäner med hjälp av ett klassiskt virtuellt nätverk kan du begränsa inkommande åtkomst till den här porten till följande käll-IP-adresser: *52.180.183.8*, *23.101.0.70*, *52.225.184.198*, *52.179.126.223*, *13.74.249.156*, *52.187.117.83*, *52.161.13.95*, *104.40.156.18* och *104.40.87.209*.

    > [!NOTE]
    > I 2017 blev Azure AD Domain Services tillgänglig som värd i ett Azure Resource Manager nätverk. Sedan dess har vi kunnat bygga en säkrare tjänst med Azure Resource Manager moderna funktioner. Eftersom Azure Resource Manager distributioner fullständigt ersätter klassiska distributioner, kommer Azure AD DS klassiska virtuella nätverks distributioner att dras tillbaka den 1 mars 2023.
    >
    > Mer information finns i meddelande om [officiellt utfasning](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

### <a name="port-3389---management-using-remote-desktop"></a>Port 3389 – hantering med hjälp av fjärr skrivbord

* Används för fjärr skrivbords anslutningar till domänkontrollanter i den hanterade domänen.
* Standard regeln för nätverks säkerhets gruppen använder *CorpNetSaw* -tjänst tag gen för att ytterligare begränsa trafiken.
    * Den här Service tag-koden tillåter endast säker åtkomst till arbets stationer på Microsofts företags nätverk för att använda fjärr skrivbord till den hanterade domänen.
    * Åtkomst tillåts endast med affärs justering, till exempel för hanterings-eller fel söknings scenarier.
* Den här regeln kan anges som *neka* och är bara inställd på *Tillåt* vid behov. De flesta hanterings-och övervaknings aktiviteter utförs med PowerShell-fjärrkommunikation. RDP används endast i sällsynta fall som Microsoft måste fjärrans luta till din hanterade domän för avancerad fel sökning.

> [!NOTE]
> Du kan inte manuellt välja *CorpNetSaw* service tag från portalen om du försöker redigera den här regeln för nätverks säkerhets gruppen. Du måste använda Azure PowerShell eller Azure CLI för att manuellt konfigurera en regel som använder *CorpNetSaw* service tag.
>
> Du kan till exempel använda följande skript för att skapa en regel som tillåter RDP: 
>
> `Get-AzureRmNetworkSecurityGroup -Name "nsg-name" -ResourceGroupName "resource-group-name" | Add-AzureRmNetworkSecurityRuleConfig -Name "new-rule-name" -Access "Allow" -Protocol "TCP" -Direction "Inbound" -Priority "priority-number" -SourceAddressPrefix "CorpNetSaw" -SourcePortRange "" -DestinationPortRange "3389" -DestinationAddressPrefix "" | Set-AzureRmNetworkSecurityGroup`

## <a name="user-defined-routes"></a>Användardefinierade vägar

Användardefinierade vägar skapas inte som standard och behövs inte för att Azure AD DS ska fungera korrekt. Undvik att göra ändringar i den *0.0.0.0* vägen om du måste använda routningstabeller. Ändringar i den här vägen avbryter Azure AD DS och placerar den hanterade domänen i ett tillstånd som inte stöds.

Du måste också dirigera inkommande trafik från IP-adresserna som ingår i respektive Azure Service-taggar till den hanterade domänens undernät. Mer information om service märken och deras associerade IP-adresser från finns i avsnittet om [Azure IP-intervall och service märken – offentligt moln](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

> [!CAUTION]
> Dessa IP-intervall för Azure-datacenter kan ändras utan föregående meddelande. Se till att du har de senaste IP-adresserna för att kontrol lera att du har.

## <a name="next-steps"></a>Nästa steg

Mer information om vissa nätverks resurser och anslutnings alternativ som används av Azure AD DS finns i följande artiklar:

* [Peering för Azure Virtual Network](../virtual-network/virtual-network-peering-overview.md)
* [Azure VPN-gatewayer](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Azure-nätverkssäkerhetsgrupper](../virtual-network/network-security-groups-overview.md)