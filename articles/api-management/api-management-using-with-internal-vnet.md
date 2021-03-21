---
title: Använda Azure API Management med interna virtuella nätverk
titleSuffix: Azure API Management
description: Lär dig hur du konfigurerar och konfigurerar Azure API Management i ett internt virtuellt nätverk
services: api-management
documentationcenter: ''
author: vladvino
manager: kjoshi
editor: ''
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/09/2021
ms.author: apimpm
ms.openlocfilehash: 10154f496d76ce6b9eb19d610fdff8d7a4023c2d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565962"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Använda Azure API Management-tjänsten med ett internt virtuellt nätverk
Med Azure Virtual Networks kan Azure API Management hantera API: er som inte är tillgängliga på Internet. Det finns ett antal VPN-tekniker som kan upprätta anslutningen. API Management kan distribueras i två huvud lägen i ett virtuellt nätverk:
* Extern
* Intern

När API Management distribuerar i ett internt virtuellt nätverks läge, visas alla tjänst slut punkter (proxy-gatewayen, Developer-portalen, direkt hantering och git) bara i ett virtuellt nätverk som du styr åtkomsten till. Ingen av tjänstens slut punkter har registrerats på den offentliga DNS-servern.

> [!NOTE]
> Eftersom det inte finns några DNS-poster för tjänst slut punkterna kommer dessa slut punkter inte att vara tillgängliga förrän [DNS har kon figurer ATS](#apim-dns-configuration) för det virtuella nätverket.

Med API Management i internt läge kan du uppnå följande scenarier:

* Skapa API: er som finns i ditt privata data Center på ett säkert sätt från tredje part med hjälp av VPN-anslutningar för plats-till-plats eller Azure ExpressRoute.
* Aktivera scenarier med hybrid moln genom att exponera dina molnbaserade API: er och lokala API: er via en gemensam Gateway.
* Hantera dina API: er som finns på flera geografiska platser genom att använda en enda Gateway-slutpunkt.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Förutsättningar

För att utföra stegen som beskrivs i den här artikeln måste du ha:

+ **En aktiv Azure-prenumeration**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **En Azure API Management-instans**. Mer information finns i [skapa en Azure API Management-instans](get-started-create-service-instance.md).
+ När en API Management-tjänst distribueras i ett virtuellt nätverk används en [lista över portar](./api-management-using-with-vnet.md#required-ports) som måste öppnas. 

## <a name="creating-an-api-management-in-an-internal-virtual-network"></a><a name="enable-vpn"> </a>Skapa en API Management i ett internt virtuellt nätverk
Tjänsten API Management i ett internt virtuellt nätverk finns bakom en [intern belastningsutjämnare (klassisk)](/previous-versions/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud). Detta är det enda tillgängliga alternativet och kan inte ändras.

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Aktivera en virtuell nätverks anslutning med hjälp av Azure Portal

1. Bläddra till Azure API Management-instansen i [Azure Portal](https://portal.azure.com/).
2. Välj **virtuellt nätverk**.
3. Konfigurera API Management-instansen som ska distribueras i det virtuella nätverket.

    ![Meny för att konfigurera ett Azure-API Management i ett internt virtuellt nätverk][api-management-using-internal-vnet-menu]

4. Välj **Spara**.

När distributionen har slutförts bör du se **privat** virtuell IP-adress och **offentlig** virtuell IP-adress för din API Management-tjänst på översikts bladet. Den **privata** virtuella IP-adressen är en belastningsutjämnad IP-adress i det API Management delegerade under nätet som `gateway` , `portal` `management` och `scm` slut punkterna kan nås. Den **offentliga** virtuella IP-adressen används **endast** för kontroll Plans trafik till `management` slut punkt via port 3443 och kan låsas ned till [API Management][ServiceTags] -servicetag.

![API Management instrument panel med ett internt virtuellt nätverk konfigurerat][api-management-internal-vnet-dashboard]

> [!NOTE]
> Test konsolen som är tillgänglig på Azure-portalen fungerar inte för **interna** distribuerade VNet-tjänster eftersom Gateway-URL: en inte är registrerad på den offentliga DNS-adressen. I stället bör du använda test konsolen som finns på **Developer-portalen**.

### <a name="deploy-api-management-into-virtual-network"></a><a name="deploy-apim-internal-vnet"> </a>Distribuera API Management till Virtual Network

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-internal-vnet%2Fazuredeploy.json)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

Du kan också aktivera virtuell nätverks anslutning med hjälp av PowerShell-cmdletar.

* Skapa en API Management tjänst i ett virtuellt nätverk: Använd cmdleten [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) för att skapa en Azure API Management-tjänst i ett virtuellt nätverk och konfigurera den så att den använder den interna virtuella nätverks typen.

* Uppdatera en befintlig distribution av en API Management tjänst i ett virtuellt nätverk: Använd cmdlet [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) för att flytta en befintlig API Management tjänst i ett virtuellt nätverk och konfigurera den så att den använder den interna virtuella nätverks typen.

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>DNS-konfiguration
När API Management är i ett externt virtuellt nätverks läge, hanteras DNS av Azure. För internt virtuellt nätverks läge måste du hantera din egen DNS. Vi rekommenderar att du konfigurerar en Azure DNS privat zon och länkar den till den virtuella nätverks API Managements tjänsten.  Klicka [här](../dns/private-dns-getstarted-portal.md) om du vill lära dig att konfigurera en privat zon i Azure DNS.

> [!NOTE]
> API Management tjänsten lyssnar inte på begär Anden som kommer från IP-adresser. Den svarar bara på begär anden till värd namnet som kon figurer ATS i tjänstens slut punkter. Dessa slut punkter inkluderar Gateway, Azure Portal och Developer-portalen, direkt hanterings slut punkt och git.

### <a name="access-on-default-host-names"></a>Åtkomst på standard värd namn
När du skapar en API Management-tjänst med namnet "contosointernalvnet", konfigureras till exempel följande tjänst slut punkter som standard:

   * Gateway eller proxy: contosointernalvnet.azure-api.net

   * Developer-portalen: contosointernalvnet.portal.azure-api.net

   * Den nya Developer-portalen: contosointernalvnet.developer.azure-api.net

   * Slut punkt för direkt hantering: contosointernalvnet.management.azure-api.net

   * Git: contosointernalvnet.scm.azure-api.net

För att få åtkomst till dessa API Management tjänst slut punkter kan du skapa en virtuell dator i ett undernät som är anslutet till det virtuella nätverk där API Management har distribuerats. Förutsatt att den interna virtuella IP-adressen för din tjänst är 10.1.0.5 kan du mappa hosts-filen%SystemDrive%\drivers\etc\hosts, enligt följande:

   * 10.1.0.5 contosointernalvnet.azure-api.net

   * 10.1.0.5 contosointernalvnet.portal.azure-api.net

   * 10.1.0.5 contosointernalvnet.developer.azure-api.net

   * 10.1.0.5 contosointernalvnet.management.azure-api.net

   * 10.1.0.5 contosointernalvnet.scm.azure-api.net

Du kan sedan komma åt alla tjänst slut punkter från den virtuella dator som du har skapat.
Om du använder en anpassad DNS-server i ett virtuellt nätverk kan du också skapa en DNS-post och komma åt dessa slut punkter från var som helst i det virtuella nätverket.

### <a name="access-on-custom-domain-names"></a>Åtkomst för anpassade domän namn

1. Om du inte vill komma åt tjänsten API Management med standard värd namnen kan du ange anpassade domän namn för alla dina tjänst slut punkter enligt följande bild:

   ![Konfigurera en anpassad domän för API Management][api-management-custom-domain-name]

2. Sedan kan du skapa poster i din DNS-server för att få åtkomst till de slut punkter som endast är tillgängliga i det virtuella nätverket.

## <a name="routing"></a><a name="routing"></a> Routning

* En belastningsutjämnad *privat* virtuell IP-adress från under nätet är reserverad och används för att få åtkomst till API Management tjänstens slut punkter inifrån det virtuella nätverket. Du hittar den här *privata* IP-adressen på översikts bladet för tjänsten i Azure Portal. Adressen måste vara registrerad hos de DNS-servrar som används av det virtuella nätverket.
* En belastningsutjämnad *offentlig* IP-adress (VIP) kommer också att vara reserverad för att ge åtkomst till hanterings tjänstens slut punkt via port 3443. Du hittar den här *offentliga* IP-adressen på översikts bladet för tjänsten i Azure Portal. Den *offentliga* IP-adressen används endast för kontroll Plans trafik till `management` slut punkten via port 3443 och kan låsas ned till [API Management][ServiceTags] -servicetag.
* IP-adresser från under nätets IP-intervall (DIP) tilldelas varje virtuell dator i tjänsten och kommer att användas för att få åtkomst till resurser i det virtuella nätverket. En offentlig IP-adress (VIP) kommer att användas för att få åtkomst till resurser utanför det virtuella nätverket. Om listor med IP-begränsningar används för att skydda resurser i det virtuella nätverket måste hela intervallet för under nätet där API Management-tjänsten distribueras anges för att bevilja eller begränsa åtkomst från tjänsten.
* Du hittar de belastningsutjämnade offentliga och privata IP-adresserna i översikts bladet i Azure Portal.
* De IP-adresser som tilldelas offentlig och privat åtkomst kan ändras om tjänsten tas bort från och sedan läggs tillbaka i det virtuella nätverket. Om detta inträffar kan det vara nödvändigt att uppdatera DNS-registreringar, routningsregler och listor över IP-begränsningar i det virtuella nätverket.

## <a name="related-content"></a><a name="related-content"> </a>Relaterat innehåll
Mer information finns i följande artiklar:
* [Vanliga problem med nätverks konfiguration när du konfigurerar Azure-API Management i ett virtuellt nätverk][Common network configuration problems]
* [Vanliga frågor och svar om virtuellt nätverk](../virtual-network/virtual-networks-faq.md)
* [Skapa en post i DNS](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
