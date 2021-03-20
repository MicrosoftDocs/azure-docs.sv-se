---
title: Granska aktivitetsrapporter i Azure Active Directory-portalen | Microsoft Docs
description: Introduktion till granskning av aktivitetsrapporter i Azure Active Directory-portalen
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 09/17/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5aa8f675e3fd36fbebfecf42db0f02b0f0f00115
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95995989"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Granska aktivitetsrapporter i Azure Active Directory-portalen 

Med Azure Active Directory (Azure AD)-rapporter kan du få den information du behöver för att avgöra hur din miljö fungerar.



Rapporterings arkitekturen består av följande komponenter:

- **Aktivitet** 
    - **Inloggningar** – [inloggnings rapporten](concept-sign-ins.md) innehåller information om användningen av hanterade program och användar inloggnings aktiviteter.
    - **Gransknings loggar** – ger spårning genom loggar för alla ändringar som gjorts av olika funktioner i Azure AD. Exempel på granskningsloggar är de resursändringar som görs i Azure AD, som att lägga till eller ta bort användare, appar, grupper, roller och principer.
    - **Etablerings loggar**  -  Med [etablerings loggar](./concept-provisioning-logs.md) kan kunder övervaka aktivitet från etablerings tjänsten, till exempel skapa en grupp i ServiceNow eller en användare som importer ATS från Workday. 
- **Säkerhet** 
    - **Riskfyllda inloggningar** – en [riskfylld inloggning](../identity-protection/overview-identity-protection.md) är en indikator för ett inloggnings försök som kan ha utförts av någon som inte är en legitim ägare till ett användar konto. 
    - **Användare som har flaggats för risk** – en [riskfylld användare](../identity-protection/overview-identity-protection.md) är en indikator för ett användar konto som kan ha komprometterats.

Den här artikeln ger en översikt över gransknings rapporten.
 
## <a name="who-can-access-the-data"></a>Vem som kan komma åt data?

* Användare i rollerna **säkerhets administratör**, **säkerhets läsare**, **rapport läsare** , **global läsare** eller **Global administratör**

## <a name="audit-logs"></a>Granskningsloggar

I gransknings loggarna i Azure AD finns register över system aktiviteter för efterlevnad. Välj **gransknings loggar** i avsnittet **övervakning** i **Azure Active Directory** för att få åtkomst till gransknings rapporten. 



En granskningslogg har en standardlistvy som visar:

- datum och tid för förekomsten
- tjänsten som loggade förekomsten
- aktivitetens kategori och namn (*vad*) 
- aktivitetens status (lyckades eller misslyckades)
- målet
- initieraren/aktören (vem) för en aktivitet

![Granskningsloggar](./media/concept-audit-logs/listview.png "Granskningsloggar")

Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.

![Gransknings kolumner](./media/concept-audit-logs/columns.png "Gransknings kolumner")

På så sätt kan du visa ytterligare fält eller ta bort fält som redan visas.

![Ta bort fält](./media/concept-audit-logs/columnselect.png "Ta bort fält")

Välj ett objekt i listvyn om du vill ha mer detaljerad information.

![Välj objekt](./media/concept-audit-logs/details.png "Välj objekt")


## <a name="filtering-audit-logs"></a>Filtrera granskningsloggar

Du kan filtrera gransknings data i följande fält:

- Tjänst
- Kategori
- Aktivitet
- Status
- Mål
- Initierad av (aktör)
- Datumintervall

![Filtrera objekt](./media/concept-audit-logs/filter.png "Filtrera objekt")

Med **tjänst** filtret kan du välja från en listruta med följande tjänster:

- Alla
- AAD-hantering UX
- Åtkomstgranskningar
- Kontoetablering
- Programproxy
- Autentiseringsmetoder
- B2C
- Villkorlig åtkomst
- Kärnkatalog
- Hantering av rättigheter
- Hybrid autentisering
- Identity Protection
- Inbjudna användare
- MIM-tjänst
- MyApps
- PIM
- Självbetjäning, grupphantering
- Hantering av lösenord för självbetjäning
- Användningsvillkor

Med filtret **kategori** kan du välja något av följande filter:

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
- KerberosDomain
- Hantering av
- Etikett
- Övrigt
- PermissionGrantPolicy
- Policy
- ResourceManagement
- RoleManagement
- UserManagement

**Aktivitets** filtret baseras på resurs typs valet kategori och aktivitet som du gör. Du kan välja en specifik aktivitet som du vill visa eller välja alla. 

Du kan hämta listan över alla gransknings aktiviteter med hjälp av Graph API: `https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

Med **status** filtret kan du filtrera baserat på status för en gransknings åtgärd. Statusen kan vara något av följande:

- Alla
- Klart
- Fel

Med **mål** filtret kan du söka efter ett visst mål genom att starta namnet eller User Principal Name (UPN). Mål namnet och UPN är Skift läges känsliga. 

Med filtret **initierad av** kan du definiera vad en aktörs namn eller ett universellt huvud namn (UPN) börjar med. Namnet och UPN är Skift läges känsliga.

Med filtret för **datum intervall** kan du definiera en tidsram för de data som returneras.  
Möjliga värden:

- 7 dagar
- 24 timmar
- Anpassad

När du väljer en anpassad tidsram kan du konfigurera en starttid och en sluttid.

Du kan också välja att ladda ned filtrerade data, upp till 250 000 poster, genom att välja knappen **Hämta** . Du kan hämta loggarna i antingen CSV-eller JSON-format. Antalet poster som du kan hämta begränsas av [Azure Active Directory bevarande principer för rapporter](reference-reports-data-retention.md).

![Nedladdning av data](./media/concept-audit-logs/download.png "Nedladdning av data")

## <a name="audit-logs-shortcuts"></a>Genvägar till granskningsloggar

Förutom **Azure Active Directory** finns det ytterligare två ställen på Azure Portal där du kan granska data:

- Användare och grupper
- Företagsprogram

### <a name="users-and-groups-audit-logs"></a>Granskningsloggar för användare och grupper

Med användar- och gruppbaserade granskningsrapporter kan du få svar på frågor som:

- Vilka typer av uppdateringar har använts för användarna?

- Hur många användare ändrades?

- Hur många lösenord ändrades?

- Vad har en administratör gjort i en katalog?

- Vilka grupper har lagts till?

- Finns det grupper med ändringar i medlemskap?

- Har ägare av en grupp ändrats?

- Vilka licenser har tilldelats en grupp eller en användare?

Om du bara vill granska gransknings data som är relaterade till användare kan du hitta en filtrerad vy under **gransknings loggar** i avsnittet **övervakning** på fliken **användare** . Den här start punkten har **UserManagement** som förvalt kategori.

![Användare](./media/concept-audit-logs/users.png "User")

Om du bara vill granska gransknings data som är relaterade till grupper kan du hitta en filtrerad vy under **gransknings loggar** i avsnittet **övervakning** på fliken **grupper** . Den här start punkten har **GroupManagement** som förvalt kategori.

![Filtrera grupper](./media/concept-audit-logs/groups.png "Filtrera grupper")

### <a name="enterprise-applications-audit-logs"></a>Granskningsloggar för företagsprogram

Med programbaserade granskningsrapporter kan du få svar på frågor som:

* Vilka program har lagts till eller uppdaterats?
* Vilka program har tagits bort?
* Har ett tjänst huvud namn för ett program ändrats?
* Har namnen på program ändrats?
* Vem gav tillstånd till ett program?

Om du vill granska gransknings data som är relaterade till dina program kan du hitta en filtrerad vy under **gransknings loggar** i avsnittet **aktivitet** på bladet **företags program** . Den här start punkten har **företags program** som är förvalda som **program typ**.

![Företags program](./media/concept-audit-logs/enterpriseapplications.png "Företagsprogram")

## <a name="microsoft-365-activity-logs"></a>Microsoft 365 aktivitets loggar

Du kan visa Microsoft 365 aktivitets loggar från [Microsoft 365 administrations Center](/office365/admin/admin-overview/about-the-admin-center). Även om Microsoft 365 aktivitet och Azure AD-aktivitets loggar delar en stor del av katalog resurserna, ger endast administrations centret Microsoft 365 en fullständig vy över de Microsoft 365 aktivitets loggarna. 

Du kan också komma åt Microsoft 365 aktivitets loggar via programmering med hjälp av [API: er för Office 365-hantering](/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Nästa steg

- [Referens över granskningsaktiviteter i Azure AD](reference-audit-activities.md)
- [Referens för kvarhållning av Azure AD-rapporter](reference-reports-data-retention.md)
- [Referens för fördröjning i Azure AD-logg](reference-reports-latencies.md)