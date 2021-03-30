---
title: Tvingad tunnel trafik i Azure-brandväggen
description: Du kan konfigurera Tvingad tunnel trafik för att dirigera Internet-baserad trafik till ytterligare en brand vägg eller virtuell nätverks installation för vidare bearbetning.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 08/19/2020
ms.author: victorh
ms.openlocfilehash: da2b206bf24cb33180305e32e270b989eb64dfa3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88612611"
---
# <a name="azure-firewall-forced-tunneling"></a>Tvingad tunnel trafik i Azure-brandväggen

När du konfigurerar en ny Azure Firewall-instans kan du dirigera all trafik till internet via ett särskilt nästa hopp i stället för att den går direkt till internet. Du kan till exempel ha en lokal gräns brand vägg eller en annan virtuell nätverks installation (NVA) för att bearbeta nätverks trafik innan den skickas till Internet. Du kan dock inte konfigurera en befintlig brand vägg för Tvingad tunnel trafik.

Som standard är Tvingad tunnel trafik inte tillåten i Azure-brandväggen för att se till att alla dess utgående Azure-beroenden är uppfyllda. UDR-konfigurationer (User Defined Route) på *AzureFirewallSubnet* som har en standard väg som inte kommer direkt till Internet är inaktiverade.

## <a name="forced-tunneling-configuration"></a>Konfiguration av Tvingad tunnel trafik

För att stödja Tvingad tunnel trafik separeras Service Management-trafiken från kund trafiken. Ytterligare ett dedikerat undernät med namnet *AzureFirewallManagementSubnet* (minsta under näts storlek/26) krävs med en egen ASSOCIERAD offentlig IP-adress. Den enda vägen som tillåts i det här under nätet är en standard väg till Internet och en vidarebefordring av BGP-vägar måste inaktive ras.

Om du har en standard väg som annonseras via BGP för att tvinga trafik till lokalt, måste du skapa *AzureFirewallSubnet* och *AzureFirewallManagementSubnet* innan du distribuerar brand väggen och har en UDR med en standard väg till Internet, och **sprida Gateway-vägar** inaktiverade.

I den här konfigurationen kan *AzureFirewallSubnet* nu inkludera vägar till valfri lokal brand vägg eller NVA för att bearbeta trafik innan den skickas till Internet. Du kan också publicera dessa vägar via BGP till *AzureFirewallSubnet* om **spridning av Gateway-vägar** är aktiverat på det här under nätet.

Du kan till exempel skapa en standard väg på *AzureFirewallSubnet* med din VPN-gateway som nästa hopp för att komma till din lokala enhet. Eller så kan du aktivera **spridning av Gateway-vägar** för att hämta lämpliga vägar till det lokala nätverket.

:::image type="content" source="media/forced-tunneling/route-propagation.png" alt-text="Väg spridning för virtuell nätverks-Gateway":::

Om du aktiverar Tvingad tunnel trafik är Internet-baserad trafik SNATed till en av brand väggens privata IP-adresser i AzureFirewallSubnet, vilket döljer källan från din lokala brand vägg.

Om din organisation använder ett offentligt IP-adressintervall för privata nätverk SNATs trafiken till någon av brand väggens privata IP-adresser i AzureFirewallSubnet med Azure-brandväggen. Du kan dock konfigurera Azure-brandväggen så att den **inte** bevarar ditt offentliga IP-adressintervall. Mer information finns i [Azure FIREWALL SNAT privata IP-adressintervall](snat-private-range.md).

När du har konfigurerat Azure Firewall som stöd för Tvingad tunnel trafik kan du inte återställa konfigurationen. Om du tar bort alla andra IP-konfigurationer i brand väggen tas även hanterings-IP-konfigurationen bort och brand väggen frigörs. Det går inte att ta bort den offentliga IP-adress som tilldelats hanterings-IP-konfigurationen, men du kan tilldela en annan offentlig IP-adress.

## <a name="next-steps"></a>Nästa steg

- [Självstudie: Distribuera och konfigurera Azure-brandväggen i ett hybrid nätverk med hjälp av Azure Portal](tutorial-hybrid-portal.md)
