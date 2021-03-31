---
title: Windows Virtual Desktop (klassisk) diagnostisera problem – Azure
description: Så här använder du funktionen Windows Virtual Desktop (klassisk) för att diagnosticera problem.
author: Heidilohr
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7e652f04b42b132e7c1307503b1764dda7b2036b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88009349"
---
# <a name="identify-and-diagnose-issues-in-windows-virtual-desktop-classic"></a>Identifiera och diagnostisera problem i Windows Virtual Desktop (klassisk)

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer (klassisk), vilket inte stöder Azure Resource Manager virtuella Skriv bords objekt i Windows. Om du försöker hantera Azure Resource Manager virtuella Windows Desktop-objekt, se [den här artikeln](../diagnostics-role-service.md).

Windows Virtual Desktop erbjuder en diagnostisk funktion som gör det möjligt för administratören att identifiera problem via ett enda gränssnitt. Windows Virtual Desktop-roller loggar en diagnostisk aktivitet när en användare interagerar med systemet. Varje logg innehåller relevant information, till exempel de Windows-roller för virtuella skriv bord som ingår i transaktionen, fel meddelanden, klient information och användar information. Diagnostiska aktiviteter skapas av både slutanvändare och administrativa åtgärder och kan kategoriseras i tre huvud buckets:

* Flödes prenumerations aktiviteter: slutanvändaren utlöser dessa aktiviteter när de försöker ansluta till sina flöden via Microsoft Fjärrskrivbord-program.
* Anslutnings aktiviteter: slutanvändaren utlöser dessa aktiviteter när de försöker ansluta till en stationär eller RemoteApp via Microsoft Fjärrskrivbord-program.
* Hanterings aktiviteter: administratören utlöser dessa aktiviteter när de utför hanterings åtgärder i systemet, till exempel att skapa värdar, tilldela användare till app-grupper och skapa roll tilldelningar.

Anslutningar som inte når Windows Virtual Desktop visas inte i diagnostiska resultat, eftersom själva roll tjänsten för diagnostik är en del av det virtuella Windows-skrivbordet. Problem med Windows anslutning till virtuella skriv bord kan inträffa när slutanvändaren har problem med nätverks anslutningen.

Kom igång genom att [Hämta och importera Windows Virtual Desktop PowerShell-modulen](/powershell/windows-virtual-desktop/overview/) som ska användas i PowerShell-sessionen om du inte redan gjort det. Sedan kör du följande cmdlet för att logga in på ditt konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="diagnose-issues-with-powershell"></a>Diagnostisera problem med PowerShell

Windows Virtual Desktop Diagnostics använder bara en PowerShell-cmdlet men innehåller många valfria parametrar som hjälper dig att begränsa och isolera problem. I följande avsnitt listas de cmdlets som du kan köra för att diagnosticera problem. De flesta filter kan appliceras tillsammans. Värden som anges inom hakparenteser, till exempel `<tenantName>` , ska ersättas med de värden som gäller för din situation.

>[!IMPORTANT]
>Funktionen diagnostik är för fel sökning av enskilda användare. Alla frågor som använder PowerShell måste innehålla antingen parametrarna *-username* eller *-ActivityId* . Använd Log Analytics för att övervaka funktioner. Mer information om hur du skickar diagnostikdata till din arbets yta finns i [använda Log Analytics för Diagnostics-funktionen](diagnostics-log-analytics-2019.md) .

### <a name="filter-diagnostic-activities-by-user"></a>Filtrera diagnostiska aktiviteter per användare

Parametern **-username** returnerar en lista med diagnostiska aktiviteter som initierats av den angivna användaren, som du ser i följande exempel-cmdlet.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

Parametern **-username** kan också kombineras med andra valfria filtrerings parametrar.

### <a name="filter-diagnostic-activities-by-time"></a>Filtrera diagnostiska aktiviteter per tid

Du kan filtrera den returnerade listan med diagnostiska aktiviteter med parametrarna **-StartTime** och **-slut** tid. Parametern **-StartTime** returnerar en lista med diagnostiska aktiviteter som börjar från ett visst datum, som du ser i följande exempel.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018"
```

Parametern **-** timmarsperiod kan läggas till i en cmdlet med parametern **-StartTime** för att ange en viss tids period som du vill ta emot resultat för. Följande exempel-cmdlet kommer att returnera en lista över diagnostiska aktiviteter från mellan 1 augusti och 10 augusti.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

Parametrarna **-StartTime** och **-slut** tid kan också kombineras med andra valfria filtrerings parametrar.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Filtrera diagnostiska aktiviteter efter aktivitets typ

Du kan också filtrera diagnostiska aktiviteter efter aktivitets typ med **-activityType-** parametern. Följande cmdlet kommer att returnera en lista över slut användar anslutningar:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -ActivityType Connection
```

Följande cmdlet kommer att returnera en lista över administratörs hanterings aktiviteter:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

**Get-RdsDiagnosticActivities** -cmdlet har för närvarande inte stöd för att ange feed som activityType.

### <a name="filter-diagnostic-activities-by-outcome"></a>Filtrera diagnostiska aktiviteter efter resultat

Du kan filtrera listan över returnerad diagnostisk aktivitet genom att följa resultatet med **-parametern-resultat** . Följande exempel-cmdlet kommer att returnera en lista över lyckade diagnostiska aktiviteter.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -Outcome Success
```

Följande exempel-cmdlet kommer att returnera en lista över misslyckade diagnostiska aktiviteter.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

Parametern **-resultat** kan också kombineras med andra valfria filtrerings parametrar.

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Hämta en speciell diagnostisk aktivitet per aktivitets-ID

Parametern **-ActivityId** returnerar en speciell diagnostisk aktivitet om den finns, som du ser i följande exempel-cmdlet.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>Visa fel meddelanden för en misslyckad aktivitet per aktivitets-ID

Om du vill visa fel meddelandena för en misslyckad aktivitet måste du köra cmdleten med den **-detaljerade** parametern. Du kan visa listan med fel genom att köra cmdleten **Select-Object** .

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="retrieve-detailed-diagnostic-activities"></a>Hämta detaljerade diagnostiska aktiviteter

Den **-detaljerade** parametern ger ytterligare information om varje diagnostisk aktivitet som returneras. Formatet för varje aktivitet varierar beroende på typen av aktivitet. Den **-detaljerade** parametern kan läggas till i en **Get-RdsDiagnosticActivities** -fråga, som visas i följande exempel.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityGuid> -Detailed
```

## <a name="common-error-scenarios"></a>Vanliga felscenarier

Fel scenarier kategoriseras internt till tjänsten och externa till Windows Virtual Desktop.

* Internt problem: anger scenarier som inte kan begränsas av klient administratören och som måste lösas som ett support ärende. När du ger feedback via [Tech-communityn för Windows Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop), inkluderar du aktivitets-ID och ungefärlig tids ram för när problemet uppstod.
* Externt problem: relatera till scenarier som kan begränsas av system administratören. Dessa är externa för virtuella Windows-datorer.

I följande tabell visas vanliga fel som dina administratörer kan köra i.

>[!NOTE]
>Den här listan innehåller de vanligaste felen och uppdateras i en vanlig takt. Se till att du har den senaste informationen genom att gå tillbaka till den här artikeln minst en gång i månaden.

### <a name="external-management-error-codes"></a>Fel koder för externa hantering

|Numerisk kod|Felkod|Föreslagen lösning|
|---|---|---|
|1322|ConnectionFailedNoMappingOfSIDinAD|Användaren är inte medlem i Azure Active Directory. Följ instruktionerna i [Active Directory Administrationscenter](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center) för att lägga till dem.|
|3|UnauthorizedAccess|Användaren som försökte köra den administrativa PowerShell-cmdleten har antingen inte behörighet att göra det eller felaktigt angett sitt användar namn.|
|1000|TenantNotFound|Klient namnet du angav matchar inte några befintliga klienter. Granska klient namnet för skrivfel och försök igen.|
|1006|TenantCannotBeRemovedHasSessionHostPools|Du kan inte ta bort en klient så länge den innehåller objekt. Ta bort värdarna för serversessionen först och försök sedan igen.|
|2000|HostPoolNotFound|Det värdnamn du angav matchar inte några befintliga värdar. Granska namnet på värddatorn för skrivfel och försök igen.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|Du kan inte ta bort en adresspool så länge den innehåller objekt. Ta först bort alla app-grupper i poolen.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|Ta bort alla sessioner innan du tar bort värd för sessionen.|
|5001|SessionHostNotFound|Den session värd som du har frågat om kan vara offline. Kontrol lera poolinställningarna status.|
|5008|SessionHostUserSessionsExist |Du måste logga ut alla användare på sessionsvärdservern innan du kan köra din avsedda hanterings aktivitet.|
|6000|AppGroupNotFound|Det angivna grupp namnet för appen matchar inte några befintliga app-grupper. Granska appens grupp namn och försök igen.|
|6022|RemoteAppNotFound|Det RemoteApp-namn som du angav matchar inte några RemoteApp-objekt. Granska RemoteApp-namnet för skrivfel och försök igen.|
|6010|PublishedItemsExist|Namnet på den resurs som du försöker publicera är detsamma som en resurs som redan finns. Ändra resurs namnet och försök igen.|
|7002|NameNotValidWhiteSpace|Använd inte blank steg i namnet.|
|8000|InvalidAuthorizationRoleScope|Roll namnet du angav matchar inte några befintliga rollnamn. Granska roll namnet för skrivfel och försök igen. |
|8001|UserNotFound |Det användar namn som du angav matchar inte några befintliga användar namn. Granska namnet på skrivfel och försök igen.|
|8005|UserNotFoundInAAD |Det användar namn som du angav matchar inte några befintliga användar namn. Granska namnet på skrivfel och försök igen.|
|8008|TenantConsentRequired|Följ anvisningarna [här](tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) för att ge din klient tillåtelse.|

### <a name="external-connection-error-codes"></a>Fel koder för extern anslutning

|Numerisk kod|Felkod|Föreslagen lösning|
|---|---|---|
|– 2147467259|ConnectionFailedAdErrorNoSuchMember|Användaren är inte medlem i Active Directory. Följ instruktionerna i [Active Directory Administrationscenter](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center) för att lägga till dem.|
|– 2147467259|ConnectionFailedAdTrustedRelationshipFailure|Sessions värden är inte korrekt ansluten till Active Directory.|
|– 2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|Anslutningarna misslyckades eftersom sessions värden inte är tillgänglig. Kontrol lera hälso tillståndet för sessionens värd.|
|– 2146233088|ConnectionFailedClientDisconnect|Om det här felet ofta visas kontrollerar du att användarens dator är ansluten till nätverket.|
|– 2146233088|ConnectionFailedNoHealthyRdshAvailable|Sessionen som värd användaren försökte ansluta till är inte felfri. Felsök den virtuella datorn.|
|– 2146233088|ConnectionFailedUserNotAuthorized|Användaren har inte behörighet att komma åt den publicerade appen eller Skriv bordet. Felet kan visas efter att administratören har tagit bort publicerade resurser. Be användaren att uppdatera feeden i programmet för fjärr skrivbord.|
|2|FileNotFound|Programmet som användaren försökte komma åt är antingen felaktigt installerat eller har angetts till en felaktig sökväg.|
|3|InvalidCredentials|Användar namnet eller lösen ordet som användaren angav stämmer inte överens med befintliga användar namn eller lösen ord. Granska autentiseringsuppgifterna för skrivfel och försök igen.|
|8|ConnectionBroken|Anslutningen mellan klienten och gatewayen eller servern släpptes. Ingen åtgärd krävs om den inte sker utan förvarning.|
|14|UnexpectedNetworkDisconnect|Anslutningen till nätverket har släppts. Be användaren att ansluta igen.|
|24|ReverseConnectFailed|Den virtuella datorns värd har ingen direkt rad information till RD Gateway. Se till att IP-adressen för gatewayen kan matchas.|
|1322|ConnectionFailedNoMappingOfSIDinAD|Användaren är inte medlem i Active Directory. Följ instruktionerna i [Active Directory Administrationscenter](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center) för att lägga till dem.|

## <a name="next-steps"></a>Nästa steg

Mer information om roller i Windows Virtual Desktop finns i [Windows Virtual Desktop-miljö](environment-setup-2019.md).

Om du vill se en lista över tillgängliga PowerShell-cmdletar för virtuella Windows-datorer, se [PowerShell-referensen](/powershell/windows-virtual-desktop/overview).
