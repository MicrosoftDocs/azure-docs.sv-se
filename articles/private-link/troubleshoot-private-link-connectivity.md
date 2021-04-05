---
title: Felsöka anslutningsproblem för Azure Private Link
description: Steg-för-steg-anvisningar för att diagnostisera anslutning till privata länkar
services: private-link
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
ms.openlocfilehash: 45a7a146dd929408b50a0045fe2598726ee05505
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95544317"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>Felsöka anslutningsproblem för Azure Private Link

Den här artikeln innehåller steg-för-steg-anvisningar för att verifiera och diagnostisera anslutning för konfigurationen av en privat Azure-länk.

Med Azures privata länk kan du komma åt Azure Platform as a Service (PaaS)-tjänster, till exempel Azure Storage, Azure Cosmos DB och Azure SQL Database och Azure-värdbaserade kund-eller partner tjänster via en privat slut punkt i det virtuella nätverket. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsoft stamnät nätverket, vilket eliminerar exponering från det offentliga Internet. Du kan också skapa en egen privat länk-tjänst i ditt virtuella nätverk och leverera den privat för kunderna.

Du kan aktivera din tjänst som körs bakom standard-nivån för Azure Load Balancer för åtkomst till privat länk. Konsumenter av din tjänst kan skapa en privat slut punkt i sitt virtuella nätverk och mappa den till den här tjänsten för att få åtkomst till den privat.

Här är de anslutnings scenarier som är tillgängliga med privat länk:

- Virtuellt nätverk från samma region
- Regionalt peer-peerade virtuella nätverk
- Globalt peer-kopplat virtuella nätverk
- Kund lokalt via VPN eller Azure ExpressRoute-kretsar

## <a name="deployment-troubleshooting"></a>Distributions fel sökning

Läs informationen om hur du [inaktiverar nätverks principer i tjänsten för privata länkar](./disable-private-link-service-network-policy.md) för fel söknings fall där du inte kan välja käll-IP-adress från det undernät du väljer för din privata länk tjänst.

Kontrol lera att inställningen **privateLinkServiceNetworkPolicies** är inaktive rad för under nätet som du väljer käll-IP-adressen från.

## <a name="diagnose-connectivity-problems"></a>Diagnostisera anslutnings problem

Om du får anslutnings problem med konfigurationen av den privata länken kan du gå igenom de här stegen för att se till att alla vanliga konfigurationer är som förväntat.

1. Granska konfigurationen av privat länk genom att bläddra i resursen.

    a. Gå till **Private Link Center**.

      ![Privat länk Center](./media/private-link-tsg/private-link-center.png)

    b. I det vänstra fönstret väljer du **privata länk tjänster**.

      ![Privata länk tjänster](./media/private-link-tsg/private-link-service.png)

    c. Filtrera och välj den privata länk tjänst som du vill diagnostisera.

    d. Granska de privata slut punkts anslutningarna.
     - Se till att den privata slut punkten som du letar efter anslutning från visas med ett **godkänt** anslutnings tillstånd.
     - Om statusen är **väntande** väljer du den och godkänner den.

       ![Anslutningar för privata slut punkter](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - Gå till den privata slut punkt som du ansluter från genom att välja namnet. Kontrol lera att anslutnings statusen visas som **godkänd**.

       ![Översikt över anslutning till privat slut punkt](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - När båda sidorna har godkänts kan du försöka ansluta igen.

    e. Granska **alias** på fliken **Översikt** och **resurs-ID** på fliken **Egenskaper** .
     - Kontrol lera att **alias** **-och resurs-ID-** informationen matchar **aliaset** och **resurs-ID: t** som du använder för att skapa en privat slut punkt till den här tjänsten.

       ![Verifiera Ali Aset information](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Verifiera resurs-ID-information](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. Granska **Synlighets** informationen på fliken **Översikt** .
     - Se till att din prenumeration ligger under **Synlighets** omfånget.

       ![Verifiera Synlighets information](./media/private-link-tsg/pls-overview-pane-visibility.png)

    ex. Granska informationen om **belastningsutjämnare** på fliken **Översikt** .
     - Du kan gå till belastningsutjämnaren genom att välja länken belastnings utjämning.

       ![Verifiera information om belastningsutjämnare](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Se till att inställningarna för belastningsutjämnare är konfigurerade enligt dina förväntningar.
       - Granska **klient delens IP-konfiguration**.
       - Granska **backend-pooler**.
       - Granska **regler för belastnings utjämning**.

       ![Verifiera egenskaper för belastningsutjämnare](./media/private-link-tsg/pls-ilb-properties.png)

     - Se till att belastningsutjämnaren fungerar enligt de tidigare inställningarna.
       - Välj en virtuell dator i något annat undernät än under nätet där belastningsutjämnaren backend-poolen är tillgänglig.
       - Försök att komma åt belastningsutjämnarens klient del från den tidigare virtuella datorn.
       - Om anslutningen gör den till backend-poolen enligt reglerna för belastnings utjämning, fungerar belastningsutjämnaren.
       - Du kan också granska belastnings Utjämnings måttet genom Azure Monitor för att se om data flödar genom belastningsutjämnaren.

1. Använd [Azure Monitor](../azure-monitor/overview.md) för att se om data flödar.

    a. Välj **mått** i resursen för privata länk tjänster.
     - Välj **byte in** eller **byte ut**.
     - Se om data flödar när du försöker ansluta till den privata länk tjänsten. Vänta en fördröjning på ungefär 10 minuter.

       ![Verifiera mått för privata länk tjänster](./media/private-link-tsg/pls-metrics.png)

1. Kontakta support teamet för [Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) om problemet fortfarande är olöst och det fortfarande finns anslutnings problem.

## <a name="next-steps"></a>Nästa steg

 * [Skapa en privat länk tjänst (CLI)](./create-private-link-service-cli.md)
 * [Fel söknings guide för Azure privat slut punkt](troubleshoot-private-endpoint-connectivity.md)