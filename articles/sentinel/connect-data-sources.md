---
title: Anslut data källor till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter data källor som Microsoft 365 Defender (tidigare Microsoft Threat Protection), Microsoft 365 och Office 365, Azure AD, ATP och Cloud App Security till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: 3df78d6b53f8e8739307e9b870aa03d76bfd6771
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101718615"
---
# <a name="connect-data-sources"></a>Ansluta till datakällor

När du har aktiverat Azure Sentinel måste du först ansluta dina data källor. Azure Sentinel levereras med ett antal anslutningar för Microsoft-lösningar, som är tillgängliga direkt och ger real tids integrering, inklusive Microsoft 365 Defender (tidigare lösningar för Microsoft Threat Protection), Microsoft 365 källor (inklusive Office 365), Azure AD, Microsoft Defender för identitet (tidigare Azure ATP), Microsoft Cloud App Security med mera. Dessutom finns det inbyggda anslutnings program till det bredare säkerhets eko systemet för lösningar som inte kommer från Microsoft. Du kan också använda common Event format (CEF), syslog eller REST-API för att ansluta dina data källor med Azure Sentinel.

1. På menyn väljer du **data kopplingar**. På den här sidan kan du se en fullständig lista över de anslutningar som Azure Sentinel tillhandahåller och deras status. Välj den anslutning som du vill ansluta och välj **Öppna kopplings sida**. 

   ![Galleri för data anslutningar](./media/collect-data/collect-data-page.png)

1. På sidan för den aktuella anslutningen ser du till att du har uppfyllt alla krav och följer anvisningarna för att ansluta data till Azure Sentinel. Det kan ta lite tid innan loggarna börjar synkronisera med Azure Sentinel. När du har anslutit visas en sammanfattning av data i grafen med **mottagna data** och anslutnings status för data typerna.

   ![Konfigurera data anslutningar](./media/collect-data/opened-connector-page.png)
  
1. Klicka på fliken **Nästa steg** om du vill hämta en lista över innehåll i Azure Sentinel som är färdiga för den aktuella data typen.

   ![Nästa steg för kopplingar](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Dataanslutningsmetoder

Följande data anslutnings metoder stöds av Azure Sentinel:

- **Integrering av tjänst till tjänst**:<br> Vissa tjänster är anslutna till varandra, till exempel AWS och Microsoft-tjänster, de här tjänsterna utnyttjar Azure Foundation för out of Box-integreringen. följande lösningar kan vara anslutna med några klick:
    - [Amazon Web Services-CloudTrail](connect-aws.md)
    - [Azure Active Directory](connect-azure-active-directory.md) gransknings loggar och inloggnings loggar
    - [Azure-aktivitet](connect-azure-activity.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Azure DDoS Protection](connect-azure-ddos-protection.md)
    - [Azure Defender för IoT](connect-asc-iot.md) (tidigare Azure Security Center för IoT)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Azure Firewall](connect-azure-firewall.md)
    - [Azure Security Center](connect-azure-security-center.md) – aviseringar från Azure Defender-lösningar
    - [Azure Web Application-brandvägg (WAF)](connect-azure-waf.md) (tidigare Microsoft-WAF)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Domännamnserver](connect-dns.md)
    - [Microsoft 365 Defender](connect-microsoft-365-defender.md) – innehåller M365D-incidenter och MDE rå data
    - [Microsoft Defender för slut punkt](connect-microsoft-defender-advanced-threat-protection.md) (tidigare Microsoft Defender Avancerat skydd)
    - [Microsoft Defender för identitet](connect-azure-atp.md) (tidigare Azure Advanced Threat Protection)
    - [Microsoft Defender för Office 365](connect-office-365-advanced-threat-protection.md) (tidigare Office 365 Advanced Threat Protection)
    - [Office 365](connect-office-365.md) (nu med Teams!)
    - [Windows-brandvägg](connect-windows-firewall.md)
    - [Windows säkerhetshändelser](connect-windows-security-events.md)

- **Externa lösningar via API**: vissa data källor är anslutna via API: er som tillhandahålls av den anslutna data källan. Oftast tillhandahåller de flesta säkerhets tekniker en uppsättning API: er via vilka händelse loggar som kan hämtas. API: erna ansluter till Azure Sentinel och samlar in vissa data typer och skickar dem till Azure Log Analytics. Enheter som är anslutna via API inkluderar:
    
    - [Agari phishing-skydd och varumärkes skydd](connect-agari-phishing-defense.md)
    - [Alcide kAudit](connect-alcide-kaudit.md)
    - [Barracuda WAF](connect-barracuda.md)
    - [Barracuda CloudGen-brandväggen](connect-barracuda-cloudgen-firewall.md)
    - [BETTER Mobile Threat Defense](connect-better-mtd.md)
    - [Utöver säkerhet – beSECURE](connect-besecure.md)
    - [Cisco Umbrella](connect-cisco-umbrella.md)
    - [Citrix Analytics (Security)](connect-citrix-analytics.md)
    - [F5 BIG-IP](connect-f5-big-ip.md)
    - [Forcepoint DLP](connect-forcepoint-dlp.md)
    - [Google-arbetsyta (tidigare G Suite)](connect-google-workspace.md)
    - [NXLog (Windows) DNS-loggar](connect-nxlog-dns.md)
    - [NXLog LinuxAudit](connect-nxlog-linuxaudit.md)
    - [Okta SSO](connect-okta-single-sign-on.md)
    - [Orca Security](connect-orca-security-alerts.md)
    - [Perimeter 81-loggar](connect-perimeter-81-logs.md)
    - [Proofpoint på begäran (POD) e-postsäkerhet](connect-proofpoint-pod.md)
    - [Proofpoint TAP](connect-proofpoint-tap.md)
    - [Qualys VM](connect-qualys-vm.md)
    - [Salesforce Service Cloud](connect-salesforce-service-cloud.md)
    - [Sophos Cloud Optix](connect-sophos-cloud-optix.md)
    - [Squadra Technologies secRMM](connect-squadra-secrmm.md)
    - [Symantec ICDX](connect-symantec.md)
    - [VMware Carbon Black Cloud Endpoint Standard](connect-vmware-carbon-black.md)
    - [Zimperium](connect-zimperium-mtd.md)


- **Externa lösningar via agent**: Azure Sentinel kan anslutas via en agent till en annan data källa som kan utföra logg strömning i real tid med syslog-protokollet.

    De flesta apparater använder syslog-protokollet för att skicka händelse meddelanden som innehåller själva loggen och data om loggen. Formatet på loggarna varierar, men de flesta apparater stöder CEF-baserad formatering för loggdata. 

    Azure Sentinel-agenten, som faktiskt är Log Analytics agent, konverterar CEF-formaterade loggar till ett format som kan matas in av Log Analytics. Beroende på typ av installation installeras agenten antingen direkt på enheten eller på en dedikerad Linux-baserad logg vidarebefordrare. Agenten för Linux tar emot händelser från syslog-daemon över UDP, men om en Linux-dator förväntas samla in en stor mängd Syslog-händelser skickas de via TCP från syslog-daemon till agenten och därifrån att Log Analytics.

    - **Brand väggar, proxyservrar och slut punkter – CEF:**
        - [AI Vectra Detect](connect-ai-vectra-detect.md)
        - [Akamai säkerhets händelser](connect-akamai-security-events.md)
        - [Aruba ClearPass](connect-aruba-clearpass.md)
        - [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [Citrix WAF](connect-citrix-waf.md)
        - [CyberArk Enterprise – valv för lösenord](connect-cyberark.md)
        - [ExtraHop Reveal(x)](connect-extrahop.md)
        - [F5 ASM](connect-f5.md)
        - [Forcepoint-produkter](connect-forcepoint-casb-ngfw.md)
        - [Fortinet](connect-fortinet.md)
        - [Illusive Networks AMS](connect-illusive-attack-management-system.md)
        - [Imperva WAF-Gateway](connect-imperva-waf-gateway.md)
        - [One Identity Safeguard](connect-one-identity.md)
        - [Palo Alto Networks](connect-paloalto.md)
        - [Thycotic Secret Server](connect-thycotic-secret-server.md)
        - [Trend Micro Deep Security](connect-trend-micro.md)
        - [Trend Micro TippingPoint](connect-trend-micro-tippingpoint.md)
        - [WireX Network data utredning Platform](connect-wirex-systems.md)
        - [Zscaler](connect-zscaler.md)
        - [Andra CEF-baserade apparater](connect-common-event-format.md)
    - **Brand väggar, proxyservrar och slut punkter – syslog:**
        - [Alsid för Active Directory](connect-alsid-active-directory.md)
        - [Cisco Meraki](connect-cisco-meraki.md)
        - [Cisco Unified Computing System (UCS)](connect-cisco-ucs.md)
        - [Infoblox NIOS](connect-infoblox.md)
        - [Juniper SRX](connect-juniper-srx.md)
        - [Pulse Connect Secure](connect-pulse-connect-secure.md)
        - [Sophos-XG](connect-sophos-xg-firewall.md)
        - [Squid Proxy](connect-squid-proxy.md)
        - [Symantec Proxy SG](connect-symantec-proxy-sg.md)
        - [Symantec VIP](connect-symantec-vip.md)
        - [VMware ESXi](connect-vmware-esxi.md)
        - [Andra syslog-baserade apparater](connect-syslog.md)
    - [Apache HTTP-Server](connect-apache-http-server.md)
    - DLP-lösningar
    - [Leverantörer av hotinformation](connect-threat-intelligence.md)
    - [DNS-datorer](connect-dns.md) – Agent installeras direkt på DNS-datorn
    - [Azure Stack virtuella datorer](connect-azure-stack.md)
    - Linux-servrar
    - Andra moln
    
## <a name="agent-connection-options"></a>Alternativ för agentanslutning<a name="agent-options"></a>

Om du vill ansluta din externa installation till Azure Sentinel måste agenten distribueras på en dedikerad dator (VM eller lokalt) för att stödja kommunikationen mellan-enheten och Azure Sentinel. Du kan distribuera agenten automatiskt eller manuellt. Automatisk distribution är bara tillgängligt om din dedikerade dator är en ny virtuell dator som du skapar i Azure. 

![CEF i Azure](./media/connect-cef/cef-syslog-azure.png)

Alternativt kan du distribuera agenten manuellt på en befintlig virtuell Azure-dator, på en virtuell dator i ett annat moln eller på en lokal dator.

![CEF lokalt](./media/connect-cef/cef-syslog-onprem.png)

## <a name="map-data-types-with-azure-sentinel-connection-options"></a>Mappa datatyper med anslutningsalternativ för Azure Sentinel


| **Datatyp** | **Så här ansluter du** | **Data koppling?** | **Kommentarer** |
|------|---------|-------------|------|
| AWSCloudTrail | [Ansluta AWS](connect-aws.md) | &#10003; | |
| AzureActivity | Översikt över [Anslut Azure-aktivitet](connect-azure-activity.md) och [aktivitets loggar](../azure-monitor/essentials/platform-logs-overview.md)| &#10003; | |
| AuditLogs | [Ansluta till Azure Active Directory](connect-azure-active-directory.md)  | &#10003; | |
| SigninLogs | [Ansluta till Azure Active Directory](connect-azure-active-directory.md)  | &#10003; | |
| AzureFirewall |[Microsoft Azure Diagnostics](../firewall/firewall-diagnostics.md) | &#10003; | |
| InformationProtectionLogs_CL  | [Azure Information Protection rapporter](/azure/information-protection/reports-aip)<br>[Ansluta till Azure Information Protection](connect-azure-information-protection.md)  | &#10003; | Detta använder vanligt vis **InformationProtectionEvents** -funktionen tillsammans med data typen. Mer information finns i [ändra rapporterna och skapa anpassade frågor](/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)|
| AzureNetworkAnalytics_CL  | Trafik [analys schema](../network-watcher/traffic-analytics.md) [](../network-watcher/traffic-analytics.md) analys  | | |
| CommonSecurityLog  | [Anslut CEF](connect-common-event-format.md)  | &#10003; | |
| OfficeActivity | [Ansluta Office 365](connect-office-365.md) | &#10003; | |
| SecurityEvents | [Ansluta till Windows säkerhetshändelser](connect-windows-security-events.md)  | &#10003; | Arbets böckerna för oskyddade protokoll finns i [arbets boks konfiguration för oskyddade protokoll](./quickstart-get-visibility.md#use-built-in-workbooks)  |
| Syslog | [Ansluta till Syslog](connect-syslog.md) | &#10003; | |
| Microsoft-brand vägg för webbaserade program (WAF) – (AzureDiagnostics) |[Anslut brand väggen för Microsoft-webbprogram](./connect-azure-waf.md) | &#10003; | |
| SymantecICDx_CL | [Anslut Symantec](connect-symantec.md) | &#10003; | |
| ThreatIntelligenceIndicator  | [Ansluta till hotinformation](connect-threat-intelligence.md)  | &#10003; | |
| VMConnection <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Azure Monitor tjänst karta](../azure-monitor/vm/service-map.md)<br>[Azure Monitor VM Insights onboarding](../azure-monitor/vm/vminsights-enable-overview.md) <br> [Aktivera Azure Monitor VM-insikter](../azure-monitor/vm/vminsights-enable-overview.md) <br> [Använda en enda virtuell dator](../azure-monitor/vm/vminsights-enable-portal.md)<br>  [Använda fordonsbaserad via princip](../azure-monitor/vm/vminsights-enable-policy.md)| &#10007; | Arbets bok för VM Insights  |
| DnsEvents | [Anslut DNS](connect-dns.md) | &#10003; | |
| W3CIISLog | [Anslut IIS-loggar](../azure-monitor/agents/data-sources-iis-logs.md)  | &#10007; | |
| WireData | [Anslut tråd data](../azure-monitor/insights/wire-data.md) | &#10007; | |
| WindowsFirewall | [Anslut Windows-brandväggen](connect-windows-firewall.md) | &#10003; | |
| AADIP SecurityAlert  | [Ansluta Azure AD Identity Protection](connect-azure-ad-identity-protection.md)  | &#10003; | |
| AATP SecurityAlert  | [Anslut Microsoft Defender för identitet](connect-azure-atp.md) (tidigare Azure ATP) | &#10003; | |
| ASC-SecurityAlert  | [Ansluta Azure Defender-aviseringar](connect-azure-security-center.md) från Azure Security Center  | &#10003; | |
| MCAS SecurityAlert  | [Anslut Microsoft Cloud App Security](connect-cloud-app-security.md)  | &#10003; | |
| SecurityAlert | | | |
| Sysmon (händelse) | [Anslut Sysmon](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [Anslut Windows-händelser](../azure-monitor/agents/data-sources-windows-events.md) <br> [Hämta Sysmon-parsern](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/Sysmon/Sysmon-v10.42-Parser.txt)| &#10007; | Sysmon-samlingen installeras inte som standard på virtuella datorer. Mer information om hur du installerar Sysmon-agenten finns i [Sysmon](/sysinternals/downloads/sysmon). |
| ConfigurationData  | [Automatisera VM-inventering](../automation/change-tracking/overview.md)| &#10007; | |
| ConfigurationChange  | [Automatisera spårning av virtuella datorer](../automation/change-tracking/overview.md) | &#10007; | |
| F5 BIG-IP | [Ansluta F5 BIG-IP](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)  | &#10007; | |
| McasShadowItReporting  |  | &#10007; | |
| Barracuda_CL | [Ansluta till Barracuda](connect-barracuda.md) | &#10003; | |


## <a name="next-steps"></a>Nästa steg

- För att komma igång med Azure Sentinel behöver du en prenumeration på Microsoft Azure. Om du inte har en prenumeration kan du registrera dig för en [gratis provversion](https://azure.microsoft.com/free/).
- Lär dig hur du kan [publicera dina data till Azure Sentinel](quickstart-onboard.md)och [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).