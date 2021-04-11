---
title: Använd Azure-brandväggen för att skydda Windows Virtual Desktop
description: Lär dig hur du använder Azure Firewall för att skydda Windows-distributioner för virtuella datorer
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 05/06/2020
ms.author: victorh
ms.openlocfilehash: d5320f44aa5d922cea852ab09e5141fad277e2b0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566034"
---
# <a name="use-azure-firewall-to-protect-window-virtual-desktop-deployments"></a>Använda Azure Firewall för att skydda Window Virtual Desktop-distributioner

Windows Virtual Desktop är en Desktop-och app Virtualization-tjänst som körs på Azure. När en användare ansluter till en Windows Virtual Desktop-miljö körs sessionen av en adresspool. En Host-pool är en samling virtuella Azure-datorer som registreras på Windows Virtual Desktop som värdar för Windows-sessioner. De här virtuella datorerna körs i det virtuella nätverket och omfattas av de virtuella nätverks säkerhets kontrollerna. De behöver utgående Internet åtkomst till Windows Virtual Desktop-tjänsten för att fungera korrekt och kan också kräva utgående Internet åtkomst för slutanvändare. Azure-brandväggen kan hjälpa dig att låsa din miljö och filtrera utgående trafik.

[![Windows-arkitektur ](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png) för virtuella skriv bord](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png#lightbox)

Följ rikt linjerna i den här artikeln för att ge ytterligare skydd för Windows-poolen med virtuella skriv bord med hjälp av Azure-brandväggen.

## <a name="prerequisites"></a>Förutsättningar


 - En distribuerad Windows Virtual Desktop-miljö och Host-pool.

   Mer information finns i [Självstudier: skapa en adresspool med hjälp av Azure Marketplace](../virtual-desktop/create-host-pools-azure-marketplace.md) och [skapa en adresspool med en Azure Resource Manager-mall](../virtual-desktop/virtual-desktop-fall-2019/create-host-pools-arm-template.md).

Mer information om Windows Virtual Desktop-miljöer finns i [Windows Virtual Desktop-miljö](../virtual-desktop/environment-setup.md).

## <a name="host-pool-outbound-access-to-windows-virtual-desktop"></a>Utgående värd pool till Windows Virtual Desktop

De virtuella Azure-datorer som du skapar för virtuella Windows-datorer måste ha åtkomst till flera fullständigt kvalificerade domän namn (FQDN) för att fungera korrekt. Azure-brandväggen tillhandahåller en Windows Virtual Desktop FQDN-tagg för att förenkla den här konfigurationen. Använd följande steg för att tillåta utgående plattforms trafik för virtuella Windows-datorer:

- Distribuera Azure-brandväggen och konfigurera din Windows-UDR (User Defined Route) för virtuella Skriv bords värd för att dirigera all trafik via Azure-brandväggen. Din standard väg pekar nu på brand väggen.
- Skapa en regel samling för program och Lägg till en regel för att aktivera *WindowsVirtualDesktop* FQDN-taggen. Käll-IP-adressintervallet är det virtuella nätverk som är värd för poolen, protokollet är **https** och målet är **WindowsVirtualDesktop**.

- Uppsättningen med nödvändiga lagrings-och Service Bus-konton för Windows-poolen för virtuella skriv bord är distributions bestämd, så den har ännu inte registrerats i WindowsVirtualDesktop FQDN-taggen. Du kan åtgärda detta på något av följande sätt:

   - Tillåt https-åtkomst från värd poolens undernät till * xt.blob.core.windows.net, * eh.servicebus.windows.net och * xt.table.core.windows.net. Dessa jokertecken för jokertecken aktiverar nödvändig åtkomst, men är mindre restriktiva.
   - Använd följande Log Analytics-fråga för att lista de exakta FQDN: er som krävs, och Tillåt dem uttryckligen i brand Väggs program reglerna:
   ```
   AzureDiagnostics
   | where Category == "AzureFirewallApplicationRule"
   | search "Deny"
   | search "gsm*eh.servicebus.windows.net" or "gsm*xt.blob.core.windows.net" or "gsm*xt.table.core.windows.net"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" *
   | project TimeGenerated,Protocol,FQDN
   ```

- Skapa en regel samling för nätverk Lägg till följande regler:

   - Tillåt DNS – Tillåt trafik från din privata IP-adress till * för TCP-och UDP-portarna 53.
   - Tillåt KMS – Tillåt trafik från virtuella Windows-datorer med virtuella datorer till Windows Activation Service TCP-port 1688. Mer information om mål-IP-adresser finns i [Windows-aktivering Miss lyckas i Tvingad tunnel trafik](/troubleshoot/azure/virtual-machines/custom-routes-enable-kms-activation#solution).

> [!NOTE]
> Vissa distributioner kanske inte behöver DNS-regler, till exempel Azure Active Directory domänkontrollanter vidarebefordrar DNS-frågor till Azure DNS på 168.63.129.16.

## <a name="host-pool-outbound-access-to-the-internet"></a>Utgående värd pool till Internet

Beroende på organisationens behov kanske du vill aktivera säker utgående Internet åtkomst för slutanvändarna. I de fall där listan över tillåtna mål är väldefinierad (till exempel [Microsoft 365 åtkomst](/microsoft-365/enterprise/microsoft-365-ip-web-service)) kan du använda Azures brand Väggs program och nätverks regler för att konfigurera den nödvändiga åtkomsten. Detta dirigerar slut användar trafik direkt till Internet för bästa möjliga prestanda.

Om du vill filtrera utgående användares Internet trafik med en befintlig lokal säker webbgateway kan du konfigurera webbläsare eller andra program som körs på Windows-poolen för virtuella skriv bord med en explicit proxykonfiguration. Se till exempel att [använda kommando rads alternativ från Microsoft Edge för att konfigurera proxyinställningar](/deployedge/edge-learnmore-cmdline-options-proxy-settings). Dessa proxyinställningar påverkar bara din slutanvändares Internet-åtkomst, vilket tillåter att utgående trafik i Windows Virtual Desktop Platform direkt via Azure-brandväggen.

## <a name="additional-considerations"></a>Annat som är bra att tänka på

Du kan behöva konfigurera ytterligare brand Väggs regler, beroende på dina krav:

- Åtkomst till NTP-server

   Som standard ansluter virtuella datorer som kör Windows till time.windows.com via UDP-port 123 för tidssynkronisering. Skapa en nätverks regel för att tillåta den här åtkomsten eller för en tids server som du använder i din miljö.


## <a name="next-steps"></a>Nästa steg

- Läs mer om Windows Virtual Desktop: [Vad är Windows Virtual Desktop?](../virtual-desktop/overview.md)