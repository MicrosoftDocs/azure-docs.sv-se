---
title: Azure säkerhets kontroll – loggning och övervakning
description: Loggning och övervakning av Azures säkerhets kontroll
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: ccfbb585ccf50366721925b0b31b17088fd36371
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102612430"
---
# <a name="security-control-logging-and-monitoring"></a>Säkerhets kontroll: loggning och övervakning

Säkerhets loggning och övervakning fokuserar på aktiviteter som rör aktivering, hämtning och lagring av gransknings loggar för Azure-tjänster.

## <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

Microsoft hanterar tids källor för Azure-resurser, men du har möjlighet att hantera tidssynkroniserings inställningarna för dina beräknings resurser.

- [Så här konfigurerar du tidssynkronisering för Azure Windows Compute-resurser](../../virtual-machines/windows/time-sync.md)

- [Så här konfigurerar du tidssynkronisering för Azure Linux-beräknings resurser](../../virtual-machines/linux/time-sync.md)

## <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 2.2 | 6,5, 6,6 | Kund |

Mata in loggar via Azure Monitor för att samla in säkerhets data som genererats av slut punkts enheter, nätverks resurser och andra säkerhets system. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring.

Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM från tredje part. 

- [Publicera Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Samla in plattforms loggar och mått med Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

- [Så här samlar du in interna värd loggar för virtuella Azure-datorer med Azure Monitor](../../azure-monitor/vm/quick-collect-azurevm.md)

- [Komma igång med Azure Monitor och SIEM-integrering från tredje part](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

## <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 2.3 | 6,2, 6,3 | Kund |

Aktivera diagnostiska inställningar på Azure-resurser för åtkomst till gransknings-, säkerhets-och diagnostikloggar. Aktivitets loggar, som är automatiskt tillgängliga, innehåller händelse källa, datum, användare, tidsstämpel, käll adresser, mål adresser och andra användbara element.

- [Samla in plattforms loggar och mått med Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

- [Förstå loggning och olika logg typer i Azure](../../azure-monitor/essentials/platform-logs-overview.md)

## <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 2.4 | 6,2, 6,3 | Kund |

Om beräknings resursen ägs av Microsoft ansvarar Microsoft för att övervaka den. Om beräknings resursen ägs av din organisation är det ditt ansvar att övervaka den. Du kan använda Azure Security Center för att övervaka operativ systemet. Data som samlas in av Security Center från operativ systemet innehåller OS-typ och version, OS (Windows-händelseloggen), processer som körs, dator namn, IP-adresser och inloggad användare. Log Analytics agent samlar även in krasch dum par-filer.

- [Så här samlar du in interna värd loggar för virtuella Azure-datorer med Azure Monitor](../../azure-monitor/vm/quick-collect-azurevm.md)

- [Förstå Azure Security Center insamling av data](../../security-center/security-center-enable-data-collection.md)

## <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 2.5 | 6.4 | Kund |

I Azure Monitor anger Log Analytics du arbets ytans lagrings period enligt organisationens regler för efterlevnad. Använd Azure Storage konton för långsiktig/Arkiv lagring.

- [Ändra data lagrings perioden i Log Analytics](../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Konfigurera bevarande princip för Azure Storage konto loggar](../../storage/common/manage-storage-analytics-logs.md#configure-logging)

## <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 2,6 | 6.7 | Kund |

Analysera och övervaka loggar för avvikande beteende och granska resultaten regelbundet. Använd Azure Monitor Log Analytics arbets ytan för att granska loggar och köra frågor om loggdata.

Alternativt kan du aktivera och fordonsbaserad information till Azure Sentinel eller en SIEM från tredje part. 

- [Publicera Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Förstå Log Analytics arbets yta](../../azure-monitor/logs/log-analytics-tutorial.md)

- [Så här utför du anpassade frågor i Azure Monitor](../../azure-monitor/logs/get-started-queries.md)

## <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 2.7 | 6.8 | Kund |

Använd Azure Security Center med Log Analytics arbets yta för övervakning och aviseringar om avvikande aktivitet i säkerhets loggar och händelser.

Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel.

- [Publicera Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Hantera aviseringar i Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md)

- [Så här aviserar du om Log Analytics-loggdata](../../azure-monitor/alerts/tutorial-response.md)

## <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 2.8 | 8,6 | Kund |

Aktivera insamling av händelse samling för program mot skadlig kod för Azure Virtual Machines och Cloud Services.

- [Så här konfigurerar du Microsoft Antimalware för Virtual Machines](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension)

- [Så här konfigurerar du Microsoft Antimalware för Cloud Services](/powershell/module/servicemanagement/azure.service/set-azureserviceantimalwareextension)

- [Förstå Microsoft Antimalware](../fundamentals/antimalware.md)

## <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 2.9 | 8.7 | Kund |

Implementera en lösning från tredje part från Azure Marketplace för DNS-loggning av lösningar enligt organisationens behov.  

## <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 2,10 | 8,8 | Kund |

Använd Microsoft Monitoring Agent på alla virtuella Azure Windows-datorer som stöds för att logga skapande händelsen för processen och fältet kommandorad.   För virtuella Azure Linux-datorer som stöds kan du manuellt konfigurera konsol loggning per nod och använda syslog för att lagra data.  Använd också Azure Monitor Log Analytics arbets ytan för att granska loggar och köra frågor om loggade data från virtuella Azure-datorer. 

- [Datainsamling i Azure Security Center](../../security-center/security-center-enable-data-collection.md#data-collection-tier)

- [Så här utför du anpassade frågor i Azure Monitor](../../azure-monitor/logs/get-started-queries.md)

- [Syslog-datakällor i Azure Monitor](../../azure-monitor/agents/data-sources-syslog.md)


## <a name="next-steps"></a>Nästa steg

- Se nästa säkerhets kontroll: [identitet och Access Control](security-control-identity-access-control.md)