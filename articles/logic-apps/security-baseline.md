---
title: Azures säkerhets bas linje för Logic Apps
description: Logic Apps säkerhets bas linje ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: logic-apps
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: cf4e02f925e0c8c4024137589c0c9c4074c0aa11
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596762"
---
# <a name="azure-security-baseline-for-logic-apps"></a>Azures säkerhets bas linje för Logic Apps

Azures säkerhets bas linje för Logic Apps innehåller rekommendationer som hjälper dig att förbättra säkerhets position för din distribution.

Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](../security/benchmarks/overview.md), som ger rekommendationer om hur du kan skydda dina moln lösningar i Azure med våra bästa praxis rikt linjer.

Mer information finns i [Översikt över Azure Security-bas linjer](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets principen för Azure-säkerhet: nätverks säkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: anslutningar som körs i "globala" Logic Apps tjänsten för flera innehavare distribueras och hanteras av Microsoft. Dessa anslutningar tillhandahåller utlösare och åtgärder för att komma åt moln tjänster, lokala system eller både, inklusive Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint med mera. Du kan använda AzureConnectors service tag när du anger regler för nätverks säkerhets gruppen eller Azure-brandväggen för att tillåta åtkomst till relaterade resurser.

För Logic Apps som behöver direkt åtkomst till resurser i ett virtuellt Azure-nätverk kan du skapa en integrerings tjänst miljö (ISE) där du kan skapa, distribuera och köra Logi Kap par på dedikerade resurser. Vissa virtuella Azure-nätverk använder privata slut punkter (Azures privata länk) för att ge åtkomst till Azure PaaS-tjänster, till exempel Azure Storage, Azure Cosmos DB, Azure SQL Database, partner tjänster eller kund tjänster som finns på Azure. Om dina Logi Kap par behöver åtkomst till virtuella nätverk som använder privata slut punkter, måste du skapa, distribuera och köra dessa Logic Apps i en ISE.

När du skapar din ISE kan du välja att använda antingen interna eller externa slut punkter för åtkomst. Ditt val bestämmer om begäran eller webhook-utlösare på Logic Apps i din ISE kan ta emot samtal utanför det virtuella nätverket. Interna och externa åtkomst slut punkter påverkar också om du kan visa din Logic Apps körnings historik, inklusive indata och utdata för en körning, från i eller utanför ditt virtuella nätverk.

Kontrol lera att alla virtuella nätverk under nät distributioner som är relaterade till din ISE har en nätverks säkerhets grupp som tillämpas med nätverks åtkomst kontroller som är specifika för programmets betrodda portar och källor. Använd privat länk när du distribuerar dina Logi Kap par i en ISE. Med Azures privata länk kan du få åtkomst till Azure PaaS Services och Azure-värdbaserade kund tjänster/partner tjänster över en privat slut punkt i det virtuella nätverket. Om du har ett speciellt användnings fall kan du uppfylla det här kravet genom att implementera Azure-brandväggen. För att minska komplexiteten när du skapar säkerhets regler, använder du tjänst taggar som representerar grupper med IP-adressprefix för en speciell Azure-tjänst.

- [Förstå anslutningar för Logic Apps](../connectors/apis-list.md)

- [Förstå service märken i Azure](../virtual-network/service-tags-overview.md)

- [Förstå åtkomst till Azure Virtual Network-resurser från Azure Logic Apps med hjälp av integrerings tjänst miljöer (ISEs)](connect-virtual-network-vnet-isolated-environment-overview.md)

- [Förstå Virtual Network tjänstens slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md)

- [Förstå privat Azure-länk](../private-link/private-link-overview.md)

- [Förstå åtkomst till ISE-slutpunkt](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [Så här skapar du en Virtual Network](../virtual-network/quick-create-portal.md)

- [Så här skapar du en NSG med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

- [Distribuera och konfigurera Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

- [Så här aktiverar du åtkomst för ISE](connect-virtual-network-vnet-isolated-environment.md#enable-access-for-ise)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverks gränssnitt

**Vägledning**: om du kör Logi Kap par i en integrerings tjänst miljö (ISE) som använder en extern åtkomst punkt kan du använda en nätverks säkerhets grupp (NSG) för att minska risken för data exfiltrering. Aktivera NSG Flow-loggar och skicka loggar till ett Azure Storage konto för trafik granskning. Du kan också skicka NSG Flow-loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

- [Förstå åtkomst till ISE-slutpunkt](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: om din Logic app använder en begärd utlösare, som tar emot inkommande samtal eller begär Anden, till exempel begäran eller webhook-utlösare, kan du begränsa åtkomsten så att endast auktoriserade klienter kan anropa din Logic app.

Om du kör Logi Kap par i en integration service Environment (ISE) aktiverar du DDoS Protection standard på det virtuella nätverk som är kopplat till din ISE för att skydda mot DDoS-attacker. Använd Azure Security Center integrerad Hot information för att neka kommunikation med kända skadliga eller oanvända Internet-IP-adresser.

Distribuera Azure-brandväggen på var och en av organisationens nätverks gränser med hot information aktive rad och konfigurerad för "varning och neka" för skadlig nätverks trafik.

Använd Azure Security Center just-in-Time Network Access för att konfigurera NSG: er för att begränsa exponering av slut punkter till godkända IP-adresser under en begränsad period.

Använd Azure Security Center anpassad nätverks härdning för att rekommendera NSG-konfigurationer som begränsar portar och käll-IP-adresser baserat på faktisk trafik och hot information.

- [Så här säkrar du inkommande anrop till Logic Apps](logic-apps-securing-a-logic-app.md#secure-inbound-requests)

- [Så här begränsar du inkommande IP-adresser](logic-apps-securing-a-logic-app.md#restrict-inbound-ip-addresses)

- [Så här konfigurerar du DDoS-skydd](../ddos-protection/manage-ddos-protection.md)

- [Så här distribuerar du Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

- [Förstå Azure Security Center integrerad Hot information](../security-center/azure-defender.md)

- [Förstå Azure Security Center anpassad nätverks härdning](../security-center/security-center-adaptive-network-hardening.md)

- [Förstå Azure Security Center just-in-Time-nätverk Access Control](../security-center/security-center-just-in-time.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets"></a>1,5: registrera nätverks paket

**Vägledning**: om du kör Logi Kap par i en integrerings tjänst miljö (ISE) som använder en extern åtkomst punkt kan du använda en nätverks säkerhets grupp (NSG) för att minska risken för data exfiltrering. Aktivera NSG Flow-loggar och skicka loggar till ett Azure Storage konto för trafik granskning. Du kan också skicka NSG Flow-loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

Om du vill ge ytterligare skydd och information om nätverks trafiken kan du referera till åtkomst loggar som bara skapas om du har aktiverat dem på varje Application Gateway instans. Du kan använda den här loggen för att Visa Application Gateway åtkomst mönster och analysera viktig information. Detta inkluderar anroparens IP, begärd URL, svars svars tid, retur kod och byte in och ut.

Annars kan du utnyttja en lösning från tredje part från Marketplace för att uppfylla det här kravet.

- [Förstå åtkomst till ISE-slutpunkt](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

- [Så här integrerar du API Management i ett internt virtuellt nätverk med Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [Så här förstår du WAF-åtkomst loggar](../web-application-firewall/ag/web-application-firewall-logs.md#access-log)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera nätverksbaserade intrångs identifiering/system för skydd mot intrång (ID/IP-adresser)

**Vägledning**: Välj ett erbjudande från Azure Marketplace som stöder ID/IP-funktioner med funktioner för nytto Last kontroll.  Om intrångs identifiering och/eller skydd som baseras på nytto lasts granskning inte är ett krav kan du använda Azure-brandväggen med hot information. Azure Firewall Threat Intelligence-baserad filtrering kan varna och neka trafik till och från kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-flödet.

Distribuera den brand Väggs lösning som du väljer för var och en av organisationens nätverks gränser för att upptäcka och/eller neka skadlig trafik.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Så här distribuerar du Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

- [Konfigurera aviseringar med Azure-brandväggen](../firewall/threat-intel.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: om du kör Logic Apps i en integrerings tjänst miljö (ISE) distribuerar du Azure Application Gateway.

- [Så här integrerar du API Management i ett internt virtuellt nätverk med Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [Så här konfigurerar du Application Gateway att använda HTTPS](../application-gateway/create-ssl-portal.md) 

- [Förstå belastnings utjämning för Layer 7 med Azure Web Application Gateway](../application-gateway/overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: för resurser som behöver åtkomst till dina Azure Logic Apps-instanser använder du tjänst taggar för virtuella nätverk för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper eller Azure-brandvägg. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange namnet på service tag gen (t. ex. LogicApps, LogicAppsManagement) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

- [Mer information om att använda service märken](../virtual-network/service-tags-overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för nätverks resurser som är relaterade till dina Azure Logic Apps-instanser med Azure policy. Använd Azure Policy alias i namn områdena "Microsoft. Logic" och "Microsoft. Network" om du vill skapa anpassade principer för att granska eller tillämpa nätverks konfigurationen för dina Azure Logic Apps-instanser. Du kan också använda inbyggda princip definitioner som:

- Diagnostikloggar i Logic Apps ska vara aktive rad

- DDoS Protection standard ska vara aktive rad

Du kan också använda Azure-ritningar för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljö artefakter, till exempel Azure Resource Manager mallar, rollbaserad åtkomst kontroll i Azure (Azure RBAC) och principer, i en enda skiss definition. Använd enkelt skissen på nya prenumerationer och miljöer och finjustera kontroll och hantering genom versions hantering.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du en Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd fältet Beskrivning för enskilda NSG-regler för att ange affärs behov och/eller varaktighet (osv.) för alla regler som tillåter trafik till/från ett nätverk.

Använd någon av de inbyggda Azure Policy definitionerna som är relaterade till taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och meddela dig om befintliga otaggade resurser.

Du kan använda Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

- [Så här skapar du en Virtual Network](../virtual-network/quick-create-portal.md)

- [Så här skapar du en NSG med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

- [Lista över Azure Policy definitioner för Logic Apps](./policy-reference.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar för nätverks resurser som är relaterade till dina Azure Logic Apps-instanser. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks resurser sker.

- [Visa och hämta Azure aktivitets logg händelser](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

**Vägledning**: Microsoft hanterar den tids källa som används för Azure-resurser, till exempel Azure Logic Apps för tidsstämplar i loggarna.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: om du vill få bättre fel söknings information om dina Logi Kap par under körningen kan du konfigurera och använda Azure Monitor loggar för att registrera och lagra information om körnings data och händelser, t. ex. utlösa händelser, köra händelser och åtgärds händelser i en Log Analytics arbets yta. Azure Monitor hjälper dig att övervaka molnet och lokala miljöer så att du enklare kan underhålla deras tillgänglighet och prestanda. Genom att använda Azure Monitor loggar kan du skapa logg frågor som hjälper dig att samla in och granska den här informationen. Du kan också använda dessa diagnostikdata med andra Azure-tjänster, till exempel Azure Storage och Azure Event Hubs.

Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM från tredje part. 

- [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](../azure-monitor/essentials/activity-log.md)

- [Så här konfigurerar du Azure Monitor loggar och samlar in diagnostikdata för Azure Logic Apps](monitor-logic-apps-log-analytics.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md) 

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: om du vill få bättre fel söknings information om dina Logi Kap par under körningen kan du konfigurera och använda Azure Monitor loggar för att registrera och lagra information om körnings data och händelser, t. ex. utlösa händelser, köra händelser och åtgärds händelser i en Log Analytics arbets yta. Azure Monitor hjälper dig att övervaka molnet och lokala miljöer så att du enklare kan underhålla deras tillgänglighet och prestanda. Genom att använda Azure Monitor loggar kan du skapa logg frågor som hjälper dig att samla in och granska den här informationen. Du kan också använda dessa diagnostikdata med andra Azure-tjänster, till exempel Azure Storage och Azure Event Hubs.

Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM från tredje part. 

- [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](../azure-monitor/essentials/activity-log.md)

- [Så här konfigurerar du Azure Monitor loggar och samlar in diagnostikdata för Azure Logic Apps](monitor-logic-apps-log-analytics.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md) 

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: när du har skapat och kört en Logic-app kan du kontrol lera att appens körnings status, kör historik, utlösnings historik och prestanda. För händelse övervakning i real tid och bättre fel sökning, ställer du in diagnostikloggning för din Logic app genom att använda Azure Monitor loggar. Med den här Azure-tjänsten kan du övervaka molnet och lokala miljöer så att du enklare kan underhålla deras tillgänglighet och prestanda. Du kan sedan söka efter och Visa händelser, t. ex. utlösa händelser, köra händelser och åtgärds händelser. Genom att lagra informationen i Azure Monitor loggar kan du skapa logg frågor som hjälper dig att hitta och analysera den här informationen. Du kan också använda dessa diagnostikdata med andra Azure-tjänster, till exempel Azure Storage och Azure Event Hubs.

I Azure Monitor anger du logg kvarhållningsperiod för loggar som är kopplade till dina Azure Logic Apps instanser enligt organisationens regler för efterlevnad.

- [Övervaka körnings status, granska utlösarens historik och konfigurera aviseringar för Azure Logic Apps](monitor-logic-apps.md)

- [Ange parametrar för logg bevarande](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: om du vill konfigurera loggning för din Logi Kap par kan du aktivera Log Analytics när du skapar din Logic app, eller så kan du installera Logic Apps hanterings lösningen i din Log Analytics arbets yta för befintliga Logi Kap par. Den här lösningen ger aggregerad information för din Logi Kap par och innehåller information som status, körnings tid, status för återsändning och korrelations-ID. Om du vill aktivera loggning och skapa frågor för den här informationen konfigurerar du Azure Monitor loggar.

Du kan också aktivera inställningar för Azure aktivitets loggs diagnostik och skicka loggarna till en Log Analytics-arbetsyta. Utför frågor i Log Analytics för att söka efter termer, identifiera trender, analysera mönster och tillhandahålla många andra insikter baserat på de aktivitets logg data som kan ha samlats in för Azure Logic Apps.

Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM från tredje part. 

- [Så här konfigurerar du Azure Monitor loggar och samlar in diagnostikdata för Azure Logic Apps](monitor-logic-apps-log-analytics.md)

- [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](../azure-monitor/essentials/activity-log.md)

- [Samla in och analysera Azure-aktivitets loggar i Log Analytics i Azure Monitor](../azure-monitor/essentials/activity-log.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md) 

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: Använd Azure Security Center med Log Analytics för övervakning och aviseringar om avvikande aktivitet i säkerhets loggar och händelser.

Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel.

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Hantera aviseringar i Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Så här aviserar du om Log Analytics-loggdata](../azure-monitor/alerts/tutorial-response.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: ej tillämpligt; Azure Logic Apps bearbetar eller skapar inte relaterade loggar mot skadlig kod.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: ej tillämpligt; Azure Logic Apps bearbetar eller skapar inte DNS-relaterade loggar.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azures säkerhets benchmark: identitets-och åtkomst kontroll](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: Azure Active Directory (AD) har inbyggda roller som måste tilldelas explicit och som kan frågas. Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

För att enkelt komma åt andra resurser som skyddas av Azure Active Directory (Azure AD) och autentisera din identitet utan att logga in, kan din Logic app använda en hanterad identitet (tidigare Hanterad tjänstidentitet eller MSI) i stället för autentiseringsuppgifter eller hemligheter. Azure hanterar den här identiteten åt dig och hjälper till att skydda dina autentiseringsuppgifter eftersom du inte måste ange eller rotera hemligheter.

Varje begär ande slut punkt i en Logic app har en signatur för delad åtkomst (SAS) i slut punktens URL. Om du delar slut punkts-URL: en för en begärd utlösare med andra parter kan du generera återanrops-URL: er som använder vissa nycklar och som har förfallo datum. På så sätt kan du sömlöst återställa nycklar eller begränsa åtkomsten till att utlösa din Logi Kap par baserat på ett angivet tidsintervall.

- [Så här hämtar du en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Autentisera åtkomst till Azure-resurser med hjälp av hanterade identiteter i Azure Logic Apps](create-managed-service-identity.md)

- [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [Skydda åtkomst och data i Azure Logic Apps med hjälp av SAS](logic-apps-securing-a-logic-app.md#sas)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: Azure Active Directory och Azure Logic Apps har inte begreppet standard lösen ord.

Om grundläggande autentisering används måste du ange ett användar namn och lösen ord. När du skapar de här autentiseringsuppgifterna ska du se till att konfigurera ett starkt lösen ord för autentisering.

Om du använder infrastruktur som kod bör du undvika att lagra lösen ord i kod och i stället använda Azure Key Vault för att lagra och hämta autentiseringsuppgifter.

- [Skydda och komma åt data i Logic Apps](logic-apps-securing-a-logic-app.md)

- [Så här ställer du in och hämtar en hemlighet från Azure Key Vault](../key-vault/general/quick-create-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton. Använd Azure Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton.

För att hjälpa dig att hålla koll på dedikerade administrativa konton kan du dessutom använda rekommendationer från Azure Security Center eller inbyggda Azure-principer, t. ex.:

- Det bör finnas fler än en ägare som tilldelats din prenumeration
- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med ägar behörigheter bör tas bort från din prenumeration

- [Använda Azure Security Center för att övervaka identitet och åtkomst (för hands version)](../security-center/security-center-identity-access.md)

- [Använda Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Använd Azure Active Directory enkel inloggning (SSO)

**Vägledning**: Använd en Azure App-registrering (tjänstens huvud namn) för att hämta en token som kan användas för att interagera med dina Recovery Services-valv via API-anrop.

Många anslutningar kräver också att du först skapar en anslutning till mål tjänsten eller systemet och anger autentiseringsuppgifter för autentisering eller annan konfigurations information innan du kan använda en utlösare eller åtgärd i din Logic app. Du måste till exempel auktorisera en anslutning till ett Twitter-konto för att komma åt data eller för att publicera åt dig.

För kopplingar som använder Azure Active Directory (Azure AD) OAuth, skapar en anslutning en inloggning till tjänsten, till exempel Office 365, Salesforce eller GitHub, där din åtkomsttoken är krypterad och lagras säkert i ett Azure Secret Store. Andra anslutningar, till exempel FTP och SQL, kräver en anslutning som har konfigurations information, till exempel Server adressen, användar namnet och lösen ordet. Dessa anslutnings konfigurations uppgifter krypteras och lagras på ett säkert sätt.

- [Så här anropar du Azure REST API: er](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Registrera klient programmet med Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [API-information för Workflow triggers](/rest/api/logic/workflowtriggers)

- [Förstå kopplings konfiguration](../connectors/apis-list.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory-baserad åtkomst

**Vägledning**: Aktivera Azure Active Directory (AD) Multi-Factor Authentication (MFA) och följ rekommendationer för Azure Security Center identitets-och åtkomst hantering.

- [Aktivera MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Använd säkra, Azure-hanterade arbets stationer för administrativa uppgifter

**Vägledning**: Använd Paw (Privileged Access Workstation) med Multi-Factor Authentication (MFA) konfigurerat för att logga in på och konfigurera Azure-resurser.

- [Lär dig mer om arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Aktivera MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: Använd Azure Active Directory (AD) PRIVILEGED Identity Management (PIM) för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön.

Dessutom kan du använda Azure AD-farlighets identifiering för att visa aviseringar och rapporter om riskfyllda användar beteenden.

- [Distribuera Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Förstå identifieringar av Azure AD-risker](../active-directory/identity-protection/overview-identity-protection.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Använd villkorlig åtkomst med namngivna platser för att tillåta åtkomst till Azure Portal från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

Dessutom har varje begär ande slut punkt i en Logic app en signatur för delad åtkomst (SAS) i slut punktens URL. Du kan begränsa din Logic app så att den endast accepterar begär Anden från vissa IP-adresser.

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

- [Förstå hur du begränsar inkommande IP-adresser i Logic Apps](logic-apps-securing-a-logic-app.md#restrict-inbound-ip-addresses)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: Använd Azure Active Directory (AD) som central autentiserings-och auktoriserings system för dina Azure Logic Apps-instanser. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

Om det finns stöd i Logic Apps kan du använda en hanterad identitet för att enkelt komma åt andra resurser som skyddas av Azure Active Directory (Azure AD) och autentisera din identitet utan att logga in, snarare än autentiseringsuppgifter eller hemligheter. Azure hanterar den här identiteten åt dig och hjälper till att skydda dina autentiseringsuppgifter eftersom du inte måste ange eller rotera hemligheter.

Azure Logic Apps stöder både system- och användartilldelade hanterade identiteter. Logikappen kan använda antingen den systemtilldelade identiteten eller en enskild användartilldelad identitet, som du kan dela i en grupp av logikappar, men inte båda. För närvarande stöder bara specifika inbyggda utlösare och åtgärder hanterade identiteter, inte hanterade anslutningsprogram eller anslutningar. Till exempel:

-  HTTP

-  Azure Functions

-  Azure API Management

-  Azure App Services 

- [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Autentisera åtkomst till Azure-resurser med hjälp av hanterade identiteter i Azure Logic Apps](create-managed-service-identity.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure Active Directory (AD) innehåller loggar för att hjälpa dig att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användar åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst. 

- [Förstå Azure AD repor ting](../active-directory/reports-monitoring/index.yml)

- [Så här använder du granskningar av Azure Identity Access](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: Använd Azure Active Directory (AD) som central autentiserings-och auktoriserings system för dina Azure Logic Apps-instanser. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

Du har åtkomst till Azure AD-inloggning, gransknings-och risk händelse logg källor, som gör att du kan integrera med Azure Sentinel eller en SIEM från tredje part.

Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka gransknings loggar och inloggnings loggar till en Log Analytics-arbetsyta. Du kan konfigurera önskade logg aviseringar i Log Analytics.

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Aktivera Azure-kontroll på kort](../sentinel/quickstart-onboard.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: varning vid inloggnings beteende för konto

**Vägledning**: Använd funktioner i Azure AD-risk och identitets skydd för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter. Du kan också mata in data i Azure Sentinel för ytterligare undersökning. 

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md) 

- [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: inte tillgänglig för tillfället. Customer Lockbox stöds ännu inte för Azure Logic Apps.

- [Lista över Customer Lockbox tjänster som stöds](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security benchmark: Data Protection](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: anslutningar som körs i "globala" Logic Apps tjänsten för flera innehavare distribueras och hanteras av Microsoft. Dessa anslutningar tillhandahåller utlösare och åtgärder för att komma åt moln tjänster, lokala system eller både, inklusive Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint med mera.

För Logic Apps som behöver direkt åtkomst till resurser i ett virtuellt Azure-nätverk kan du skapa en integrerings tjänst miljö (ISE) där du kan skapa, distribuera och köra Logi Kap par på dedikerade resurser. Vissa virtuella Azure-nätverk använder privata slut punkter (Azures privata länk) för att ge åtkomst till Azure PaaS-tjänster, till exempel Azure Storage, Azure Cosmos DB eller Azure SQL Database, partner tjänster eller kund tjänster som finns i Azure. Om dina Logi Kap par behöver åtkomst till virtuella nätverk som använder privata slut punkter, måste du skapa, distribuera och köra dessa Logic Apps i en ISE.

När du skapar din ISE kan du välja att använda antingen interna eller externa slut punkter för åtkomst. Ditt val bestämmer om begäran eller webhook-utlösare på Logic Apps i din ISE kan ta emot samtal utanför det virtuella nätverket.

Implementera dessutom isolering med separata prenumerationer och hanterings grupper för enskilda säkerhets domäner, till exempel miljö typ och data känslighets nivå. Du kan begränsa åtkomst nivån till dina Azure-resurser som dina program och företags miljöer kräver. Du kan styra åtkomsten till Azure-resurser via rollbaserad åtkomst kontroll i Azure (Azure RBAC).

- [Förstå anslutningar för Logic Apps](../connectors/apis-list.md)

- [Åtkomst till Azure Virtual Network-resurser från Azure Logic Apps med hjälp av integrerings tjänst miljöer (ISEs)](connect-virtual-network-vnet-isolated-environment-overview.md)

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md) 

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md) 

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: inte tillgänglig för tillfället. funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Logic Apps.

Utnyttja en lösning från en tredje part från Azure Marketplace på nätverks-perimeter som övervakar för obehörig överföring av känslig information och blockerar sådana överföringar, samtidigt som de meddelar information om informations säkerhet. 

Microsoft hanterar den underliggande infrastrukturen för Azure Logic Apps och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Delad

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: kryptera all känslig information under överföring. I Azure Logic Apps krypteras alla data under en Logic app-körning under överföringen med hjälp av Transport Layer Security (TLS) och rest. När du visar den logiska appens körnings historik autentiserar Logic Apps åtkomsten och tillhandahåller länkar till indata och utdata för begär Anden och svar för varje körning. För åtgärder som hanterar lösen ord, hemligheter, nycklar eller annan känslig information vill du dock hindra andra från att visa och komma åt dessa data. Om din Logic app till exempel får en hemlighet från Azure Key Vault att använda vid autentisering av en HTTP-åtgärd, vill du dölja hemligheten från vyn.

Begär ande utlösare stöder endast Transport Layer Security (TLS) 1,2 för inkommande begär Anden. Se till att alla klienter som ansluter till dina Azure-resurser kan förhandla TLS 1,2 eller senare. Utgående anrop med stöd för HTTP-anslutning Transport Layer Security (TLS) 1,0, 1,1 och 1,2. 

Följ Azure Security Center rekommendationer för kryptering i vila och kryptering under överföring, i förekommande fall.

- [Skydda åtkomst och data i Azure Logic Apps inkommande anrop till begär ande-baserade utlösare](logic-apps-securing-a-logic-app.md#secure-inbound-requests)

- [Säker åtkomst och data i Azure Logic Apps utgående samtal till andra tjänster och system](logic-apps-securing-a-logic-app.md#secure-outbound-requests)

- [Förstå kryptering i överföring med Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Förstå data kryptering – i vila med Azure](../security/fundamentals/encryption-atrest.md)

- [Konfigurera Kundhanterade nycklar för att kryptera data i vila för integrerings tjänst miljöer (ISEs) i Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: i Azure Logic Apps har många utlösare och åtgärder inställningar som du kan aktivera för att skydda indata, utdata eller båda genom att dölja dessa data från en Logic Apps körnings historik.

Microsoft hanterar den underliggande infrastrukturen för Azure Logic Apps och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

- [Säker åtkomst för körning av historik data](logic-apps-securing-a-logic-app.md#access-to-run-history-data)

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Delad

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser

**Vägledning**: du kan endast tillåta vissa användare eller grupper att köra vissa uppgifter, till exempel hantera, redigera och Visa Logic Apps. Om du vill kontrol lera deras behörigheter använder du rollbaserad åtkomst kontroll i Azure (Azure RBAC) så att du kan tilldela de anpassade eller inbyggda rollerna till medlemmarna i din Azure-prenumeration:

- Logic app-deltagare: låter dig hantera Logi Kap par, men du kan inte ändra åtkomsten till dem.
- Logic app-operator: låter dig läsa, aktivera och inaktivera Logic Apps, men du kan inte redigera eller uppdatera dem.

Om du vill hindra andra från att ändra eller ta bort din Logic app kan du använda Azure Resource lock. Den här funktionen förhindrar andra från att ändra eller ta bort produktions resurser.

- [Säker åtkomst till Azure Logic Apps åtgärder](logic-apps-securing-a-logic-app.md#access-to-logic-app-operations)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser. Microsoft hanterar den underliggande infrastrukturen för Azure Logic Apps och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

- [Data skydd för Azure-kunder](../security/fundamentals/protection-customer-data.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: Azure Logic Apps förlitar sig på Azure Storage för att lagra och automatiskt kryptera data i vila. Den här krypteringen skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden. Som standard använder Azure Storage Microsoft-hanterade nycklar för att kryptera dina data.

När du skapar en integrerings tjänst miljö (ISE) som är värd för dina Logi Kap par, och du vill ha mer kontroll över de krypterings nycklar som används av Azure Storage, kan du konfigurera, använda och hantera din egen nyckel med hjälp av Azure Key Vault. Den här funktionen kallas även "Bring Your Own Key" (BYOK) och din nyckel kallas för "kundhanterad nyckel".

- [Kryptera data i vila för integrerings tjänst miljöer i Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i Azure Logic Apps samt andra kritiska eller relaterade resurser.

- [Så här skapar du aviseringar för Azure aktivitets logg händelser](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security benchmark: sårbarhet Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: ej tillämpligt; Microsoft utför sårbarhets hantering på de underliggande system som har stöd för Azure Logic Apps.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Distribuera automatiserad korrigerings hanterings lösning för program varu titlar från tredje part

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: ej tillämpligt; Microsoft utför sårbarhets hantering på de underliggande system som har stöd för Azure Logic Apps.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: ej tillämpligt; Microsoft utför sårbarhets hantering på de underliggande system som har stöd för Azure Logic Apps.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga/identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk, portar och protokoll osv.) i din prenumeration (er).  Se till att du har rätt (Läs) behörigheter i din klient organisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via resurs diagram, rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser som går framåt.

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser som ger metadata till att logiskt organisera dem i en taxonomi.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer om det behövs för att organisera och spåra Azure-resurser. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

Använd dessutom Azure Policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper
- Tillåtna resurstyper

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla inventering av godkända Azure-resurser

**Vägledning**: skapa en inventering av godkända Azure-resurser (t. ex. anslutningar) och godkänd program vara för beräknings resurser enligt organisationens behov.

Obs! på grund av Googles principer för data och sekretess kan du endast använda Gmail Connector med Google-godkända tjänster. Den här situationen utvecklas och kan påverka andra Google-anslutningar i framtiden.

- [Lista över alla Logic Apps kopplingar](/connectors/connector-reference/connector-reference-logicapps-connectors)

- [Förstå problem och begränsningar för Gmail-kopplingar](/connectors/gmail/#known-issues-and-limitations)

- [Mer information om Googles sekretess policy](../connectors/connectors-google-data-security-privacy-policy.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i dina prenumerationer. 

Använd Azure Resource Graph för att fråga/identifiera resurser i sina prenumerationer.  Se till att alla Azure-resurser som finns i miljön är godkända.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper
- Tillåtna resurstyper

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/index.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: underhåll en inventering av godkända program varu titlar

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Konfigurera villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: resurser som är relaterade till dina Logic Apps som krävs för affärs åtgärder, men som kan ådra sig högre risk för organisationen, bör isoleras inom en egen virtuell dator och/eller ett virtuellt nätverk och tillräckligt säkert med antingen en Azure-brandvägg eller en nätverks säkerhets grupp.

Logic Apps som krävs för affärs åtgärder, men kan ådra sig högre risk för organisationen, bör isoleras när det är möjligt via separata resurs grupper med specifika behörigheter och Azure RBAC-gränser.

- [Så här skapar du ett virtuellt nätverk](../virtual-network/quick-create-portal.md) 

- [Så här skapar du en NSG med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md) 

- [Skydda åtkomsten till Logic Apps via Azure RBAC](logic-apps-securing-a-logic-app.md#access-to-logic-app-operations)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: definiera och implementera standardkonfigurationer för dina Azure Logic Apps-instanser med Azure policy. Använd Azure Policy alias i namn området "Microsoft. Logic" för att skapa anpassade principer som ska granskas eller tillämpa konfigurationen för dina Logic Apps-instanser. Du kan till exempel hindra andra från att skapa eller använda anslutningar till resurser där du vill begränsa åtkomsten.

Dessutom har Azure Resource Manager möjlighet att exportera mallen i JavaScript Object Notation (JSON), vilken bör granskas för att säkerställa att konfigurationerna uppfyller/överskrider säkerhets kraven för din organisation.

Använd också skyddade parametrar för att skydda känsliga data och hemligheter.

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Blockera anslutningar som skapats av anslutningar i Azure Logic Apps](block-connections-connectors.md)

- [Exportera en och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Så här distribuerar du Azure Resource Manager mallar för Azure Logic Apps](logic-apps-deploy-azure-resource-manager-templates.md)

- [Förstå säkra åtgärds parametrar](logic-apps-securing-a-logic-app.md#secure-action-parameters)

- [Säkerhets rekommendationer för parametrar](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar i dina Azure-resurser.

Definiera och implementera säkerhetskonfigurationer för dina Azure Logic Apps-instanser med Azure policy. Använd Azure Policy alias i namn området "Microsoft. Logic" för att skapa anpassade principer som ska granskas eller tillämpa konfigurationen för dina Logic Apps-instanser. Du kan till exempel hindra andra från att skapa eller använda anslutningar till resurser där du vill begränsa åtkomsten.

Dessutom har Azure Resource Manager möjlighet att exportera mallen i JavaScript Object Notation (JSON), vilken bör granskas för att säkerställa att konfigurationerna uppfyller/överskrider säkerhets kraven för din organisation.

Se också till att du skyddar data i körnings historiken med hjälp av döljande.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

- [Blockera anslutningar som skapats av anslutningar i Azure Logic Apps](block-connections-connectors.md)

- [Exportera en och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Så här distribuerar du Azure Resource Manager mallar för Azure Logic Apps](logic-apps-deploy-azure-resource-manager-templates.md)

- [Säker åtkomst för att köra tidigare indata och utdata](logic-apps-securing-a-logic-app.md#obfuscate)

- [Säker åtkomst till parameter indata](logic-apps-securing-a-logic-app.md#secure-action-parameters)

- [Säkerhets rekommendationer för parametrar](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade Azure policys definitioner använder du Azure DevOps eller Azure databaser för att lagra och hantera din kod på ett säkert sätt.

Dessutom har Azure Resource Manager möjlighet att exportera mallen i JavaScript Object Notation (JSON), vilken bör granskas för att säkerställa att konfigurationerna uppfyller/överskrider säkerhets kraven för din organisation.

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Dokumentation om Azure databaser](/azure/devops/repos/index?view=azure-devops)

- [Exportera en och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: använd inbyggda Azure policy definitioner samt Azure policy alias i namn området "Microsoft. Logic" för att skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Använd Azure Policy alias för att skapa anpassade principer för att granska eller tillämpa nätverks konfigurationen för dina Azure-resurser. Dessutom kan du utveckla en process och pipeline för att hantera princip undantag.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Distribuera konfigurations hanterings verktyg för operativ system

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: använd inbyggda Azure policy definitioner samt Azure policy alias i namn området "Microsoft. Logic" för att skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Använd Azure Policy [audit], [neka] och [distribuera om det inte finns] för att automatiskt tillämpa konfigurationer för dina Azure-resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: säkra indata och utdata i Logic app-programhistoriken med hjälp av döljande. Om du distribuerar över olika miljöer bör du överväga att parametriserade värdena i din Logic Apps arbets flödes definition som varierar beroende på dessa miljöer. På så sätt kan du undvika hårdkodade data med hjälp av en Azure Resource Manager mall för att distribuera din Logi Kap par, skydda känsliga data genom att definiera säkra parametrar och skicka dessa data som separata indata via mallens parametrar genom att använda en parameter fil. Du kan använda Key Vault för att lagra känsliga data och använda säkra mallparametrar som hämtar dessa värden från Key Vault vid distributionen. Sedan kan du referera till nyckel valvet och hemligheterna i parameter filen. 

När du skapar en integrerings tjänst miljö (ISE) som är värd för dina Logi Kap par, och du vill ha mer kontroll över de krypterings nycklar som används av Azure Storage, kan du konfigurera, använda och hantera din egen nyckel med hjälp av Azure Key Vault. Den här funktionen kallas även "Bring Your Own Key" (BYOK) och din nyckel kallas för "kundhanterad nyckel".

- [Skydda indata och utdata i körnings historiken i Azure Logic Apps](logic-apps-securing-a-logic-app.md#obfuscate)

- [Säkerhets rekommendationer för parametrar](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

- [Säker åtkomst till parameter indata i Azure Logic Apps](logic-apps-securing-a-logic-app.md#access-to-parameter-inputs)

- [Överför säkra parameter värden under distributionen med Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

- [Konfigurera Kundhanterade nycklar för att kryptera data i vila för integrerings tjänst miljöer (ISEs) i Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: för att enkelt komma åt andra resurser som skyddas av Azure Active Directory (Azure AD) och autentisera din identitet utan att logga in, kan din Logic app använda en hanterad identitet (tidigare HANTERAD TJÄNSTIDENTITET eller MSI) i stället för autentiseringsuppgifter eller hemligheter. Azure hanterar den här identiteten åt dig och hjälper till att skydda dina autentiseringsuppgifter eftersom du inte måste ange eller rotera hemligheter.

För närvarande stöder bara specifika inbyggda utlösare och åtgärder hanterade identiteter, inte hanterade anslutningsprogram eller anslutningar. Till exempel:

- HTTP
- Azure Functions
- Azure API Management
- Azure App Services

- [Så här autentiserar du åtkomst till Azure-resurser med hjälp av hanterade identiteter i Azure Logic Apps](create-managed-service-identity.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: säkra indata och utdata i Logic app-programhistoriken med hjälp av döljande. Om du distribuerar över olika miljöer bör du överväga att parametriserade värdena i din Logic Apps arbets flödes definition som varierar beroende på dessa miljöer. På så sätt kan du undvika hårdkodade data med hjälp av en Azure Resource Manager mall för att distribuera din Logi Kap par, skydda känsliga data genom att definiera säkra parametrar och skicka dessa data som separata indata via mallens parametrar genom att använda en parameter fil. Du kan använda Key Vault för att lagra känsliga data och använda säkra mallparametrar som hämtar dessa värden från Key Vault vid distributionen. Sedan kan du referera till nyckel valvet och hemligheterna i parameter filen. 

Du kan också implementera autentiseringsuppgifterna för autentisering för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault. 

- [Skydda indata och utdata i körnings historiken i Azure Logic Apps](logic-apps-securing-a-logic-app.md#obfuscate)

- [Säkerhets rekommendationer för parametrar](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

- [Säker åtkomst till parameter indata i Azure Logic Apps](logic-apps-securing-a-logic-app.md#access-to-parameter-inputs)

- [Överför säkra parameter värden under distributionen med Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security benchmark: skydd mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Använd en centralt hanterad program vara mot skadlig kod

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser. Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure Logic Apps), men det körs inte på kund innehållet.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure Backup), men det körs inte på ditt innehåll. 

Genomsök alla filer som laddas upp till Azure-resurser som inte är Compute, till exempel App Service, Data Lake Storage, Blob Storage osv. 

Använd Azure Security Center s hot identifiering för data tjänster för att identifiera skadlig kod som laddats upp till lagrings konton. 

- [Förstå Microsofts program mot skadlig kod för Azure Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md)

- [Förstå Azure Security Centers hot identifiering för data tjänster](../security-center/azure-defender.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för skadlig program vara uppdateras

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Azure Security benchmark: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Säkerställ regelbunden automatisk säkerhets kopiering

**Vägledning**: implementera en lösning för haveri beredskap (Dr) så att du kan skydda data, snabbt återställa de resurser som har stöd för kritiska affärs funktioner och fortsätta driften att underhålla verksamhets kontinuitet (BC).

Den här strategin för haveri beredskap fokuserar på att konfigurera din primära Logic-app för redundans till en standby-eller säkerhets kopierings-Logic-app på en annan plats där Azure Logic Apps också är tillgängligt. På så sätt kan den sekundära åtgärden ta på arbetet om den primära förlusten uppstår, avbrott eller haverier. Den här strategin kräver att din sekundära Logic-app och beroende resurser redan har distribuerats och är redo på den alternativa platsen.

Dessutom bör du expandera din Logic Apps-underliggande arbets flödes definition till en Azure Resource Manager-mall. Den här mallen definierar infrastruktur, resurser, parametrar och annan information för etablering och distribution av din Logic app.

- [Lär dig mer om verksamhets kontinuitet och haveri beredskap för Azure Logic Apps](business-continuity-disaster-recovery-guidance.md)

- [Automatisera distribution för Azure Logic Apps med hjälp av Azure Resource Manager mallar](logic-apps-azure-resource-manager-templates-overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: implementera en lösning för haveri beredskap (Dr) så att du kan skydda data, snabbt återställa de resurser som har stöd för kritiska affärs funktioner och fortsätta driften att underhålla verksamhets kontinuitet (BC).

Den här strategin för haveri beredskap fokuserar på att konfigurera din primära Logic-app för redundans till en standby-eller säkerhets kopierings-Logic-app på en annan plats där Azure Logic Apps också är tillgängligt. På så sätt kan den sekundära åtgärden ta på arbetet om den primära förlusten uppstår, avbrott eller haverier. Den här strategin kräver att din sekundära Logic-app och beroende resurser redan har distribuerats och är redo på den alternativa platsen.

Dessutom bör du expandera din Logic Apps-underliggande arbets flödes definition till en Azure Resource Manager-mall. Den här mallen definierar infrastruktur, resurser, parametrar och annan information för etablering och distribution av din Logic app.

Varje begär ande slut punkt i en Logic app har en signatur för delad åtkomst (SAS) i slut punktens URL. Om du använder Azure Key Vault för att lagra dina hemligheter, se till att vanliga automatiserade säkerhets kopieringar av nycklar och URL: er sker.

- [Lär dig mer om verksamhets kontinuitet och haveri beredskap för Azure Logic Apps](business-continuity-disaster-recovery-guidance.md)

- [Automatisera distribution för Azure Logic Apps med hjälp av Azure Resource Manager mallar](logic-apps-azure-resource-manager-templates-overview.md)

- [Skydda åtkomst och data i Azure Logic Apps med hjälp av SAS](logic-apps-securing-a-logic-app.md#sas)

- [Säkerhetskopiera Key Vault nycklar](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: din strategi för haveri beredskap bör fokusera på att konfigurera din primära Logic-app för redundans till en standby-eller säkerhets kopierings-Logic-app på en annan plats där Azure Logic Apps också är tillgängligt. På så sätt kan den sekundära åtgärden ta på arbetet om den primära förlusten uppstår, avbrott eller haverier. Den här strategin kräver att din sekundära Logic-app och beroende resurser redan har distribuerats och är redo på den alternativa platsen.

Testa återställning av säkerhetskopierade nycklar som hanteras av kunden. Observera att detta endast gäller för Logic Apps som körs i integrerings tjänst miljöer (ISE).

- [Lär dig mer om verksamhets kontinuitet och haveri beredskap för Azure Logic Apps](business-continuity-disaster-recovery-guidance.md)

- [Konfigurera Kundhanterade nycklar för att kryptera data i vila för integrerings tjänst miljöer (ISEs) i Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

- [Återställa Key Vault-nycklar i Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: din strategi för haveri beredskap bör fokusera på att konfigurera din primära Logic-app för redundans till en standby-eller säkerhets kopierings-Logic-app på en annan plats där Azure Logic Apps också är tillgängligt. På så sätt kan den sekundära åtgärden ta på arbetet om den primära förlusten uppstår, avbrott eller haverier. Den här strategin kräver att din sekundära Logic-app och beroende resurser redan har distribuerats och är redo på den alternativa platsen. 

Skydda säkerhets kopiering av Kundhanterade nycklar. Observera att detta endast gäller för Logic Apps som körs i integrerings tjänst miljöer (ISE).

Aktivera Soft-Delete och rensa skydd i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

- [Lär dig mer om verksamhets kontinuitet och haveri beredskap för Azure Logic Apps](business-continuity-disaster-recovery-guidance.md)

- [Konfigurera Kundhanterade nycklar för att kryptera data i vila för integrerings tjänst miljöer (ISEs) i Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

- [Aktivera Soft-Delete och rensa skydd i Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security benchmark: incident svar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten. 

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Utnyttja NISTs hanterings guide för dator säkerhet för att hjälpa dig att skapa en egen incident svars plan](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen. 

Dessutom är det tydligt att markera prenumerationer (t. ex. produktion, icke-Prod.) med hjälp av taggar och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser, särskilt för bearbetning av känsliga data.  Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems funktioner för incident svar på en vanlig takt för att hjälpa till att skydda dina Azure-resurser. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [NIST-guide för att testa, träna och träna program för IT-planer och funktioner](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olagligt eller obehörig part. Granska incidenter när du är säker på att problemen är lösta.

- [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export för att identifiera risker för Azure-resurser. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för arbets flödes automatisering i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer för att skydda dina Azure-resurser.

- [Konfigurera automatisering av arbets flöden och Logic Apps](../security-center/workflow-automation.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstester och Red Team-tester

*Mer information finns i [övningen för Azure Security benchmark: inträngande tester och röda team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program.

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)