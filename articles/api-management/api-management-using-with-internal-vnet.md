---
title: Använda Azure API Management med interna virtuella nätverk
titleSuffix: Azure API Management
description: Lär dig hur du konfigurerar Azure API Management i ett internt virtuellt nätverk
services: api-management
documentationcenter: ''
author: vladvino
editor: ''
ms.service: api-management
ms.topic: how-to
ms.date: 04/12/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 35d813b6dfedbd7f76a88713757ce83c2644ff95
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813154"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Använda Azure API Management-tjänsten med ett internt virtuellt nätverk
Med virtuella Azure-nätverk kan Azure API Management hantera API:er som inte är tillgängliga på Internet. Ett antal VPN-tekniker är tillgängliga för att upprätta anslutningen. API Management kan distribueras i två huvudlägen i ett virtuellt nätverk:
* Extern
* Intern

När API Management distribueras i internt virtuellt nätverksläge visas endast alla tjänstslutpunkter (proxygatewayen, utvecklarportalen, direkthantering och Git) i ett virtuellt nätverk som du styr åtkomsten till. Ingen av tjänstslutpunkterna är registrerade på den offentliga DNS-servern.

> [!NOTE]
> Eftersom det inte finns några DNS-poster för tjänstslutpunkterna är dessa slutpunkter inte tillgängliga förrän [DNS](#apim-dns-configuration) har konfigurerats för det virtuella nätverket.

Med API Management i internt läge kan du uppnå följande scenarier:

* Gör API:er som finns i ditt privata datacenter säkert åtkomliga av tredje part utanför det med hjälp av plats-till-plats eller Azure ExpressRoute VPN-anslutningar.
* Aktivera hybridmolnscenarier genom att exponera dina molnbaserade API:er och lokala API:er via en gemensam gateway.
* Hantera dina API:er som finns på flera geografiska platser med hjälp av en enda gatewayslutpunkt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Förutsättningar

Om du vill utföra stegen som beskrivs i den här artikeln måste du ha:

+ **En aktiv Azure-prenumeration.**

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **En Azure API Management instans**. Mer information finns i Skapa [en Azure API Management-instans.](get-started-create-service-instance.md)

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

När en API Management-tjänst distribueras i ett virtuellt nätverk används en lista över portar och måste öppnas. [](./api-management-using-with-vnet.md#required-ports) 

## <a name="creating-an-api-management-in-an-internal-virtual-network"></a><a name="enable-vpn"> </a>Skapa en API Management i ett internt virtuellt nätverk
Tjänsten API Management i ett internt virtuellt nätverk finns bakom en intern basic-SKU för lastbalanserare om tjänsten skapas med klient-API-version 2020-12-01. För tjänster som skapats med klienter med API-version 2021-01-01-preview och som har en offentlig IP-adress från kundens prenumeration finns den bakom en intern standard-SKU för lastbalanserare. Mer information finns i Azure Load Balancer [SKU:er](../load-balancer/skus.md).

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Aktivera en virtuell nätverksanslutning med hjälp av Azure Portal

1. Bläddra till din Azure API Management-instans i [Azure Portal](https://portal.azure.com/).
1. Välj **Virtuellt nätverk**.
1. Konfigurera den **interna** åtkomsttypen. Detaljerade anvisningar finns i Aktivera [VNET-anslutning med hjälp av Azure Portal](api-management-using-with-vnet.md#enable-vnet-connectivity-using-the-azure-portal).

    ![Meny för att konfigurera en Azure-API Management i ett internt virtuellt nätverk][api-management-using-internal-vnet-menu]

4. Välj **Spara**.

När distributionen är utförd  bör du  se privat virtuell IP-adress och offentlig virtuell IP-adress för API Management-tjänsten på översiktsbladet. Den **privata** virtuella IP-adressen är en `gateway` `portal` belastningsutjämnad IP-adress inifrån det API Management delegerade undernätet som slutpunkterna , och `management` kan nås `scm` över. Den **offentliga** virtuella IP-adressen används endast för trafik på kontrollplanet till slutpunkten via port 3443 och kan låsas till  `management` [tjänsttaggen ApiManagement.][ServiceTags]

![API Management instrumentpanel med ett internt virtuellt nätverk konfigurerat][api-management-internal-vnet-dashboard]

> [!NOTE]
> Testkonsolen som är tillgänglig på  Azure Portal fungerar inte för internt VNET-distribuerad tjänst eftersom gateway-URL:en inte är registrerad på den offentliga DNS-servern. Du bör i stället använda testkonsolen som finns på **utvecklarportalen.**

### <a name="deploy-api-management-into-virtual-network"></a><a name="deploy-apim-internal-vnet"> </a>Distribuera API Management till Virtual Network

Du kan också aktivera virtuell nätverksanslutning med hjälp av följande metoder.


### <a name="api-version-2020-12-01"></a>API-version 2020-12-01

* Azure Resource Manager [mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-api-management-create-with-internal-vnet)

     [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-internal-vnet%2Fazuredeploy.json)

* Azure PowerShell cmdlets – [Skapa eller](/powershell/module/az.apimanagement/new-azapimanagement) [uppdatera en](/powershell/module/az.apimanagement/update-azapimanagementregion) API Management instans i ett virtuellt nätverk

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>DNS-konfiguration
När API Management är i externt virtuellt nätverksläge hanteras DNS av Azure. För internt virtuellt nätverksläge måste du hantera din egen DNS. Att konfigurera Azure DNS privat zon och länka den till det virtuella API Management som tjänsten distribueras till är det rekommenderade alternativet. Lär dig hur [du ställer in en privat zon i Azure DNS](../dns/private-dns-getstarted-portal.md).

> [!NOTE]
> API Management-tjänsten lyssnar inte på begäranden som kommer från IP-adresser. Den svarar bara på begäranden till värdnamnet som konfigurerats på dess tjänstslutpunkter. Dessa slutpunkter omfattar gateway, Azure Portal och utvecklarportalen, slutpunkten för direkthantering och Git.

### <a name="access-on-default-host-names"></a>Åtkomst på standardvärdnamn
När du skapar API Management tjänst med namnet "contosointernalvnet" konfigureras följande tjänstslutpunkter som standard:

   * Gateway eller proxy: contosointernalvnet.azure-api.net

   * Utvecklarportalen: contosointernalvnet.portal.azure-api.net

   * Den nya utvecklarportalen: contosointernalvnet.developer.azure-api.net

   * Slutpunkt för direkthantering: contosointernalvnet.management.azure-api.net

   * Git: contosointernalvnet.scm.azure-api.net

Om du vill API Management dessa tjänstslutpunkter kan du skapa en virtuell dator i ett undernät som är anslutet till det virtuella nätverket där API Management distribueras. Förutsatt att den interna virtuella IP-adressen för din tjänst är 10.1.0.5 kan du mappa värdfilen %SystemDrive%\drivers\etc\hosts enligt följande:

   * 10.1.0.5 contosointernalvnet.azure-api.net

   * 10.1.0.5 contosointernalvnet.portal.azure-api.net

   * 10.1.0.5 contosointernalvnet.developer.azure-api.net

   * 10.1.0.5 contosointernalvnet.management.azure-api.net

   * 10.1.0.5 contosointernalvnet.scm.azure-api.net

Du kan sedan komma åt alla tjänstslutpunkter från den virtuella datorn som du skapade.
Om du använder en anpassad DNS-server i ett virtuellt nätverk kan du också skapa A DNS-poster och komma åt dessa slutpunkter var som helst i ditt virtuella nätverk.

### <a name="access-on-custom-domain-names"></a>Åtkomst till anpassade domännamn

1. Om du inte vill komma åt API Management-tjänsten med standardvärdnamnen kan du konfigurera anpassade domännamn för alla tjänstslutpunkter enligt följande bild:

   ![Konfigurera en anpassad domän för API Management][api-management-custom-domain-name]

2. Sedan kan du skapa poster i DNS-servern för att få åtkomst till slutpunkter som endast är tillgängliga från ditt virtuella nätverk.

## <a name="routing"></a><a name="routing"></a> Routning

* En *belastningsutjämnad* privat virtuell IP-adress från undernätsintervallet reserveras och används för att komma åt API Management-tjänstslutpunkter inifrån det virtuella nätverket. Den *här* privata IP-adressen finns på bladet Översikt för tjänsten i Azure Portal. Den här adressen måste registreras med DNS-servrarna som används av det virtuella nätverket.
* En *belastningsutjämnad* offentlig IP-adress (VIP) reserveras också för att ge åtkomst till hanteringstjänstens slutpunkt via port 3443. Den *här* offentliga IP-adressen finns på översiktsbladet för tjänsten i Azure Portal. Den *offentliga* IP-adressen används endast för kontrollplanstrafik till slutpunkten via port 3443 och kan låsas till `management` [tjänsttaggen ApiManagement.][ServiceTags]
* IP-adresser från undernätets IP-intervall (DIP) tilldelas till varje virtuell dator i tjänsten och används för att komma åt resurser i det virtuella nätverket. En offentlig IP-adress (VIP) används för att komma åt resurser utanför det virtuella nätverket. Om IP-begränsningslistor används för att skydda resurser i det virtuella nätverket måste hela intervallet för det undernät där API Management-tjänsten distribueras anges för att bevilja eller begränsa åtkomst från tjänsten.
* De belastningsutjämnade offentliga och privata IP-adresserna finns på bladet Översikt i Azure Portal.
* IP-adresserna som tilldelats för offentlig och privat åtkomst kan ändras om tjänsten tas bort från och sedan läggs tillbaka i det virtuella nätverket. Om detta inträffar kan det vara nödvändigt att uppdatera DNS-registreringar, routningsregler och IP-begränsningslistor i det virtuella nätverket.

## <a name="related-content"></a><a name="related-content"> </a>Relaterat innehåll
Mer information finns i följande artiklar:
* [Vanliga problem med nätverkskonfiguration vid konfiguration av Azure API Management i ett virtuellt nätverk][Common network configuration problems]
* [Vanliga frågor och svar om virtuella nätverk](../virtual-network/virtual-networks-faq.md)
* [Skapa en post i DNS](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
