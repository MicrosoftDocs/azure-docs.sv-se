---
title: Felsöka anslutningsproblem för privata slutpunkter i Azure
description: Stegvisa anvisningar för att diagnostisera anslutning till privat slutpunkt
services: private-endpoint
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: 2a4f86d9fae7b78a57cf8da7ab42d2d4a4cd7be5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835406"
---
# <a name="troubleshoot-azure-private-endpoint-connectivity-problems"></a>Felsöka anslutningsproblem för privata slutpunkter i Azure

Den här artikeln innehåller stegvisa anvisningar för att verifiera och diagnostisera konfigurationen av din privata Slutpunktsanslutning i Azure.

Azure Private Endpoint är ett nätverksgränssnitt som ansluter dig privat och säkert till en privat länktjänst. Den här lösningen hjälper dig att skydda dina arbetsbelastningar i Azure genom att tillhandahålla privata anslutningar till dina Azure-tjänstresurser från ditt virtuella nätverk. Den här lösningen tar effektivt dessa tjänster till ditt virtuella nätverk.

Här är de anslutningsscenarier som är tillgängliga med privat slutpunkt:

- Virtuellt nätverk från samma region
- Regionalt peer-peerade virtuella nätverk
- Globalt peer-peerade virtuella nätverk
- Kunder lokalt via VPN eller Azure ExpressRoute kretsar

## <a name="diagnose-connectivity-problems"></a>Diagnostisera anslutningsproblem 

Granska de här stegen för att se till att alla vanliga konfigurationer är som förväntat för att lösa anslutningsproblem med konfigurationen av den privata slutpunkten.

1. Granska konfigurationen av privat slutpunkt genom att bläddra i resursen.

    a. Gå till **Private Link Center**.

      ![Private Link Center](./media/private-endpoint-tsg/private-link-center.png)

    b. I den vänstra rutan väljer du **Privata slutpunkter.**
    
      ![Privata slutpunkter](./media/private-endpoint-tsg/private-endpoints.png)

    c. Filtrera och välj den privata slutpunkt som du vill diagnostisera.

    d. Granska det virtuella nätverket och DNS-informationen.
     - Kontrollera att anslutningstillståndet är **Godkänd.**
     - Kontrollera att den virtuella datorn har anslutning till det virtuella nätverk som är värd för de privata slutpunkterna.
     - Kontrollera att FQDN-informationen (kopiera) och den privata IP-adressen har tilldelats.
    
       ![Virtuellt nätverk och DNS-konfiguration](./media/private-endpoint-tsg/vnet-dns-configuration.png)
    
1. Använd [Azure Monitor](../azure-monitor/overview.md) för att se om data flödar.

    a. På den privata slutpunktsresursen väljer du **Övervaka**.
     - Välj **Byte in-** eller **byte ut**. 
     - Se om data flödar när du försöker ansluta till den privata slutpunkten. Förvänta dig en fördröjning på cirka 10 minuter.
    
       ![Verifiera telemetri för privat slutpunkt](./media/private-endpoint-tsg/private-endpoint-monitor.png)

1.  Använd **felsökning av VM-anslutning** från Azure Network Watcher.

    a. Välj den virtuella klientdatorn.

    b. Välj **Anslutningsfelsök** och välj sedan **fliken Utgående** anslutningar.
    
      ![Network Watcher – Testa utgående anslutningar](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c. Välj **Använd Network Watcher för detaljerad anslutningsspårning.**
    
      ![Network Watcher – Felsökning av anslutning](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d. Välj **Testa efter FQDN**.
     - Klistra in FQDN från resursen för den privata slutpunkten.
     - Ange en port. Använd vanligtvis 443 för Azure Storage eller Azure Cosmos DB och 1336 för SQL.

    e. Välj **Testa** och verifiera testresultaten.
    
      ![Network Watcher – Testresultat](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
1. DNS-upplösningen från testresultaten måste ha samma privata IP-adress tilldelad till den privata slutpunkten.

    a. Om DNS-inställningarna är felaktiga följer du dessa steg:
     - Om du använder en privat zon: 
       - Kontrollera att den virtuella klientdatorns virtuella nätverk är associerat med den privata zonen.
       - Kontrollera att posten för den privata DNS-zonen finns. Om den inte finns skapar du den.
     - Om du använder anpassad DNS:
       - Granska dina anpassade DNS-inställningar och kontrollera att DNS-konfigurationen är korrekt.
       Vägledning finns i Översikt [över privat slutpunkt: DNS-konfiguration.](./private-endpoint-overview.md#dns-configuration)

    b. Om anslutningen misslyckas på grund av nätverkssäkerhetsgrupper (NSG:er) eller användardefinierade vägar:
     - Granska NSG:ns regler för utgående trafik och skapa lämpliga regler för utgående trafik.
    
       ![Regler för utgående NSG](./media/private-endpoint-tsg/nsg-outbound-rules.png)

1. Den virtuella källdatorn ska ha vägen till den privata slutpunktens IP-nästa hopp som InterfaceEndpoints i de NIC-effektiva vägarna. 

    a. Om du inte kan se den privata slutpunktsvägen i den virtuella källdatorn kontrollerar du om 
     - Den virtuella källdatorn och den privata slutpunkten tillhör samma virtuella nätverk. Om ja, måste du kontakta supporten. 
     - Den virtuella källdatorn och den privata slutpunkten är en del av olika virtuella nätverk och söker sedan efter IP-anslutningen mellan de virtuella nätverken. Om det finns EN IP-anslutning och du fortfarande inte kan se vägen kan du kontakta supporten. 

1. Om anslutningen har verifierade resultat kan anslutningsproblemet vara relaterat till andra aspekter som hemligheter, token och lösenord på programnivån.
   - I det här fallet granskar du konfigurationen av den privata länkresurs som är associerad med den privata slutpunkten. Mer information finns i Azure Private Link [felsökningsguiden](troubleshoot-private-link-connectivity.md)
   
1. Det är alltid bra att begränsa innan du höjer supportbiljetten. 

    a. Om källan är lokal anslutning till privat slutpunkt i Azure och har problem kan du försöka ansluta 
      - Till en annan virtuell dator från lokal plats och kontrollera om du har IP-anslutning till Virtual Network från den lokala datorn. 
      - Från en virtuell dator i Virtual Network till den privata slutpunkten.
      
    b. Om källan är Azure och den privata slutpunkten finns i Virtual Network försöker du ansluta 
      - Till den privata slutpunkten från en annan källa. På så sätt kan du isolera eventuella problem som rör den virtuella datorn. 
      - För alla virtuella datorer som ingår i samma Virtual Network för den privata slutpunkten.  

1. Kontakta [Azure Support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) om problemet fortfarande är löst och det fortfarande finns ett anslutningsproblem.

## <a name="next-steps"></a>Nästa steg

 * [Skapa en privat slutpunkt i det uppdaterade undernätet (Azure Portal)](./create-private-endpoint-portal.md)
 * [Azure Private Link felsökningsguide](troubleshoot-private-link-connectivity.md)
