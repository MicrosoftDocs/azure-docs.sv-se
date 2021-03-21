---
title: Attribut synkroniserade med Azure AD Connect | Microsoft Docs
description: Visar en lista över de attribut som synkroniseras till Azure Active Directory.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c2bb36e0-5205-454c-b9b6-f4990bcedf51
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ec05c4160c6502904644bf7035bda0bed66cc33
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94413198"
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Azure AD Connect-synkronisering: Attribut synkroniserade till Azure Active Directory
I det här avsnittet visas de attribut som synkroniseras med Azure AD Connect Sync.  
Attributen grupperas av den relaterade Azure AD-appen.

## <a name="attributes-to-synchronize"></a>Attribut som ska synkroniseras
En vanlig fråga är *vilken lista med minsta attribut som ska synkroniseras*. Standardvärdet och den rekommenderade metoden är att behålla standardattributen så att en fullständig GAL (global adress lista) kan skapas i molnet och för att få alla funktioner i Microsoft 365 arbets belastningar. I vissa fall finns det attribut som din organisation inte vill synkronisera till molnet eftersom dessa attribut innehåller känsliga personliga data, t. ex. i det här exemplet:  
![Felaktiga attribut](./media/reference-connect-sync-attributes-synchronized/badextensionattribute.png)

I det här fallet börjar du med listan med attribut i det här avsnittet och identifierar de attribut som skulle innehålla personliga data och inte kan synkroniseras. Avmarkera sedan attributen under installationen med hjälp av [filtrering av Azure AD-appar och-attribut](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering).

> [!WARNING]
> När du avmarkerar attribut bör du vara försiktig och bara avmarkera dessa attribut absolut inte möjligt att synkronisera. Om du avmarkerar andra attribut kan det påverka funktionerna negativt.
>
>

## <a name="microsoft-365-apps-for-enterprise"></a>Microsoft 365-appar för företag
| Attributnamn | User | Kommentar |
| --- |:---:| --- |
| accountEnabled |X |Definierar om ett konto är aktiverat. |
| nomenklatur |X | |
| displayName |X | |
| objekt-SID |X |mekanisk egenskap. AD-användarnamn som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| pwdLastSet |X |mekanisk egenskap. Används för att veta när redan utfärdade token ska inverifieras. Används av både hash-synkronisering av lösen ord, direkt autentisering och Federation. |
|samAccountName|X| |
| sourceAnchor |X |mekanisk egenskap. Oföränderlig identifierare som upprätthåller relationen mellan tillägg och Azure AD. |
| usageLocation |X |mekanisk egenskap. Användarens land/region. Används för licens tilldelning. |
| userPrincipalName |X |UPN är inloggnings-ID: t för användaren. Oftast samma som [mail]-värdet. |

## <a name="exchange-online"></a>Exchange Online
| Attributnamn | User | Kontakt | Group | Kommentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definierar om ett konto är aktiverat. |
| - |X |X | | |
| altRecipient |X | | |Kräver Azure AD Connect build 1.1.552.0 eller efter. |
| authOrig |X |X |X | |
| c |X |X | | |
| nomenklatur |X | |X | |
| co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| avdelning |X |X | | |
| beskrivning | | |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| förnamn |X |X | | |
| homePhone |X |X | | |
| information |X |X |X |Det här attributet används inte för närvarande för grupper. |
| Initialer |X |X | | |
| l |X |X | | |
| legacyExchangeDN |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| medlem | | |X | |
| mobil |X |X | | |
| msDS-HABSeniorityIndex |X |X |X | |
| msDS-PhoneticDisplayName |X |X |X | |
| msExchArchiveGUID |X | | | |
| msExchArchiveName |X | | | |
| msExchAssistantName |X |X | | |
| msExchAuditAdmin |X | | | |
| msExchAuditDelegate |X | | | |
| msExchAuditDelegateAdmin |X | | | |
| msExchAuditOwner |X | | | |
| msExchBlockedSendersHash |X |X | | |
| msExchBypassAudit |X | | | |
| msExchBypassModerationLink | | |X |Tillgängligt i Azure AD Connect version 1.1.524.0 |
| msExchCoManagedByLink | | |X | |
| msExchDelegateListLink |X | | | |
| msExchELCExpirySuspensionEnd |X | | | |
| msExchELCExpirySuspensionStart |X | | | |
| msExchELCMailboxFlags |X | | | |
| msExchEnableModeration |X | |X | |
| msExchExtensionCustomAttribute1 |X |X |X |Det här attributet används inte för närvarande av Exchange Online. |
| msExchExtensionCustomAttribute2 |X |X |X |Det här attributet används inte för närvarande av Exchange Online. |
| msExchExtensionCustomAttribute3 |X |X |X |Det här attributet används inte för närvarande av Exchange Online. |
| msExchExtensionCustomAttribute4 |X |X |X |Det här attributet används inte för närvarande av Exchange Online. |
| msExchExtensionCustomAttribute5 |X |X |X |Det här attributet används inte för närvarande av Exchange Online. |
| msExchHideFromAddressLists |X |X |X | |
| msExchImmutableID |X | | | |
| msExchLitigationHoldDate |X |X |X | |
| msExchLitigationHoldOwner |X |X |X | |
| msExchMailboxAuditEnable |X | | | |
| msExchMailboxAuditLogAgeLimit |X | | | |
| msExchMailboxGuid |X | | | |
| msExchModeratedByLink |X |X |X | |
| msExchModerationFlags |X |X |X | |
| msExchRecipientDisplayType |X |X |X | |
| msExchRecipientTypeDetails |X |X |X | |
| msExchRemoteRecipientType |X | | | |
| msExchRequireAuthToSendTo |X |X |X | |
| msExchResourceCapacity |X | | | |
| msExchResourceDisplay |X | | | |
| msExchResourceMetaData |X | | | |
| msExchResourceSearchProperties |X | | | |
| msExchRetentionComment |X |X |X | |
| msExchRetentionURL |X |X |X | |
| msExchSafeRecipientsHash |X |X | | |
| msExchSafeSendersHash |X |X | | |
| msExchSenderHintTranslations |X |X |X | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| msExchUserHoldPolicies |X | | | |
| msOrg-IsOrganizational | | |X | |
| objekt-SID |X | |X |mekanisk egenskap. AD-användarnamn som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherTelephone |X |X | | |
| personsökare |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| proxyAddresses |X |X |X | |
| publicDelegates |X |X |X | |
| pwdLastSet |X | | |mekanisk egenskap. Används för att veta när redan utfärdade token ska inverifieras. Används både för synkronisering av lösen ord och Federation. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| sn |X |X | | |
| sourceAnchor |X |X |X |mekanisk egenskap. Oföränderlig identifierare som upprätthåller relationen mellan tillägg och Azure AD. |
| st |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | |synkroniserades bara en gång från Azure AD till Exchange Online efter vilken Exchange Online blir källan till behörighet för det här attributet och senare ändringar inte kan synkroniseras från den lokala platsen. Se ([KB](https://support.microsoft.com/help/3062745/user-photos-aren-t-synced-from-the-on-premises-environment-to-exchange)) om du vill ha mer information.|
| title |X |X | | |
| unauthOrig |X |X |X | |
| usageLocation |X | | |mekanisk egenskap. Användarens land/region. Används för licens tilldelning. |
| userCertificate |X |X | | |
| userPrincipalName |X | | |UPN är inloggnings-ID: t för användaren. Oftast samma som [mail]-värdet. |
| userSMIMECertificates |X |X | | |
| wWWHomePage |X |X | | |

## <a name="sharepoint-online"></a>sharepoint online
| Attributnamn | User | Kontakt | Group | Kommentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definierar om ett konto är aktiverat. |
| authOrig |X |X |X | |
| c |X |X | | |
| nomenklatur |X | |X | |
| co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| avdelning |X |X | | |
| beskrivning |X |X |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| förnamn |X |X | | |
| hideDLMembership | | |X | |
| homephone |X |X | | |
| information |X |X |X | |
| initialer |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| e-post |X |X |X | |
| MailNickName |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| medlem | | |X | |
| middleName |X |X | | |
| mobil |X |X | | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointLinkedBy |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| objekt-SID |X | |X |mekanisk egenskap. AD-användarnamn som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherIpPhone |X |X | | |
| otherMobile |X |X | | |
| otherPager |X |X | | |
| otherTelephone |X |X | | |
| personsökare |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| postOfficeBox |X |X | |Det här attributet används inte för närvarande av SharePoint Online. |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mekanisk egenskap. Används för att veta när redan utfärdade token ska inverifieras. Används av både hash-synkronisering av lösen ord, direkt autentisering och Federation. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| sn |X |X | | |
| sourceAnchor |X |X |X |mekanisk egenskap. Oföränderlig identifierare som upprätthåller relationen mellan tillägg och Azure AD. |
| st |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | |synkroniserades bara en gång från Azure AD till Exchange Online efter vilken Exchange Online blir källan till behörighet för det här attributet och senare ändringar inte kan synkroniseras från den lokala platsen. Se ([KB](https://support.microsoft.com/help/3062745/user-photos-aren-t-synced-from-the-on-premises-environment-to-exchange)) om du vill ha mer information.|
| title |X |X | | |
| unauthOrig |X |X |X | |
| url |X |X | | |
| usageLocation |X | | |mekanisk egenskap. Användarens land/region
. Används för licens tilldelning. |
| userPrincipalName |X | | |UPN är inloggnings-ID: t för användaren. Oftast samma som [mail]-värdet. |
| wWWHomePage |X |X | | |

## <a name="teams-and-skype-for-business-online"></a>Team och Skype för företag – Online
| Attributnamn | User | Kontakt | Group | Kommentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definierar om ett konto är aktiverat. |
| c |X |X | | |
| nomenklatur |X | |X | |
| co |X |X | | |
| company |X |X | | |
| avdelning |X |X | | |
| beskrivning |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X |X | |
| förnamn |X |X | | |
| homephone |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| e-post |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| medlem | | |X | |
| mobil |X |X | | |
| msExchHideFromAddressLists |X |X |X | |
| msRTCSIP – ApplicationOptions |X | | | |
| msRTCSIP – DeploymentLocator |X |X | | |
| msRTCSIP-rad |X |X | | |
| msRTCSIP – OptionFlags |X |X | | |
| msRTCSIP – OwnerUrn |X | | | |
| msRTCSIP – PrimaryUserAddress |X |X | | |
| msRTCSIP – UserEnabled |X |X | | |
| objekt-SID |X | |X |mekanisk egenskap. AD-användarnamn som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| otherTelephone |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mekanisk egenskap. Används för att veta när redan utfärdade token ska inverifieras. Används av både hash-synkronisering av lösen ord, direkt autentisering och Federation. |
| sn |X |X | | |
| sourceAnchor |X |X |X |mekanisk egenskap. Oföränderlig identifierare som upprätthåller relationen mellan tillägg och Azure AD. |
| st |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | |synkroniserades bara en gång från Azure AD till Exchange Online efter vilken Exchange Online blir källan till behörighet för det här attributet och senare ändringar inte kan synkroniseras från den lokala platsen. Se ([KB](https://support.microsoft.com/help/3062745/user-photos-aren-t-synced-from-the-on-premises-environment-to-exchange)) om du vill ha mer information.|
| title |X |X | | |
| usageLocation |X | | |mekanisk egenskap. Användarens land/region. Används för licens tilldelning. |
| userPrincipalName |X | | |UPN är inloggnings-ID: t för användaren. Oftast samma som [mail]-värdet. |
| wWWHomePage |X |X | | |

## <a name="azure-rms"></a>Azure RMS
| Attributnamn | User | Kontakt | Group | Kommentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definierar om ett konto är aktiverat. |
| nomenklatur |X | |X |Eget namn eller alias. Oftast är prefixet för [mail]-värdet. |
| displayName |X |X |X |En sträng som representerar det namn som ofta visas som eget namn (förnamn efter namn). |
| e-post |X |X |X |fullständig e-postadress. |
| medlem | | |X | |
| objekt-SID |X | |X |mekanisk egenskap. AD-användarnamn som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| proxyAddresses |X |X |X |mekanisk egenskap. Används av Azure AD. Innehåller alla sekundära e-postadresser för användaren. |
| pwdLastSet |X | | |mekanisk egenskap. Används för att veta när redan utfärdade token ska inverifieras. |
| sourceAnchor |X |X |X |mekanisk egenskap. Oföränderlig identifierare som upprätthåller relationen mellan tillägg och Azure AD. |
| usageLocation |X | | |mekanisk egenskap. Användarens land/region. Används för licens tilldelning. |
| userPrincipalName |X | | |Detta UPN är inloggnings-ID: t för användaren. Oftast samma som [mail]-värdet. |

## <a name="intune"></a>Intune
| Attributnamn | User | Kontakt | Group | Kommentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definierar om ett konto är aktiverat. |
| c |X |X | | |
| nomenklatur |X | |X | |
| beskrivning |X |X |X | |
| displayName |X |X |X | |
| e-post |X |X |X | |
| MailNickName |X |X |X | |
| medlem | | |X | |
| objekt-SID |X | |X |mekanisk egenskap. AD-användarnamn som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mekanisk egenskap. Används för att veta när redan utfärdade token ska inverifieras. Används av både hash-synkronisering av lösen ord, direkt autentisering och Federation. |
| sourceAnchor |X |X |X |mekanisk egenskap. Oföränderlig identifierare som upprätthåller relationen mellan tillägg och Azure AD. |
| usageLocation |X | | |mekanisk egenskap. Användarens land/region. Används för licens tilldelning. |
| userPrincipalName |X | | |UPN är inloggnings-ID: t för användaren. Oftast samma som [mail]-värdet. |

## <a name="dynamics-crm"></a>Dynamics CRM
| Attributnamn | User | Kontakt | Group | Kommentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definierar om ett konto är aktiverat. |
| c |X |X | | |
| nomenklatur |X | |X | |
| co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| beskrivning |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| förnamn |X |X | | |
| l |X |X | | |
| managedBy | | |X | |
| manager |X |X | | |
| medlem | | |X | |
| mobil |X |X | | |
| objekt-SID |X | |X |mekanisk egenskap. AD-användarnamn som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| pwdLastSet |X | | |mekanisk egenskap. Används för att veta när redan utfärdade token ska inverifieras. Används av både hash-synkronisering av lösen ord, direkt autentisering och Federation. |
| sn |X |X | | |
| sourceAnchor |X |X |X |mekanisk egenskap. Oföränderlig identifierare som upprätthåller relationen mellan tillägg och Azure AD. |
| st |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| title |X |X | | |
| usageLocation |X | | |mekanisk egenskap. Användarens land/region. Används för licens tilldelning. |
| userPrincipalName |X | | |UPN är inloggnings-ID: t för användaren. Oftast samma som [mail]-värdet. |

## <a name="3rd-party-applications"></a>program från tredje part
Den här gruppen är en uppsättning attribut som används som de minimala attribut som behövs för en allmän arbets belastning eller ett program. Den kan användas för en arbets belastning som inte listas i ett annat avsnitt eller för en app som inte kommer från Microsoft. Den används explicit för följande:

* Yammer (endast användare förbrukas)
* [Hybrid samarbets scenarier för företag till företag (B2B) över org som erbjuds av resurser som SharePoint](/sharepoint/create-b2b-extranet)

Den här gruppen är en uppsättning attribut som kan användas om Azure AD-katalogen inte används för att stödja Microsoft 365, Dynamics eller Intune. Den har en liten uppsättning Core-attribut. Observera att enkel inloggning eller etablering till vissa tredjepartsprogram kräver att du konfigurerar synkronisering av attribut utöver de attribut som beskrivs här. Program kraven beskrivs i [själv studie kursen om SaaS-appar](../saas-apps/tutorial-list.md) för varje program.

| Attributnamn | User | Kontakt | Group | Kommentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definierar om ett konto är aktiverat. |
| nomenklatur |X | |X | |
| displayName |X |X |X | |
| anställnings-ID |X |  |  | |
| förnamn |X |X | | |
| e-post |X | |X | |
| managedBy | | |X | |
| smeknamn för e-post |X |X |X | |
| medlem | | |X | |
| objekt-SID |X | | |mekanisk egenskap. AD-användarnamn som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mekanisk egenskap. Används för att veta när redan utfärdade token ska inverifieras. Används av både hash-synkronisering av lösen ord, direkt autentisering och Federation. |
| sn |X |X | | |
| sourceAnchor |X |X |X |mekanisk egenskap. Oföränderlig identifierare som upprätthåller relationen mellan tillägg och Azure AD. |
| usageLocation |X | | |mekanisk egenskap. Användarens land/region. Används för licens tilldelning. |
| userPrincipalName |X | | |UPN är inloggnings-ID: t för användaren. Oftast samma som [mail]-värdet. |

## <a name="windows-10"></a>Windows 10
En Windows 10-domänansluten dator (enhet) synkroniserar vissa attribut till Azure AD. Mer information om scenarier finns i [ansluta domänanslutna enheter till Azure AD för Windows 10-upplevelser](../devices/hybrid-azuread-join-plan.md). Dessa attribut synkroniseras alltid och Windows 10 visas inte som en app som du kan avmarkera. En domänansluten Windows 10-dator identifieras genom att attributet userCertificate har fyllts i.

| Attributnamn | Enhet | Kommentar |
| --- |:---:| --- |
| accountEnabled |X | |
| deviceTrustType |X |Hårdkodad-värde för domänanslutna datorer. |
| displayName |X | |
| ms-DS-CreatorSID |X |Kallas även registeredOwnerReference. |
| objectGUID |X |Kallas även deviceID. |
| objekt-SID |X |Kallas även onPremisesSecurityIdentifier. |
| operatingSystem |X |Kallas även deviceOSType. |
| operatingSystemVersion |X |Kallas även deviceOSVersion. |
| userCertificate |X | |

De här attributen för **användaren** är förutom de andra appar som du har valt.  

| Attributnamn | User | Kommentar |
| --- |:---:| --- |
| domainFQDN |X |Kallas även dnsDomainName. Till exempel contoso.com. |
| domainNetBios |X |Kallas även netBiosName. Till exempel CONTOSO. |
| msDS-KeyCredentialLink |X |När användaren har registrerats i Windows Hello för företag. | 

## <a name="exchange-hybrid-writeback"></a>Exchange hybrid tillbakaskrivning
Dessa attribut skrivs tillbaka från Azure AD till lokala Active Directory när du väljer att aktivera **Exchange hybrid**. Beroende på din Exchange-version kan färre attribut synkroniseras.

| Attributnamn (lokal AD) | Attributnamn (Connect UI) | User | Kontakt | Group | Kommentar |
| --- |:---:|:---:|:---:| --- |---|
| msDS-ExternalDirectoryObjectID| ms-DS-extern-Directory-Object-ID |X | | |Härlett från cloudAnchor i Azure AD. Det här attributet är nytt i Exchange 2016 och Windows Server 2016 AD. |
| msExchArchiveStatus| MS-ArchiveStatus |X | | |Online-Arkiv: gör det möjligt för kunder att arkivera e-post. |
| msExchBlockedSendersHash| MS-BlockedSendersHash |X | | |Filtrering: skriver tillbaka lokal filtrering och online säkra och blockerade avsändar data från klienter. |
| msExchSafeRecipientsHash| MS-SafeRecipientsHash  |X | | |Filtrering: skriver tillbaka lokal filtrering och online säkra och blockerade avsändar data från klienter. |
| msExchSafeSendersHash| MS-SafeSendersHash  |X | | |Filtrering: skriver tillbaka lokal filtrering och online säkra och blockerade avsändar data från klienter. |
| msExchUCVoiceMailSettings| MS-UCVoiceMailSettings |X | | |Aktivera Unified Messaging (UM) – online röst meddelande: används av Microsoft Lync Server integration för att indikera att den lokala Lync-servern har röst meddelanden i onlinetjänster. |
| msExchUserHoldPolicies| MS-UserHoldPolicies |X | | |Tvister: aktiverar moln tjänster för att avgöra vilka användare som omfattas av tvister. |
| proxyAddresses| proxyAddresses |X |X |X |Endast x500-adressen från Exchange Online har infogats. |
| publicDelegates| MS-multikommunikation – offentligt – ombud  |X | | |Tillåter att en Exchange Online-postlåda beviljas SendOnBehalfTo-rättigheter till användare med lokal Exchange-postlåda. Kräver Azure AD Connect build 1.1.552.0 eller efter. |

## <a name="exchange-mail-public-folder"></a>Offentlig Exchange-e-postmapp
Dessa attribut synkroniseras från lokala Active Directory till Azure AD när du väljer att aktivera **offentlig Exchange-e-postmapp**.

| Attributnamn | PublicFolder | Kommentar |
| --- | :---:| --- |
| displayName | X |  |
| e-post | X |  |
| msExchRecipientTypeDetails | X |  |
| objectGUID | X |  |
| proxyAddresses | X |  |
| targetAddress | X |  |

## <a name="device-writeback"></a>Tillbakaskrivning av enheter
Enhets objekt skapas i Active Directory. Dessa objekt kan vara enheter som är anslutna till Azure AD-eller domänanslutna Windows 10-datorer.

| Attributnamn | Enhet | Kommentar |
| --- |:---:| --- |
| altSecurityIdentities |X | |
| displayName |X | |
| dn |X | |
| msDS-CloudAnchor |X | |
| msDS-DeviceID |X | |
| msDS-DeviceObjectVersion |X | |
| msDS-DeviceOSType |X | |
| msDS-DeviceOSVersion |X | |
| msDS-DevicePhysicalIDs |X | |
| msDS-KeyCredentialLink |X |Endast med Windows Server 2016 AD-schema |
| msDS-IsCompliant |X | |
| msDS-IsEnabled |X | |
| msDS-IsManaged |X | |
| msDS-RegisteredOwner |X | |

## <a name="notes"></a>Kommentarer
* När du använder ett alternativt ID synkroniseras det lokala attributet userPrincipalName med Azure AD-attributet onPremisesUserPrincipalName. Det alternativa ID-attributet, till exempel mail, synkroniseras med Azure AD-attributet userPrincipalName.
* I listorna ovan gäller objekt typ **användaren** även för objekt typen **InetOrgPerson**.

## <a name="next-steps"></a>Nästa steg
Läs mer om [Azure AD Connect Sync](how-to-connect-sync-whatis.md) -konfigurationen.

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).