---
title: Så här fungerar synkronisering i Azure AD Domain Services | Microsoft Docs
description: Lär dig hur synkroniseringsprocessen fungerar för objekt och autentiseringsuppgifter från en Azure AD-klient eller en lokal Active Directory Domain Services miljö till en Azure Active Directory Domain Services hanterad domän.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 41ba337765b4a0a93be52f08ae6656707cf7aa73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618815"
---
# <a name="how-objects-and-credentials-are-synchronized-in-an-azure-active-directory-domain-services-managed-domain"></a>Hur objekt och autentiseringsuppgifter synkroniseras i en Azure Active Directory Domain Services hanterad domän

Objekt och autentiseringsuppgifter i en Azure Active Directory Domain Services (Azure AD DS)-hanterad domän kan antingen skapas lokalt i domänen eller synkroniseras från en Azure Active Directory (Azure AD)-klient. När du först distribuerar Azure AD DS konfigureras en automatisk enkelriktad synkronisering och startas för att replikera objekten från Azure AD. Den här enkelriktade synkroniseringen fortsätter att köras i bakgrunden för att hålla den hanterade Azure AD DS-domänen uppdaterad med eventuella ändringar från Azure AD. Ingen synkronisering sker från Azure AD DS tillbaka till Azure AD.

I en hybrid miljö kan objekt och autentiseringsuppgifter från en lokal AD DS-domän synkroniseras till Azure AD med hjälp av Azure AD Connect. När objekten har synkroniserats till Azure AD, gör den automatiska bakgrundssynkroniseringen att dessa objekt och autentiseringsuppgifter är tillgängliga för program som använder den hanterade domänen.

Om lokal AD DS och Azure AD har kon figurer ATS för federerad autentisering med hjälp av ADFS, finns det inget (aktuellt/giltigt) lösen ords-hash tillgängligt i Azure DS. Azure AD-användarkonton som skapats innan den ursprungliga autentiseringen har implementerats kan ha en gammal hash för lösen ord men det är troligt vis inte en hash av sitt lokal lösen ord. Därför kommer Azure AD DS inte att kunna verifiera användarens autentiseringsuppgifter.

Följande diagram illustrerar hur synkroniseringen fungerar mellan Azure AD DS, Azure AD och en valfri lokal AD DS-miljö:

![Översikt över synkronisering för en Azure AD Domain Services hanterad domän](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-azure-ad-to-azure-ad-ds"></a>Synkronisering från Azure AD till Azure AD DS

Användar konton, grupp medlemskap och autentiseringsuppgifter för autentisering synkroniseras ett sätt från Azure AD till Azure AD DS. Den här synkroniseringsprocessen är automatisk. Du behöver inte konfigurera, övervaka eller hantera den här synkroniseringsprocessen. Den inledande synkroniseringen kan ta några timmar till några dagar, beroende på antalet objekt i Azure AD-katalogen. När den första synkroniseringen är klar synkroniseras ändringar som görs i Azure AD, till exempel lösen ord eller attributändringar, automatiskt till Azure AD DS.

När en användare skapas i Azure AD synkroniseras de inte till Azure AD DS förrän de ändrar sitt lösen ord i Azure AD. Den här processen för lösen ords ändring gör att lösen ords hashar för Kerberos-och NTLM-autentisering skapas och lagras i Azure AD. Lösen ords-hasharna krävs för att kunna autentisera en användare i Azure AD DS.

Synkroniseringsprocessen är enkelriktad/enkelriktad genom design. Det finns ingen omvänd synkronisering av ändringar från Azure AD DS tillbaka till Azure AD. En hanterad domän är i stort sett skrivskyddad, förutom anpassade organisationsenheter som du kan skapa. Du kan inte göra ändringar i användarattribut, användar lösen ord eller grupp medlemskap i en hanterad domän.

## <a name="attribute-synchronization-and-mapping-to-azure-ad-ds"></a>Synkronisera attribut och mappa till Azure AD DS

I följande tabell visas några vanliga attribut och hur de synkroniseras till Azure AD DS.

| Attribut i Azure AD DS | Källa | Kommentarer |
|:--- |:--- |:--- |
| UPN | Användarens *UPN* -attribut i Azure AD-klienten | UPN-attributet från Azure AD-klienten synkroniseras med Azure AD DS. Det mest pålitliga sättet att logga in på en hanterad domän är att använda UPN. |
| Sam | Användarens *smek namn* -attribut i Azure AD-klient eller automatiskt genererad | Attributet *sAMAccountName* hämtas från attributet *smek namn* i Azure AD-klienten. Om flera användar konton har samma *startalias* -attribut skapas *sAMAccountName* automatiskt. Om användarens *smek namn* eller *UPN* -prefix är längre än 20 tecken genereras *sAMAccountName* automatiskt för att uppfylla gränsen på 20 tecken i *sAMAccountName* -attribut. |
| Lösenord | Användarens lösen ord från Azure AD-klienten | Äldre hashvärden för lösen ord krävs för NTLM-eller Kerberos-autentiseringen synkroniseras från Azure AD-klienten. Om Azure AD-klienten har kon figurer ATS för Hybrid synkronisering med Azure AD Connect, kommer dessa lösen ords hashs från den lokala AD DS-miljön. |
| Primär användare/grupp-SID | Automatiskt skapade texter | Huvud-SID för användare/grupp-konton skapas automatiskt i Azure AD DS. Det här attributet matchar inte det primära användare/grupp-SID för objektet i en lokal AD DS-miljö. Detta matchnings fel beror på att den hanterade domänen har ett annat SID-namnområde än den lokala AD DS-domänen. |
| SID-historik för användare och grupper | Lokal primär användare och grupp-SID | Attributet *SidHistory* för användare och grupper i Azure AD DS är inställt på att matcha motsvarande primära användare eller grupp-sid i en lokal AD DS-miljö. Den här funktionen gör det lättare att lyfta och byta lokala program till Azure AD DS eftersom du inte behöver göra en ny ACL-resurs. |

> [!TIP]
> **Logga in på den hanterade domänen med UPN-formatet** Attributet *sAMAccountName* , till exempel `AADDSCONTOSO\driley` , kan genereras automatiskt för vissa användar konton i en hanterad domän. Användarens automatiskt genererade *sAMAccountName* kan skilja sig från sina UPN-prefix, så det är inte alltid ett tillförlitligt sätt att logga in.
>
> Om t. ex. flera användare har samma *smek namn* -attribut eller om användarna har över långa UPN-prefix, kan *sAMAccountName* för dessa användare genereras automatiskt. Använd UPN-formatet, till exempel `driley@aaddscontoso.com` , för att logga in på en hanterad domän på ett tillförlitligt sätt.

### <a name="attribute-mapping-for-user-accounts"></a>Mappning av attribut för användar konton

I följande tabell visas hur särskilda attribut för användar objekt i Azure AD synkroniseras med motsvarande attribut i Azure AD DS.

| Användarattribut i Azure AD | Användarattribut i Azure AD DS |
|:--- |:--- |
| accountEnabled |userAccountControl (anger eller tar bort ACCOUNT_DISABLED bitar) |
| city |l |
| land |co |
| avdelning |avdelning |
| displayName |displayName |
| employeedId |Anställnings |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| förnamn |förnamn |
| jobTitle |title |
| e-post |e-post |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (kan ibland skapas automatiskt) |
| manager |manager |
| mobil |mobil |
| objectID |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl (anger eller tar bort DONT_EXPIRE_PASSWORD bitar) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| proxyAddresses | proxyAddresses |
| state |st |
| streetAddress |streetAddress |
| surname |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Attributmappning för grupper

I följande tabell visas hur särskilda attribut för grupp objekt i Azure AD synkroniseras med motsvarande attribut i Azure AD DS.

| Gruppattribut i Azure AD | Gruppattribut i Azure AD DS |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (kan ibland skapas automatiskt) |
| e-post |e-post |
| mailNickname |msDS-AzureADMailNickname |
| objectID |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| proxyAddresses | proxyAddresses |
| securityEnabled |grupptyp |

## <a name="synchronization-from-on-premises-ad-ds-to-azure-ad-and-azure-ad-ds"></a>Synkronisering från lokala AD DS till Azure AD och Azure AD DS

Azure AD Connect används för att synkronisera användar konton, grupp medlemskap och hash-autentiseringsuppgifter från en lokal AD DS-miljö till Azure AD. Attribut för användar konton som UPN och lokal säkerhets identifierare (SID) är synkroniserade. För att logga in med Azure AD DS, synkroniseras äldre lösen ords-hashar för NTLM och Kerberos-autentisering även till Azure AD.

> [!IMPORTANT]
> Azure AD Connect bör endast installeras och konfigureras för synkronisering med lokala AD DS-miljöer. Det finns inte stöd för att installera Azure AD Connect i en hanterad domän för att synkronisera objekt tillbaka till Azure AD.

Om du konfigurerar Skriv tillbaka, synkroniseras ändringar från Azure AD tillbaka till den lokala AD DS-miljön. Om en användare till exempel ändrar sitt lösen ord med hjälp av lösen ords hantering i Azure AD, uppdateras lösen ordet igen i den lokala AD DS-miljön.

> [!NOTE]
> Använd alltid den senaste versionen av Azure AD Connect för att se till att du har korrigeringar för alla kända buggar.

### <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Synkronisering från en lokal miljö med flera skogar

Många organisationer har en ganska komplex lokal AD DS-miljö som innehåller flera skogar. Azure AD Connect stöder synkronisering av användare, grupper och inloggnings-hashar från miljöer med flera skogar till Azure AD.

Azure AD har ett mycket enklare och ett platt namn område. Om du vill ge användare tillförlitlig åtkomst till program som skyddas av Azure AD löser du UPN-konflikter mellan användar konton i olika skogar. Hanterade domäner använder en ORGANISATIONSENHETs struktur som liknar Azure AD. Alla användar konton och grupper lagras i behållaren *AADDC Users* , trots att de synkroniseras från olika lokala domäner eller skogar, även om du har konfigurerat en hierarkisk ou-struktur lokalt. Den hanterade domänen fören klar alla hierarkiska OU-strukturer.

Som tidigare beskrivs finns det ingen synkronisering från Azure AD DS tillbaka till Azure AD. Du kan [skapa en anpassad organisationsenhet (OU)](create-ou.md) i Azure AD DS och sedan användare, grupper eller tjänst konton inom dessa anpassade organisationsenheter. Inget av de objekt som har skapats i anpassade organisationsenheter synkroniseras tillbaka till Azure AD. Dessa objekt är bara tillgängliga i den hanterade domänen och är inte synliga med Azure AD PowerShell-cmdletar, Microsoft Graph API eller med Azure AD management UI.

## <a name="what-isnt-synchronized-to-azure-ad-ds"></a>Vad är inte synkroniserat med Azure AD DS

Följande objekt eller attribut synkroniseras inte från en lokal AD DS-miljö till Azure AD eller Azure AD DS:

* **Exkluderade attribut:** Du kan välja att undanta vissa attribut från synkronisering till Azure AD från en lokal AD DS-miljö med hjälp av Azure AD Connect. De uteslutna attributen är inte tillgängliga i Azure AD DS.
* **Grup principer:** Grup principer som kon figurer ATS i en lokal AD DS-miljö synkroniseras inte med Azure AD DS.
* **SYSVOL-mapp:** Innehållet i *SYSVOL* -mappen i en lokal AD DS-miljö synkroniseras inte med Azure AD DS.
* **Dator objekt:** Dator objekt för datorer som är anslutna till en lokal AD DS-miljö synkroniseras inte med Azure AD DS. De här datorerna har inte någon förtroende relation med den hanterade domänen och tillhör bara den lokala AD DS-miljön. I Azure AD DS visas endast dator objekt för datorer som uttryckligen är domänanslutna till den hanterade domänen.
* **SidHistory-attribut för användare och grupper:** Primär användare och primär grupp sid från en lokal AD DS-miljö synkroniseras med Azure AD DS. Befintliga *SidHistory* -attribut för användare och grupper synkroniseras dock inte från den lokala AD DS-miljön till Azure AD DS.
* **Organisations enheter (OU):** Organisationsenheter som definieras i en lokal AD DS-miljö synkroniseras inte med Azure AD DS. Det finns två inbyggda organisationsenheter i Azure AD DS – en för användare och en för datorer. Den hanterade domänen har en strukturerad OU-struktur. Du kan välja att [skapa en anpassad organisationsenhet i din hanterade domän](create-ou.md).

## <a name="password-hash-synchronization-and-security-considerations"></a>Lösenordssynkronisering och säkerhets aspekter

När du aktiverar Azure AD DS krävs äldre hashvärden för NTLM + Kerberos-autentisering. Azure AD lagrar inte lösen ord i klartext, så dessa hash-värden kan inte genereras automatiskt för befintliga användar konton. När NTLM och Kerberos-kompatibla hash-värden för lösen ord har genererats och lagrats lagras de alltid på ett krypterat sätt i Azure AD.

Krypterings nycklarna är unika för varje Azure AD-klient. Dessa hash-värden krypteras så att endast Azure AD DS har åtkomst till krypterings nycklarna. Ingen annan tjänst eller komponent i Azure AD har åtkomst till krypterings nycklarna.

Äldre hashvärden för lösen ord synkroniseras sedan från Azure AD till domän kontrol Lanterna för en hanterad domän. Diskarna för de här hanterade domän kontrol Lanterna i Azure AD DS är krypterade i vila. Dessa lösen ords-hashar lagras och skyddas på dessa domänkontrollanter på liknande sätt som lösen ord lagras och skyddas i en lokal AD DS-miljö.

För endast molnbaserade Azure AD-miljöer [måste användarna återställa/ändra sitt lösen ord](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) för att nödvändiga lösen ords-hashar ska genereras och lagras i Azure AD. För alla moln användar konton som skapats i Azure AD när du har aktiverat Azure AD Domain Services genereras lösen ords hasharna och lagras i de NTLM-och Kerberos-kompatibla formaten. Alla moln användar konton måste ändra sina lösen ord innan de synkroniseras till Azure AD DS.

För Hybrid användar konton som synkroniseras från den lokala AD DS-miljön med hjälp av Azure AD Connect måste du [konfigurera Azure AD Connect för att synkronisera lösen ords-hashar i de NTLM-och Kerberos-kompatibla formaten](tutorial-configure-password-hash-sync.md).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du synkroniserar Lösenordssynkronisering finns i så här [fungerar hash-synkronisering av lösen ord med Azure AD Connect](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md?context=/azure/active-directory-domain-services/context/azure-ad-ds-context).

[Skapa en hanterad domän](tutorial-create-instance.md)för att komma igång med Azure AD DS.
