---
title: Vad är Azure Firewall?
description: Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc, contperf-fy21q1
ms.date: 04/20/2021
ms.author: victorh
ms.openlocfilehash: 9ed46af7e4c62b2b7213b9e7a3d71c1b4776c806
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835352"
---
# <a name="what-is-azure-firewall"></a>Vad är Azure Firewall?

![ICSA-certifiering](media/overview/icsa-cert-firewall-small.png)

Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser. Det är en fullständigt tillståndskänslig tjänst med inbyggd hög tillgänglighet och obegränsad molnskalbarhet.

![Översikt över brandväggar](media/overview/firewall-threat.png)

Du kan centralt skapa, framtvinga och logga principer för tillämpning och nätverksanslutning över prenumerationer och virtuella nätverk. Azure Firewall använder en statisk offentlig IP-adress för din virtuella nätverksresurser som tillåter att externa brandväggar identifierar trafik som kommer från ditt virtuella nätverk.  Tjänsten är helt integrerad med Azure Monitor för loggning och analys.

Mer information om Azure Firewall finns i [Azure Firewall funktioner](features.md).

## <a name="azure-firewall-premium-preview"></a>Azure Firewall Premium Preview

Azure Firewall Premium Preview är en nästa generations brandvägg med funktioner som krävs för mycket känsliga och reglerade miljöer. Dessa funktioner omfattar TLS-inspektion, IDPS, URL-filtrering och webbkategorier.

Mer information om Azure Firewall Premium-förhandsgranskningsfunktioner finns i [Azure Firewall Förhandsgranskningsfunktioner i Premium.](premium-features.md)


Om du vill se hur förhandsgranskningen av Firewall Premium konfigureras i Azure Portal, se [Azure Firewall Premium Preview i Azure Portal](premium-portal.md).


## <a name="pricing-and-sla"></a>Priser och serviceavtal

Mer Azure Firewall prisinformation finns i [Azure Firewall prissättning.](https://azure.microsoft.com/pricing/details/azure-firewall/)

Information Azure Firewall serviceavtal finns i [Azure Firewall serviceavtal](https://azure.microsoft.com/support/legal/sla/azure-firewall/).

## <a name="whats-new"></a>Nyheter

Information om vad som är nytt med Azure Firewall finns i [Azure-uppdateringar.](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Firewall)


## <a name="known-issues"></a>Kända problem

Azure Firewall har följande kända problem:

|Problem  |Beskrivning  |Åtgärd  |
|---------|---------|---------|
|Nätverksfiltreringsregler för icke-TCP-/UDP-protokoll (till exempel ICMP) fungerar inte för Internetbunden trafik|Nätverksfiltreringsregler för icke-TCP/UDP-protokoll fungerar inte med SNAT till din offentliga IP-adress. Icke-TCP-/UDP-protokoll stöds mellan ekerundernät och virtuella nätverk.|Azure Firewall använder Standard Load Balancer, [som för närvarande inte stöder SNAT för IP-protokoll](../load-balancer/load-balancer-overview.md). Vi utforskar alternativ för att stödja det här scenariot i en framtida version.|
|Saknat PowerShell- och CLI-stöd för ICMP|Azure PowerShell och CLI stöder inte ICMP som ett giltigt protokoll i nätverksregler.|Det går fortfarande att använda ICMP som protokoll via portalen och REST API. Vi arbetar med att lägga till ICMP i PowerShell och CLI snart.|
|FQDN-taggar kräver att protokoll: port anges|Programregler med FQDN-taggar kräver port: protokolldefinition.|Du kan använda **https** som port: protokoll-värde. Vi arbetar med att göra det här fältet valfritt när FQDN-taggar används.|
|Det går inte att flytta en brandvägg till en annan resursgrupp eller prenumeration|Det går inte att flytta en brandvägg till en annan resursgrupp eller prenumeration.|Stöd för den här funktionen finns i vår planering. För att kunna flytta en brandvägg till en annan resursgrupp eller prenumeration måste du ta bort den aktuella instansen och återskapa den i den nya resursgruppen eller prenumerationen.|
|Aviseringar om hotinformation kan bli maskerade|Nätverksregler med mål 80/443 för utgående filtrering maskerar hotinformationsaviseringar när de är konfigurerade för endast aviseringsläge.|Skapa utgående filtrering för 80/443 med hjälp av programregler. Eller ändra hotinformationsläget till **Avisering och Neka**.|
|Azure Firewall DNAT fungerar inte för privata IP-mål|Azure Firewall DNAT-stöd är begränsat till utgående/ingress från Internet. DNAT fungerar för närvarande inte för privata IP-mål. Till exempel eker till eker.|Det här är en aktuell begränsning.|
|Det går inte att ta bort den första offentliga IP-konfigurationen|Varje Azure Firewall offentlig IP-adress tilldelas till en *IP-konfiguration*.  Den första IP-konfigurationen tilldelas under brandväggsdistributionen och innehåller vanligtvis även en referens till brandväggsundernätet (om det inte uttryckligen konfigureras annorlunda via en malldistribution). Du kan inte ta bort den här IP-konfigurationen eftersom den skulle av allokera brandväggen. Du kan fortfarande ändra eller ta bort den offentliga IP-adress som är associerad med den här IP-konfigurationen om brandväggen har minst en annan offentlig IP-adress som kan användas.|Det här är avsiktligt.|
|Tillgänglighetszoner kan bara konfigureras under distributionen.|Tillgänglighetszoner kan bara konfigureras under distributionen. Du kan inte konfigurera Tillgänglighetszoner när en brandvägg har distribuerats.|Det här är avsiktligt.|
|SNAT på inkommande anslutningar|Förutom DNAT är anslutningar via brandväggens offentliga IP-adress (inkommande) SNATed till en av brandväggens privata IP-adresser. Det här kravet i dag (även för aktiva/aktiva NVA:er) för att säkerställa symmetrisk routning.|Överväg att använda [XFF-huvuden](https://en.wikipedia.org/wiki/X-Forwarded-For) för att bevara den ursprungliga källan för HTTP/S. Du kan till exempel använda en [Azure Front Door](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) [eller Azure Application Gateway](../application-gateway/rewrite-http-headers.md) framför brandväggen. Du kan också lägga till WAF som en del Azure Front Door och kedja i brandväggen.
|SQL FQDN-filtreringsstöd endast i proxyläge (port 1433)|För Azure SQL Database, Azure Synapse Analytics och Azure SQL Managed Instance:<br><br>SQL FQDN-filtrering stöds endast i proxyläge (port 1433).<br><br>För Azure SQL IaaS:<br><br>Om du använder icke-standardportar kan du ange dessa portar i programreglerna.|För SQL i omdirigeringsläge (standard om du ansluter inifrån Azure) kan du i stället filtrera åtkomst med hjälp av SQL-tjänsttaggen som en del Azure Firewall nätverksregler.
|Utgående trafik på TCP-port 25 tillåts inte| Utgående SMTP-anslutningar som använder TCP-port 25 blockeras. Port 25 används främst för oauticerad e-postleverans. Det här är standardplattformsbeteendet för virtuella datorer. Mer information finns i Felsöka problem [med utgående SMTP-anslutning i Azure.](../virtual-network/troubleshoot-outbound-smtp-connectivity.md) Men till skillnad från virtuella datorer är det för närvarande inte möjligt att aktivera den här funktionen på Azure Firewall. Obs! Om du vill tillåta autentiserad SMTP (port 587) eller SMTP via en annan port än 25 måste du konfigurera en nätverksregel och inte en programregel eftersom SMTP-inspektion inte stöds just nu.|Följ den rekommenderade metoden för att skicka e-post, enligt vad som beskrivs i felsökningsartikeln för SMTP. Eller så kan du undanta den virtuella dator som behöver utgående SMTP-åtkomst från standardvägen till brandväggen. Konfigurera i stället utgående åtkomst direkt till Internet.
|SNAT-portutmattning|Azure Firewall stöder för närvarande 1 024 portar per offentlig IP-adress per instans av VM-skalningsuppsättningen på serverdatorn. Som standard finns det två VMSS-instanser.|Det här är en SLB-begränsning och vi letar ständigt efter möjligheter att öka gränserna. Under tiden rekommenderar vi att du konfigurerar Azure Firewall med minst fem offentliga IP-adresser för distributioner som är sårbara för SNAT-uttömning. Detta ökar antalet tillgängliga SNAT-portar med fem gånger. Allokera från ett IP-adressprefix för att förenkla underordnade behörigheter.|
|DNAT stöds inte med tvingad tunneling aktiverat|Brandväggar som distribueras med tvingad tunneling aktiverad har inte stöd för inkommande åtkomst från Internet på grund av asymmetrisk routning.|Detta beror på asymmetrisk routning. Retursökvägen för inkommande anslutningar går via den lokala brandväggen, som inte har sett anslutningen upprättad.
|Utgående passiv FTP kanske inte fungerar för brandväggar med flera offentliga IP-adresser, beroende på FTP-serverkonfigurationen.|Passiv FTP upprättar olika anslutningar för kontroll- och datakanaler. När en brandvägg med flera offentliga IP-adresser skickar utgående data väljer den slumpmässigt en av dess offentliga IP-adresser för källans IP-adress. FTP kan misslyckas när data och kontrollkanaler använder olika IP-källadresser, beroende på din FTP-serverkonfiguration.|En explicit SNAT-konfiguration planeras. Under tiden kan du konfigurera FTP-servern så att den accepterar data och kontrollkanaler från olika käll-IP-adresser (se [ett exempel för IIS](/iis/configuration/system.applicationhost/sites/sitedefaults/ftpserver/security/datachannelsecurity)). Du kan också överväga att använda en enskild IP-adress i den här situationen.|
|Inkommande passiv FTP kanske inte fungerar beroende på ftp-serverkonfigurationen |Passiv FTP upprättar olika anslutningar för kontroll- och datakanaler. Inkommande anslutningar på Azure Firewall SNATed till en av brandväggens privata IP-adresser för att säkerställa symmetrisk routning. FTP kan misslyckas när data- och kontrollkanaler använder olika IP-källadresser, beroende på ftp-serverkonfigurationen.|Vi undersöker bevarandet av den ursprungliga KÄLL-IP-adressen. Under tiden kan du konfigurera FTP-servern så att den accepterar data och kontrollkanaler från olika IP-källadresser.|
|NetworkRuleHit-måttet saknar en protokolldimension|ApplicationRuleHit-måttet tillåter filtreringsbaserat protokoll, men den här funktionen saknas i motsvarande NetworkRuleHit-mått.|En korrigering undersöks.|
|NAT-regler med portar mellan 64000 och 65535 stöds inte|Azure Firewall tillåter alla portar i intervallet 1–65535 i nätverks- och programregler, men NAT-regler stöder endast portar i intervallet 1–63999.|Det här är en aktuell begränsning.
|Konfigurationsuppdateringar kan ta i genomsnitt fem minuter|En Azure Firewall uppdatering av konfigurationen kan ta i genomsnitt tre till fem minuter, och parallella uppdateringar stöds inte.|En korrigering undersöks.|
|Azure Firewall använder SNI TLS-huvuden för att filtrera HTTPS- och MSSQL-trafik|Om webbläsaren eller serverprogramvaran inte stöder SNI-tillägget (Server name indicator) kan du inte ansluta via Azure Firewall.|Om webbläsare eller serverprogramvara inte stöder SNI kan du kontrollera anslutningen med hjälp av en nätverksregel i stället för en programregel. Se [Servernamnindikator](https://wikipedia.org/wiki/Server_Name_Indication) programvara som stöder SNI.|
|Anpassad DNS fungerar inte med tvingad tunneling|Om tvingad tunneling är aktiverat fungerar inte anpassad DNS.|En korrigering undersöks.|
|Start/stopp fungerar inte med en brandvägg som konfigurerats i tvingad tunnelläge|Start/stopp fungerar inte med Azure Firewall som konfigurerats i tvingad tunnelläge. Försök att starta Azure Firewall med konfigurerad tvingad tunneling resulterar i följande fel:<br><br>*Set-AzFirewall: AzureFirewall FW-xx-hanterings-IP-konfiguration kan inte läggas till i en befintlig brandvägg. Distribuera om med en IP-hanteringskonfiguration om du vill använda stöd för tvingad tunneldirigering. <br> StatusCode: 400 <br> ReasonPhrase: Felaktig begäran*|Under undersökning.<br><br>Som en lösning kan du ta bort den befintliga brandväggen och skapa en ny med samma parametrar.|
|Det går inte att lägga till brandväggsprinciptaggar med hjälp av portalen|Azure Firewall princip har en begränsning för korrigeringsstöd som förhindrar att du lägger till en tagg med hjälp av Azure Portal. Följande fel genereras: *Det gick inte att spara taggarna för resursen*.|En korrigering undersöks. Du kan också använda cmdleten Azure PowerShell för `Set-AzFirewallPolicy` att uppdatera taggar.|
|IPv6 stöds inte än|Om du lägger till en IPv6-adress i en regel misslyckas brandväggen.|Använd endast IPv4-adresser. IPv6-stöd är under undersökning.|
|Uppdatering av flera IP-grupper misslyckas med konfliktfel.|När du uppdaterar två eller flera IPGroups som är kopplade till samma brandvägg hamnar en av resurserna i ett misslyckat tillstånd.|Detta är ett känt problem/en begränsning. <br><br>När du uppdaterar en IPGroup utlöses en uppdatering för alla brandväggar som IPGroup är kopplad till. Om en uppdatering till en andra IPGroup startas  medan brandväggen fortfarande är i uppdateringstillstånd misslyckas IPGroup-uppdateringen.<br><br>För att undvika felet måste IPGroups som är kopplade till samma brandvägg uppdateras en i taget. Tillåt tillräckligt med tid mellan uppdateringarna för att brandväggen ska kunna ta sig ur *uppdateringstillståndet.*| 


## <a name="next-steps"></a>Nästa steg

- [Snabbstart: Skapa en Azure Firewall och en brandväggsprincip – ARM-mall](../firewall-manager/quick-firewall-policy.md)
- [Snabbstart: Distribuera Azure Firewall med Tillgänglighetszoner – ARM-mall](deploy-template.md)
- [Självstudie: Distribuera och konfigurera Azure Firewall via Azure Portal](tutorial-firewall-deploy-portal.md)