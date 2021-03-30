---
title: Jämföra Active Directory och Azure Active Directory
description: I det här dokumentet jämförs Active Directory Domain Services (läggs till) i Azure Active Directory (AD). Det beskriver viktiga begrepp i båda identitets lösningarna och förklarar hur det är annorlunda eller liknande.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 02/26/2020
ms.author: martinco
ms.openlocfilehash: 64a8dabaedc3922ebd8d163b1ea162b7d1584de2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371927"
---
# <a name="compare-active-directory-to-azure-active-directory"></a>Jämföra Active Directory och Azure Active Directory

Azure Active Directory är nästa utveckling av identitets-och åtkomst hanterings lösningar för molnet. Microsoft introducerade Active Directory Domain Services i Windows 2000 för att ge organisationer möjlighet att hantera flera lokala infrastruktur komponenter och system med hjälp av en enda identitet per användare.

Azure AD tar den här metoden till nästa nivå genom att tillhandahålla organisationer med en IDaaS-lösning (Identity as a Service) för alla sina appar i molnet och lokalt.

De flesta IT-administratörer är bekanta med Active Directory Domain Services koncept. I följande tabell beskrivs skillnaderna och likheter mellan Active Directory koncept och Azure Active Directory.

|Koncept|Active Directory (AD)|Azure Active Directory |
|:-|:-|:-|
|**Användare**|||
|Etablering: användare | Organisationer skapar interna användare manuellt eller använder ett internt eller automatiserat etablerings system, till exempel Microsoft Identity Manager, för att integrera med ett HR-system.|Befintliga AD-organisationer använder [Azure AD Connect](../hybrid/how-to-connect-sync-whatis.md) för att synkronisera identiteter i molnet.</br> Azure AD lägger till stöd för att automatiskt skapa användare från [moln HR-system](../saas-apps/workday-tutorial.md). </br>Azure AD kan etablera identiteter i [scim-aktiverade](../app-provisioning/use-scim-to-provision-users-and-groups.md) SaaS-appar för att automatiskt förse appar med nödvändig information för att tillåta åtkomst för användare. |
|Etablering: externa identiteter| Organisationer skapar externa användare manuellt som vanliga användare i en dedikerad extern AD-skog, vilket leder till administrations kostnader för att hantera livs cykeln för externa identiteter (gäst användare)| Azure AD tillhandahåller en speciell klass av identitet som stöder externa identiteter. [Azure AD B2B](/azure/active-directory/b2b/) hanterar länken till den externa användarens identitet för att kontrol lera att de är giltiga. |
| Rättighets hantering och grupper| Administratörer gör användare medlemmar i grupper. Appar och resurs ägare ger grupper åtkomst till appar eller resurser.| [Grupper](./active-directory-groups-create-azure-portal.md) är också tillgängliga i Azure AD och administratörer kan också använda grupper för att ge åtkomst till resurser. I Azure AD kan administratörer tilldela medlemskap till grupper manuellt eller använda en fråga för att dynamiskt inkludera användare i en grupp. </br> Administratörer kan använda [rättighets hantering](../governance/entitlement-management-overview.md) i Azure AD för att ge användare åtkomst till en samling av appar och resurser med hjälp av arbets flöden och, vid behov, tidsbaserade kriterier. |
| Administratörs hantering|Organisationer kommer att använda en kombination av domäner, organisationsenheter och grupper i AD för att delegera administrativa rättigheter för att hantera den katalog och de resurser som den kontrollerar.| Azure AD tillhandahåller [inbyggda roller](./active-directory-users-assign-role-azure-portal.md) med sin Azure AD-rollbaserade åtkomst kontroll (Azure AD RBAC)-system, med begränsat stöd för att [skapa anpassade roller](../roles/custom-overview.md) för att delegera privilegie rad åtkomst till identitets systemet, de appar och resurser som den kontrollerar.</br>Hantering av roller kan förbättras med [Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) för att tillhandahålla just-in-Time-, tidsbegränsad-eller arbets flödes-baserad åtkomst till privilegierade roller. |
| Hantering av autentiseringsuppgifter| Autentiseringsuppgifterna i Active Directory baseras på lösen ord, certifikatautentisering och autentisering med smartkort. Lösen ord hanteras med hjälp av lösen ords principer som baseras på lösen ords längd, förfallo datum och komplexitet.|Azure AD använder intelligent [lösen ords skydd](../authentication/concept-password-ban-bad.md) för molnet och lokalt. Skyddet omfattar Smart utelåsning och blockering av vanliga och anpassade lösen ords fraser och ersättningar. </br>Azure AD ökar avsevärt säkerheten [genom Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) och [lösen ords utan lösen ord](../authentication/concept-authentication-passwordless.md) , t. ex. FIDO2. </br>Azure AD minskar support kostnaderna genom att ge användarna ett [självbetjänings system för återställning av lösen ord](../authentication/concept-sspr-howitworks.md) . |
| **Appar**|||
| Infrastruktur program|Active Directory utgör grunden för många infrastruktur komponenter lokalt, till exempel DNS, DHCP, IPSec, WiFi, NPS och VPN-åtkomst|I en ny moln värld är Azure AD det nya kontroll planet för att få åtkomst till appar, jämfört med nätverks kontroller. När användare autentiseras kontrollerar[villkorlig åtkomst (ca)](../conditional-access/overview.md)vilka användare som har åtkomst till de appar som krävs under krav.|
| Traditionella och äldre appar| De flesta lokala appar använder LDAP, Windows-Integrated autentisering (NTLM och Kerberos) eller en huvud-baserad autentisering för att kontrol lera åtkomsten till användare.| Azure AD kan ge åtkomst till dessa typer av lokala appar med hjälp av [Azure AD Application Proxy](../manage-apps/application-proxy.md) -agenter som körs lokalt. Med den här metoden kan Azure AD autentisera Active Directory användare lokalt med hjälp av Kerberos medan du migrerar eller behöver samverka med äldre appar. |
| SaaS-appar|Active Directory stöder inte SaaS-appar internt och kräver Federations system, till exempel AD FS.|SaaS appar som stöder OAuth2, SAML och WS- \* Authentication kan integreras för att använda Azure AD för autentisering. |
| Branschspecifika appar (LOB) med modern autentisering|Organisationer kan använda AD FS med Active Directory för att stödja LOB-appar som kräver modern autentisering.| LOB-appar som kräver modern autentisering kan konfigureras att använda Azure AD för autentisering. |
| Tjänster på mellan nivå/daemon|Tjänster som körs i lokala miljöer använder normalt AD-tjänst konton eller grupphanterade tjänst konton (gMSA) för att köra. Dessa appar ärver sedan behörigheter för tjänst kontot.| Azure AD tillhandahåller [hanterade identiteter](../managed-identities-azure-resources/index.yml) för att köra andra arbets belastningar i molnet. Livs cykeln för dessa identiteter hanteras av Azure AD och är knuten till resurs leverantören kan inte användas för andra ändamål för att få åtkomst till bakdörr.|
| **Egenskaper**|||
| Mobilt|Active Directory har inte inbyggt stöd för mobila enheter utan lösningar från tredje part.| Microsofts lösning för hantering av mobila enheter Microsoft Intune integreras med Azure AD. Microsoft Intune tillhandahåller enhets tillstånds information till identitets systemet för att utvärdera under autentisering. |
| Windows-datorer|Active Directory ger möjlighet till domän anslutning till Windows-enheter för att hantera dem med hjälp av grupprincip, System Center Configuration Manager eller andra lösningar från tredje part.|Windows-enheter kan [anslutas till Azure AD](../devices/index.yml). Villkorlig åtkomst kan kontrol lera om en enhet är Azure AD-ansluten som en del av autentiseringsprocessen. Windows-enheter kan också hanteras med [Microsoft Intune](/intune/what-is-intune). I det här fallet kommer villkorlig åtkomst att överväga om en enhet är kompatibel (till exempel uppdaterade säkerhets korrigeringar och virus signaturer) innan åtkomst tillåts till apparna.|
| Windows-servrar| Active Directory tillhandahåller kraftfulla hanterings funktioner för lokala Windows-servrar som använder grupprincip eller andra hanterings lösningar.| Virtuella Windows Server-datorer i Azure kan hanteras med [Azure AD Domain Services](../../active-directory-domain-services/index.yml). [Hanterade identiteter](../managed-identities-azure-resources/index.yml) kan användas när virtuella datorer behöver åtkomst till identitetens system katalog eller resurser.|
| Linux/UNIX-arbetsbelastningar|Active Directory har inbyggt stöd för icke-Windows utan lösningar från tredje part, även om Linux-datorer kan konfigureras för autentisering med Active Directory som en Kerberos-sfär.|Virtuella Linux/UNIX-datorer kan använda [hanterade identiteter](../managed-identities-azure-resources/index.yml) för att få åtkomst till identitets systemet eller-resurserna. Vissa organisationer, migrera dessa arbets belastningar till moln behållar teknik, som också kan använda hanterade identiteter.|

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure Active Directory?](./active-directory-whatis.md)
- [Jämför självhanterade Active Directory Domain Services, Azure Active Directory och hanterade Azure Active Directory Domain Services](../../active-directory-domain-services/compare-identity-solutions.md)
- [Vanliga frågor och svar om Azure Active Directory](./active-directory-faq.md)
- [Vad är nytt i Azure Active Directory?](./whats-new.md)
