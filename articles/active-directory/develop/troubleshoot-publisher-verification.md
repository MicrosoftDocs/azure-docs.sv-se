---
title: Felsöka utgivarens verifiering | Azure
titleSuffix: Microsoft identity platform
description: 'Beskriver hur du felsöker Publisher-verifiering för Microsoft Identity Platform genom att anropa Microsoft Graph API: er.'
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 10609f2706d257dbe5d8f43b85da5f06cb986cae
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756191"
---
# <a name="troubleshoot-publisher-verification"></a>Felsöka verifiering av utgivare
Om du inte kan slutföra processen eller har oväntad funktion med [utgivar verifieringen](publisher-verification-overview.md), bör du börja med att göra följande om du får fel eller om du får oväntade beteenden: 

1. Granska [kraven](publisher-verification-overview.md#requirements) och se till att alla är uppfyllda.

1. Läs anvisningarna för att [Markera en app som utgivare verifierad](mark-app-as-publisher-verified.md) och se till att alla steg har utförts.

1. Granska listan med [vanliga problem](#common-issues).

1. Återskapa begäran med hjälp av [Graph Explorer](#making-microsoft-graph-api-calls) för att samla in ytterligare information och utesluta eventuella problem i användar gränssnittet.

## <a name="common-issues"></a>Vanliga problem
Nedan visas några vanliga problem som kan uppstå under processen. 

- **Jag känner inte till mitt Microsoft Partner Network-ID (MPN-ID) eller jag vet inte vem den primära kontakten för kontot är** 
    1. Gå till [sidan för MPN-registrering](https://partner.microsoft.com/dashboard/account/v3/enrollment/joinnow/basicpartnernetwork/new)
    1. Logga in med ett användar konto i organisationens primära Azure AD-klient 
    1. Om det redan finns ett MPN-konto kommer detta att identifieras och du kommer att läggas till i kontot 
    1. Gå till [sidan partner profil](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) där MPN-ID och primär konto kontakt visas

- **Jag vet inte som global administratör för Azure AD (även kallat företags administratör eller klient organisations administratör), hur hittar jag dem? Vad gäller för program administratören eller moln program administratören?**
    1. Logga in på [Azure AD-portalen](https://aad.portal.azure.com) med ett användar konto i din organisations primära klient organisation
    1. Navigera till [roll hantering](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)
    1. Klicka på önskad administratörs roll
    1. Listan över användare som har tilldelats rollen kommer att visas

- **Jag vet inte vem som är administratör (er) för mitt MPN-konto** Gå till [sidan för användar hantering av MPN](https://partner.microsoft.com/pcv/users) och filtrera användar listan för att se vilka användare som finns i olika administratörs roller.

- **Jag får ett fel meddelande om att mitt MPN-ID är ogiltigt eller att jag inte har åtkomst till den.**
    1. Gå till din [partner profil](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) och kontrol lera att: 
        - MPN-ID: t är korrekt. 
        - Det finns inga fel eller "väntande åtgärder" visas och verifierings statusen under juridisk företags profil och partner information anger både "auktoriserad" eller "lyckad".
    1. Gå till [sidan för MPN-klient hantering](https://partner.microsoft.com/dashboard/account/v3/tenantmanagement) och bekräfta att klienten som appen är registrerad i och att du loggar med ett användar konto från finns i listan över associerade klienter. Om du behöver lägga till ytterligare en klient, följer du anvisningarna [här](/partner-center/multi-tenant-account). Observera att alla globala administratörer för alla klient organisationer som du lägger till beviljas globala administratörs behörigheter på ditt partner Center-konto.
    1. Gå till [sidan för användar hantering av MPN](https://partner.microsoft.com/pcv/users) och bekräfta att användaren som du loggar in som är antingen en global administratör, MPN-administratör eller konto administratör. Om du behöver lägga till en användare till en roll i Partner Center följer du instruktionerna [här](/partner-center/create-user-accounts-and-set-permissions).

- **När jag loggar in på Azure AD-portalen ser jag inga appar registrerade. Varför?** 
    Dina app-registreringar kan ha skapats med ett annat användar konto i den här klienten, ett personligt/konsument konto eller en annan klient. Se till att du är inloggad med rätt konto i klient organisationen där dina app-registreringar skapades.

- **Jag får ett fel som rör Multi-Factor Authentication. Vad ska jag göra?** 
    Kontrol lera att [Multi-Factor Authentication](../fundamentals/concept-fundamentals-mfa-get-started.md) har Aktiver ATS och **krävs** för den användare som du loggar in med och för det här scenariot. MFA kan till exempel vara:
    - Krävs alltid för den användare som du loggar in med
    - [Krävs för Azure-hantering](../conditional-access/howto-conditional-access-policy-azure-management.md).
    - [Krävs för den typ av administratör](../conditional-access/howto-conditional-access-policy-admin-mfa.md) som du loggar in med.

## <a name="making-microsoft-graph-api-calls"></a>Göra Microsoft Graph API-anrop 

Om du har problem men inte kan förstå varför baserat på vad du ser i användar gränssnittet, kan det vara bra att utföra ytterligare fel sökning genom att använda Microsoft Graph anrop för att utföra samma åtgärder som du kan utföra i appens registrerings Portal.

Det enklaste sättet att göra dessa förfrågningar med hjälp av [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). Du kan också överväga andra alternativ som att använda [Postman](https://www.postman.com/)eller använda PowerShell för att [anropa en webbegäran](/powershell/module/microsoft.powershell.utility/invoke-webrequest).  

Du kan använda Microsoft Graph för att både ange och inaktivera appens verifierade utgivare och kontrol lera resultatet när du har utfört någon av dessa åtgärder. Resultatet kan visas [både för programobjektet](/graph/api/resources/application) som motsvarar din app-registrering och alla [tjänst huvud namn](/graph/api/resources/serviceprincipal) som har instansierats från appen. Mer information om relationen mellan dessa objekt finns i: [program-och tjänst huvud objekt i Azure Active Directory](app-objects-and-service-principals.md).  

Här följer några exempel på några användbara förfrågningar:  

### <a name="set-verified-publisher"></a>Ange verifierad utgivare 

Förfrågan

```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/setVerifiedPublisher 

{ 

    "verifiedPublisherId": "12345678" 

} 
```
 
Svarsåtgärder 
```
204 No Content 
```
> [!NOTE]
> *verifiedPublisherID* är ditt MPN-ID. 

### <a name="unset-verified-publisher"></a>Unset verifierad utgivare 

Begäran:  
```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/unsetVerifiedPublisher 
```
 
Svarsåtgärder 
```
204 No Content 
```
### <a name="get-verified-publisher-info-from-application"></a>Hämta en verifierad utgivar information från programmet 
 
```
GET https://graph.microsoft.com/v1.0/applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec 

HTTP/1.1 200 OK 

{ 
    "id": "0cd04273-0d11-4e62-9eb3-5c3971a7cbec", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

### <a name="get-verified-publisher-info-from-service-principal"></a>Hämta en verifierad utgivar information från tjänstens huvud namn 
```
GET https://graph.microsoft.com/v1.0/servicePrincipals/010422a7-4d77-4f40-9335-b81ef5c23dd4 

HTTP/1.1 200 OK 

{ 
    "id": "010422a7-4d77-4f40-9335-b81ef5c22dd4", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

## <a name="error-reference"></a>Fel referens 

Följande är en lista med de möjliga fel koder som du kan få, antingen när du felsöker med Microsoft Graph eller går igenom processen i registrerings portalen för appen.

### <a name="mpnaccountnotfoundornoaccess"></a>MPNAccountNotFoundOrNoAccess     

Det MPN-ID som du angav ( <MPNID> ) finns inte eller så har du inte åtkomst till det. Ange ett giltigt MPN-ID och försök igen.
    
Det som oftast orsakas av att den inloggade användaren inte är medlem i den korrekta rollen för MPN-kontot i Partner Center, se [krav](publisher-verification-overview.md#requirements) för en lista över giltiga roller och se [vanliga problem](#common-issues) för mer information. Kan också orsakas av klienten som appen är registrerad i och som inte läggs till i MPN-kontot eller ett ogiltigt MPN-ID.

### <a name="mpnglobalaccountnotfound"></a>MPNGlobalAccountNotFound     

Det MPN-ID som du angav ( <MPNID> ) är inte giltigt. Ange ett giltigt MPN-ID och försök igen.
    
Det vanligaste inträffar när ett MPN-ID anges som motsvarar ett partner plats konto (PLA). Endast partner globala konton stöds. Mer information finns i [konto strukturen för partner Center](/partner-center/account-structure) .

### <a name="mpnaccountinvalid"></a>MPNAccountInvalid    

Det MPN-ID som du angav ( <MPNID> ) är inte giltigt. Ange ett giltigt MPN-ID och försök igen.
    
Det beror oftast på att fel MPN-ID har angetts.

### <a name="mpnaccountnotvetted"></a>MPNAccountNotVetted  

MPN-ID ( <MPNID> ) som du angav har inte slutfört först konsumentsajter-processen. Slutför den här processen i Partner Center och försök igen. 
    
Vanligt vis orsakade av när MPN-kontot inte har slutfört [verifierings](/partner-center/verification-responses) processen.

### <a name="nopublisheridonassociatedmpnaccount"></a>NoPublisherIdOnAssociatedMPNAccount  

Det MPN-ID som du angav ( <MPNID> ) är inte giltigt. Ange ett giltigt MPN-ID och försök igen. 
   
Det beror oftast på att fel MPN-ID har angetts.

### <a name="mpniddoesnotmatchassociatedmpnaccount"></a>MPNIdDoesNotMatchAssociatedMPNAccount    

Det MPN-ID som du angav ( <MPNID> ) är inte giltigt. Ange ett giltigt MPN-ID och försök igen.
    
Det beror oftast på att fel MPN-ID har angetts.

### <a name="applicationnotfound"></a>ApplicationNotFound  

Det går inte att hitta mål programmet ( <AppId> ). Ange ett giltigt program-ID och försök igen.
    
Vanligt vis orsakade att verifiering utförs via Graph API och ID: t för det angivna programmet är felaktigt. Obs!-ID: t för programmet måste anges, inte AppId/ClientId.

### <a name="b2ctenantnotallowed"></a>B2CTenantNotAllowed  

Den här funktionen stöds inte i en Azure AD B2C klient. 

### <a name="emailverifiedtenantnotallowed"></a>EmailVerifiedTenantNotAllowed    

Den här funktionen stöds inte i en verifierad e-postklient. 

### <a name="nopublisherdomainonapplication"></a>NoPublisherDomainOnApplication   

Mål programmet ( \<AppId\> ) måste ha en utgivares domän uppsättning. Ange en utgivar domän och försök igen.

Inträffar när en [publicerings domän](howto-configure-publisher-domain.md) inte har kon figurer ATS i appen.

### <a name="publisherdomainmismatch"></a>PublisherDomainMismatch  

Mål programmets utgivare domän ( <publisherDomain> ) matchar inte domänen som används för att utföra e-postverifiering i Partner Center ( <pcDomain> ). Se till att domänerna matchar och försök igen. 
    
Inträffar när varken appens [utgivare domän](howto-configure-publisher-domain.md) eller någon av de [anpassade domänerna](../fundamentals/add-custom-domain.md) som läggs till i Azure AD-klienten matchar den domän som används för att utföra e-postverifiering i Partner Center.

### <a name="notauthorizedtoverifypublisher"></a>NotAuthorizedToVerifyPublisher   

Du har inte behörighet att ange egenskapen verifierad utgivare för programmet ( <AppId> ) 
  
Det som vanligt vis orsakas av att den inloggade användaren inte är medlem i den rätta rollen för MPN-kontot i Azure AD, se [krav](publisher-verification-overview.md#requirements) för en lista över giltiga roller och se [vanliga problem](#common-issues) för mer information.

### <a name="mpnidwasnotprovided"></a>MPNIdWasNotProvided  

MPN-ID: t angavs inte i begär ande texten eller så var innehålls typen för begäran inte "Application/JSON". 

### <a name="msanotsupported"></a>MSANotSupported  

Den här funktionen stöds inte för Microsoft-konsument konton. Endast program som är registrerade i Azure AD av en Azure AD-användare stöds.

### <a name="interactionrequired"></a>InteractionRequired

Inträffar när Multi-Factor Authentication inte har utförts innan du försöker lägga till en verifierad utgivare i appen. Se [vanliga problem](#common-issues) för mer information. Obs: MFA måste utföras i samma session när du försöker lägga till en verifierad utgivare. Om MFA är aktiverat men inte krävs för att utföras i sessionen, kommer begäran att Miss förväntas.   

Det fel meddelande som visas är: "på grund av en konfigurations ändring som gjorts av administratören eller på grund av att du har flyttat till en ny plats måste du använda Multi-Factor Authentication för att fortsätta."

## <a name="next-steps"></a>Nästa steg

Om du har granskat all föregående information och fortfarande får ett fel från Microsoft Graph, kan du samla in så mycket av följande information som möjligt som är relaterad till den misslyckade begäran och [kontakta Microsoft Support](developer-support-help-options.md#open-a-support-request).

- Timestamp 
- CorrelationId 
- ObjectID eller UserPrincipalName för inloggad användare 
- ObjectId för mål programmet
- AppId för mål programmet
- TenantId där appen har registrerats
- MPN-ID
- REST-begäran görs 
- Felkod och meddelande som returneras