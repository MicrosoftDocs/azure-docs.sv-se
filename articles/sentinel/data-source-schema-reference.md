---
title: Schema referens för Azure Sentinel data source
description: Den här artikeln innehåller Azure-och data käll scheman från tredje part som stöds av Azure Sentinel, med länkar till referens dokumentation.
author: batamig
ms.author: bagol
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.custom: ''
ms.date: 01/14/2021
ms.openlocfilehash: 0cc1a083e1400b5a3167376e374af6c7bf7ab7c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698356"
---
# <a name="data-source-schema-reference"></a>Schema referens för data Källa

Den här artikeln innehåller en lista över Azure-scheman och data käll scheman från tredje part som stöds, med länkar till referens dokumentation.

## <a name="azure-data-sources"></a>Azure-datakällor

| Typ                             | Datakälla             | Log Analytics TableName | Schema referens |
| -------------------------------- | ---------------------- | ---------------------- | ---------------- |
| **Azure**                            | Azure Active Directory | SigninEvents           | [Inloggnings egenskaper för Azure AD-aktivitets rapporter](/graph/api/resources/signin#properties) |
| **Azure**                            | Azure Active Directory | AuditLogs              | [Azure Monitor AuditLogs-referens](/azure/azure-monitor/reference/tables/auditlogs) |
| **Azure**                            | Azure Active Directory | AzureActivity          | [Azure Monitor AzureActivity-referens](/azure/azure-monitor/reference/tables/azureactivity) |
| **Azure**                            | Office                 | OfficeActivity         | API-scheman för Office 365 Management activity: <br>- [Gemensamt schema ](/office/office-365-management-api/office-365-management-activity-api-schema#common-schema)   <br>- [Exchange admin-schema ](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-admin-schema) <br>- [Exchange Mailbox-schema](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-mailbox-schema)  <br>- [Bas schema för SharePoint](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-base-schema)   <br>- [SharePoint-filåtgärder](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-file-operations) |
| **Azure**                            | Azure Key Vault         | AzureDiagnostics       | [Azure Monitor AzureDiagnostics-referens](/azure/azure-monitor/reference/tables/azurediagnostics) |
| **Värd**                             | Linux                  | Syslog                 | [Azure Monitor syslog-referens](/azure/azure-monitor/reference/tables/syslog) |
| **Nätverk**                          | IIS-loggar               | W3CIISLog              | [Azure Monitor W3CIISLog-referens](/azure/azure-monitor/reference/tables/w3ciislog) |
| **Nätverk**                          | VMinsights             | VMConnection           | [Azure Monitor VMConnection-referens](/azure/azure-monitor/reference/tables/vmconnection) |
| **Nätverk**                          | Lösning för kabel data     | WireData               | [Azure Monitor WireData-referens](/azure/azure-monitor/reference/tables/wiredata) |
| **Nätverk**                          | NSG flödes loggar          | AzureNetworkAnalytics  | [Schema-och data agg regering i Trafikanalys](../network-watcher/traffic-analytics-schema.md) |
| | | | |

> [!NOTE]
> Mer information finns i hela [Azure Monitor data referens](/azure/azure-monitor/reference/).
>
## <a name="3rd-party-vendor-data-sources"></a>leverantörs data källor från tredje part

I följande tabell visas tredjeparts leverantörer och deras syslog-eller common Event format (CEF)-mappnings dokumentation för olika typer av loggar som stöds, som innehåller fält mappningar för CEF och exempel loggar för varje kategori typ.

| Typ |    Leverantör |    Produkt | Log Analytics TableName | CEF fält – mappnings referens  |
| ----- | ----- | ----- | ----- |----- |
| **Nätverk** | Palo Alto   | PAN OS    | CommonSecurityLog |   [Integrerings guide för Pan-OS 9,0 common Event format](https://docs.paloaltonetworks.com/content/dam/techdocs/en_US/pdf/cef/pan-os-90-cef-configuration-guide.pdf) (Sök efter *logg format för CEF*) |
| **Nätverk** | Check Point  |ALL   | CommonSecurityLog | [Beskrivning av logg fält](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk109795)       |
| **Nätverk** | Fortigate   | ALL   | CommonSecurityLog | [Schema struktur för logg](https://docs.fortinet.com/document/fortigate/6.2.3/fortios-log-message-reference/738142/log-schema-structure)         |
| **Nätverk** | Barracuda | Brandvägg för webbaserade program |  CommonSecurityLog   | [Konfigurera syslog och andra loggar](https://campus.barracuda.com/product/webapplicationfirewall/doc/4259935/how-to-configure-syslog-and-other-logs/)  |
| **Nätverk** | Cisco | ASA | CommonSecurityLog | [Syslog-meddelanden för Cisco ASA-serien](https://www.cisco.com/c/en/us/td/docs/security/asa/syslog/b_syslog/about.html)    |
| **Nätverk** | Cisco | Firepower   | CommonSecurityLog | [Syslog-meddelanden för Cisco Firepower hot försvar](https://www.cisco.com/c/en/us/td/docs/security/firepower/Syslogs/b_fptd_syslog_guide.pdf)    |
| **Nätverk** | Cisco   | Avses  | Tabell med anpassade loggar  | [Logg format och versions hantering](https://docs.umbrella.com/deployment-umbrella/docs/log-formats-and-versioning)   |
| **Nätverk**   | Cisco | Meraki    | CommonSecurityLog |   [Händelse typer och logg exempel för syslog](https://documentation.meraki.com/zGeneral_Administration/Monitoring_and_Reporting/Syslog_Event_Types_and_Log_Samples)    |
| **Nätverk**   | Zscaler | Nano streaming service (NSS)|   CommonSecurityLog | [Formatera NSS-feeds](https://help.zscaler.com/zia/documentation-knowledgebase/analytics/nss/nss-feeds/formatting-nss-feeds) (endast webb-, brand Väggs-, DNS-och tunnel loggar) |
| **Nätverk**   |F5 | BigIP LTM|    CommonSecurityLog|  [Händelse meddelanden och angrepps typer](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/bigip-external-monitoring-implementations-13-0-0/15.html)  |
| **Nätverk** | F5  | BigIP ASM|    CommonSecurityLog|  [Loggning av program säkerhets händelser](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-13-1-0/14.html)                                                           |
| **Nätverk** | Citrix  |Web App-brandvägg   | CommonSecurityLog|    [Stöd för loggning av vanliga Event format (CEF) i program brand väggen](https://support.citrix.com/article/CTX136146) <br>  [NetScaler 12,0 syslog-meddelande referens](https://developer-docs.citrix.com/projects/netscaler-syslog-message-reference/en/12.0/)   |
|**Värd** |Symantec | Symantec-Endpoint Protection Manager (SEPM) | CommonSecurityLog|[Inställningar för extern loggning och allvarlighets grader för logg händelser för Endpoint Protection Manager](https://support.symantec.com/us/en/article.tech171741.html)|
|**Värd** |Trend Micro |Alla |CommonSecurityLog | [Innehålls mappning för syslog-CEF](https://docs.trendmicro.com/en-us/enterprise/control-manager-70/appendices/syslog-mapping-cef.aspx) |
| | | | | |

## <a name="next-steps"></a>Nästa steg

Lär dig mer om Azure Sentinel-kopplingar som stöds, till exempel CEF, syslog, Direct, agent och anpassade anslutningar:

- [Ansluta till datakällor](connect-data-sources.md)

- [Azure Sentinel syslog, CEF och andra anslutningar från tredje part](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-syslog-cef-and-other-3rd-party-connectors-grand/ba-p/803891)