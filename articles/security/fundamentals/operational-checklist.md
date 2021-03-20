---
title: Check lista för Azures drift säkerhet | Microsoft Docs
description: Läs igenom den här check listan för att hjälpa ditt företag att tänka igenom Azures åtgärder för drift säkerhet.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 8550c795e222e72fe423e8bc60669a78921a6fcd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570693"
---
# <a name="azure-operational-security-checklist"></a>Check lista för Azures drift säkerhet
Att distribuera ett program på Azure är snabbt, enkelt och kostnads effektivt. Innan du distribuerar moln programmet i produktions miljö är det bra att ha en check lista som hjälper dig att utvärdera ditt program mot en lista över viktiga och rekommenderade drift säkerhets åtgärder som du kan tänka på.

## <a name="introduction"></a>Introduktion

Azure tillhandahåller en uppsättning infrastruktur tjänster som du kan använda för att distribuera dina program. Azures drift säkerhet syftar på tjänster, kontroller och funktioner som är tillgängliga för användare för att skydda data, program och andra till gångar i Microsoft Azure.

-   För att få maximal nytta av moln plattformen rekommenderar vi att du utnyttjar Azure-tjänster och följer check listan.
-   Organisationer som investerar tid och resurser som bedömer drifts beredskap för sina program innan lanseringen har en mycket högre grad av nöjdhet än de som inte är det. När det här arbetet utförs kan check listor vara en värdefull mekanism för att säkerställa att program utvärderas konsekvent och holistiskt.
-   Nivån på drifts bedömning varierar beroende på organisationens förfallo nivå för molnet och programmets utvecklings fas, tillgänglighets behov och krav för data känslighet.

## <a name="checklist"></a>Checklista

Den här check listan är avsedd att hjälpa företag att tänka igenom olika operativa säkerhets aspekter när de distribuerar avancerade företags program i Azure. Det kan också användas för att bygga en säker molnbaserad migrering och åtgärds strategi för din organisation.

|Check lista kategori| Beskrivning|
| ------------ | -------- |
| [<br>Säkerhets roller & åtkomst kontroller](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>Använd [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) för att ge användarspecifika användar specifika behörigheter som används för att tilldela behörigheter till användare, grupper och program i ett visst omfång.</li></ul> |
| [<br>Data insamling & lagring](../../storage/blobs/security-recommendations.md)|<ul><li>Använd hanterings plan säkerhet för att skydda ditt lagrings konto med hjälp av [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).</li><li>Säkerhet för data planet för att skydda åtkomsten till dina data med hjälp av [signaturer för delad åtkomst (SAS)](../../storage/common/storage-sas-overview.md) och lagrade åtkomst principer.</li><li>Använd Transport-Level kryptering – med HTTPS och kryptering som används av [SMB-protokoll (Server Message Block) 3,0](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) för [Azure-filresurser](../../storage/files/storage-dotnet-how-to-use-files.md).</li><li>Använd [kryptering på klient sidan](../../storage/common/storage-client-side-encryption.md) för att skydda data som du skickar till lagrings konton när du behöver ensam kontroll över krypterings nycklar. </li><li>Använd [kryptering för lagringstjänst (SSE)](../../storage/common/storage-service-encryption.md)  för att automatiskt kryptera data i Azure Storage och [Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) för att kryptera virtuella datorers DISKPARTITIONer för operativ system-och data diskar.</li><li>Använd Azure [Lagringsanalys](/rest/api/storageservices/storage-analytics) för att övervaka typ av auktorisering; precis som med Blob Storage kan du se om användarna har använt en signatur för delad åtkomst eller lagrings konto nycklar.</li><li>Använd [resurs delning mellan ursprung (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) för att få åtkomst till lagrings resurser från olika domäner.</li></ul> |
|[<br>Säkerhets principer & rekommendationer](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>Använd [Azure Security Center](../../security-center/security-center-services.md#supported-endpoint-protection-solutions-) för att distribuera slut punkts lösningar.</li><li>Lägg till en brand vägg för webbaserade [program (WAF)](../../web-application-firewall/ag/ag-overview.md) för att skydda webb program.</li><li>  Använd en [brand vägg](../../sentinel/connect-data-sources.md) från en Microsoft-partner för att öka säkerhets skyddet. </li><li>Använd säkerhets kontakt information för din Azure-prenumeration. [Microsoft Security Response Center (MSRC)](https://technet.microsoft.com/security/dn528958.aspx) kontaktar dig om det upptäcker att din kund information har öppnats av en olagligt eller obehörig part.</li></ul> |
| [<br>Identitets & åtkomst hantering](identity-management-best-practices.md)|<ul><li>[Synkronisera din lokala katalog med din moln katalog med hjälp av Azure AD](../../active-directory/hybrid/whatis-hybrid-identity.md).</li><li>Använd [enkel inloggning](https://azure.microsoft.com/resources/videos/overview-of-single-sign-on/) för att ge användarna åtkomst till sina SaaS-program baserat på deras organisations konto i Azure AD.</li><li>Använd [aktivitets rapporten registrering av lösen ords återställning](../../active-directory/authentication/howto-sspr-reporting.md) för att övervaka de användare som registreras.</li><li>Aktivera [Multi-Factor Authentication (MFA)](../../active-directory/authentication/concept-mfa-howitworks.md) för användare.</li><li>Utvecklare kan använda säkra identitets funktioner för appar som [Microsoft Security Development Lifecycle (SDL)](https://www.microsoft.com/download/details.aspx?id=12379).</li><li>Övervaka aktivt för misstänkta aktiviteter genom att använda Azure AD Premium avvikelse rapporter och [funktioner för Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md).</li></ul> |
|[<br>Fortlöpande säkerhetsövervakning](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>Använd lösning för utvärdering av skadlig kod [Azure Monitor loggar](../../azure-monitor/logs/log-query-overview.md) för att rapportera om status för skydd mot skadlig kod i din infrastruktur.</li><li>Använd [uppdaterings utvärdering](../../automation/update-management/overview.md) för att fastställa den övergripande exponeringen för potentiella säkerhets problem och om eller hur viktiga uppdateringar är för din miljö.</li><li>[Identiteten och åtkomsten](../../security-center/security-center-monitoring.md) ger dig en översikt över användaren </li><ul><li>status för användar identitet,</li><li>antal misslyckade försök att logga in</li><li> användarens konto som användes vid dessa försök, konton som har låsts ut</li> <li>konton med ändrat eller återställt lösen ord </li><li>Antalet konton som är inloggade för tillfället.</li></ul></ul> |
| [<br>Identifieringsfunktioner i Azure Security Center](../../security-center/security-center-alerts-overview.md#detect-threats)|<ul><li>Använd [identifierings funktioner](../../security-center/security-center-alerts-overview.md#detect-threats)för att identifiera aktiva hot som riktar sig mot dina Microsoft Azure-resurser.</li><li>Använd [integrerad Hot information](/archive/blogs/azuresecurity/get-threat-intelligence-reports-with-azure-security-center) som söker efter kända dåliga aktörer genom att dra nytta av Global Threat Intelligence från Microsofts produkter och tjänster, [Microsoft Digital brottslighet Unit (DCU)](https://www.microsoft.com/trustcenter/security/cybercrime), [Microsoft Security Response Center (MSRC)](https://www.microsoft.com/msrc?rtc=1)och externa feeds.</li><li>Använd [beteende analys](https://blogs.technet.microsoft.com/enterprisemobility/2016/06/30/ata-behavior-analysis-monitoring/) som använder kända mönster för att identifiera skadligt beteende. </li><li>Använd [avvikelse identifiering](/azure/machine-learning/studio-module-reference/anomaly-detection) som använder statistisk profilering för att bygga en historisk bas linje.</li></ul> |
| [<br>Utvecklings åtgärder (DevOps)](/azure/architecture/checklist/dev-ops)|<ul><li>[Infrastruktur som kod (IaC)](../../azure-resource-manager/templates/template-syntax.md) är en praxis som möjliggör automatisering och validering av skapande och Teardown av nätverk och virtuella datorer för att hjälpa till med att leverera säkra, stabila program värd plattformar.</li><li>[Kontinuerlig integrering och distributions](/visualstudio/containers/overview#continuous-delivery-and-continuous-integration-cicd) enhet är den pågående sammanslagningen och testningen av kod, vilket leder till att hitta fel tidigt. </li><li>[Versions hantering](/azure/devops/pipelines/overview?viewFallbackFrom=azure-devops) Hantera automatiserade distributioner via varje steg i din pipeline.</li><li>[Övervakning av appars prestanda](../../azure-monitor/app/asp-net.md) för program som körs, inklusive produktions miljöer för program hälso-och kund användnings hjälp organisationer bildar en hypotes och snabbt verifierar eller förvisar strategier.</li><li>Genom att använda [belastnings testning & autoskalning](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) kan du hitta prestanda problem i vår app för att förbättra distributions kvaliteten och se till att vår app alltid är igång eller tillgänglig för att tillgodose affärs behoven.</li></ul> |


## <a name="conclusion"></a>Slutsats
Många organisationer har distribuerat och driva sina moln program på Azure. Check listorna markeras med flera check listor som är viktiga och som hjälper dig att öka sannolikheten för lyckade distributioner och kostnads fria åtgärder. Vi rekommenderar starkt dessa operativa och strategiska överväganden för dina befintliga och nya program distributioner på Azure.

## <a name="next-steps"></a>Nästa steg
Mer information om säkerhet finns i följande artiklar:

- [Design-och drift säkerhet](https://www.microsoft.com/trustcenter/security/designopsecurity).
- [Azure Security Center planering och åtgärder](../../security-center/security-center-planning-and-operations-guide.md).