---
title: Styrande Azure Active Directory tjänst konton
description: Principer och procedurer för att hantera livs cykeln för tjänst konton i Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee6ac21d67f32fbc61db19b348fc29cdf3ee9fd7
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418189"
---
# <a name="governing-azure-ad-service-accounts"></a>Styrande Azure AD-tjänstekonton

Det finns tre typer av tjänst konton i Azure Active Directory (Azure AD): [hanterade identiteter](service-accounts-managed-identities.md), [tjänstens huvud namn](service-accounts-principal.md)och användar konton som används som tjänst konton. När du skapar dessa tjänst konton för automatisk användning beviljas de behörighet att komma åt resurser i Azure och Azure AD. Resurser kan omfatta Microsoft 365 tjänster, SaaS-program (program vara som en tjänst), anpassade program, databaser, HR-system och så vidare. Att hantera Azure AD-tjänstekonton innebär att du hanterar deras skapande, behörigheter och livs cykel för att säkerställa säkerhet och kontinuitet.

> [!IMPORTANT] 
> Vi rekommenderar inte att du använder användar konton som tjänst konton eftersom de är mycket mindre säkra. Detta inkluderar lokala tjänst konton som synkroniseras till Azure AD, eftersom de inte konverteras till tjänstens huvud namn. I stället rekommenderar vi att du använder hanterade identiteter eller tjänstens huvud namn. Observera att för närvarande går det inte att använda principer för villkorlig åtkomst med tjänstens huvud namn, men funktionen kommer.


## <a name="plan-your-service-account"></a>Planera ditt tjänst konto

Innan du skapar ett tjänst konto, eller registrerar ett program, dokumenterar du tjänst kontots viktig information. Med information som dokumenteras gör det lättare att effektivt övervaka och styra kontot. Vi rekommenderar att du samlar in följande data och spårar dem i din centraliserade konfigurations hanterings databas (CMDB).

| Data| Beskrivning| Information |
| - | - | - |
| Ägare| Användare eller grupp som är konto för att hantera och övervaka tjänst kontot.| Etablera ägaren med nödvändiga behörigheter för att övervaka kontot och implementera ett sätt att åtgärda problem. Ärende minskning kan göras av ägaren eller via en begäran till den. |
| Syfte| Hur kontot ska användas.| Mappa tjänst kontot till en specifik tjänst, ett program eller ett skript. Undvik att skapa tjänst konton för flera användare. |
| Behörigheter (omfattningar)| Förväntad uppsättning behörigheter.| Dokumentera de resurser som den kommer att ha åtkomst till och behörigheterna till dessa resurser. |
| CMDB-länk| Länk till de resurser som ska nås och skript där tjänst kontot används.| Se till att du dokumenterar resurs-och skript ägare så att du kan kommunicera eventuella nödvändiga överordnade och underordnade effekter av ändringar. |
| Riskbedömning| Risk-och företags påverkan om kontot skulle komprometteras.| Använd den här informationen för att begränsa omfattningen av behörigheter och bestämma vem som ska ha åtkomst till konto informationen. |
| Period för granskning| Det schema som tjänst kontot ska granskas av av ägaren.| Använd den här för att schemalägga granskning av kommunikation och recensioner. Dokumentera vad som ska hända om en granskning inte utförs vid en viss tidpunkt efter den schemalagda gransknings perioden. |
| Giltighet| Förväntad högsta livstid för konto.| Använd den här för att schemalägga kommunikation till ägaren och inaktivera sedan kontona. När det är möjligt anger du ett förfallo datum för autentiseringsuppgifter, där autentiseringsuppgifterna inte kan återställas automatiskt. |
| Name| Standardiserat konto namn| Skapa ett namngivnings schema för alla tjänst konton så att du enkelt kan söka, sortera och filtrera efter tjänst konton. |


## <a name="use-the-principle-of-least-privileges"></a>Använd principen om lägsta behörighet
Bevilja tjänst kontot bara de behörigheter som krävs för att utföra dess uppgifter och inte fler. Om ett tjänst konto behöver behörigheter på hög nivå, till exempel en global administratörs nivå för privilegier, utvärdera varför och försök att minska de nödvändiga behörigheterna.

Vi rekommenderar följande metoder för behörigheter för tjänst konton.

**Behörigheter**

* Tilldela inte inbyggda roller till tjänst konton. Använd i stället [OAuth2-modellen för behörighets beviljande för Microsoft Graph](/graph/api/resources/oauth2permissiongrant),

* Om tjänstens huvud namn måste tilldelas en privilegie rad roll, bör du överväga att tilldela en [anpassad roll](https://docs.microsoft.com/azure/active-directory/roles/custom-create) med särskilda, nödvändiga privilegier, i en tids gräns.

* Ta inte med tjänst konton som medlemmar i grupper med utökade behörigheter. 

* [Använd PowerShell för att räkna upp medlemmar i privilegierade roller](/powershell/module/azuread/get-azureaddirectoryrolemember), till exempel   
`Get-AzureADDirectoryRoleMember`och filter för objectType "tjänstens huvud namn".

   eller Använd  
`Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

* [Använd OAuth 2,0-omfattningar](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent) för att begränsa vilka funktioner ett tjänst konto har åtkomst till på en resurs.
* Tjänstens huvud namn och hanterade identiteter kan använda OAuth 2,0-scope i antingen en delegerad kontext som personifierar en inloggad användare eller som tjänst konto i program kontexten. I program kontexten är ingen inloggad.

* Kontrol lera begär Anden tjänst begär Anden för att få resurser för att säkerställa att de är lämpliga. Om ett konto till exempel begär filer. ReadWrite. all, utvärdera om det verkligen bara behöver fil. Read. all. Mer information om behörigheter finns i [Microsoft Graph behörighets referens](https://docs.microsoft.com/graph/permissions-reference).

* Se till att du litar på program-eller API-utvecklaren med den åtkomst som krävs för dina resurser.

**Varaktighet**

* Begränsa tjänst kontots autentiseringsuppgifter (klient hemlighet, certifikat) till en förväntad användnings period.

* Schema periodiska granskningar av användning och syfte för tjänst konton. Se till att granskningar utförs innan kontot upphör att gälla.

När du har en tydlig förståelse av syftet, omfattningen och de behörigheter som krävs skapar du ditt tjänst konto. 

[Skapa och Använd hanterade identiteter](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)

[Skapa och använda tjänstens huvud namn](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

Använd en hanterad identitet när det är möjligt. Om du inte kan använda en hanterad identitet använder du ett huvud namn för tjänsten. Om du inte kan använda ett huvud namn för tjänsten och sedan använda ett Azure AD-användarkonto.

 

## <a name="build-a-lifecycle-process"></a>Bygg en livs cykel process

Att hantera livs cykeln för ett tjänst konto börjar med planering och slutar med permanent borttagning. 

Den här artikeln har tidigare täckt planerings-och skapande delen. Du måste också övervaka, granska behörigheter, fastställa ett kontos fortsatta användning och slutligen avetablera kontot.

### <a name="monitor-service-accounts"></a>Övervaka tjänst konton

Övervaka dina tjänst konton proaktivt för att se till att tjänst kontots användnings mönster återspeglar de avsedda mönstren och att tjänst kontot fortfarande används aktivt.

**Samla in och övervaka inloggnings tillägg för tjänst konton med någon av följande metoder:**

* Använda Azure AD Sign-In loggar i Azure AD-portalen.

* Exportera Azure AD Sign-In-loggar till [Azure Storage](https://docs.microsoft.com/azure/storage/), [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)eller [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/logs/data-platform-logs).


![Skärm bild som visar inloggnings skärmen för tjänstens huvud namn.](./media/securing-service-accounts/service-accounts-govern-azure-1.png)

**Information som du bör söka efter i Sign-In loggar innehåller:**

* Finns det tjänst konton som inte längre loggar in på klienten?

* Ändras inloggnings mönstren för tjänst konton?

Vi rekommenderar att du exporterar inloggnings loggar för Azure AD och importerar dem till dina befintliga verktyg för säkerhets informations-och händelse hantering (SIEM), till exempel Azure Sentinel. Använd din SIEM för att bygga aviseringar och instrument paneler.

### <a name="review-service-account-permissions"></a>Granska tjänst konto behörigheter

Granska de behörigheter som har beviljats och omfattningarna som används av tjänst konton regelbundet för att se om de kan minskas.

* Använd [PowerShell](/powershell/module/azuread/get-azureadserviceprincipaloauth2permissiongrant) för att [bygga automatisering för att kontrol lera och dokumentera](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) omfånget som medgivande beviljas till ett tjänst konto.

* Använd PowerShell för att [granska befintliga autentiseringsuppgifter för tjänstens huvud namn](https://github.com/AzureAD/AzureADAssessment) och kontrol lera deras giltighet.

* Ange inte tjänstens huvud namn till "upphör aldrig att gälla".

* Använd certifikat eller autentiseringsuppgifter som lagras i Azure-valv där det är möjligt.

Microsofts kostnads fria PowerShell-exempel samlar in tjänstens huvud namn för OAuth2-bidrag och autentiseringsinformation, registrerar dem i en fil med kommaavgränsade värden (CSV) och en instrument panel för Power BI exempel för att tolka och använda data. Mer information finns i [AzureAD/AzureADAssessment: verktyg för att utvärdera ett Azure AD-klient tillstånd och-konfiguration (GitHub.com)](https://github.com/AzureAD/AzureADAssessment)

### <a name="recertify-service-account-use"></a>Omcertifiera användning av tjänst konto

Upprätta en gransknings process för att säkerställa att tjänst konton regelbundet granskas av sina ägare och säkerhets-eller IT-teamet med jämna mellanrum. 

**Processen bör innehålla:**

* Hur du fastställer varje tjänst kontos gransknings cykel (bör dokumenteras i CMDB).

* Kommunikationen till ägare och säkerhet eller IT-team innan granskningar startar.

* Tids inställningen och innehållet för varnings kommunikation om granskningen saknas.

* Anvisningar om vad du kan göra om ägarna inte kan granska eller svara. Du kanske till exempel vill inaktivera (men inte ta bort) kontot förrän granskningen är klar.

* Instruktioner för att fastställa överordnade och underordnade beroenden och meddela andra resurs ägare om eventuella effekter.

**Granskningen bör omfatta ägare och IT-partner som certifierar att:**

* Kontot är fortfarande nödvändigt.

* Behörigheterna som har tilldelats kontot är tillräckliga och nödvändiga, eller så begärs en ändring.

* Åtkomsten till kontot och autentiseringsuppgifterna kontrol leras.

* De autentiseringsuppgifter som kontot använder är lämpliga för den risk som kontot utvärderades med (både autentiseringstypen för autentiseringsuppgifter och livstid för autentiseringsuppgifter)

* Kontots risk Poäng har inte ändrats sedan den senaste återcertifieringen

* En uppdatering av kontots förväntade livs längd och nästa återcertifierings datum.

### <a name="deprovision-service-accounts"></a>Deetablering av tjänst konton

* * Deetablering av tjänst konton under följande omständigheter: * * * *

* Skriptet eller programmet som tjänst kontot skapades för har dragits tillbaka.

* Funktionen i skriptet eller programmet som tjänst kontot används för (till exempel åtkomst till en specifik resurs) dras tillbaka.


* Tjänst kontot ersätts med ett annat tjänst konto.

* Autentiseringsuppgifterna har upphört att gälla, eller så är kontot annars inte funktionellt, och det finns inga klagomål.

**Processerna för avetablering bör omfatta följande uppgifter.**

1. När det associerade programmet eller skriptet har avetablerats, [övervakar du inloggningar](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#sign-ins-report) och resurs åtkomst av tjänst kontot.

   * Om kontot fortfarande är aktivt, avgör du hur det används innan du vidtar efterföljande steg.
 
2. Om detta är en hanterad tjänst identitet inaktiverar du tjänst kontot från att logga in, men tar inte bort det från katalogen.

3. Återkalla roll tilldelningar och OAuth2 medgivande-bidrag för tjänst kontot.

4. Ta bort tjänst kontot från katalogen efter en definierad period och en mycket gott varning till ägare.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du skyddar Azure-tjänstekonton finns i:

[Introduktion till Azures tjänst konton](service-accounts-introduction-azure.md)

[Skydda hanterade identiteter](service-accounts-managed-identities.md)

[Skydda tjänst principer](service-accounts-principal.md)




 

 
