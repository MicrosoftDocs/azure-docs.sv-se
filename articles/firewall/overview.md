---
title: Vad är Azure Firewall?
description: Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc, contperf-fy21q1
ms.date: 03/10/2021
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: 0982f0293b452c29a1c9fbb46cb24d47e70c0f5e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102615575"
---
# <a name="what-is-azure-firewall"></a>Vad är Azure Firewall?

![ICSA-certifiering](media/overview/icsa-cert-firewall-small.png)

Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser. Det är en fullständigt tillståndskänslig tjänst med inbyggd hög tillgänglighet och obegränsad molnskalbarhet.

![Översikt över brandväggar](media/overview/firewall-threat.png)

Du kan centralt skapa, framtvinga och logga principer för tillämpning och nätverksanslutning över prenumerationer och virtuella nätverk. Azure Firewall använder en statisk offentlig IP-adress för din virtuella nätverksresurser som tillåter att externa brandväggar identifierar trafik som kommer från ditt virtuella nätverk.  Tjänsten är helt integrerad med Azure Monitor för loggning och analys.

Information om Azure Firewall-funktioner finns i [Azure Firewall-funktioner](features.md).

## <a name="azure-firewall-premium-preview"></a>För hands version av Azure Firewall Premium

Azure Firewall Premium Preview är en nästa generations brand vägg med funktioner som krävs för mycket känsliga och reglerade miljöer. Dessa funktioner omfattar TLS-inspektion, IDP: er, URL-filtrering och webb kategorier.

Information om för hands versions funktionerna i Azure Firewall Premium finns i för [hands versionen av Azure Firewall Premium](premium-features.md).


För att se hur för hands versionen av brand väggen är konfigurerad i Azure Portal, se [Azure Firewall Premium Preview i Azure Portal](premium-portal.md).


## <a name="pricing-and-sla"></a>Priser och service nivå avtal

Pris information om Azure-brandväggen finns i [prissättning för Azure-brandvägg](https://azure.microsoft.com/pricing/details/azure-firewall/).

Information om SLA för Azure-brandväggen finns i [SLA för Azure Firewall](https://azure.microsoft.com/support/legal/sla/azure-firewall/).

## <a name="whats-new"></a>Nyheter

Information om vad som är nytt med Azure-brandväggen finns i [Azure updates](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Firewall).


## <a name="known-issues"></a>Kända problem

Azure Firewall har följande kända problem:

|Problem  |Beskrivning  |Åtgärd  |
|---------|---------|---------|
|Om du uppdaterar en regel från IP-adress till IP-grupp eller vice versa med portalen, sparas båda typerna, men bara en visas på portalen.|Det här problemet uppstår med klassiska regler.<br><br>När du använder portalen för att uppdatera en NAT-regels käll typ från IP-adress till IP-grupp eller vice versa sparar den båda typerna i Server delen men visar bara den nyligen uppdaterade typen.<br><br>Samma problem uppstår när du uppdaterar en nätverks-eller program regels mål typ från IP-adress till IP-grupptyp eller vice versa.|En portal korrigering är avsedd för mars 2021.<br><br>Under tiden använder du Azure PowerShell, Azure CLI eller API för att ändra en regel från IP-adress till IP-grupp eller vice versa.|
|Nätverksfiltreringsregler för icke-TCP-/UDP-protokoll (till exempel ICMP) fungerar inte för Internetbunden trafik|Nätverks filtrerings regler för icke-TCP/UDP-protokoll fungerar inte med SNAT till din offentliga IP-adress. Icke-TCP-/UDP-protokoll stöds mellan ekerundernät och virtuella nätverk.|Azure Firewall använder Standard Load Balancer, [som för närvarande inte stöder SNAT för IP-protokoll](../load-balancer/load-balancer-overview.md). Vi ska utforska alternativ för att stödja det här scenariot i en framtida version.|
|Saknat PowerShell- och CLI-stöd för ICMP|Azure PowerShell och CLI stöder inte ICMP som ett giltigt protokoll i nätverks regler.|Det går fortfarande att använda ICMP som protokoll via portalen och REST API. Vi arbetar snart med att lägga till ICMP i PowerShell och CLI.|
|FQDN-taggar kräver att protokoll: port anges|Program regler med FQDN-Taggar kräver port: protokoll definition.|Du kan använda **https** som port: protokoll-värde. Vi arbetar för att göra det här fältet valfritt när FQDN-Taggar används.|
|Det finns inte stöd för att flytta en brand vägg till en annan resurs grupp eller prenumeration|Det finns inte stöd för att flytta en brand vägg till en annan resurs grupp eller prenumeration.|Stöd för den här funktionen finns i vår planering. För att kunna flytta en brandvägg till en annan resursgrupp eller prenumeration måste du ta bort den aktuella instansen och återskapa den i den nya resursgruppen eller prenumerationen.|
|Hot informations aviseringar kan komma att maskeras|Nätverks regler med destination 80/443 för utgående filtrering maskar hot informations aviseringar när de är konfigurerade för enbart aviserings läge.|Skapa utgående filtrering för 80/443 med hjälp av program regler. Eller så kan du ändra hot informations läge till **varning och neka**.|
|Azure Firewall-DNAT fungerar inte för privata IP-mål|Azure Firewall DNAT-stöd är begränsat till utgående/ingångs händelser på Internet. DNAT fungerar för närvarande inte för privata IP-mål. Till exempel eker till ekrar.|Detta är en aktuell begränsning.|
|Det går inte att ta bort den första offentliga IP-konfigurationen|Varje offentlig IP-adress för Azure Firewall tilldelas en *IP-konfiguration*.  Den första IP-konfigurationen tilldelas under brand Väggs distributionen och innehåller vanligt vis en referens till brand Väggs under nätet (om den inte uttryckligen har kon figurer ATS på annat sätt via en mall distribution). Du kan inte ta bort den här IP-konfigurationen eftersom den inte skulle allokera brand väggen. Du kan fortfarande ändra eller ta bort den offentliga IP-adressen som är associerad med den här IP-konfigurationen om brand väggen har minst en offentlig IP-adress som är tillgänglig för användning.|Det här är avsiktligt.|
|Tillgänglighets zoner kan bara konfigureras under distributionen.|Tillgänglighets zoner kan bara konfigureras under distributionen. Du kan inte konfigurera Tillgänglighetszoner när en brand vägg har distribuerats.|Det här är avsiktligt.|
|SNAT på inkommande anslutningar|Förutom DNAT är anslutningar via den offentliga brand Väggs adressen (inkommande) SNATed till en av brand väggens privata IP-adresser. Detta krav idag (även för aktiva/aktiva NVA) för att säkerställa symmetrisk routning.|Överväg att använda [XFF](https://en.wikipedia.org/wiki/X-Forwarded-For) -rubriker för att bevara den ursprungliga källan för http/S. Du kan till exempel använda en tjänst som till exempel [Azure-frontend](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) eller [Azure Application Gateway](../application-gateway/rewrite-http-headers.md) framför brand väggen. Du kan också lägga till WAF som en del av Azures frontend-dörr och-kedja i brand väggen.
|Stöd för SQL-FQDN-filtrering i proxyläge (port 1433)|För Azure SQL Database, Azure Synapse Analytics och Azure SQL-hanterad instans:<br><br>SQL-FQDN-filtrering stöds endast i proxy-läge (port 1433).<br><br>För Azure SQL-IaaS:<br><br>Om du använder portar som inte är standard kan du ange dessa portar i program reglerna.|För SQL i omdirigeringsläge (standard om du ansluter inifrån Azure) kan du i stället Filtrera åtkomst med SQL-tjänstprogrammet som en del av nätverks reglerna i Azure Firewall.
|Utgående trafik på TCP-port 25 tillåts inte| Utgående SMTP-anslutningar som använder TCP-port 25 blockeras. Port 25 används främst för oautentiserad e-postleverans. Detta är standard beteendet för plattformen för virtuella datorer. Mer information finns i mer [fel sökning av utgående SMTP-anslutningsproblem i Azure](../virtual-network/troubleshoot-outbound-smtp-connectivity.md). Till skillnad från virtuella datorer är det dock inte möjligt för tillfället att aktivera den här funktionen i Azure-brandväggen. Obs! Om du vill tillåta autentiserad SMTP (port 587) eller SMTP över en annan port än 25, se till att du konfigurerar en nätverks regel och inte en program regel eftersom SMTP-kontrollen inte stöds för tillfället.|Följ den rekommenderade metoden för att skicka e-post, enligt beskrivningen i artikeln SMTP-felsökning. Eller utelämna den virtuella datorn som behöver utgående SMTP-åtkomst från din standard väg till brand väggen. Konfigurera i stället utgående åtkomst direkt till Internet.
|Mått för SNAT-port användning visar 0%|Måttet för Azure Firewall SNAT-port användning kan visa 0% användning även när SNAT-portar används. I det här fallet är det ett felaktigt resultat om du använder måttet som en del av hälso måttet för brand väggen.|Det här problemet har åtgärd ATS och distributionen till produktion är avsedd för maj 2020. I vissa fall löser brand Väggs distributionen problemet, men det är inte konsekvent. Som en mellanliggande lösning använder du endast brand väggens hälso tillstånd för att söka efter *status = degraderad*, inte för *status = ej felfri*. Port överbelastning visas som *degraderad*. *Inte felfri* är reserverad för framtida användning när är fler mått för att påverka brand väggens hälsa.
|DNAT stöds inte med Tvingad tunnel trafik aktive rad|Brand väggar som distribueras med Tvingad tunnel trafik aktive rad kan inte stödja inkommande åtkomst från Internet på grund av asymmetrisk routning.|Detta är avsiktligt på grund av asymmetrisk routning. Retur Sök vägen för inkommande anslutningar skickas via den lokala brand väggen, vilket inte visade anslutningen.
|Utgående passiv FTP kanske inte fungerar för brand väggar med flera offentliga IP-adresser, beroende på din FTP-serverkonfiguration.|Passiv FTP upprättar olika anslutningar för kontroll-och data kanaler. När en brand vägg med flera offentliga IP-adresser skickar data utgående väljer den slumpmässigt en av dess offentliga IP-adresser för käll-IP-adressen. FTP kan Miss lyckas när data-och kontroll kanaler använder olika käll-IP-adresser, beroende på din FTP-serverkonfiguration.|En explicit SNAT-konfiguration planeras. Under tiden kan du Konfigurera FTP-servern så att den accepterar data-och kontroll kanaler från olika käll-IP-adresser (se [ett exempel för IIS](/iis/configuration/system.applicationhost/sites/sitedefaults/ftpserver/security/datachannelsecurity)). Du kan också överväga att använda en enda IP-adress i den här situationen.|
|Inkommande passiv FTP kanske inte fungerar beroende på din FTP-serverkonfiguration |Passiv FTP upprättar olika anslutningar för kontroll-och data kanaler. Inkommande anslutningar i Azure Firewall är SNATed till en brand Väggs privata IP-adresser för att säkerställa symmetrisk routning. FTP kan Miss lyckas när data-och kontroll kanaler använder olika käll-IP-adresser, beroende på din FTP-serverkonfiguration.|Att bevara den ursprungliga käll-IP-adressen unders öks. Under tiden kan du Konfigurera FTP-servern så att den accepterar data-och kontroll kanaler från olika käll-IP-adresser.|
|NetworkRuleHit-måttet saknar en protokoll dimension|ApplicationRuleHit-måttet tillåter filtrering baserat protokoll, men den här funktionen saknas i motsvarande NetworkRuleHit-mått.|En korrigering undersökas.|
|NAT-regler med portar mellan 64000 och 65535 stöds inte|Azure-brandväggen tillåter alla portar i 1-65535-intervallet i nätverks-och program regler, men NAT-regler stöder bara portar i 1-63999-intervallet.|Detta är en aktuell begränsning.
|Konfigurations uppdateringar kan ta fem minuter i genomsnitt|En konfigurations uppdatering för Azure Firewall kan ta tre till fem minuter i genomsnitt, och parallella uppdateringar stöds inte.|En korrigering undersökas.|
|Azure-brandväggen använder SNI TLS-huvuden för att filtrera HTTPS-och MSSQL-trafik|Om webb läsar-eller Server program inte stöder tillägget server namns indikator (SNI) kan du inte ansluta via Azure-brandväggen.|Om webb läsar-eller Server program inte har stöd för SNI kan du kontrol lera anslutningen med en nätverks regel i stället för en program regel. Se [servernamnindikator](https://wikipedia.org/wiki/Server_Name_Indication) för program vara som stöder SNI.|
|Anpassad DNS fungerar inte med Tvingad tunnel trafik|Om Tvingad tunnel trafik är aktive rad fungerar inte anpassad DNS.|En korrigering undersökas.|
|Starta/stoppa fungerar inte med en brand vägg som kon figurer ATS i Tvingad tunnel läge|Starta/stoppa fungerar inte med Azure-brandväggen konfigurerad i Tvingad tunnel läge. Försök att starta Azure-brandväggen med Tvingad tunnel trafik har kon figurer ATS i följande fel:<br><br>*Set-AzFirewall: AzureFirewall VB-XX Management IP-konfiguration kan inte läggas till i en befintlig brand vägg. Distribuera om med en hanterings-IP-konfiguration om du vill använda Tvingad tunnel trafik. <br> StatusCode: 400 <br> ReasonPhrase: felaktig begäran*|Under undersökning.<br><br>Som en lösning kan du ta bort den befintliga brand väggen och skapa en ny med samma parametrar.|
|Det går inte att lägga till brand Väggs princip Taggar med portalen|Azure Firewall-principen har en begränsning för korrigerings stöd som förhindrar att du lägger till en tagg med hjälp av Azure Portal. Följande fel genereras: *Det gick inte att spara taggarna för resursen*.|En korrigering undersökas. Du kan också använda Azure PowerShell-cmdlet: en `Set-AzFirewallPolicy` för att uppdatera taggar.|
|IPv6 stöds ännu inte|Om du lägger till en IPv6-adress i en regel, Miss lyckas brand väggen.|Använd endast IPv4-adresser. IPv6-stöd är under undersökning.|


## <a name="next-steps"></a>Nästa steg

- [Snabb start: skapa en Azure-brandvägg och en brand Väggs princip – ARM-mall](../firewall-manager/quick-firewall-policy.md)
- [Snabb start: Distribuera Azure-brandväggen med Tillgänglighetszoner ARM-mall](deploy-template.md)
- [Självstudie: Distribuera och konfigurera Azure Firewall via Azure Portal](tutorial-firewall-deploy-portal.md)