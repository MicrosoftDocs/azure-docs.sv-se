---
title: Felsöka anslutningsproblem för privata slutpunkter i Azure
description: Steg-för-steg-anvisningar för hur du diagnostiserar privat slut punkts anslutning
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
ms.openlocfilehash: 90831c0e8d5ab73f65dc801319a357d59799cbc6
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/29/2020
ms.locfileid: "97807560"
---
# <a name="troubleshoot-azure-private-endpoint-connectivity-problems"></a>Felsöka anslutningsproblem för privata slutpunkter i Azure

Den här artikeln innehåller steg-för-steg-anvisningar om hur du verifierar och diagnostiserar konfigurationen av anslutnings programmet för Azure privat slutpunkt.

Den privata Azure-slutpunkten är ett nätverks gränssnitt som ansluter privat och säkert till en privat länk tjänst. Den här lösningen hjälper dig att skydda dina arbets belastningar i Azure genom att tillhandahålla privat anslutning till dina Azure-tjänsteresurser från det virtuella nätverket. Med den här lösningen kan du effektivt använda tjänsterna i ditt virtuella nätverk.

Här är de anslutnings scenarier som är tillgängliga med privat slut punkt:

- Virtuellt nätverk från samma region
- Regionalt peer-peerade virtuella nätverk
- Globalt peer-kopplat virtuella nätverk
- Kund lokalt via VPN eller Azure ExpressRoute-kretsar

## <a name="diagnose-connectivity-problems"></a>Diagnostisera anslutnings problem 

Granska de här stegen för att se till att alla vanliga konfigurationer är de som förväntas lösa anslutnings problem med din privata slut punkts konfiguration.

1. Granska den privata slut punkts konfigurationen genom att bläddra i resursen.

    a. Gå till **Private Link Center**.

      ![Privat länk Center](./media/private-endpoint-tsg/private-link-center.png)

    b. I det vänstra fönstret väljer du **privata slut punkter**.
    
      ![Privata slut punkter](./media/private-endpoint-tsg/private-endpoints.png)

    c. Filtrera och välj den privata slut punkt som du vill diagnostisera.

    d. Granska det virtuella nätverket och DNS-informationen.
     - Kontrol lera att anslutnings status är **godkänd**.
     - Se till att den virtuella datorn är ansluten till det virtuella nätverket som är värd för de privata slut punkterna.
     - Kontrol lera att FQDN-informationen (kopia) och den privata IP-adressen har tilldelats.
    
       ![Virtuellt nätverk och DNS-konfiguration](./media/private-endpoint-tsg/vnet-dns-configuration.png)
    
1. Använd [Azure Monitor](../azure-monitor/overview.md) för att se om data flödar.

    a. Välj **övervaka** på den privata slut punkts resursen.
     - Välj **data i** eller **ut data**. 
     - Se om data flödar när du försöker ansluta till den privata slut punkten. Vänta en fördröjning på ungefär 10 minuter.
    
       ![Verifiera privat slut punkt för telemetri](./media/private-endpoint-tsg/private-endpoint-monitor.png)

1.  Använd **fel sökning av VM-anslutning** från Azure Network Watcher.

    a. Välj den virtuella klient datorn.

    b. Välj **fel sökning av anslutning** och välj sedan fliken **utgående anslutningar** .
    
      ![Network Watcher-testa utgående anslutningar](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c. Välj **använd Network Watcher för detaljerad anslutnings spårning**.
    
      ![Network Watcher fel sökning av anslutning](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d. Välj **Testa efter FQDN**.
     - Klistra in det fullständiga domän namnet från resursen för den privata slut punkten.
     - Ange en port. Använd vanligt vis 443 för Azure Storage eller Azure Cosmos DB och 1336 för SQL.

    e. Välj **test** och verifiera test resultaten.
    
      ![Network Watcher-test resultat](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
1. DNS-matchning från test resultaten måste ha samma privata IP-adress som tilldelats till den privata slut punkten.

    a. Följ dessa steg om DNS-inställningarna är felaktiga:
     - Om du använder en privat zon: 
       - Se till att det virtuella klient DATORns virtuella nätverk är associerat med den privata zonen.
       - Kontrol lera att det finns en privat DNS-zon post. Om den inte finns skapar du den.
     - Om du använder anpassad DNS:
       - Granska dina anpassade DNS-inställningar och kontrol lera att DNS-konfigurationen är korrekt.
       Vägledning finns i [Översikt över privat slut punkt: DNS-konfiguration](./private-endpoint-overview.md#dns-configuration).

    b. Om anslutningen inte fungerar på grund av nätverks säkerhets grupper (NSG: er) eller användardefinierade vägar:
     - Granska utgående regler för NSG och skapa lämpliga utgående regler som tillåter trafik.
    
       ![NSG utgående regler](./media/private-endpoint-tsg/nsg-outbound-rules.png)

1. Den virtuella käll datorn ska ha vägen till privat slut punkt IP-adress för nästa hopp som InterfaceEndpoints i NÄTVERKSKORTets effektiva vägar. 

    a. Om du inte kan se det privata slut punkts flödet på den virtuella käll datorn kontrollerar du om 
     - Den virtuella käll datorn och den privata slut punkten tillhör samma VNET. Om ja, måste du delta i supporten. 
     - Den virtuella käll datorn och den privata slut punkten är en del av olika virtuella nätverk och kontrollerar sedan IP-anslutningen mellan virtuella nätverk. Om det finns en IP-anslutning och fortfarande inte kan se vägen kan du engagera supporten. 

1. Om anslutningen har verifierats kan anslutnings problemet vara relaterat till andra aspekter som hemligheter, tokens och lösen ord på program nivån.
   - I det här fallet granskar du konfigurationen av den privata länk resurs som är associerad med den privata slut punkten. Mer information finns i [fel söknings guiden för Azure privat länk](troubleshoot-private-link-connectivity.md)
   
1. Det är alltid bäst att begränsa innan support ärendet höjs. 

    a. Om källan är lokal anslutning till privat slut punkt i Azure har problem, försöker du ansluta 
      - Till en annan virtuell dator från lokal plats och kontrol lera om du har IP-anslutning till Virtual Network lokalt. 
      - Från en virtuell dator i Virtual Network till den privata slut punkten.
      
    b. Om källan är Azure och den privata slut punkten är i olika Virtual Network försöker du ansluta 
      - Till den privata slut punkten från en annan källa. Genom att göra detta kan du isolera eventuella specifika problem med en virtuell dator. 
      - Till en virtuell dator som är en del av samma Virtual Network av den privata slut punkten.  

1. Kontakta support teamet för [Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) om problemet fortfarande är olöst och det fortfarande finns anslutnings problem.

## <a name="next-steps"></a>Nästa steg

 * [Skapa en privat slut punkt på det uppdaterade under nätet (Azure Portal)](./create-private-endpoint-portal.md)
 * [Fel söknings guide för Azure privat länk](troubleshoot-private-link-connectivity.md)
