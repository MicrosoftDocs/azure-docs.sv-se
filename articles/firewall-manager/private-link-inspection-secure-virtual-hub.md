---
title: Skydda trafik som är avsedd för privata slut punkter i Azure Virtual WAN
description: Lär dig hur du använder nätverks regler och program regler för att skydda trafik som är avsedd för privata slut punkter i Azure Virtual WAN
services: firewall-manager
author: jocortems
ms.service: firewall-manager
ms.topic: how-to
ms.date: 04/02/2021
ms.author: jocorte
ms.openlocfilehash: 7322bab635d398fc7a5335546ba6fef327ff24b2
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259361"
---
# <a name="secure-traffic-destined-to-private-endpoints-in-azure-virtual-wan"></a>Skydda trafik som är avsedd för privata slut punkter i Azure Virtual WAN

> [!NOTE]
> Den här artikeln gäller endast för säkra virtuella nav. Om du vill inspektera trafik som är avsedd för privata slut punkter med Azure-brandväggen i ett virtuellt nav nätverk, se [Använd Azure-brandvägg för att kontrol lera trafik som är avsedd för en privat slut punkt](../private-link/inspect-traffic-with-azure-firewall.md).

Den [privata Azure-slutpunkten](../private-link/private-endpoint-overview.md) är det grundläggande Bygg blocket för [Azures privata länk](../private-link/private-link-overview.md). Privata slut punkter gör det möjligt för Azure-resurser som distribuerats i ett virtuellt nätverk att kommunicera privat med privata länk resurser.

Privata slut punkter ger resurser åtkomst till den privata länk tjänsten som distribueras i ett virtuellt nätverk. Åtkomst till den privata slut punkten via virtuell nätverks-peering och lokala nätverks anslutningar utökar anslutningen.

Du kan behöva filtrera trafik från klienter antingen lokalt eller i Azure till tjänster som exponeras via privata slut punkter i ett virtuellt WAN-kopplat virtuellt nätverk. Den här artikeln vägleder dig genom den här uppgiften med hjälp av en [säker virtuell hubb](../firewall-manager/secured-virtual-hub.md) med [Azure Firewall](../firewall/overview.md) som säkerhetsprovider.

Azure-brandväggen filtrerar trafik med någon av följande metoder:

* [FQDN i nätverks regler](../firewall/fqdn-filtering-network-rules.md) för TCP-och UDP-protokoll
* [FQDN i program regler](../firewall/features.md#application-fqdn-filtering-rules) för http, https och MSSQL.
* Käll-och mål-IP-adresser, port och protokoll med hjälp av [nätverks regler](../firewall/features.md#network-traffic-filtering-rules)

Använd program regler över nätverks regler för att inspektera trafik som är avsedd för privata slut punkter.
En säker virtuell hubb hanteras av Microsoft och kan inte länkas till en [privat DNS zon](../dns/private-dns-privatednszone.md). Detta krävs för att matcha en [privat länk resurs](../private-link/private-endpoint-overview.md#private-link-resource) -FQDN till dess motsvarande privata slut punkts-IP-adress.

SQL-FQDN-filtrering stöds endast i [proxy-läge](../azure-sql/database/connectivity-architecture.md#connection-policy) (port 1433). *Proxyläge* kan resultera i mer latens jämfört med *omdirigering*. Om du vill fortsätta använda omdirigeringsläge, vilket är standardinställningen för klienter som ansluter i Azure, kan du filtrera åtkomst med FQDN i brand Väggs nätverks regler.

## <a name="filter-traffic-using-fqdn-in-network-and-application-rules"></a>Filtrera trafik med FQDN i regler för nätverk och program

Följande steg gör det möjligt för Azure-brandvägg att filtrera trafik med FQDN i regler för nätverk och program:

1. Distribuera en virtuell [DNS-vidarebefordrare](../private-link/private-endpoint-dns.md#virtual-network-and-on-premises-workloads-using-a-dns-forwarder) i ett virtuellt nätverk som är anslutet till den skyddade virtuella hubben och länkas till privat DNS zoner som är värdar för de olika post typerna för de privata slut punkterna.

2. Konfigurera [anpassade DNS-inställningar](../firewall/dns-settings.md#configure-custom-dns-servers---azure-portal) så att de pekar på IP-adressen för den virtuella datorns IP-adress och aktiverar DNS-proxy i brand Väggs principen som är kopplad till den Azure-brandvägg som distribueras i den säkra virtuella hubben

3. Konfigurera [anpassade DNS-servrar](../virtual-network/manage-virtual-network.md#change-dns-servers) för de virtuella nätverk som är anslutna till den skyddade virtuella hubben så att den pekar på den privata IP-adress som är kopplad till den Azure-brandvägg som distribueras i den säkra virtuella hubben

4. Konfigurera lokala DNS-servrar för att vidarebefordra DNS-frågor för privata DNS-zoner till den privata IP-adress som är kopplad till den Azure-brandvägg som distribueras i den säkra virtuella hubben.

5. Konfigurera en [program regel](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule) eller [nätverks regel](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule) vid behov i brand Väggs principen som är kopplad till den Azure-brandvägg som distribueras i den säkra virtuella hubben med *mål typen* FQDN och den privata länk resursens offentliga domän namn som *mål*.

6. Gå till *skyddade virtuella hubbar* i brand Väggs principen som är kopplad till den Azure-brandvägg som distribueras i den skyddade virtuella hubben och välj den skyddade virtuella hubben där trafik filtrering som är avsedd för privata slut punkter ska konfigureras.

7. Navigera till **säkerhets konfiguration** och välj **Skicka via Azure-brandvägg** under **privat trafik**.

8. Välj prefix för **privata trafik** för att redigera de CIDR-prefix som ska kontrol leras via Azure Firewall i skyddad virtuell hubb och Lägg till ett/32-prefix för varje privat slut punkt enligt följande:

   > [!IMPORTANT]
   > Om dessa/32-prefix inte har kon figurer ATS hoppar trafik till privata slut punkter Azure-brandvägg.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="Säkerhets konfiguration för brand Väggs hanteraren" border="true":::

De här stegen fungerar bara när klienterna och privata slut punkter distribueras i olika virtuella nätverk som är anslutna till samma skyddade virtuella hubb och för lokala klienter. Om klienterna och de privata slut punkterna distribueras i samma virtuella nätverk måste en UDR med/32-vägar för de privata slut punkterna skapas. Konfigurera dessa vägar med **nästa hopp-typ**  inställd på **Virtual** -installation och **nästa hopp-adress** som är inställd på den privata IP-adressen för den Azure-brandvägg som distribueras i den säkra virtuella hubben. **Spridning av Gateway-vägar** måste anges till **Ja**.

Följande diagram illustrerar DNS-och data trafik flöden för olika klienter för att ansluta till en privat slut punkt som distribueras i Azure Virtual WAN:

:::image type="content" source="./media/private-link-inspection-secure-virtual-hub/private-link-inspection-virtual-wan-architecture.png" alt-text="Trafik flöden" border="true":::

## <a name="troubleshooting"></a>Felsökning

De huvudsakliga problem som du kan ha när du försöker filtrera trafik som är avsedd för privata slut punkter via säkrat virtuellt NAV är:

- Klienterna kan inte ansluta till privata slut punkter.

- Azure-brandväggen ignoreras. Det här problemet kan verifieras genom att inga nätverks-eller program regel loggar poster i Azure-brandväggen.

I de flesta fall orsakas problemen av något av följande problem:

- Felaktig DNS-namnmatchning

- Felaktig konfigurering av Routning

### <a name="incorrect-dns-name-resolution"></a>Felaktig DNS-namnmatchning

1. Kontrol lera att DNS-servrarna för virtuella nätverk är inställda på *anpassade* och att IP-adressen är den privata IP-adressen för Azure-brandväggen i en säker virtuell hubb.

   Azure CLI:

   ```azurecli-interactive
   az network vnet show --name <VNET Name> --resource-group <Resource Group Name> --query "dhcpOptions.dnsServers"
   ```
2. Kontrol lera att klienterna i samma virtuella nätverk som den virtuella datorn för DNS-vidarebefordraren kan matcha det offentliga domän namnet för den privata slut punkten till dess motsvarande privata IP-adress genom att fråga den virtuella datorn som är konfigurerad som DNS-vidarebefordrare direkt.

   Linux:

   ```bash
   dig @<DNS forwarder VM IP address> <Private endpoint public FQDN>
   ```
3. Granska *AzureFirewallDNSProxy* för Azure-brandväggen och verifiera att den kan ta emot och lösa DNS-frågor från klienterna.

   ```kusto
   AzureDiagnostics
   | where Category contains "DNS"
   | where msg_s contains "database.windows.net"
   ```
4. Kontrol lera att *DNS-proxy* har Aktiver ATS och att en *anpassad* DNS-server som pekar på IP-adressen för den virtuella datorns IP-adress för DNS-vidarebefordraren har kon figurer ATS i brand Väggs principen som är kopplad till Azure-brandväggen i den säkra

   Azure CLI:

   ```azurecli-interactive
   az network firewall policy show --name <Firewall Policy> --resource-group <Resource Group Name> --query dnsSettings
   ```

### <a name="incorrect-routing-configuration"></a>Felaktig konfigurering av Routning

1. Verifiera *säkerhets konfigurationen* i brand Väggs principen som är kopplad till den Azure-brandvägg som distribueras i den säkra virtuella hubben. Se till att den **privata trafik** kolumnen visas som **skyddad av Azure-brandväggen** för alla virtuella nätverk och förgrenings anslutningar som du vill filtrera trafik för.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-policy-private-traffic-configuration.png" alt-text="Privat trafik som skyddas av Azure-brandväggen" border="true":::

2. Verifiera **säkerhets konfigurationen** i brand Väggs principen som är kopplad till den Azure-brandvägg som distribueras i den säkra virtuella hubben. Se till att det finns en/32-post för varje privat IP-adress för privat slut punkt som du vill filtrera trafik för under **prefix för privata trafik**.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="Säkerhets konfiguration för brand Väggs hanteraren – prefix för privata trafik" border="true":::

3. I den skyddade virtuella hubben under virtuellt WAN, kan du granska effektiva vägar för de väg tabeller som är kopplade till de virtuella nätverk och förgreningar som du vill filtrera trafik för. Se till att det finns/32 poster för varje privat IP-adress för privat slut punkt som du vill filtrera trafik för.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/secured-virtual-hub-effective-routes.png" alt-text="Effektiva vägar för säker virtuell hubb" border="true":::

4. Kontrol lera de effektiva vägarna på nätverkskorten som är anslutna till de virtuella datorer som distribueras i de virtuella nätverk som du vill filtrera trafik för. Se till att det finns/32 poster för varje privat IP-adress för privat slut punkt som du vill filtrera trafik för.
 
   Azure CLI:

   ```azurecli-interactive
   az network nic show-effective-route-table --name <Network Interface Name> --resource-group <Resource Group Name> -o table
   ```
5. Kontrol lera vägvals tabellerna för dina lokala enheter för routning. Kontrol lera att du lär adress utrymmena för de virtuella nätverk där de privata slut punkterna har distribuerats.

   Azure Virtual WAN annonserar inte de prefix som kon figurer ATS under **privata trafik prefix** i brand Väggs principens **säkerhets konfiguration** till lokalt. Det förväntas att/32-posterna inte ska visas i vägvals tabellerna för dina lokala enheter för routning.

6. Granska **AzureFirewallApplicationRule** och **AzureFirewallNetworkRule** Azure Firewall-loggar. Kontrol lera att trafik som är avsedd för privata slut punkter loggas.

   **AzureFirewallNetworkRule** logg poster innehåller inte FQDN-information. Filtrera efter IP-adress och port när du inspekterar nätverks regler.

   När du filtrerar trafik som är avsedd för [Azure Files](../storage/files/storage-files-introduction.md) privata slut punkter genereras **AzureFirewallNetworkRule** logg poster bara när en klient monterar eller ansluter till fil resursen. Azure-brandväggen genererar inte loggar för [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) -åtgärder för filer i fil resursen. Detta beror på att CRUD-åtgärder överförs via den beständiga TCP-kanalen som öppnas när klienten först ansluter eller monterar till fil resursen.

   Exempel på program regel logg fråga:

   ```kusto
   AzureDiagnostics
   | where msg_s contains "database.windows.net"
   | where Category contains "ApplicationRule"
   ```
## <a name="next-steps"></a>Nästa steg

- [Använda Azure Firewall för att kontrollera trafik till en privat slutpunkt](../private-link/inspect-traffic-with-azure-firewall.md)
