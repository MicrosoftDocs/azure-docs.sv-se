---
title: 'Självstudie: Utföra infångade paket Azure Virtual WAN plats-till-plats-anslutningar'
description: I den här självstudien lär du dig hur du utför paketinsamling på Virtual WAN plats-till-plats-VPN Gateway.
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: wellee
Customer intent: As someone with a networking background using Virtual WAN, I want to perform a packet capture on my Site-to-site VPN Gateway.
ms.openlocfilehash: dbe7e06484797063ed4122ee3fdde625dc66c41f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879172"
---
# <a name="perform-packet-capture-on-the-azure-virtual-wan-site-to-site-vpn-gateway"></a>Utför paketinsamling på Azure Virtual WAN plats-till-plats-VPN Gateway 

Anslutningsproblem och prestandarelaterade problem är ofta komplexa. Det kan ta lång tid och tid att bara begränsa orsaken till problemet. Paketfångst kan hjälpa dig att begränsa omfattningen av ett problem till vissa delar av nätverket. Det kan hjälpa dig att avgöra om problemet finns på kundens sida av nätverket, Azure-sidan av nätverket eller någonstans däremellan. När du har begränsat problemet är det mer effektivt att felsöka och vidta åtgärder.

I följande artikel beskrivs hur du startar och stoppar en paketinsamling på Azure Virtual WAN plats-till-plats-VPN Gateway. Den här funktionen är för närvarande endast tillgänglig via PowerShell.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda stegen i den här artikeln måste du ha följande konfiguration som redan har ställts in i din miljö:

* En Virtual WAN, virtuell hubb och en plats-till-plats-VPN Gateway distribueras i den virtuella hubben. Du kan också ha anslutningar som ansluter VPN-platser till din plats-till-plats-VPN Gateway.


### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="set-up-the-environment"></a>Konfigurera miljön

Kontrollera att du har rätt prenumerationskontext genom att köra följande skript i PowerShell. Detta säkerställer att du är inloggad på en användare som har behörighet att utföra paketinsamlingen på plats-till-plats-VPN Gateway.

   ```azurepowershell-interactive
    $subid = “<insert Virtual WAN subscription ID here>”
    Set-AzContext -SubscriptionId $subid
   ```

## <a name="create-a-storage-account"></a><a name="createstorage"></a> Skapa ett lagringskonto

Du måste skapa ett lagringskonto under din Azure-prenumeration för att lagra resultatet av paketinfångst. I det här [dokumentet finns](.././storage/common/storage-account-create.md) anvisningar om hur du skapar ett lagringskonto.

När du har skapat ditt konto kör du följande kommandon för att generera en SAS-URL (signatur för delad åtkomst). Resultatet av din paketinfångning lagras via den här URL:en.
   ```azurepowershell-interactive
  $rgname = “<resource group name containing storage account>” 
$storeName = “<name of storage account> “
$containerName = “<name of container you want to store packet capture in>
$key = Get-AzStorageAccountKey -ResourceGroupName $rgname -Name $storeNAme
$context = New-AzStorageContext -StorageAccountName  $storeName -StorageAccountKey $key[0].value
New-AzStorageContainer -Name $containerName -Context $context
$container = Get-AzStorageContainer -Name $containerName  -Context $context
$now = get-date
$sasurl = New-AzureStorageContainerSASToken -Name $containerName -Context $context -Permission "rwd" -StartTime $now.AddHours(-1) -ExpiryTime $now.AddDays(1) -FullUri
   ```

## <a name="start-the-packet-capture"></a>Starta infångade paket

För att starta paketinsamlingen har du två alternativ: att samla in paket på en enda VPN-anslutning eller på hela plats-till-plats-VPN Gateway. Observera att om du väljer att utföra avfångade anslutningar på VPN-anslutningar kan du bara utföra avfångade på 6 anslutningar samtidigt.

### <a name="packet-capture-on-a-site-to-site-vpn-gateway-all-connections"></a>Paketinsamling på en plats-till-plats-VPN Gateway (alla anslutningar)

Kör följande kommandon. Namnet på plats-till-plats-VPN Gateway kan hittas genom att gå till din virtuella hubb och klicka på VPN (plats-till-plats) under Anslutning.

:::image type="content" source="./media/virtual-wan-pcap-screenshots/vpn-gateway-name.png" alt-text="Bild av Virtual WAN gatewaynamn." lightbox="./media/virtual-wan-pcap-screenshots/vpn-gateway-name.png":::

   ```azurepowershell-interactive
Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>" -Sasurl $sasurl
   ```

### <a name="packet-capture-on-specific-site-to-site-vpn-connections"></a>Paketfångst på specifika PLATS-till-plats-VPN-anslutningar

>[!NOTE]
> Observera att du bara kan köra en paketfångst på 5 VPN-anslutningar samtidigt.


Kör följande kommandon. Namnet på VPN-anslutningar för plats-till-plats hittar du genom att gå till din virtuella hubb och klicka på VPN (plats-till-plats) under Anslutning. Gå sedan till den VPN-plats där du vill utföra paketinsamlingen och klicka på de tre punkterna till höger. Klicka **på Redigera VPN-anslutning** på menyn som visas.

:::image type="content" source="./media/virtual-wan-pcap-screenshots/sample-connection.png" alt-text="Bild av hur du hittar VPN-anslutningsnamn." lightbox="./media/virtual-wan-pcap-screenshots/sample-connection.png":::

Du hittar namnet på länkarna som är anslutna till en specifik VPN-plats genom att klicka på VPN-platsen från föregående avsnitt och titta på **tabellen** Länkar. 

:::image type="content" source="./media/virtual-wan-pcap-screenshots/link-name-sample.png" alt-text="Bild av hur du hittar VPN-länknamn." lightbox="./media/virtual-wan-pcap-screenshots/link-name-sample.png":::

   ```azurepowershell-interactive
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links eg. "link1,link2">” -Sasurl $sasurl 
   ```

## <a name="optional-specifying-filters"></a>Valfritt: Ange filter

Det finns några vanliga verktyg för paketinfångning. Det kan vara besvärligt att hämta relevanta paketinfångade paket med dessa verktyg, särskilt i scenarier med hög volymtrafik. För att förenkla infångade paket kan du ange filter på paketinfångst för att fokusera på specifika beteenden. Nedan visas tillgängliga parametrar:

>[!NOTE]
> För TracingFlags och TCPFlags kan du ange flera protokoll genom att lägga till de numeriska värdena för de protokoll som du vill avbilda (samma som ett logiskt OR). Om du till exempel bara vill samla in ESP- och OPVN-paket anger du värdet TracingFlag på 8+1 = 9.  

| Parameter | Beskrivning | Standardvärden | Tillgängliga värden|
|--- |--- | --- | ---|
| TracingFlags | Heltal som avgör vilka typer av paket som fångas in | 11 (ESP, IKE, OVPN) | ESP = 1 IKE = 2 OPVN = 8 |
| TCPFlags | Heltal som avgör vilka typer av TCP-paket som avbildas | 0 (ingen) | FIN = 1, SYN = 2, RST = 4, PSH = 8, ACK = 16,URG = 32, ECE = 64, CWR = 128| 
| MaxPacketBufferSize|Maximal storlek på ett avbildat paket i byte. Paket trunkeras om de är större än det angivna värdet. |120|Valfri|
| MaxFileSize |Maximal infångade filstorlek i Mb. Infångade data lagras i en cirkulär buffert så spill hanteras på FIFO-sätt (äldre paket tas bort först)|100|Valfri|
|SourceSubnets|Paket från de angivna CIDR-intervallen avbildas. Anges som en matris.|[] (alla IPv4-adresser)|Matris med kommaavgränsade IPV4-undernät|
| DestinationSubnets |Paket som är avsedda för de angivna CIDR-intervallen avbildas. Anges som en matris. |[] (alla IPv4-adresser)|Matris med kommaavgränsade IPV4-undernät|
|SourcePort |Paket med källan i de angivna intervallen avbildas. Anges som en matris.|[] (alla portar)|Matris med kommaavgränsade portar|
|DestinationPort |Paket med mål inom de angivna intervallen avbildas. Anges som en matris.|[] (alla portar)|Matris med kommaavgränsade portar|
| CaptureSingleDirectionTrafficOnly |Om det är sant visas endast en riktning för ett dubbelriktat flöde i paketinfångst. Då avbildas alla möjliga kombinationer av IP-adresser och portar.|Sant|Sant, Falskt|
|Protokoll|En matris med heltal som motsvarar IANA-protokoll. |[] (alla protokoll)| Alla protokoll som finns [här](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml) |

Nedan visas ett exempel på paketinfångst med hjälp av en filtersträng. Du kan ändra parametrarna så att de passar dina behov baserat på föregående tabell.  

   ```azurepowershell-interactive
$filter="{`"TracingFlags`":11,`"MaxPacketBufferSize`":120,`"MaxFileSize`":500,`"Filters`":[{`"SourceSubnets`":[`"10.19.0.4/32`",`"10.20.0.4/32`"],`"DestinationSubnets`":[`"10.20.0.4/32`",`"10.19.0.4/32`"],`"TcpFlags`":9,`"CaptureSingleDirectionTrafficOnly`":true}]}"
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links>” -Sasurl $sasurl -FilterData $filter

Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>" -Sasurl $sasurl -FilterData $filter
   ```

## <a name="stopping-the-packet-capture"></a>Stoppa paketinfångst
Vi rekommenderar att du låter paketfångst köras i minst 600 sekunder. På grund av synkroniseringsproblem mellan flera komponenter på sökvägen kanske kortare paketinfångst inte tillhandahåller fullständiga data. När du är redo att stoppa paketinfångst kör du följande kommando.

Parametrarna liknar dem som finns i avsnittet Starta en paketinfångst. SAS-URL:en genererades i [avsnittet Skapa ett lagringskonto.](#createstorage)

### <a name="gateway-level-packet-capture"></a>Paketinfångst på gatewaynivå

   ```azurepowershell-interactive
Stop-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name <GatewayName> -SasUrl $sas
   ```

### <a name="connection-level-packet-captures"></a>Infångade paket på anslutningsnivå

   ```azurepowershell-interactive
Stop-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name <name of the VPN connection> -ParentResourceName "<name of VPN Gateway>" -LinkConnectionName <comma separated list of links e.g. "link1,link2">-SasUrl $sas
   ```

## <a name="viewing-your-packet-capture"></a>Visa infångade paket

I Azure Portal du till lagringskontot som skapades i "Skapa ett lagringskonto" Klicka på containern och ladda ned filen. Datafiler för paketfångst genereras i PCAP-format. Du kan använda Wireshark eller något annat vanligt program för att öppna PCAP-filer.

## <a name="next-steps"></a>Nästa steg

Mer information om Virtual WAN finns i:

> [!div class="nextstepaction"]
> * [Vanliga frågor om Virtual WAN](virtual-wan-faq.md)