---
title: Azures säkerhets bas linje för Azure Cloud Services
description: Azure Cloud Services Security-datalinjen ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: cloud-services
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 105fd185900692669fdc40e0c47a03474524e250
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731110"
---
# <a name="azure-security-baseline-for-azure-cloud-services"></a>Azures säkerhets bas linje för Azure Cloud Services

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 1,0](../security/benchmarks/overview-v1.md) till Microsoft Azure Cloud Services. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure.
Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Cloud Services. **Kontroller** som inte är tillämpliga på Cloud Services har uteslutits.

 
Om du vill se hur Cloud Services helt mappar till Azures säkerhets mätning, se den [fullständiga Cloud Services mappnings filen för säkerhets bas linjen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: skapa en klassisk Azure-Virtual Network med separata offentliga och privata undernät för att upprätthålla isolering baserat på betrodda portar och IP-adressintervall. Dessa virtuella nätverk och undernät måste vara de klassiska Virtual Network-baserade (klassiska distributioner) resurserna och inte de aktuella Azure Resource Manager resurserna.  

Tillåt eller neka trafik med en nätverks säkerhets grupp som innehåller regler för åtkomst kontroll baserat på trafik riktning, protokoll, käll adress och port samt mål adress och port. Reglerna för en nätverks säkerhets grupp kan ändras när som helst, och ändringarna tillämpas på alla associerade instanser.

Microsoft Azure Cloud Services (klassisk) kan inte placeras i Azure Resource Manager virtuella nätverk. Resource Manager-baserade virtuella nätverk och klassiska virtuella nätverk kan dock anslutas via peering. 

- [Översikt över nätverks säkerhets grupp](../virtual-network/network-security-groups-overview.md)

- [Peering för virtuella nätverk](https://docs.microsoft.com/azure/cloud-services/cloud-services-connectivity-and-networking-faq#how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

**Vägledning**: dokumentera din Azure Cloud Services-konfiguration och övervaka den för ändringar. Använd tjänstens konfigurations fil för att ange antalet roll instanser som ska distribueras för varje roll i tjänsten, värdena för alla konfigurations inställningar och tumavtrycken för alla certifikat som är associerade med en roll. 

Om tjänsten ingår i ett virtuellt nätverk måste konfigurations information för nätverket anges i tjänst konfigurations filen, samt i konfigurations filen för det virtuella nätverket. Standard tillägget för tjänst konfigurations filen är. cscfg. Observera att Azure Policy inte stöds för klassiska distributioner för konfigurations tvång.

Ange konfigurations värden för en moln tjänst i tjänst konfigurations filen (. cscfg) och definitionen i en tjänst definitions fil (. csdef). Använd tjänst definitions filen för att definiera tjänst modellen för ett program. Definiera de roller som är tillgängliga för en moln tjänst och ange även tjänstens slut punkter. Logga konfigurationen för Azure Cloud Services med tjänst konfigurations filen. Eventuell omkonfiguration kan göras via filen ServiceConfig. cscfg. 

Övervaka den valfria NetworkTrafficRules element service-definitionen som begränsar vilka roller som kan kommunicera med angivna interna slut punkter. Konfigurera NetworkTrafficRules-noden, ett valfritt element i tjänst definitions filen, för att ange hur roller ska kommunicera med varandra. Placera gränser för vilka roller som har åtkomst till de interna slut punkterna för den aktuella rollen.  Observera att tjänst definitionen inte kan ändras. 

Aktivera flödes loggar för nätverks säkerhets grupper och skicka loggarna till ett Azure Storage konto för granskning. Skicka flödes loggarna till en Log Analytics arbets yta och Använd Trafikanalys för att ge insikter om trafik mönster i din Azure-klient. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet, identifiera frekventa fläckar och säkerhetshot, förstå trafikflödes mönster och hitta nätverks-felkonfigurationer.

- [Azure Resource Manager kontra klassisk distribution – förstå distributions modeller och status för dina resurser](../azure-resource-manager/management/deployment-models.md)

- [Cloud Services konfigurations fil](schema-cscfg-file.md)

- [Lista över tjänster som stöds av Azure Policy](/cli/azure/azure-services-the-azure-cli-can-manage)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: Microsoft använder Transport Layer Security (TLS) protokoll v 1.2 för att skydda data när de reser mellan Azure Cloud Services och kunder. Microsoft-datacentren förhandlar en TLS-anslutning med klient system som ansluter till Azure-tjänster. TLS ger stark autentisering, meddelande sekretess och integritet (aktiverar identifiering av meddelande manipulation, avlyssning och förfalskning), samverkan, algoritm flexibilitet och enkel distribution och användning.

- [Grundläggande kryptering](../security/fundamentals/encryption-overview.md)

- [Konfigurera TLS/SSL-certifikat](cloud-services-configure-ssl-certificate-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Azure Cloud implementerar en Multilayer nätverks säkerhet för att skydda sina plattforms tjänster mot distribuerade DDoS-attacker (Denial-of-Service). Azure DDoS Protection är en del av Azures molnets kontinuerliga övervaknings process, som kontinuerligt förbättras genom inträngande tester. Den här DDoS Protection är utformad för att motstå inte bara angrepp från utsidan utan även från andra Azure-klienter. 

Det finns flera olika sätt att blockera eller neka kommunikation förutom plattforms nivå skydd i Azure Cloud Services. Dessa är: 

-  Skapa en start aktivitet för att selektivt blockera vissa angivna IP-adresser
-  Begränsa en Azure-webbrolls åtkomst till en uppsättning angivna IP-adresser genom att ändra din IIS-web.config fil

Förhindra inkommande trafik till standard-URL: en eller namnet på din Cloud Services, till exempel *. cloudapp.net. Ange värd rubriken till ett anpassat DNS-namn under plats bindnings konfigurationen i Cloud Services definitions filen (*. csdef).

Konfigurera en neka-tillämpa regel för administratörs tilldelningar för klassiska prenumerationer. Som standard när en intern slut punkt har definierats kan kommunikationen flöda från vilken roll som helst till den interna slut punkten för en roll utan några begränsningar. Om du vill begränsa kommunikationen måste du lägga till ett NetworkTrafficRules-element i service definition-elementet i tjänst definitions filen.

- [Hur kan jag blockera/inaktivera inkommande trafik till standard-URL: en för min moln tjänst](https://docs.microsoft.com/azure/cloud-services/cloud-services-connectivity-and-networking-faq#how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service)

- [Azure DDOS-skydd](https://docs.microsoft.com/azure/cloud-services/cloud-services-connectivity-and-networking-faq#how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service)

- [Blockera en speciell IP-adress](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks-common#block-a-specific-ip-address)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="15-record-network-packets"></a>1,5: registrera nätverks paket

**Vägledning**: använd Azure Network Watcher, övervakning av nätverks prestanda, diagnostik och analys tjänster, som gör det möjligt att övervaka Azure-nätverk. Tillägget Network Watcher agent virtuell dator måste vara ett krav för att samla in nätverks trafik på begäran och andra avancerade funktioner på Azure Virtual Machines. Installera den virtuella datorn Network Watcher agent-tillägg och aktivera flödes loggar för nätverks säkerhets grupper.

Konfigurera flödes loggning i en nätverks säkerhets grupp. Läs mer om hur du distribuerar Network Watcher virtuell dators tillägg till en befintlig virtuell dator som distribueras via den klassiska distributions modellen.

- [Konfigurera flödes loggning i en nätverks säkerhets grupp](../virtual-machines/extensions/network-watcher-linux.md)

- [Mer information om hur du konfigurerar flödes loggar finns i](/cli/azure/azure-services-the-azure-cli-can-manage)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera nätverksbaserade intrångs identifiering/system för skydd mot intrång (ID/IP-adresser)

**Vägledning**: Azure Cloud Services saknar inbyggda ID eller IP-funktioner. Kunder kan välja och distribuera ett extra nätverksbaserade ID eller en IP-lösning från Azure Marketplace baserat på deras organisations krav. När du använder lösningar från tredje part måste du kontrol lera att du testar dina valda ID: n eller IP-lösningen med Azure Cloud Services för att säkerställa korrekt drift och funktionalitet.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: tjänst certifikat, som är anslutna till Azure Cloud Services, aktiverar säker kommunikation till och från tjänsten. Dessa certifikat definieras i definitionerna för tjänsterna och distribueras automatiskt till den virtuella datorn som kör en instans av en webb roll. Som exempel kan du använda ett tjänst certifikat som kan autentisera en exponerad HTTPS-slutpunkt för en webbroll. 

Om du vill uppdatera certifikatet är det bara nödvändigt att ladda upp ett nytt certifikat och ändra tumavtryck-värdet i tjänst konfigurations filen.

Använd TLS 1,2-protokollet, den vanligaste metoden för att skydda data för att tillhandahålla konfidentialitet och integritets skydd. 

För att skydda webb program och för att skydda dem mot attacker som OWASP topp 10 kan du distribuera en brand vägg för Azure-webbprogram som är aktive rad Azure Application Gateway för att skydda webb program. 

- [Tjänst certifikat](cloud-services-certs-create.md)

- [Konfigurera TLS för ett program i Azure](cloud-services-configure-ssl-certificate-portal.md)

- [Så här distribuerar du Application Gateway](../application-gateway/quick-create-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: härdning av Azure Cloud Services-konfigurationen och övervaka den för ändringar. Tjänst konfigurations filen anger antalet roll instanser som ska distribueras för varje roll i tjänsten, värdena för alla konfigurations inställningar och tumavtrycken för alla certifikat som är associerade med en roll. 

Om din tjänst ingår i ett virtuellt nätverk måste konfigurations informationen för nätverket anges i tjänst konfigurations filen, samt i konfigurations filen för det virtuella nätverket. Standard tillägget för tjänst konfigurations filen är. cscfg.

Observera att Azure Policy inte stöds med Azure Cloud Services för konfigurations tvång.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Azure-nätverks säkerhets grupper kan användas för att filtrera nätverks trafik till och från Azure-resurser i en azure-Virtual Network. En nätverks säkerhets grupp innehåller säkerhets regler som tillåter eller nekar inkommande nätverks trafik till, eller utgående nätverks trafik från, flera typer av Azure-resurser. För varje regel kan du ange källa och mål, port och protokoll.

Använd fältet Beskrivning för enskilda regler för nätverks säkerhets grupper i Azure Cloud Services för att dokumentera reglerna, som tillåter trafik till eller från ett nätverk.

- [Filtrera nätverks trafik med regler för nätverks säkerhets grupper](../virtual-network/tutorial-filter-network-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: använd Azure Traffic Managers inbyggda slut punkts övervakning och funktioner för automatisk slut punkt redundans. De hjälper dig att leverera program med hög tillgänglighet som är elastiska till slut punkter och fel i Azure-regioner. Om du vill konfigurera slut punkts övervakning måste du ange vissa inställningar i Traffic Manager profilen.

Samla in insikter från aktivitets loggen, en plattforms logg i Azure, i händelser på prenumerations nivå. Den innehåller sådan information som när en resurs ändras eller när en virtuell dator startas. Visa aktivitets loggen i Azure Portal eller hämta poster med PowerShell och CLI. 

Skapa en diagnostisk inställning för att skicka aktivitets loggen till Azure Monitor, Azure Event Hubs att vidarebefordra utanför Azure eller till Azure Storage för arkivering. Konfigurera Azure Monitor för aviserings aviseringar när kritiska resurser i Azure-Cloud Services ändras. 

- [Azure aktivitets logg](/azure/azure-monitor/platform/activity-log)

- [Skapa, Visa och hantera aktivitets logg aviseringar med hjälp av Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

- [Traffic Manager övervakning](../traffic-manager/traffic-manager-monitoring.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

**Vägledning**: Microsoft hanterar tids källor för Azure-resurser för Azure Cloud Services. Kunder kan behöva skapa en nätverks regel för att tillåta åtkomst till en tids server som används i deras miljö, via port 123 med UDP-protokoll.

- [Åtkomst till NTP-server](https://docs.microsoft.com/azure/firewall/protect-windows-virtual-desktop#additional-considerations)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: använda moln tjänsten för strömning av data program mässigt med Azure Event Hubs. Integrera och skicka alla dessa data till Azure Sentinel för att övervaka och granska dina loggar, eller Använd en SIEM från tredje part. För central hantering av säkerhets loggar konfigurerar du kontinuerlig export av dina valda Azure Security Center data till Azure Event Hubs och konfigurerar lämplig anslutning för din SIEM. Här följer några alternativ för Azure Sentinel, inklusive verktyg från tredje part:

- Azure Sentinel – Använd den inbyggda Security Center Alerts data Connector
- Splunk – Använd Azure Monitor-tillägget för Splunk
- IBM-QRadar – Använd en manuellt konfigurerad logg källa
- ArcSight – Använd SmartConnector

Läs Azure Sentinel-dokumentationen om du vill ha mer information om tillgängliga anslutningar med Azure Sentinel. 

- [Ansluta till datakällor](../sentinel/connect-data-sources.md)

- [Integrera med en SIEM](../security-center/continuous-export.md)

- [Lagra diagnostikdata](diagnostics-extension-to-storage.md)

- [Konfigurera SIEM-integrering via Azure Event Hubs](../security-center/continuous-export.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Konfigurera Visual Studio för att konfigurera Azure-diagnostik för fel sökning av Azure Cloud Services som fångar upp system-och loggnings data på virtuella datorer, inklusive virtuella dator instanser som kör Azure-Cloud Services. Diagnostikdata överförs till ett valfritt lagrings konto. Aktivera diagnostik i Azure Cloud Services-projekt innan deras distribution.

 
Visa ändrings historiken för vissa händelser i aktivitets loggen i Azure Monitor. Granska vilka ändringar som gjorts under en händelse tids period. Välj en händelse i aktivitets loggen för djupare kontroll med ändrings historik (för hands version) fliken. Skicka diagnostikdata till Application Insights när du publicerar en Azure-Cloud Services från Visual Studio. Skapa Application Insights Azure-resursen vid den tiden eller skicka data till en befintlig Azure-resurs. 

Azure Cloud Services kan övervakas av Application Insights för tillgänglighet, prestanda, haverier och användning. Du kan lägga till anpassade diagram i Application Insights så att du kan se de data som är mest viktiga. Roll instans data kan samlas in med hjälp av Application Insights SDK i Azure Cloud Services-projektet. 

- [Aktivera diagnostik i Visual Studio innan du distribuerar](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#to-turn-on-diagnostics-in-visual-studio-before-deployment)

- [Visa ändrings historik](/azure/azure-monitor/platform/activity-log#view-change-history)

- [Application Insights för Azure Cloud Service (klassisk)](../azure-monitor/app/cloudservices.md)

- [Konfigurera diagnostik för Azure Cloud Service (klassisk) och virtuella datorer](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: du kan använda avancerad övervakning med Azure Cloud Services som gör det möjligt att sampla och samla in ytterligare mått i intervall på 5 minuter, 1 timme och 12 timmar. De aggregerade data lagras i lagrings kontot i tabeller och rensas efter 10 dagar. Det lagrings konto som används konfigureras dock av rollen och du kan använda olika lagrings konton för olika roller. Detta konfigureras med en anslutnings sträng i. csdef-och. cscfg-filerna.

Observera att avancerad övervakning innebär att använda Azure-diagnostik tillägget (Application Insights SDK är valfritt) på den roll som du vill övervaka. Tillägget för diagnostik använder en konfigurations fil (per roll) med namnet Diagnostics. wadcfgx för att konfigurera de diagnostik mått som övervakas. Azure Diagnostic-tillägget samlar in och lagrar data i ett Azure Storage-konto. Dessa inställningar konfigureras i. wadcfgx-,. csdef-och. cscfg-filerna.

- [Introduktion till moln tjänst övervakning](cloud-services-how-to-monitor.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: grundläggande eller avancerade övervaknings lägen är tillgängliga för Azure Cloud Services. Azure Cloud Services samlar automatiskt in grundläggande övervaknings data (CPU-procent, nätverk in/ut och disk läsning/skrivning) från en virtuell värd dator. Visa insamlade övervaknings data på sidan Översikt och mått i en moln tjänst i Azure Portal. 

Aktivera diagnostik i Azure Cloud Services för att samla in diagnostikdata som program loggar, prestanda räknare med mera, samtidigt som du använder tillägget Azure-diagnostik. Aktivera eller uppdatera diagnostikinställningar på en moln tjänst som redan körs med Set-AzureServiceDiagnosticsExtension-cmdlet eller distribuera en moln tjänst med diagnostiskt tillägg automatiskt. Du kan också installera Application Insights SDK. Skicka prestanda räknare till Azure Monitor.

Azure Diagnostic-tillägget samlar in och lagrar data i ett Azure Storage-konto. Överför diagnostikdata till Microsoft Azure Storage-emulator eller till Azure Storage eftersom de inte lagras permanent. När den är lagrad kan den visas med ett av flera tillgängliga verktyg, till exempel Server Explorer i Visual Studio Microsoft Azure Storage Explorer Azure Management Studio. Konfigurera diagnostiska mått som ska övervakas med en konfigurations fil (per roll) med namnet Diagnostics. wadcfgx i tillägget Diagnostics. 

- [Introduktion till moln tjänst övervakning](cloud-services-how-to-monitor.md)

- [Så här aktiverar du diagnostik i en arbets roll – integrera med en SIEM](../security-center/continuous-export.md)

- [Aktivera diagnostik i Azure Cloud Services med PowerShell](cloud-services-diagnostics-powershell.md)

- [Lagra och visa diagnostikdata i Azure Storage](https://docs.microsoft.com/azure/cloud-services/diagnostics-extension-to-storage?&amp;preserve-view=true)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: du kan övervaka Azure Cloud Services loggdata genom att integrera med Azure Sentinel eller med en Siem från tredje part genom att aktivera aviseringar för avvikande aktiviteter.

- [Integrera med en SIEM](../security-center/continuous-export.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: Microsoft Antimalware för Azure, skyddar Azure Cloud Services och virtuella datorer. Du har möjlighet att distribuera säkerhetslösningar från tredje part, till exempel brand väggar för webb program, nätverks brand väggar, program mot skadlig kod, intrångs identifiering och skydds system (ID eller IP-adresser) med mera.

- [Vilka är de funktioner och funktioner som Azure Basic IP-adresser och DDOS tillhandahåller](https://docs.microsoft.com/azure/cloud-services/cloud-services-configuration-and-management-faq#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security benchmark: identitet och Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: Microsoft rekommenderar att du hanterar åtkomst till Azure-resurser med hjälp av rollbaserad åtkomst kontroll i Azure (Azure RBAC). Azure Cloud Services stöder dock inte Azure RBAC-modellen eftersom den inte är en Azure Resource Manager baserad tjänst och du måste använda en klassisk prenumeration

Som standard är konto administratör, tjänst administratör och Co-Administrator de tre klassiska prenumerations administratörs rollerna i Azure. 

Administratörer för klassiska prenumerationer har fullständig åtkomst till Azure-prenumerationen. De kan hantera resurser med hjälp av Azure-portalen, Azure Resource Manager-API:er och den klassiska distributionsmodellens API:er. Det konto som används för att registrera sig för Azure anges automatiskt som både kontoadministratör och tjänstadministratör. Ytterligare Co-Administrators kan läggas till senare. 

Tjänst administratören och Co-Administrators har likvärdig åtkomst till användare som har tilldelats ägar rollen (en Azure-roll) i prenumerations omfånget. Hantera Co-Administrators eller Visa tjänst administratören med hjälp av fliken klassiska administratörer på Azure Portal. 

Lista roll tilldelningar för klassisk tjänst administratör och-administratörer med PowerShell med kommandot:

Get-AzRoleAssignment-IncludeClassicAdministrators

Granska skillnaderna mellan administrativa roller i den klassiska prenumerationen. 

- [Skillnader mellan tre klassiska prenumerationer administrativa roller](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles#classic-subscription-administrator-roles)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: Vi rekommenderar att du skapar standard procedurer kring användningen av dedikerade administrativa konton, baserat på tillgängliga roller och de behörigheter som krävs för att hantera och hantera Azure Cloud Services-resurser.

- [Skillnader mellan administrativa roller för den klassiska prenumerationen](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles#classic-subscription-administrator-roles)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: Undvik att hantera separata identiteter för program som körs på Azure Cloud Services. Använd enkel inloggning för att undvika att användarna måste hantera flera identiteter och autentiseringsuppgifter.

- [Vad är enkel inloggning (SSO)](../active-directory/manage-apps/what-is-single-sign-on.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Vi rekommenderar att du använder en säker, Azure-hanterad arbets Station (kallas även en privilegie rad åtkomst arbets Station) för administrativa uppgifter som kräver förhöjda privilegier.

- [Förstå säkra, Azure-hanterade arbets stationer](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Aktivera Azure Active Directory (Azure AD) multifaktorautentisering](../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd Azure Cloud Service REST-API: er för att inventera dina Azure Cloud Service-resurser för känslig information. Avsök de distribuerade moln tjänst resurserna för att hämta konfigurations-och. pkg-resurser.

 Till exempel visas några API: er nedan:

- Hämta distribution – åtgärden Hämta distribution returnerar konfigurations information, status och system egenskaper för en distribution.
- Hämta paket – åtgärden Hämta paket hämtar ett moln tjänst paket för en distribution och lagrar paketfilerna i Microsoft Azure Blob Storage
- Hämta egenskaper för moln tjänsten – med åtgärden Hämta moln tjänst egenskaper hämtas egenskaper för den angivna moln tjänsten

Läs dokumentationen för Azure Cloud Service REST API: er och skapa en process för data skydd av känslig information, baserat på organisationens krav.

- [Hämta distribution](/rest/api/compute/cloudservices/rest-get-deployment)

- [Hämta egenskaper för moln tjänst](/rest/api/compute/cloudservices/rest-get-cloud-service-properties)

- [Hämta paket](/rest/api/compute/cloudservices/rest-get-package)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera isolering med separata prenumerationer och hanterings grupper för enskilda säkerhets domäner som miljö typ och data känslighets nivå för Azure Cloud Services.

Du kan också redigera "permissionLevel" i Azure Cloud Service Certificate-elementet för att ange de åtkomst behörigheter som tilldelats roll processerna. Om du bara vill att utökade processer ska kunna komma åt den privata nyckeln anger du förhöjd behörighet. limitedOrElevated-behörighet tillåter alla roll processer att komma åt den privata nyckeln. Möjliga värden är limitedOrElevated eller utökade. Standardvärdet är limitedOrElevated.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du hanterings grupper](../governance/management-groups/create-management-group-portal.md)

- [WebRole-schema](https://docs.microsoft.com/azure/cloud-services/schema-csdef-webrole#Certificate)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: Vi rekommenderar att du använder en lösning från tredje part från Azure Marketplace i nätverks omkretser för att övervaka obehörig överföring av känslig information och blockera sådana överföringar samtidigt som du varnar information om information om säkerhets tekniker.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: Konfigurera TLS v2 för Azure Cloud Services. Använd Azure Portal för att lägga till certifikatet i den mellanlagrade Azure Cloud Services-distributionen och Lägg till certifikat informationen i tjänsterna CSDEF och CSCFG. Paketera ditt program igen och uppdatera din mellanlagrade distribution för att använda det nya paketet. 

Använd tjänst certifikat i Azure som är anslutna till Azure Cloud Services för att aktivera säker kommunikation till och från tjänsten. Ange ett certifikat som kan autentisera en exponerad HTTPS-slutpunkt. Definiera tjänst certifikat i moln tjänstens tjänst definition och distribuera dem automatiskt till den virtuella datorn, och kör en instans av din roll.

Autentisera med hanterings certifikat för hanterings-API med hanterings certifikat kan du autentisera med den klassiska distributions modellen. Många program och verktyg (som Visual Studio och Azure SDK) använder sådana här certifikat till att automatisera konfigurationen och distributionen av olika Azure-tjänster. 

För ytterligare referens ger den klassiska distributions modellens API programmerings åtkomst till den klassiska distributions modell funktionen som är tillgänglig via Azure Portal. Azure SDK för python kan användas för att hantera Azure-Cloud Services och Azure Storage-konton. Azure SDK för python radbryter den klassiska distributions modellens API, en REST API. Alla API-åtgärder utförs via TLS och autentiseras ömsesidigt genom att använda X. 509 v3-certifikat. Hanterings tjänsten kan nås inifrån en tjänst som körs i Azure. Det kan också nås direkt via Internet från alla program som kan skicka en HTTPS-begäran och få ett HTTPS-svar.

- [Konfigurera TLS för ett program i Azure](cloud-services-configure-ssl-certificate-portal.md)

- [Använd den klassiska distributions modellen från python](cloud-services-python-how-to-use-service-management.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: Vi rekommenderar att du använder ett aktivt identifierings verktyg från tredje part för att identifiera all känslig information som lagras, bearbetas eller överförs av organisationens teknik system, inklusive de som finns på plats eller på en fjärran sluten tjänst leverantör, och sedan uppdatera organisationens känsliga informations inventering.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: gäller inte för moln tjänst (klassisk). Det upprätthåller inte data förlust skydd.

Vi rekommenderar att du implementerar ett verktyg från tredje part, till exempel en automatiserad värdbaserade lösning för data förlust skydd för att tillämpa åtkomst kontroller på data även när data kopieras av ett system.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: Azure Cloud Services stöder inte kryptering vid vila. Detta beror på att Azure Cloud Services har utformats för att vara tillstånds lösa. Azure Cloud Services stöder extern lagring, till exempel Azure Storage, som är som standard krypterat i vila.  

Program data som lagras på temporära diskar är inte krypterade. Kunden ansvarar för att hantera och kryptera dessa data, om det behövs.  

- [Förstå kryptering vid vila i Azure](../security/fundamentals/encryption-atrest.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: du kan använda klassiska mått varningar i Azure Monitor för att få ett meddelande när ett av dina mått tillämpas på kritiska resurser mellan ett tröskelvärde. Klassiska mått varningar är en äldre funktion som endast tillåter aviseringar på icke-dimensionella mått. Det finns en befintlig ny funktion som kallas mått aviseringar som har förbättrade funktioner i klassiska mått varningar. 

Dessutom kan Application Insights övervaka Azure Cloud Services-appar för tillgänglighet, prestanda, haverier och användning. Detta använder kombinerade data från Application Insights SDK: er med Azure-diagnostik data från Azure-Cloud Services.

- [Skapa, Visa och hantera klassiska mått varningar med Azure Monitor](/azure/azure-monitor/platform/alerts-classic-portal)

- [Översikt över mått varningar](/azure/azure-monitor/platform/alerts-metric-overview) 

- [Application Insights för Azure Cloud Service (klassisk)](../azure-monitor/app/cloudservices.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security benchmark: sårbarhet Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: Observera att den här informationen är relaterad till Azure gäst operativ system för Azure Cloud Services Worker och webb roller med PaaS (Platform as a Service). Det gäller dock inte för Virtual Machines med IaaS (Infrastructure as a Service).

Som standard uppdaterar Azure regelbundet kundens gäst operativ system till den senaste avbildning som stöds i den operativ system familj som de har angett i tjänst konfigurationen (. cscfg), till exempel Windows Server 2016.

När en kund väljer en specifik operativ system version för sin Azure Cloud Services-distribution, inaktiverar den automatiska uppdateringar av operativ systemet och gör korrigeringen av deras ansvar. Kunden måste se till att deras roll instanser tar emot uppdateringar eller att de kan exponera sina program för säkerhets risker.

- [Azure gäst operativ system](cloud-services-guestos-msrc-releases.md)

- [Support och policy för Azure gäst operativ systemets support](cloud-services-guestos-retirement-policy.md)

- [Konfigurera en moln tjänst (klassisk)](cloud-services-how-to-configure-portal.md)

- [Hantera gäst operativ system version](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal#manage-guest-os-version)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Distribuera en automatiserad korrigerings hanterings lösning för program varu titlar från tredje part

**Vägledning**: Använd en lösning för korrigerings hantering från tredje part. Kunder som redan använder Configuration Manager i sin miljö kan också använda System Center Updates Publisher, så att de kan publicera anpassade uppdateringar i Windows Server Update-tjänsten. 

På så sätt kan Uppdateringshantering korrigera datorer som använder Configuration Manager som uppdaterings lagrings plats med program vara från tredje part.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: Vi rekommenderar att en kund förstår omfattningen av deras risk från en DDoS-attack kontinuerligt. 

Vi rekommenderar att du funderar på följande scenarier:

- Vilka nya offentligt tillgängliga Azure-resurser behöver skydd?
- Finns det en enskild felpunkt i tjänsten?
- Hur kan tjänster isoleras för att begränsa effekten av ett angrepp samtidigt som tjänster blir tillgängliga för giltiga kunder?
- Finns det virtuella nätverk där DDoS Protection standard ska aktive ras, men är det inte?
- Är mina tjänster aktiva/aktiva med redundans över flera regioner?

Support dokumentation:

- [Riskbedömning av dina Azure-resurser](https://docs.microsoft.com/azure/security/fundamentals/ddos-best-practices#risk-evaluation-of-your-azure-resources)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: gäller inte för Azure Cloud Services. Den här rekommendationen gäller för IaaS-beräknings resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Vi rekommenderar att du avstämr inventering regelbundet och ser till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla en inventering av godkända Azure-resurser

**Vägledning**: kunden bör definiera godkända Azure-resurser och godkänd program vara för beräknings resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd funktionen för anpassningsbar program kontroll som finns i Azure Security Center. Det är en intelligent, automatiserad lösning från slut punkt till slut punkt från Security Center som hjälper dig att styra vilka program som kan köras på dina Windows-och Linux-, Azure-och icke-Azure-datorer. Den hjälper också till att förstärka dina datorer mot skadlig kod. 

Den här funktionen är tillgänglig för både Azure-och icke-Azure-fönster (alla versioner, klassiska eller Azure Resource Manager) och Linux-datorer.

Security Center använder Machine Learning för att analysera programmen som körs på dina datorer och skapar en lista över tillåtna från denna intelligens. Den här funktionen fören klar processen att konfigurera och underhålla listan över tillåtna List principer för program, så att du kan:
- Blockera eller Varna vid försök att köra skadliga program, inklusive de som annars kan missas av lösningar mot skadlig kod.

- Uppfylla organisationens säkerhetsprincip som anger att bara licensierade program får användas.
- Undvika oönskad programvara som ska användas i din miljö.
- Undvik att gamla program som inte stöds kan köras.
- Förhindra vissa verktyg som inte tillåts i din organisation.
- Aktivera IT för att kontrollera åtkomsten till känsliga data via appanvändning.

Mer information finns på de länkar som refereras till.

- [Anpassningsbara programkontroller](../security-center/security-center-adaptive-application.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: Använd funktionen för anpassningsbar program kontroll som finns i Azure Security Center. Det är en intelligent, automatiserad lösning från slut punkt till slut punkt från Security Center som hjälper dig att styra vilka program som kan köras på dina Windows-och Linux-, Azure-och icke-Azure-datorer. Den hjälper också till att förstärka dina datorer mot skadlig kod. 

Den här funktionen är tillgänglig för både Azure-och icke-Azure-fönster (alla versioner, klassiska eller Azure Resource Manager) och Linux-datorer.

Security Center använder Machine Learning för att analysera programmen som körs på dina datorer och skapar en lista över tillåtna från denna intelligens. Den här funktionen fören klar processen att konfigurera och underhålla listan över tillåtna List principer för program, så att du kan:

- Blockera eller Varna vid försök att köra skadliga program, inklusive de som annars kan missas av lösningar mot skadlig kod.

- Uppfylla organisationens säkerhetsprincip som anger att bara licensierade program får användas.

- Undvika oönskad programvara som ska användas i din miljö.

- Undvik att gamla program som inte stöds kan köras.

- Förhindra vissa verktyg som inte tillåts i din organisation.

- Aktivera IT för att kontrollera åtkomsten till känsliga data via appanvändning.

Mer information finns på de länkar som refereras till.

- [Anpassningsbara programkontroller](../security-center/security-center-adaptive-application.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: Använd funktionen för anpassningsbar program kontroll som finns i Azure Security Center. Det är en intelligent, automatiserad lösning från slut punkt till slut punkt från Security Center som hjälper dig att styra vilka program som kan köras på dina Windows-och Linux-, Azure-och icke-Azure-datorer. Den hjälper också till att förstärka dina datorer mot skadlig kod. 

Den här funktionen är tillgänglig för både Azure-och icke-Azure-fönster (alla versioner, klassiska eller Azure Resource Manager) och Linux-datorer.

Security Center använder Machine Learning för att analysera programmen som körs på dina datorer och skapar en lista över tillåtna från denna intelligens. Den här funktionen fören klar processen att konfigurera och underhålla listan över tillåtna List principer för program, så att du kan:

- Blockera eller Varna vid försök att köra skadliga program, inklusive de som annars kan missas av lösningar mot skadlig kod.

- Uppfylla organisationens säkerhetsprincip som anger att bara licensierade program får användas.

- Undvika oönskad programvara som ska användas i din miljö.

- Undvik att gamla program som inte stöds kan köras.

- Förhindra vissa verktyg som inte tillåts i din organisation.

- Aktivera IT för att kontrollera åtkomsten till känsliga data via appanvändning.

Mer information finns på de länkar som refereras till.

- [Anpassningsbara programkontroller](../security-center/security-center-adaptive-application.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: Använd funktionen för anpassningsbar program kontroll som finns i Azure Security Center. Det är en intelligent, automatiserad lösning från slut punkt till slut punkt från Security Center som hjälper dig att styra vilka program som kan köras på dina Windows-och Linux-, Azure-och icke-Azure-datorer. Den hjälper också till att förstärka dina datorer mot skadlig kod. 

Den här funktionen är tillgänglig för både Azure-och icke-Azure-fönster (alla versioner, klassiska eller Azure Resource Manager) och Linux-datorer.

Security Center använder Machine Learning för att analysera programmen som körs på dina datorer och skapar en lista över tillåtna från denna intelligens. Den här funktionen fören klar processen att konfigurera och underhålla listan över tillåtna List principer för program, så att du kan:

- Blockera eller Varna vid försök att köra skadliga program, inklusive de som annars kan missas av lösningar mot skadlig kod.

- Uppfylla organisationens säkerhetsprincip som anger att bara licensierade program får användas.

- Undvika oönskad programvara som ska användas i din miljö.

- Undvik att gamla program som inte stöds kan köras.

- Förhindra vissa verktyg som inte tillåts i din organisation.

- Aktivera IT för att kontrollera åtkomsten till känsliga data via appanvändning.

Mer information finns på de länkar som refereras till.

- [Anpassningsbara programkontroller](../security-center/security-center-adaptive-application.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: underhåll en inventering av godkända program varu titlar

**Vägledning**: Använd funktionen för anpassningsbar program kontroll som finns i Azure Security Center. Det är en intelligent, automatiserad lösning från slut punkt till slut punkt från Security Center som hjälper dig att styra vilka program som kan köras på dina Windows-och Linux-, Azure-och icke-Azure-datorer. Den hjälper också till att förstärka dina datorer mot skadlig kod. 

Den här funktionen är tillgänglig för både Azure-och icke-Azure-fönster (alla versioner, klassiska eller Azure Resource Manager) och Linux-datorer.

Security Center använder Machine Learning för att analysera programmen som körs på dina datorer och skapar en lista över tillåtna från denna intelligens. Den här funktionen fören klar processen att konfigurera och underhålla listan över tillåtna List principer för program, så att du kan:
- Blockera eller Varna vid försök att köra skadliga program, inklusive de som annars kan missas av lösningar mot skadlig kod.

- Uppfylla organisationens säkerhetsprincip som anger att bara licensierade program får användas.
- Undvika oönskad programvara som ska användas i din miljö.
- Undvik att gamla program som inte stöds kan köras.
- Förhindra vissa verktyg som inte tillåts i din organisation.
- Aktivera IT för att kontrollera åtkomsten till känsliga data via appanvändning.

Mer information finns på de länkar som refereras till.

- [Anpassningsbara programkontroller](../security-center/security-center-adaptive-application.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: Använd funktionen för anpassningsbar program kontroll som finns i Azure Security Center. Det är en intelligent, automatiserad lösning från slut punkt till slut punkt från Security Center som hjälper dig att styra vilka program som kan köras på dina Windows-och Linux-, Azure-och icke-Azure-datorer. Den hjälper också till att förstärka dina datorer mot skadlig kod. 

Den här funktionen är tillgänglig för både Azure-och icke-Azure-fönster (alla versioner, klassiska eller Azure Resource Manager) och Linux-datorer.

Security Center använder Machine Learning för att analysera programmen som körs på dina datorer och skapar en lista över tillåtna från denna intelligens. Den här funktionen fören klar processen att konfigurera och underhålla listan över tillåtna List principer för program, så att du kan:

- Blockera eller Varna vid försök att köra skadliga program, inklusive de som annars kan missas av lösningar mot skadlig kod.

- Uppfylla organisationens säkerhetsprincip som anger att bara licensierade program får användas.

- Undvika oönskad programvara som ska användas i din miljö.

- Undvik att gamla program som inte stöds kan köras.

- Förhindra vissa verktyg som inte tillåts i din organisation.

- Aktivera IT för att kontrollera åtkomsten till känsliga data via appanvändning.

Mer information finns på de länkar som refereras till.

- [Anpassningsbara programkontroller](../security-center/security-center-adaptive-application.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: för känsliga eller högrisk program med Azure Cloud Services, implementera separata prenumerationer eller hanterings grupper för att tillhandahålla isolering.

Använd en nätverks säkerhets grupp, skapa en inkommande säkerhets regel, Välj en tjänst, till exempel http, Välj en anpassad port, ge den en prioritet och ett namn. Prioriteten påverkar ordningen i vilken reglerna tillämpas, det lägre numeriska värdet, den tidigare regeln tillämpas. Du måste associera nätverks säkerhets gruppen med ett undernät eller ett speciellt nätverks gränssnitt för att isolera eller segmentera nätverks trafiken utifrån dina affärs behov.

Mer information finns på de länkar som refereras till.

- [Självstudie – filtrera nätverks trafik med en nätverks säkerhets grupp med hjälp av Azure Portal](../virtual-network/tutorial-filter-network-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: Använd rekommendationerna från Azure Security Center som en säker konfigurations bas linje för dina Azure Cloud Services-resurser. 

På Azure Portal väljer du Security Center, sedan beräknar du &amp; appar och Azure-Cloud Services för att se de rekommendationer som gäller för dina tjänst resurser.

- [Säkerhetsrekommendationer – en referensguide](../security-center/recommendations-reference.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: gäller inte för Azure Cloud Services. Den är baserad på den klassiska distributions modellen. Vi rekommenderar att du använder en lösning från tredje part för att upprätthålla säker Azure-resurs konfiguration

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: Azure Cloud Services konfigurations fil lagrar startattribut för en resurs. Du kan lagra en kopia av konfigurationsfilerna till ett säkert lagrings konto.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: gäller inte för Azure Cloud Services. Den är baserad på den klassiska distributions modellen och kan inte hanteras av Azure Resource Manager distributions konfigurations verktyg.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Distribuera konfigurations hanterings verktyg för operativ system

**Vägledning**: gäller inte för Azure Cloud Services. Den här rekommendationen gäller för IaaS-baserade (Infrastructure as a Service) data bearbetnings resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: Använd Azure Security Center för att utföra bas linjes ökningar för dina Azure-resurser.  

- [Så här åtgärdar du rekommendationer i Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: i Azure Security Center väljer du funktionen Compute &amp; Apps och följer rekommendationerna för virtuella datorer, servrar och behållare.

- [Förstå rekommendationer för Azure Security Center-container](../security-center/container-security.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Azure Cloud Services baseras på en klassisk distributions modell och integreras inte med Azure Key Vault.

Du kan skydda hemligheter som autentiseringsuppgifter som används i Azure Cloud Services så att du inte behöver ange ett lösen ord varje gången. Börja med genom att ange ett lösen ord för oformaterad text, konvertera det till en säker sträng med hjälp av ConvertTo-SecureString, PowerShell-kommando. Konvertera sedan den här säkra strängen till en krypterad standard sträng med hjälp av ConvertFrom-SecureString.  Nu kan du spara den krypterade standard strängen till en fil med hjälp av Set-Content.

Dessutom rekommenderar vi att du lagrar privata nycklar för certifikat som används i Azure Cloud Services till en säker lagring.

- [Konfigurera fjärr skrivbord från PowerShell](https://docs.microsoft.com/azure/cloud-services/cloud-services-role-enable-remote-desktop-powershell#configure-remote-desktop-from-powershell)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: skydda hemligheter som autentiseringsuppgifter som används i Azure Cloud Services så att du inte behöver ange ett lösen ord varje gången. 
 

Börja genom att ange ett lösen ord för oformaterad text, ändra det till en säker sträng med hjälp av ConvertTo-SecureString, PowerShell-kommando. Konvertera sedan den här säkra strängen till en krypterad standard sträng med hjälp av ConvertFrom-SecureString. Spara nu den här krypterade standard strängen till en fil med hjälp av kommandot Set-Content.

Lagra privata nycklar för certifikat som används i Azure Cloud Services till en säker lagrings plats.

- [Konfigurera fjärr skrivbord från PowerShell](https://docs.microsoft.com/azure/cloud-services/cloud-services-role-enable-remote-desktop-powershell#configure-remote-desktop-from-powershell)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security benchmark: skydd mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: Använd centralt hanterad program vara för program mot skadlig kod

**Vägledning**: Microsoft Antimalware för Azure är tillgängligt för Azure Cloud Services och Virtual Machines. Det är ett kostnads fritt real tids skydd som hjälper till att identifiera och ta bort virus, spionprogram och annan skadlig program vara. Den genererar aviseringar när en känd skadlig eller oönskad program vara försöker installera sig själv eller köra på dina Azure-system. 

Använd PowerShell-baserad cmdlet för program mot skadlig kod för att hämta konfigurationen för program mot skadlig kod med "Get-AzureServiceAntimalwareConfig".

Aktivera tillägget för program mot skadlig kod med ett PowerShell-skript i Start aktiviteten i Azure Cloud Services.

Välj funktionen adaptiva program kontroll i Azure Security Center, en intelligent, automatiserad lösning från slut punkt till slut punkt. Det hjälper dig att hindra dina datorer mot skadlig kod och gör att du kan blockera eller Varna vid försök att köra skadliga program, inklusive sådana som annars kan missas av lösningar mot skadlig kod.

- [Hur kan jag lägga till ett tillägg för program mot skadlig kod för min Azure-Cloud Services på ett automatiserat sätt](https://docs.microsoft.com/azure/cloud-services/cloud-services-configuration-and-management-faq#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)

- [Distributions scenarier för program mot skadlig kod](https://docs.microsoft.com/azure/security/fundamentals/antimalware#antimalware-deployment-scenarios)

- [Anpassningsbara programkontroller](../security-center/security-center-adaptive-application.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.

- [Konfigurera automatisering av arbets flöden i Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Kunden kan också utnyttja NISTs hanterings guide för dator säkerhet för att hjälpa till med att skapa egna incident svars planer](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Azure Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller analysera den som används för att utfärda aviseringen samt den konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen. 

Markera prenumerationer tydligt (till exempel produktion, icke-produktion) och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor, och ändra planen efter behov. 

- [Se NIST: guide för test, utbildning och övnings program för IT-planer och funktioner](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har öppnats av en olaglig eller obehörig part. Granska incidenter när du är säker på att problemen är lösta. 

- [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Security Center Data Connector för att strömma aviseringarna till Azure Sentinel. 

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md) 

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöden i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer.

- [Konfigurera automatisering av arbets flöden och Logic Apps](../security-center/workflow-automation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [övningen för Azure Security benchmark: inträngande tester och röda team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: Följ Microsoft Cloud inträngande test för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft. 

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
