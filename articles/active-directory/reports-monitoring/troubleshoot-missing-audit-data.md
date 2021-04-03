---
title: Felsöka saknade data i aktivitets loggarna | Microsoft Docs
description: Ger en lösning till saknade data i Azure Active Directory-aktivitetsloggar.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f48c26a65314e2f23513fba155f07db3805a516
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93123072"
---
# <a name="troubleshoot-missing-data-in-the-azure-active-directory-activity-logs"></a>Felsökning: Saknade data i Azure Active Directory-aktivitetsloggar 

## <a name="i-cant-find-audit-logs-for-recent-actions-in-the-azure-portal"></a>Jag hittar inte spårningsloggar för de senaste åtgärderna i Azure-portalen

### <a name="symptoms"></a>Symtom

Jag utförde vissa åtgärder i Azure Portal och förväntade att se granskningsloggarna för dessa åtgärder på bladet `Activity logs > Audit Logs`, men det går inte att hitta dem.

 ![Skärm bild som visar Gransknings logg poster.](./media/troubleshoot-missing-audit-data/01.png)
 
### <a name="cause"></a>Orsak

Åtgärder visas inte omedelbart i aktivitetsloggarna. Tabellen nedan räknar upp våra svarstidsvärden för aktivitetsloggar. 

| Rapport | Svarstid (P95) | Svarstid (P99) |
|--------|---------------|---------------|
| Kataloggranskning | 2 min | 5 min |
| Inloggningsaktivitet | 2 min | 5 min |

### <a name="resolution"></a>Lösning

Vänta i mellan 15 minuter och 2 timmar och se om åtgärderna visas i loggen. Om du inte ser loggarna även efter två timmar [skapar du en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) så undersöker vi problemet.

## <a name="i-cant-find-recent-user-sign-ins-in-the-azure-active-directory-sign-ins-activity-log"></a>Det går inte att hitta nyligen utförda användarinloggningar i aktivitetsloggen för inloggningsåtgärder i Azure Active Directory

### <a name="symptoms"></a>Symtom

Jag loggade nyligen in på Azure-portalen och förväntade mig att se inloggningsloggarna för de åtgärderna på bladet `Activity logs > Sign-ins`, men det går inte att hitta dem.

 ![Skärm bild som visar inloggningar i aktivitets loggen.](./media/troubleshoot-missing-audit-data/02.png)
 
### <a name="cause"></a>Orsak

Åtgärder visas inte omedelbart i aktivitetsloggarna. Tabellen nedan räknar upp våra svarstidsvärden för aktivitetsloggar. 

| Rapport | Svarstid (P95) | Svarstid (P99) |
|--------|---------------|---------------|
| Kataloggranskning | 2 min | 5 min |
| Inloggnings aktivitet 2 minuter | 5 min |

### <a name="resolution"></a>Lösning

Vänta i mellan 15 minuter och 2 timmar och se om åtgärderna visas i loggen. Om du inte ser loggarna även efter två timmar [skapar du en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) så undersöker vi problemet.

## <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Jag kan inte se mer än 30 dagars rapportdata i Azure-portalen

### <a name="symptoms"></a>Symtom

Jag kan inte se mer än 30 dagars inloggnings- och granskningsdata från Azure-portalen. Varför? 

 ![Skärm bild som visar menyn datum.](./media/troubleshoot-missing-audit-data/03.png)

### <a name="cause"></a>Orsak

Beroende på din licens lagrar Azure Active Directory aktivitetsrapporter under så här lång tid:

| Rapport           | Azure AD Kostnadsfri | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ---           | ---                 | ---                 |
| Kataloggranskning  |  7 dagar       | 30 dagar             | 30 dagar             |
| Inloggningsaktivitet | Inte tillgängligt. Du kan komma åt dina egna inloggningar i 7 dagar från det bladet för enskild användare | 30 dagar | 30 dagar             |

Mer information finns i [Kvarhållningsprinciper för rapporter i Azure Active Directory](reference-reports-data-retention.md).  

### <a name="resolution"></a>Lösning

Du har två alternativ för att behålla data längre än 30 dagar. Du kan använda [Azure AD Reporting-API:er](concept-reporting-api.md) för att hämta data programmatiskt och lagra dem i en databas. Du kan även integrera spårningsloggar i ett SIEM-system från tredje part, till exempel Splunk eller SumoLogic.

## <a name="next-steps"></a>Nästa steg

* [Kvarhållning av Azure AD-rapportering](reference-reports-data-retention.md).
* [Azure Active Directory rapporterings fördröjning](reference-reports-latencies.md).
* [Vanliga frågor och svar om Azure Active Directory-rapportering](reports-faq.md).

