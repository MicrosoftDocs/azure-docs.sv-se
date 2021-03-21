---
title: Hitta rapporter om användar aktiviteter i Azure Portal | Microsoft Docs
description: Lär dig var Azure Active Directory användar aktivitets rapporter finns i Azure Portal.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42afa073da9197c12e4cbd316d311a7699d9a95f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96013043"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Hitta aktivitetsrapporter på Azure-portalen

I den här artikeln får du lära dig att hitta Azure Active Directory (Azure AD) rapporter för användar aktiviteter i Azure Portal.

## <a name="audit-logs-report"></a>Granskningsloggar

Rapporten gransknings loggar kombinerar flera rapporter runt program aktiviteter i en enda vy för Sammanhangs beroende rapportering. Så här öppnar du rapporten gransknings loggar:

1. Navigera till [Azure Portal](https://portal.azure.com).
2. Välj din katalog i det övre högra hörnet och välj bladet **Azure Active Directory** i det vänstra navigerings fönstret.
3. Välj **gransknings loggar** från avsnittet **aktivitet** på bladet Azure Active Directory. 

    ![Granskningsloggar](./media/howto-find-activity-reports/482.png "Granskningsloggar")

Rapporten gransknings loggar konsoliderar följande rapporter:

* Granskningsrapport
* Lösenordsåterställningsaktivitet
* Registrerings aktivitet för lösen ords återställning
* Aktivitet för självbetjänings grupper
* Ändring av grupp namn för Office365
* Konto etablerings aktivitet
* Status för lösen ords förnyelse
* Kontoetableringsfel

### <a name="filtering-on-audit-logs"></a>Filtrera efter gransknings loggar

Du kan använda avancerad filtrering i gransknings rapporten för att få åtkomst till en viss kategori av gransknings data genom att ange den i **kategori** filtret. Om du till exempel vill visa alla aktiviteter som är relaterade till användare väljer du kategorin **UserManagement** . 

Kategorier inkluderar:

- Alla
- AdministrativeUnit
- ApplicationManagement
- Autentisering
- Auktorisering
- Kontakt
- Enhet
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- Övrigt
- Policy
- ResourceManagement
- RoleManagement
- UserManagement

Du kan också filtrera på en tjänst med **hjälp av filter List filtret** . Om du till exempel vill få alla gransknings händelser relaterade till lösen ords hantering via självbetjäning väljer du filtret för **lösen ords hantering** via självbetjäning.

Tjänsterna omfattar:

- Alla
- Åtkomstgranskningar
- Kontoetablering 
- SSO för program
- Autentiseringsmetoder
- B2C
- Villkorlig åtkomst
- Kärnkatalog
- Hantering av rättigheter
- Identity Protection
- Inbjudna användare
- PIM
- Självbetjäning, grupphantering
- Hantering av lösenord för självbetjäning
- Användningsvillkor

## <a name="sign-ins-report"></a>Rapport över inloggningar 

I vyn **inloggningar** ingår alla användar inloggningar samt **program användnings** rapporten. Du kan också visa information om program användning i avsnittet **Hantera** i översikten över **företags program** .

Få åtkomst till inloggnings rapporten:

1. Navigera till [Azure Portal](https://portal.azure.com).
2. Välj din katalog i det övre högra hörnet och välj bladet **Azure Active Directory** i det vänstra navigerings fönstret.
3. Välj **inloggningar** från avsnittet **aktivitet** på bladet Azure Active Directory. 

    ![Vyn inloggningar](./media/howto-find-activity-reports/483.png "Vyn inloggningar")


### <a name="filtering-on-application-name"></a>Filtrera efter program namn

Du kan använda inloggnings rapporten för att visa information om program användning, genom att filtrera efter användar namn eller program namn.

![Sidan filtrera Sign-In händelser](./media/howto-find-activity-reports/07.png "Sidan filtrera Sign-In händelser")

## <a name="security-reports"></a>Säkerhetsrapporter

### <a name="anomalous-activity-reports"></a>Avvikande aktivitets rapporter

Avvikande aktivitets rapporter innehåller information om säkerhetsrelaterade risk identifieringar som Azure AD kan identifiera och rapportera om.

I följande tabell visas säkerhets rapporter i Azure AD-avvikande aktivitet och motsvarande typer av risk identifiering i Azure Portal. Mer information finns i [Azure Active Directory risk identifieringar](../identity-protection/overview-identity-protection.md).  


| Rapport om avvikande aktiviteter i Azure AD |  Typ av risk identifiering för identitets skydd|
| :--- | :--- |
| Användare med läckta autentiseringsuppgifter | Läckta autentiseringsuppgifter |
| Oregelbunden inloggningsaktivitet | Omöjliga resor till ovanliga platser |
| Inloggningar från potentiellt infekterade enheter | Inloggningar från angripna enheter|
| Inloggningar från okända källor | Inloggningar från anonyma IP-adresser |
| Inloggningar från IP-adresser med misstänkt aktivitet | Inloggningar från IP-adresser med misstänkt aktivitet |
| - | Inloggningar från okända platser |

Följande säkerhets rapporter i Azure AD-avvikande aktivitet ingår inte som identifieringar av risker i Azure Portal:

* Inloggningar efter flera fel
* Inloggningar från flera geografiska områden


### <a name="detected-risk-detections"></a>Identifierade risk identifieringar

Du kan komma åt rapporter om identifierade risk identifieringar i avsnittet **säkerhet** på bladet **Azure Active Directory** i [Azure Portal](https://portal.azure.com). Identifierade risk identifieringar spåras i följande rapporter:   

- [Användare i riskzonen](../identity-protection/overview-identity-protection.md)
- [Riskfyllda inloggningar](../identity-protection/overview-identity-protection.md)

    ![Säkerhetsrapporter](./media/howto-find-activity-reports/04.png "Säkerhetsrapporter")

## <a name="troubleshoot-issues-with-activity-reports"></a>Felsöka problem med aktivitets rapporter

### <a name="missing-data-in-the-downloaded-activity-logs"></a>Data som saknas i hämtade aktivitets loggar

#### <a name="symptoms"></a>Symtom 

Jag har hämtat aktivitetsloggarna (granskning eller inloggningar) och kan inte se alla poster för den tid som jag har valt. Varför? 

 ![Skärm bild som visar knappen Ladda ned i aktivitets rapporten.](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>Orsak

När du hämtar aktivitets loggar i Azure Portal begränsar vi skalan till 250000 poster, sorterade efter senaste första. 

#### <a name="resolution"></a>Lösning

Du kan använda [rapporterings-API:er för Azure AD](concept-reporting-api.md) att hämta upp till en miljoner poster när som helst.

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Gransknings data saknas för de senaste åtgärderna i Azure Portal

#### <a name="symptoms"></a>Symtom

Jag utförde vissa åtgärder i Azure Portal och förväntade att se granskningsloggarna för dessa åtgärder på bladet `Activity logs > Audit Logs`, men det går inte att hitta dem.

 ![Skärm bild som visar aktivitets rapporten.](./media/troubleshoot-missing-audit-data/01.png)
 
#### <a name="cause"></a>Orsak

Åtgärder visas inte omedelbart i aktivitetsloggarna. Tabellen nedan räknar upp våra svarstidsvärden för aktivitetsloggar. 

| Rapport | Svarstid (P95) | Svarstid (P99) |
|--------|---------------|---------------|
| Kataloggranskning | 2 min | 5 min |
| Inloggningsaktivitet | 2 min | 5 min |

#### <a name="resolution"></a>Lösning

Vänta i mellan 15 minuter och 2 timmar och se om åtgärderna visas i loggen. Om du inte ser loggarna även efter två timmar [skapar du en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) så undersöker vi problemet.

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>Loggar som saknas för de senaste användar inloggningarna i aktivitets loggen för Azure AD-inloggning

#### <a name="symptoms"></a>Symtom

Jag loggade nyligen in på Azure-portalen och förväntade mig att se inloggningsloggarna för de åtgärderna på bladet `Activity logs > Sign-ins`, men det går inte att hitta dem.

 ![Skärm bild som visar inloggnings program för Azure Active Directory.](./media/troubleshoot-missing-audit-data/02.png)
 
#### <a name="cause"></a>Orsak

Åtgärder visas inte omedelbart i aktivitetsloggarna. Tabellen nedan räknar upp våra svarstidsvärden för aktivitetsloggar. 

| Rapport | Svarstid (P95) | Svarstid (P99) |
|--------|---------------|---------------|
| Kataloggranskning | 2 min | 5 min |
| Inloggningsaktivitet | 2 min | 5 min |

#### <a name="resolution"></a>Lösning

Vänta i mellan 15 minuter och 2 timmar och se om åtgärderna visas i loggen. Om du inte ser loggarna även efter två timmar [skapar du en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) så undersöker vi problemet.

### <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Jag kan inte se mer än 30 dagars rapportdata i Azure-portalen

#### <a name="symptoms"></a>Symtom

Jag kan inte se mer än 30 dagars inloggnings- och granskningsdata från Azure-portalen. Varför? 

 ![Skärm bild som visar menyn datum.](./media/troubleshoot-missing-audit-data/03.png)

#### <a name="cause"></a>Orsak

Beroende på din licens lagrar Azure Active Directory aktivitetsrapporter under så här lång tid:

| Rapport           | Azure AD Kostnadsfri | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ---           | ---                 | ---
| Kataloggranskning  | 7 dagar        | 30 dagar             | 30 dagar             |
| Inloggningsaktivitet | Inte tillgängligt. Du kan komma åt dina egna inloggningar i 7 dagar från det bladet för enskild användare | 30 dagar | 30 dagar             |

Mer information finns i [Kvarhållningsprinciper för rapporter i Azure Active Directory](reference-reports-data-retention.md).  

#### <a name="resolution"></a>Lösning

Du har två alternativ för att behålla data längre än 30 dagar. Du kan använda [Azure AD Reporting-API:er](concept-reporting-api.md) för att hämta data programmatiskt och lagra dem i en databas. Du kan även integrera spårningsloggar i ett SIEM-system från tredje part, till exempel Splunk eller SumoLogic.

## <a name="next-steps"></a>Nästa steg

* [Översikt över gransknings loggar](concept-audit-logs.md)
* [Översikt över inloggningar](concept-sign-ins.md)
* [Översikt över riskfyllda händelser](../identity-protection/overview-identity-protection.md)