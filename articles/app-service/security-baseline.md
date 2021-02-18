---
title: Azures säkerhets bas linje för App Service
description: App Service säkerhets bas linje ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: app-service
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d7753de473d90c45fecb20403352a594ef52dafe
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100586296"
---
# <a name="azure-security-baseline-for-app-service"></a>Azures säkerhets bas linje för App Service

Azures säkerhets bas linje för App Service innehåller rekommendationer som hjälper dig att förbättra säkerhets position för din distribution. Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](../security/benchmarks/overview-v1.md), som ger rekommendationer om hur du kan skydda dina moln lösningar i Azure med våra bästa praxis rikt linjer. Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för App Service. **Kontroller** som inte är tillämpliga på App Service har uteslutits.

Om du vill se hur App Service helt mappar till Azures säkerhets mätning, se den [fullständiga app service mappnings filen för säkerhets bas linjen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets principen för Azure-säkerhet: nätverks säkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: när du använder app service på den isolerade pris nivån, även kallat en app service-miljön (ASE) kan du distribuera direkt till ett undernät i Azure-Virtual Network. Använd Nätverks säkerhets grupper för att skydda dina Azure App Service Environment genom att blockera inkommande och utgående trafik till resurser i det virtuella nätverket eller begränsa åtkomsten till appar i en App Service-miljön.

Nätverks säkerhets grupper innehåller som standard en implicit neka-regel som lägst prioritet, vilket kräver att du uttryckligen lägger till regler för att tillåta. Lägg till regler för att tillåta nätverks säkerhets gruppen baserat på en minst privilegie rad nätverks metod. De underliggande virtuella datorerna som används som värd för App Service-miljön är inte direkt tillgängliga eftersom de finns i en Microsoft-hanterad prenumeration.

Skydda en App Service-miljön genom att dirigera trafik via en brand vägg för webbaserade program (WAF) som är aktive rad Azure Application Gateway. Använd tjänstens slut punkter tillsammans med Application Gateway för att skydda inkommande publicerings trafik till din app.  

I App Service för flera innehavare (en app som inte är i isolerad nivå) använder du nätverks säkerhets grupper för att blockera utgående trafik från din app. Aktivera dina appar för att få åtkomst till resurser i eller via en Virtual Network med hjälp av funktionen för Virtual Network-integrering. Den här funktionen kan också användas för att blockera utgående trafik till offentliga adresser från appen. Virtual Network-integrering kan inte användas för att ge inkommande åtkomst till en app.  

Skydda inkommande trafik till din app med:
- Åtkomst begränsningar – en serie tillåtna eller nekade regler som kontrollerar inkommande åtkomst
- Tjänst slut punkter – kan neka inkommande trafik från utanför de angivna virtuella nätverken eller undernäten
- Privata slut punkter – exponera din app för din Virtual Network med en privat IP-adress. När de privata slut punkterna är aktiverade på din app är de inte längre Internet-tillgängliga

När du använder Virtual Network integrerings funktion med virtuella nätverk i samma region, använder du nätverks säkerhets grupper och dirigera tabeller med användardefinierade vägar. Användardefinierade vägar kan placeras i integrations under nätet för att skicka utgående trafik som tänkt.  

Överväg att implementera en Azure-brandvägg för att centralt skapa, tillämpa och logga program-och nätverks anslutnings principer i dina prenumerationer och virtuella nätverk. Azure-brandväggen använder en statisk offentlig IP-adress för virtuella nätverks resurser, vilket tillåter yttre brand väggar att identifiera trafik som kommer från ditt virtuella nätverk. 

- [Låsa en App Service-miljön](environment/firewall-integration.md)

- [Öppna webb program säkerhets projekt (OWASP) skydd mot de 10 viktigaste säkerhets problemen](https://owasp.org/www-project-top-ten/)

- [Nätverkssäkerhetsgrupper](../virtual-network/network-security-groups-overview.md)

- [Integrera en app med ett virtuellt Azure-nätverk](web-sites-integrate-with-vnet.md)

- [Nätverksöverväganden för en App Service-miljö](environment/network-info.md)

- [Så här skapar du en extern ASE](environment/create-external-ase.md)

- [Så här skapar du en intern ASE](environment/create-ilb-ase.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverks gränssnitt

**Vägledning**: implementera rekommendationer för nätverks skydd från Azure Security Center för att skydda nätverks resurser och konfigurationer som är relaterade till dina App Service appar och API: er.

Använd Azure-brandväggen för att skicka trafik och centralt skapa, tillämpa och logga program-och nätverks anslutnings principer över prenumerationer och virtuella nätverk. Azure-brandväggen använder en statisk offentlig IP-adress för dina virtuella nätverks resurser, vilket tillåter yttre brand väggar att identifiera trafik som kommer från din Virtual Network. Azure Firewall service är också helt integrerat med Azure Monitor för loggning och analys.

- [Översikt över Azure Firewall](../firewall/overview.md)

- [Förstå nätverks säkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Så här aktiverar du övervakning och skydd av App Service](../security-center/defender-for-app-service-introduction.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: skydda en app som är tillgänglig för Internet i en app service-miljön (ASE) genom att:
- Distribuera en brand vägg för webbaserade program (WAF) med Azure Application Gateway framför en app mot Internet
- Använd åtkomst begränsningar för att skydda inkommande trafik till Application Gateway
- Skydda appen med Azure Active Directory (Azure AD) för att säkerställa autentisering
- Ange den lägsta TLS-versionen till 1,2
- Ställ endast in appen på HTTPS

Kör all utgående program trafik via en Azure Firewall-enhet och övervaka loggarna. 

För att skydda en tillgänglig Internet-app i App Service för flera innehavare (till exempel inte på den isolerade nivån)
- Distribuera en brand vägg för webbaserade program som är aktive rad framför en app
- Använd åtkomst begränsningar eller tjänst slut punkter för att skydda inkommande trafik till brand vägg för webbaserade program (WAF)
- Skydda appen med Azure AD för att säkerställa autentisering
- Ange den lägsta TLS-versionen till 1,2
- Ställ endast in appen på HTTPS
- Använd integrering med virtuella nätverk och appens inställning WEBSITE_VIRTUAL NETWORK_ROUTE_ALL för att göra all utgående trafik beroende av nätverks säkerhets grupper och användardefinierade vägar i integrations under nätet.

Precis som App Service Environment-appen, kör all program trafik utgående via en Azure Firewall-enhet och övervakar loggarna i appen.

Granska och följ dessutom rekommendationer i avsnittet låsa ett App Service-miljön dokument.

- [Låsa en App Service-miljön](environment/firewall-integration.md)

- [Azure Web Application-brandvägg på Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Begränsningar för Azure App Service åtkomst](app-service-ip-restrictions.md)

- [Spåra WAF-aviseringar och enkelt övervaka trender med Azure Monitor ](../azure-monitor/overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: skydda App Service-miljön enligt beskrivningen i avsnittet låsa en app service-miljön-dokumentation. Använd de integrerade hot informations funktionerna i Azure Security Center om du vill neka kommunikation med kända skadliga eller oanvända offentliga IP-adresser. Använd åtkomst begränsningar för att skydda inkommande trafik till Application Gateway. 

Skydda App Service för flera innehavare (en app som inte finns på en isolerad nivå) med en offentlig slut punkt från Internet. Den tillåter endast trafik från ett särskilt undernät inom Virtual Network och blockerar allt annat. Använd åtkomst begränsningar för att konfigurera nätverks Access Control listor (IP-begränsningar) för att låsa tillåten inkommande trafik.

Definiera prioritet bland listan över beställda tillåtna eller nekade för att hantera nätverks åtkomst till din app. Listan kan innehålla IP-adresser eller Virtual Network undernät. En implicit "Neka alla"-regel finns i slutet av listan när den innehåller en eller flera poster. Den här funktionen fungerar med alla App Service värdbaserade arbets belastningar inklusive, Web Apps, API Apps, Linux-appar, Linux container Apps och functions. 

Använd tjänstens slut punkter för att begränsa åtkomsten till din webbapp från en Azure-Virtual Network. Begränsa åtkomsten till en App Service för flera innehavare (en app som inte finns på en isolerad nivå), från valda undernät med tjänst slut punkter. 

- [Statiska IP-begränsningar för Azure App Service](app-service-ip-restrictions.md)

- [Azure Web Application-brandvägg på Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Konfigurera en brand vägg för webbaserade program (WAF) för App Service-miljön](environment/app-service-app-service-environment-web-application-firewall.md)

- [Skydda ASE enligt beskrivningen i låsa en App Service-miljön](environment/firewall-integration.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets"></a>1,5: registrera nätverks paket

**Vägledning**: övervakar förfrågningar och svar som skickas till och från App Service appar med Security Center. Attacker mot ett webb program kan övervakas med hjälp av en real tids Application Gateway med brand vägg för webbaserade program som har Aktiver ATS med integrerad loggning från Azure Monitor för att spåra brand Väggs aviseringar för webb program och enkelt övervaka trender.

- [Azure Web Application-brandvägg på Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: hantera trafik för en app i ett App Service-miljön:

- Skydda App Service-miljön enligt beskrivningen i låsa en App Service-miljön
- Distribuera ett Application Gateway som har en brand vägg för Azure-webbprogram framför dina appar mot Internet
- Ange att appen endast ska vara tillgänglig via HTTPS

Hantera trafik för en tillgänglig Internet-app i App Service för flera innehavare (inte på den isolerade nivån): 

- Distribuera ett Application Gateway med brand vägg för Azure-webbprogram aktive rad framför dina appar mot Internet
- Använd åtkomst begränsningar eller tjänst slut punkter för att skydda inkommande trafik till brand väggen för webb program. Funktionen åtkomst begränsningar fungerar med alla App Service värdbaserade arbets belastningar, inklusive Web Apps, API Apps, Linux-appar, Linux container Apps och functions.

- Ange att appen endast ska vara tillgänglig via HTTPS
- Begränsa åtkomsten till din App Service-app med statiska IP-begränsningar så att den bara tar emot trafik från VIP på en Programgateway som den enda adressen med åtkomst.

Granska de refererade länkarna om du vill ha mer information.

- [Statiska IP-begränsningar för Azure App Service](app-service-ip-restrictions.md)

- [Azure Web Application-brandvägg på Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Så här konfigurerar du end-to-end-TLS genom att använda Application Gateway med portalen](../application-gateway/end-to-end-ssl-portal.md)

- [Skydda ASE enligt beskrivningen i låsa en App Service](./environment/firewall-integration.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: App Service har ett antal slut punkter som används för att hantera tjänsten. Dessa slut punkts adresser ingår också i AppServiceManagement IP service tag. AppServiceManagement-taggen används endast med en App Service-miljön för att tillåta sådan trafik. 

Du kan tillåta eller neka trafiken för motsvarande tjänst genom att ange namnet på service tag i det relevanta käll-eller mål fältet för en regel. App Service inkommande adresser spåras i AppService IP service tag. Det finns ingen IP-tjänstetagg som innehåller de utgående adresser som används av App Service.

Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

- [Tjänst taggar för virtuellt nätverk](../virtual-network/service-tags-overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för nätverks inställningar som är relaterade till dina App Service-appar. 

Underhåll säkerhetskonfigurationer med hjälp av Azure Policy alias i namn områdena "Microsoft. Web" och "Microsoft. Network". Skapa anpassade principer för att granska eller tillämpa nätverks konfigurationen för dina App Service-appar. 

Använd inbyggda princip definitioner för App Service, till exempel:
- Appen bör använda en tjänst slut punkt för virtuellt nätverk
- Appen bör endast vara tillgänglig via HTTPS
- Ange den lägsta TLS-versionen till den aktuella versionen

Granska de refererade länkarna om du vill ha mer information.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här konfigurerar du end-to-end-TLS genom att använda Application Gateway med portalen](../application-gateway/end-to-end-ssl-portal.md)

- [Skydda ASE enligt beskrivningen i låsa en App Service](./environment/firewall-integration.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för nätverks säkerhets grupper och andra relaterade resurser, inklusive trafikflöde i App Service.

Ange affärs behov, varaktighet och så vidare, med fältet Beskrivning för alla regler som tillåter trafik till eller från ett nätverk för enskilda regler för nätverks säkerhets grupper.

Använd någon av de inbyggda Azure Policy definitionerna som är relaterade till taggade effekter, till exempel "Kräv tagg och dess värde", för att säkerställa att alla resurser skapas med taggar och meddela dig om eventuella befintliga otaggade resurser. Använd Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

- [Begränsningar för Azure App Service åtkomst](./app-service-ip-restrictions.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar av nätverks inställningar och eventuella resurser som är relaterade till App Service. 

Använd en av de flera Azure Policy inbyggda definitionerna för App Service, till exempel en princip som granskar appar för användning av slut punkts tjänsten för virtuella nätverk. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks inställningar eller resurser sker. 

Granska detaljerade säkerhets aviseringar och rekommendationer i Security Center på portalen eller med programmerings verktyg. Exportera den här informationen eller skicka den till andra övervaknings verktyg i din miljö. Det finns verktyg för att exportera aviseringar och rekommendationer antingen manuellt eller fort löp ande. Med dessa verktyg kan du:
 
- Exportera kontinuerligt till en Log Analytics-arbetsyta
- Exportera kontinuerligt till Azure Event Hubs (för integreringar med Siem från tredje part)
- Exportera till en CSV-fil (en stund)

Vi rekommenderar att du skapar en process med automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och snabbt identifierar ändringar.

- [Visa och hämta Azure aktivitets logg händelser](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

- [Exportera säkerhetsaviseringar och rekommendationer](../security-center/continuous-export.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: integrera din app service-miljön (ASE) med Azure Monitor för att skicka loggar till Azure Storage, Azure Event Hubs eller Log Analytics. Aktivera diagnostiska inställningar i Azure aktivitets loggen för gransknings loggning i kontroll plan. Säkerhets aviseringar från Security Center publiceras i Azure aktivitets loggen. Granska Azure Activity Log-data, vilket gör att du kan fastställa "vad, vem och när" för alla Skriv åtgärder (placering, POST, borttagning) som utförs på kontroll Plans nivån för Azure App Service och andra Azure-resurser. Spara dina frågor för framtida användning, fäst frågeresultat på Azure-instrumentpaneler och skapa logg aviseringar. Använd också data åtkomst REST API i Application Insights för att få åtkomst till din telemetri program mässigt.

Använd Microsoft Azure Sentinel, en skalbar, molnbaserad, SIEM (Security information Event Management) som är tillgänglig för att ansluta till olika data källor och anslutningar, baserat på dina affärs behov. Du kan också aktivera och anskärma data till ett SIEM-system (Security information Event Management) från tredje part, till exempel Barracuda på Azure Marketplace.

- [Logga ASE-aktivitet](environment/using-an-ase.md#logging)

- [Så här aktiverar du diagnostikinställningar för Azure App Service](troubleshoot-diagnostic-logs.md)

- [Så här aktiverar du Application Insights](../azure-monitor/app/app-insights-overview.md)

- [Exportera telemetri från Application Insights](../azure-monitor/app/export-telemetry.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Aktivera diagnostiska inställningar i Azure aktivitets loggen för kontroll Plans gransknings loggning av App Service. Skicka loggarna till en Log Analytics-arbetsyta, Azure Event Hub eller ett Azure Storage konto.
"Vad, vem och när" för Skriv åtgärder (POST, POST, DELETE) som utförs på kontroll Plans nivån kan fastställas med hjälp av Azures aktivitets logg data för App Service och andra Azure-resurser.

Dessutom tillhandahåller Azure Key Vault centraliserad hemlig hantering med åtkomst principer och gransknings historik. 

- [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](../azure-monitor/essentials/activity-log.md)

- [Så här aktiverar du diagnostikinställningar för Azure App Service](troubleshoot-diagnostic-logs.md)

- [Resource Manager-åtgärder](../role-based-access-control/resource-provider-operations.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: i Azure Monitor anger du logg kvarhållningsperioden för de Log Analytics arbets ytor som är kopplade till dina App Service resurser enligt organisationens regler för efterlevnad.
- [Ange parametrar för logg bevarande](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: granska diagnostiska inställningar i Azure aktivitets loggen i App Service resurser med loggarna som skickas till en Log Analytics arbets yta. Utför frågor i Log Analytics för att söka efter termer, identifiera trender, analysera mönster och tillhandahålla många andra insikter baserat på insamlade data.

Använd Application Insights för dina App Service appar och samla in logg-, prestanda-och fel data. Visa telemetridata som samlats in av Application Insights i Azure Portal.

Om du har distribuerat en brand vägg för webbaserade program (WAF) kan du övervaka attacker mot dina App Service-appar med hjälp av en real tids brand Väggs logg för webb program. Loggen är integrerad med Azure Monitor för att spåra brand Väggs aviseringar för webb program och enkelt övervaka trender.

Använd Azure Sentinel, en skalbar och Cloud-ursprunglig säkerhets informations händelse hantering (SIEM) för att integrera med olika data källor och anslutningar, enligt krav. Alternativt kan du aktivera och fordonsbaserad data till en lösning för händelse hantering för säkerhets information från tredje part på Azure Marketplace.

- [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](../azure-monitor/essentials/activity-log.md)

- [Så här aktiverar du Application Insights](../azure-monitor/app/app-insights-overview.md)

- [Så här integrerar du App Service-miljön med Azure Application Gateway (WAF)](environment/integrate-with-application-gateway.md)

- [Aktivera Azure-kontroll på kort](../sentinel/quickstart-onboard.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: Konfigurera Security Center i din Azure-prenumeration och granska de genererade aviseringarna. Använd Azure Monitor för att hämta dina aktivitets logg data till en Händelsehubben där den kan läsas av en SIEM-lösning (Security information Event Management), till exempel Azure Sentinel. 

Övervaka attacker mot dina App Service-appar med hjälp av en real tids brand Väggs logg för webb program med en distribuerad Azure Web Application-brandvägg (WAF). Loggen är integrerad med Azure Monitor för att spåra WAF-aviseringar (Web Application Firewall) och enkelt övervaka trender.

- [Så här integrerar du App Service-miljön med Azure Application Gateway (WAF)](environment/integrate-with-application-gateway.md)

- [Exportera säkerhetsaviseringar och rekommendationer](../security-center/continuous-export.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azures säkerhets benchmark: identitets-och åtkomst kontroll](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: Azure Active Directory (Azure AD) har inbyggda roller som måste tilldelas explicit och kunna köras av frågor. Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

- [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

- [Använda hanterade identiteter för App Service och Azure Functions](overview-managed-identity.md?context=azure%2Factive-directory%2Fmanaged-identities-azure-resources%2Fcontext%2Fmsi-context&amp;tabs=dotnet)

- [Lägga till eller ta bort rolltilldelningar för Azure med hjälp av Azure-portalen](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: Azure Active Directory (Azure AD) har inte begreppet standard lösen ord. Det ger kontroll Plans åtkomst till App Service.

Undvik vanligt vis att implementera standard lösen ord för användar åtkomst när du skapar dina egna appar. Använd en av de identitets leverantörer som är tillgängliga som standard för App Service, till exempel Azure AD, Microsoft-konto, Facebook, Google eller Twitter.

Inaktivera anonym åtkomst om du inte behöver stöd för det. 

- [Identitets leverantörer som är tillgängliga som standard i Azure App Service](overview-authentication-authorization.md#identity-providers)

- [Autentisering och auktorisering i Azure App Service och Azure Functions](overview-authentication-authorization.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton. Använd funktionerna för identitets-och åtkomst hantering i Security Center för att övervaka och spåra antalet administrativa konton. 

Använd rekommendationer från Security Center eller inbyggda Azure-principer, till exempel:

- Det bör finnas fler än en ägare som tilldelats din prenumeration. 
- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med ägar behörigheter bör tas bort från din prenumeration

Skapa en process för att övervaka konfigurationer av nätverks resurser och identifiera ändringar i administrativa konton.

- [Använda Azure Security Center för att övervaka identitet och åtkomst](../security-center/security-center-identity-access.md)

- [Använda Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Lär dig mer om att bevilja användare åtkomst till program](../role-based-access-control/overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Använd Azure Active Directory enkel inloggning (SSO)

**Vägledning**: autentisera App Service via Azure Active Directory (Azure AD). Den tillhandahåller en OAuth 2,0-tjänst för din identitetsprovider och möjliggör åtkomst till mobil-och webb program. 

App Service appar använder federerade identiteter, där en tredjeparts identitets leverantör hanterar användar identiteter och autentiseringspaket åt dig. Dessa identitets leverantörer är tillgängliga som standard:

- Azure AD
- Microsoft-konto

- Facebook

- Google

- Twitter

När du aktiverar autentisering och auktorisering med någon av dessa leverantörer, är dess inloggnings slut punkt tillgänglig för användarautentisering och för verifiering av autentiseringstoken från providern.

- [Förstå autentisering och auktorisering i Azure App Service](overview-authentication-authorization.md#identity-providers)

- [Lär dig mer om autentisering och auktorisering i Azure App Service](overview-authentication-authorization.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory-baserad åtkomst

**Vägledning**: aktivera funktionen multifaktorautentisering i Azure Active Directory (Azure AD) och följ rekommendationerna för identitets-och åtkomst hantering i Security Center.

Implementera multifaktorautentisering för Azure AD. Administratörer måste se till att prenumerations kontona i portalen är skyddade. Prenumerationen är sårbar för attacker eftersom den hanterar de resurser som du har skapat. 

- [MFA för Azure-säkerhet](/previous-versions/azure/security/develop/secure-aad-app)

- [Aktivera MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Använd säkra, Azure-hanterade arbets stationer för administrativa uppgifter

**Vägledning**: Använd Privileged Access-arbetsstationer (Paw) med multifaktorautentisering konfigurerad för att logga in på och konfigurera Azure-resurser.

- [Lär dig mer om arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Aktivera MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: Använd PRIVILEGED Identity Management (PIM) i Azure Active Directory (Azure AD) för att skapa loggar och varningar när misstänkta eller osäkra aktiviteter inträffar i miljön.

Dessutom kan du använda Azure AD-farlighets identifiering för att visa aviseringar och rapporter om riskfyllda användar beteenden.

Skydd mot hot i Security Center ger omfattande försvar för din miljö, som innehåller hot skydd för Azure Compute-resurser som Windows-datorer, Linux-datorer, App Service och Azure-behållare.

- [Distribuera Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Förstå identifieringar av Azure AD-risker](../active-directory/identity-protection/overview-identity-protection.md)

- [Skydd mot hot för Azure Compute-resurser](../security-center/azure-defender.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Använd villkorlig åtkomst med namngivna platser för att tillåta åtkomst till Azure Portal från enbart vissa logiska grupperingar av IP-adressintervall, länder eller regioner.

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: Använd Azure Active Directory (Azure AD) som central autentiserings-och auktoriserings system för dina App Service appar. Azure AD skyddar data med stark kryptering för data i vila och under överföring och även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

- [Konfigurera dina Azure App Service-appar för att använda Azure AD-inloggning](configure-authentication-provider-aad.md)

- [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: identifiera inaktuella konton med loggarna som tillhandahålls av Azure Active Directory (Azure AD). Använd granskningar av Azure Identity Access för att effektivt hantera grupp medlemskap och åtkomst till företags program, samt roll tilldelningar. Granska användar åtkomsten regelbundet för att se till att endast de avsedda användarna har fortsatt åtkomst. 

- [Förstå Azure AD repor ting](../active-directory/reports-monitoring/index.yml)

- [Så här använder du granskningar av Azure Identity Access](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: Använd Azure Active Directory (Azure AD) som central autentiserings-och auktoriserings system för dina App Service appar. Azure AD skyddar data med stark kryptering för data i vila och under överföring, salter, hashar och lagrar användarautentiseringsuppgifter på ett säkert sätt.

Åtkomst till Azure AD-inloggning, gransknings-och risk händelse logg källor gör att du kan integrera med Azure Sentinel eller en SIEM-lösning (Security information Event Management) från tredje part. Effektivisera processen genom att skapa diagnostiska inställningar för Azure AD-användarkonton och skicka gransknings-och inloggnings loggarna till en Log Analytics-arbetsyta. Önskade logg aviseringar kan konfigureras i Log Analytics.

- [Konfigurera dina Azure App Service-appar för att använda Azure AD-inloggning](configure-authentication-provider-aad.md)

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Aktivera Azure-kontroll på kort](../sentinel/quickstart-onboard.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: varning vid inloggnings beteende för konto

**Vägledning**: Använd Azure Active Directory (Azure AD) som central autentiserings-och auktoriserings system för dina App Service appar. 

Använd Azure AD Identity Protection för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter, till exempel beteende för konto inloggnings avvikelse i kontroll planet med Azure Portal. Du kan också mata in data i Azure Sentinel för ytterligare undersökning. 

- [Så här konfigurerar du din Azure App Service-app att använda Azure AD-inloggning](configure-authentication-provider-aad.md)

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: inte tillgänglig för App Service. Customer Lockbox stöds inte för Azure App Service.

- [Lista över Customer Lockbox tjänster som stöds](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security benchmark: Data Protection](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra App Service resurser som lagrar eller bearbetar känslig information.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: en app service-miljön implementera separata prenumerationer, hanterings grupper eller både och för utvecklings-, test-och produktions miljöer. Du kan isolera appar som bearbetar känslig information från andra appar på samma sätt. Distribuera din App Service-app till en Virtual Network. Använd Nätverks säkerhets grupper och undernät för ytterligare program isolering. 

Det finns två distributions typer för en App Services miljö (ASE). Båda låter dig isolera trafiken utifrån dina affärs behov.

- Extern program tjänst miljö – exponerar App Service-miljön värdbaserade appar på en IP-adress som kan nås via Internet.

-  intern belastningsutjämnare (ILB) program tjänst miljö – visar App Service-miljön värdbaserade appar på en IP-adress i Virtual Network. Den interna slut punkten är en intern belastningsutjämnare (ILB), vilket är anledningen till att den kallas en ILB-ASE. 

För App Service för flera innehavare (en app som inte finns på den isolerade nivån) använder du Virtual Network integration för appens åtkomst till resurser i det virtuella nätverket.  Använd privat plats åtkomst för att göra en app åtkomlig enbart från ett privat nätverk, till exempel en i ett virtuellt Azure-nätverk. Virtual Network integration används bara för att göra utgående samtal från din app till din Virtual Network. Virtual Network integrations funktionen fungerar annorlunda när den används med ett virtuellt nätverk i samma region och med virtuella nätverk i andra regioner. 
 
- [Nätverksöverväganden för en App Service-miljö](environment/network-info.md)

- [Så här skapar du en extern ASE](environment/create-external-ase.md)

- [Så här skapar du en intern ASE](environment/create-ilb-ase.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: medan funktionerna för data identifiering, klassificering och förlust av förlust inte är tillgängliga för App Service, kan du minska risken för data exfiltrering från det virtuella nätverket genom att ta bort alla regler där målet använder en "tag" för Internet-eller Azure-tjänster. 

Microsoft hanterar den underliggande infrastrukturen för App Service och har implementerat strikta kontroller för att förhindra förlust eller exponering av dina data.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: Använd den lägsta standard versionen av TLS 1,2 som kon figurer ATS i TLS/SSL-inställningar för kryptering av all information i överföring. Se också till att alla HTTP-begäranden omdirigeras till HTTPS.

- [Förstå kryptering i överföring för Azure App Service-webbappar](security-recommendations.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: för närvarande inte tillgänglig. Funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för App Service. 

Tagga App Service appar som kan bearbeta känslig information. Implementera lösningar från tredje part, om det behövs för efterlevnad.

Microsoft hanterar den underliggande plattformen och behandlar all kund information som känslig och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: Använd rollbaserad åtkomst kontroll för att kontrol lera åtkomst till resurser

**Vägledning**: Använd rollbaserad åtkomst kontroll (Azure RBAC) i Azure Active Directory (Azure AD) för att styra åtkomsten till App Service kontroll planet vid Azure Portal.

- [Så här konfigurerar du RBAC i Azure](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: webbplats innehåll i en app service-app, till exempel filer, lagras i Azure Storage, som automatiskt krypterar innehållet i vila. Välj att lagra program hemligheter i Key Vault och hämta dem vid körning.

Kunder som har tillhandahållit hemligheter är krypterade i vila medan de lagras i App Service konfigurations databaser.

Observera att även om lokalt anslutna diskar kan användas av webbplatser som temporär lagring, (till exempel D:\Local och% TMP%), är de inte krypterade i vila.

- [Förstå data skydds kontroller för Azure App Service]()

- [Förstå Azure Storage kryptering i vila](../storage/common/storage-service-encryption.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar vid ändringar i produktions App Service appar och andra viktiga eller relaterade resurser.

- [Så här skapar du aviseringar för Azure aktivitets logg händelser](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security benchmark: sårbarhet Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: anta en DevSecOps-metod för att se till att dina App Service appar är säkra och förblir skyddade under hela livs cykeln. DevSecOps införlivar din organisations säkerhets team och deras funktioner i din DevOps-praxis och ger säker ansvar för alla i teamet.

Granska och följ rekommendationer från Security Center för att skydda dina App Service appar.

- [Så här lägger du till kontinuerlig säkerhets validering till din CI/CD-pipeline](/azure/devops/migrate/security-validation-cicd-pipeline?preserve-view=true&view=azure-devops)

- [Så här implementerar du rekommendationer för Azure Security Center sårbarhets bedömning](../security-center/deploy-vulnerability-assessment-vm.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: Microsoft utför sårbarhets hantering på de underliggande system som har stöd för App Service. Du kan dock använda allvarlighets graden hos rekommendationerna i Security Center samt säkra Poäng för att mäta risker i din miljö. Dina säkra Poäng baseras på hur många Security Center rekommendationer som du har begränsat. För att prioritera rekommendationerna för att lösa det första, bör du tänka igenom allvarlighets graden för var och en.

- [Referens guide för säkerhets rekommendationer](../security-center/recommendations-reference.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga eller identifiera alla resurser (till exempel data bearbetning, lagring, nätverk, portar, protokoll och så vidare) i dina prenumerationer. Se till att lämpliga behörigheter tillämpas på din klient och du kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via resurs diagram, rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser som går framåt.

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser med hjälp av metadata för att logiskt organisera dem i en taxonomi.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer om det behövs för att organisera och spåra Azure-resurser. Stäm av inventering med jämna mellanrum och se till att obehöriga resurser tas bort från dina prenumerationer som en del av den här processen.

Välj Azure Policy om du vill ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer genom att använda följande inbyggda princip definitioner:

- Otillåtna resurstyper
- Tillåtna resurstyper

Granska de refererade länkarna om du vill ha mer information.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla inventering av godkända Azure-resurser

**Vägledning**: skapa en inventering av godkända Azure-resurser och godkänd program vara för beräknings resurser utifrån organisationens behov.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i dina prenumerationer.

Använd Azure Resource Graph för att fråga eller identifiera resurser i prenumerationerna.  Se till att alla Azure-resurser som finns i miljön är godkända. 

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: Använd Azure Resource Graph för att fråga eller identifiera resurser i dina prenumerationer och se till att de identifierade Azure-resurserna godkänns baserat på organisationens principer.

Använd WebJobs i App Service för att övervaka ej godkända program som distribueras i beräknings resurser. Använd WebJobs för att köra ett program eller skript i samma instans som en webbapp, API-app eller mobilapp. Definiera webb jobbs konfiguration och övervakning med loggar. På sidan jobb körnings information väljer du växla utdata för att se texten i logg innehållet. Observera att WebJobs inte stöds ännu för App Service i Linux.

- [Kör bakgrunds aktiviteter med WebJobs i Azure App Service](webjobs-create.md)

- [Självstudie – Skapa och hantera principer för att genomdriva efterlevnad](../governance/policy/tutorials/create-and-manage.md)

- [Snabb start – kör din första resurs diagram fråga med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: kontrol lera att alla Azure-resurser som finns i miljön är godkända. Använd Azure Policy för att ange begränsningar för den typ av resurser som kan skapas i dina prenumerationer. Ta bort eventuella distribuerade program som inte har godkänts enligt organisationens principer.

- [Kör bakgrunds aktiviteter med WebJobs i Azure App Service](webjobs-create.md)

- [Självstudie – Skapa och hantera principer för att genomdriva efterlevnad](../governance/policy/tutorials/create-and-manage.md)

- [Snabb start – kör din första resurs diagram fråga med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: kontrol lera att alla Azure-resurser som finns i miljön är godkända. Använd Azure Policy för att ange begränsningar för den typ av resurser som kan skapas i dina prenumerationer. Ta bort eventuella distribuerade program som inte har godkänts enligt organisationens principer. 

- [Kör bakgrunds aktiviteter med WebJobs i Azure App Service](webjobs-create.md)

- [Självstudie – Skapa och hantera principer för att genomdriva efterlevnad](../governance/policy/tutorials/create-and-manage.md)

- [Snabb start – kör din första resurs diagram fråga med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: skapa en process för att granska oauktoriserade Azure-tjänster regelbundet så att endast auktoriserade Azure-tjänster används i dina prenumerationer.

Använd Azure Resource Graph, i den här processen, för att fråga eller identifiera resurser i prenumerationerna. Se till att alla Azure-resurser som finns i miljön är godkända.

Konfigurera Azure Policy för att ange begränsningar för den typ av resurser som kan skapas i dina prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Använd WebJobs i App Service för att övervaka att program som inte godkänts distribueras i dator resurser. Använd WebJobs för att köra ett program eller skript i samma instans som en webbapp, API-app eller mobilapp. Definiera webb jobbs konfiguration och övervakning med loggar. På sidan jobb körnings information väljer du växla utdata för att se texten i logg innehållet. Observera att WebJobs inte stöds ännu för App Service i Linux.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Kör bakgrunds aktiviteter med WebJobs i Azure App Service](webjobs-create.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: underhåll en inventering av godkända program varu titlar

**Vägledning**: implementera en process för inventering och granskning av program varu titlar i dina prenumerationer regelbundet för att säkerställa att endast auktoriserade Azure-tjänster används i dina prenumerationer.

Använd Azure Resource Graph i den här processen för att fråga eller identifiera resurser i dina prenumerationer. Se till att alla Azure-resurser som identifieras i miljön godkänns.

Konfigurera Azure Policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

På samma sätt använder du WebJobs i App Service för att inventera program som inte har godkänts distribuerade i dator resurser. Definiera konfigurationen och övervakningen med loggar. På sidan jobb körnings information väljer du växla utdata för att se texten i logg innehållet. Observera att WebJobs inte stöds ännu för App Service i Linux.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Konfigurera villkorlig åtkomst i Azure för att begränsa möjligheten för användare att interagera med Azure Resource Manager, genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: WebJobs i App Service gör det möjligt för kunder att köra ett program eller skript i samma instans som en WEBBAPP, API-app eller mobilapp. Du ansvarar för att definiera konfigurationen för att begränsa eller begränsa eventuella skript som inte tillåts av organisationen. App Service tillhandahåller inte en mekanism för att begränsa skript körningen internt. Observera att WebJobs inte stöds ännu för App Service i Linux.

- [Kör bakgrunds aktiviteter med WebJobs i Azure App Service](webjobs-create.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: implementera separata prenumerationer eller hanterings grupper för att tillhandahålla isolering för hög risk App Service appar. Distribuera en app med högre risk till sin egen Virtual Network, eftersom perimeter-säkerheten i App Service uppnås genom användningen av virtuella nätverk. App Service-miljön är en distribution av App Service till ett undernät i Azure-Virtual Network. 

Det finns två typer av program tjänst miljö, extern program tjänst miljö och ILB (intern Load Balancer) program tjänst miljö. Välj den bästa arkitekturen utifrån dina krav.

- [Nätverksöverväganden för en App Service-miljö](environment/network-info.md)

- [Skapa en extern App Service-miljö](environment/create-external-ase.md)

- [Skapa och Använd en intern Load Balancer App Service-miljön](environment/create-ilb-ase.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: definiera och implementera standardkonfigurationer för dina App Service distribuerade appar med Azure policy.

Använd Azure Policy alias i namn området "Microsoft. Web" för att skapa anpassade principer som ska granskas eller tillämpas på konfigurationen av din App Service Web Apps.

Tillämpa inbyggda princip definitioner som:
- App Service bör använda en tjänst slut punkt för virtuellt nätverk
- Webb program bör endast vara tillgängliga via HTTPS

- Använd den senaste TLS-versionen i dina appar

Vi rekommenderar att du dokumenterar processen för att tillämpa de inbyggda princip definitionerna för standardiserad användning.   

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns]-effekter för att genomdriva säkra inställningar i dina Azure App Service appar.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: Välj Azure-DevOps eller Azure-databaser för att lagra och hantera din kod på ett säkert sätt när du använder anpassade Azure policys definitioner.

Använd din befintliga-pipeline för kontinuerlig integrering (CI) och kontinuerlig leverans (CD) för att distribuera en känd säker konfiguration.

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Dokumentation om Azure databaser](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: använd inbyggda Azure policy definitioner samt Azure policy alias i namn området "Microsoft. Web" för att skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Utveckla en process och pipeline för att hantera princip undantag.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: använd inbyggda Azure policy definitioner samt Azure policy alias i namn området "Microsoft. Web" för att skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. 

Använd Azure Policy [granskning], [neka] och [distribuera om det inte finns], effekterna för att automatiskt genomdriva konfigurationer för dina Azure-resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Använd hanterade identiteter för att tillhandahålla dina App Service-appar med en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Hanterade identiteter gör att dina appar kan autentisera till en tjänst som stöder Azure AD-autentisering, inklusive Key Vault, utan några autentiseringsuppgifter i din kod. Se till att mjuk borttagning är aktiverat i Azure Key Vault.

- [Så här aktiverar du mjuk borttagning i Azure Key Vault](../key-vault/general/key-vault-recovery.md)

- [Använda hanterade identiteter för App Service](overview-managed-identity.md)

- [Så här ger Key Vault autentisering med en hanterad identitet](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: Använd hanterade identiteter för att tillhandahålla dina App Service-distribuerade appar med en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Hanterade identiteter gör att dina appar kan autentisera till en tjänst som stöder Azure AD-autentisering, inklusive Key Vault, utan några autentiseringsuppgifter i din kod.

- [Använda hanterade identiteter för App Service](overview-managed-identity.md)

- [Så här ger Key Vault autentisering med en hanterad identitet](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Azure Security benchmark: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Säkerställ regelbunden automatisk säkerhets kopiering

**Vägledning**: säkerhets kopierings-och återställnings funktionen i App Service gör att du enkelt kan skapa säkerhets kopior av appar manuellt eller enligt ett schema. Du kan konfigurera säkerhets kopiorna så att de behålls på obestämd tid. Du kan återställa appen till en ögonblicks bild av ett tidigare tillstånd genom att skriva över den befintliga appen eller återställa till en annan app.

App Service kan säkerhetskopiera följande information till ett Azure Storage-konto och en behållare, som du har konfigurerat din app för att använda:
- Appkonfiguration
- Filinnehåll
- Databas ansluten till din app

Se till att regelbundna och automatiserade säkerhets kopieringar sker enligt en frekvens som definieras av organisationens principer.

- [Förstå Azure App Service säkerhets kopierings funktion](manage-backup.md)

- [Kundhanterade nycklar för Azure Storage kryptering](../storage/common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: Använd säkerhets kopierings-och återställnings funktionen i App Service för att säkerhetskopiera dina program. Säkerhets kopierings funktionerna kräver ett Azure Storage-konto för att lagra programmets säkerhets kopierings information.

- Azure Storage tillhandahåller kryptering i vilo läge, eller dina egna Kundhanterade nycklar. Det är här som dina program data lagras när de inte körs i en webbapp i Azure.
- Att köra från ett distributions paket är en distributions funktion i App Service. Du kan distribuera plats innehållet från ett Azure Storage-konto med hjälp av en URL för signatur för delad åtkomst (SAS).

- Key Vault referenser är en säkerhetsfunktion i App Service. Du kan importera hemligheter vid körning som program inställningar. Använd detta för att kryptera SAS-URL: en för ditt Azure Storage konto.

Mer information finns på de länkar som refereras till.

- [Säkerhetskopiera din app i Azure](manage-backup.md)

- [Återställa en app som körs i Azure App Service](web-sites-restore.md)

- [Förstå kryptering vid vila i Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Krypterings modell och nyckel hanterings tabell](../security/fundamentals/encryption-atrest.md)

- [Kryptering i vila med Kundhanterade nycklar](configure-encrypt-at-rest-using-cmk.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: testa regelbundet återställnings processen för säkerhets kopiering av dina App Service-program.

- [Säkerhetskopiera din app i Azure](manage-backup.md)

- [Så här återställer du en Azure App Service webbapp](web-sites-restore.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: App Service säkerhets kopior lagras i ett Azure Storage konto. Data i Azure Storage krypteras och dekrypteras transparent med 256-bitars AES-kryptering, en av de starkaste block chiffer som är tillgängliga och är FIPS 140-2-kompatibel. Azure Storage kryptering liknar BitLocker-kryptering på Windows.

Azure Storage kryptering har Aktiver ATS för alla lagrings konton, inklusive både Resource Manager och klassiska lagrings konton. Azure Storage kryptering kan inte inaktive ras. Eftersom dina data är säkra som standard behöver du inte ändra koden eller programmen för att dra nytta av Azure Storage kryptering.

Som standard krypteras data i ett lagrings konto med Microsoft-hanterade nycklar. Du kan förlita dig på Microsoft-hanterade nycklar för kryptering av dina data, eller så kan du hantera kryptering med dina egna nycklar. Se till att mjuk borttagning är aktiverat i Azure Key Vault.

- [Förstå Azure Storage kryptering för vilande data](../storage/common/storage-service-encryption.md)

- [Så här aktiverar du mjuk borttagning i Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security benchmark: incident svar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.

- [Konfigurera automatisering av arbets flöden i Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Kunden kan också utnyttja NISTs hanterings guide för dator säkerhet för att hjälpa till med att skapa egna incident svars planer](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Dessutom kan du tydligt markera prenumerationer (till exempel produktion, icke-produktion) och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser.

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa systemets funktioner för incident svar på en vanlig takt. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [Läs NIST för att testa, träna och träna program för IT-planer och funktioner](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har öppnats av en olaglig eller obehörig part.  Granska incidenter när du är säker på att problemen är lösta.

- [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Använd Security Center Data Connector för att strömma aviseringarna till Azure Sentinel per affärs behov.

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöden i Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer.

- [Konfigurera automatisering av arbets flöden och Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstester och Red Team-tester

*Mer information finns i [övningen för Azure Security benchmark: inträngande tester och röda team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Du hittar mer information om Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program.

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)