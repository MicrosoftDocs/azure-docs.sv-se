---
title: Inbyggda Azure AD-roller – Azure Active Directory
description: Beskriver Azure Active Directory inbyggda roller och behörigheter.
services: active-directory
author: rolyon
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 03/13/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2c327154e2f4eae735e7078be3b057b1d8cf1c5
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103490882"
---
# <a name="azure-ad-built-in-roles"></a>Inbyggda roller i Azure AD

Om en annan administratör eller icke-administratör behöver hantera Azure AD-resurser i Azure Active Directory (Azure AD) tilldelar du dem en Azure AD-roll som ger de behörigheter som de behöver. Du kan till exempel tilldela roller för att lägga till eller ändra användare, återställa användar lösen ord, hantera användar licenser eller hantera domän namn.

Den här artikeln innehåller inbyggda Azure AD-roller som du kan tilldela för att tillåta hantering av Azure AD-resurser. Information om hur du tilldelar roller finns i [tilldela Azure AD-roller till användare](manage-roles-portal.md).

## <a name="limit-use-of-global-administrator"></a>Begränsa användningen av global administratör

Användare som har tilldelats rollen som global administratör kan läsa och ändra alla administrativa inställningar i din Azure AD-organisation. När en användare registrerar sig för en moln tjänst från Microsoft skapas en Azure AD-klient som standard och användaren blir medlem i rollen global administratör. När du lägger till en prenumeration till en befintlig klient är du inte tilldelad rollen som global administratör. Endast globala administratörer och privilegierade roll administratörer kan delegera administratörs roller. För att minska risken för verksamheten rekommenderar vi att du tilldelar den här rollen till det minsta möjliga antalet personer i din organisation.

Som bästa praxis rekommenderar vi att du tilldelar rollen till färre än fem personer i din organisation. Om du har fler än fem administratörer tilldelade rollen global administratör i din organisation kan du använda följande metoder för att minska användningen.

## <a name="all-roles"></a>Alla roller

> [!div class="mx-tableFixed"]
> | Roll | Beskrivning | Mall-ID |
> | --- | --- | --- |
> | [Programadministratör](#application-administrator) | Kan skapa och hantera alla aspekter av registrerings-och företags program. | 9b895d92-2cd3-44c7-9d02-a6ac2d5ea5c3 |
> | [Programutvecklare](#application-developer) | Kan skapa program registreringar oberoende av inställningen användare kan registrera program. | cf1c38e5-3621-4004-a7cb-879624dced7c |
> | [Författare för angrepps nytto Last](#attack-payload-author) | Kan skapa nytto laster som en administratör kan initiera senare. | 9c6df0f2-1e7c-4dc3-b195-66dfbd24aa8f |
> | [Administratör för attack simulering](#attack-simulation-administrator) | Kan skapa och hantera alla aspekter av kampanjer för attack simulering. | c430b396-e693-46cc-96f3-db01bf8bb62a |
> | [Administratör för autentisering](#authentication-administrator) | Har åtkomst till att visa, ange och återställa information om autentiseringsinformation för användare som inte är administratörer. | c4e39bd9-1100-46d3-8c65-fb160da0071f |
> | [Administratör för autentiseringsprincip](#authentication-policy-administrator) | Kan skapa och hantera alla aspekter av autentiseringsmetoder och lösen ords skydds principer. | 0526716b-113d-4c15-b2c8-68e3c22b9f80 |
> | [Lokal administratör för Azure AD-ansluten enhet](#azure-ad-joined-device-local-administrator) | Användare som har tilldelats den här rollen läggs till i den lokala administratörs gruppen på Azure AD-anslutna enheter. | 9f06204d-73c1-4d4c-880a-6edb90606fd8 |
> | [Azure DevOps-administratör](#azure-devops-administrator) | Kan hantera Azure DevOps-organisationens principer och inställningar. | e3973bdf-4987-49ae-837a-ba8e231c7286 |
> | [Azure Information Protection administratör](#azure-information-protection-administrator) | Kan hantera alla aspekter av den Azure Information Protection produkten. | 7495fdc4-34c4-4d15-a289-98788ce399fd |
> | [B2C IEF Keys-administratör](#b2c-ief-keyset-administrator) | Kan hantera hemligheter för Federation och kryptering i IEF (Identity Experience Framework). | aaf43236-0c0d-4d5f-883a-6955382ac081 |
> | [B2C IEF-princip administratör](#b2c-ief-policy-administrator) | Kan skapa och hantera principer för förtroende ramverk i IEF (Identity Experience Framework). | 3edaf663-341e-4475-9f94-5c398ef6c070 |
> | [Fakturerings administratör](#billing-administrator) | Kan utföra vanliga fakturerings aktiviteter, t. ex. uppdatering av betalnings information. | b0f54661-2d74-4c50-afa3-1ec803f12efe |
> | [Molnprogramadministratör](#cloud-application-administrator) | Kan skapa och hantera alla aspekter av app-registreringar och företags program förutom App proxy. | 158c047a-c907-4556-b7ef-446551a6b5f7 |
> | [Moln enhets administratör](#cloud-device-administrator) | Begränsad åtkomst till hantering av enheter i Azure AD. | 7698a772-787b-4ac8-901f-60d6b08affd2 |
> | [Efterlevnadsadministratör](#compliance-administrator) | Kan läsa och hantera kompatibilitets konfiguration och rapporter i Azure AD och Microsoft 365. | 17315797-102d-40b4-93e0-432062caca18 |
> | [Efterlevnadsdataadministratör](#compliance-data-administrator) | Skapar och hanterar innehåll för efterlevnad. | e6d1a23a-da11-4be4-9570-befc86d067a7 |
> | [Administratör för villkorsstyrd åtkomst](#conditional-access-administrator) | Kan hantera funktioner för villkorlig åtkomst. | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9 |
> | [Åtkomst god kännare för kund lås](#customer-lockbox-access-approver) | Kan godkänna Microsofts support förfrågningar för att få åtkomst till kundens organisations data. | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91 |
> | [Administratör för Skriv bords analys](#desktop-analytics-administrator) | Kan komma åt och hantera Skriv bords hanterings verktyg och-tjänster. | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4 |
> | [Katalog läsare](#directory-readers) | Kan läsa grundläggande katalog information. Används ofta för att ge Läs behörighet till katalogen för program och gäster. | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b |
> | [Konton för katalog synkronisering](#directory-synchronization-accounts) | Används endast av Azure AD Connect-tjänsten. | d29b2b05-8046-44ba-8758-1e26182fcf32 |
> | [Katalog skrivare](#directory-writers) | Kan läsa och skriva grundläggande katalog information. För att bevilja åtkomst till program, som inte är avsedda för användare. | 9360feb5-F418-4baa-8175-e2a00bac4301 |
> | [Domän namns administratör](#domain-name-administrator) | Kan hantera domän namn i molnet och lokalt. | 8329153b-31d0-4727-b945-745eb3bc5f31 |
> | [Dynamics 365-administratör](#dynamics-365-administrator) | Kan hantera alla aspekter av Dynamics 365-produkten. | 44367163-eba1-44c3-98af-f5787879f96a |
> | [Exchange-administratör](#exchange-administrator) | Kan hantera alla aspekter av Exchange-produkten. | 29232cdf-9323-42fd-ade2-1d097af3e4de |
> | [Användar flödes administratör för externt ID](#external-id-user-flow-administrator) | Kan skapa och hantera alla aspekter av användar flöden. | 6e591065-9bad-43ed-90f3-e9424366d2f0 |
> | [Administratör för externt ID-attribut för användar flöde](#external-id-user-flow-attribute-administrator) | Kan skapa och hantera attributet schema tillgängligt för alla användar flöden. | 0f971eea-41eb-4569-a71e-57bb8a3eff1e |
> | [Administratör för extern identitetsprovider](#external-identity-provider-administrator) | Kan konfigurera identitets leverantörer för användning i direkt Federation. | be2f45a1-457d-42af-a067-6ec1fa63bc45 |
> | [Global administratör](#global-administrator) | Kan hantera alla aspekter av Azure AD och Microsoft-tjänster som använder Azure AD-identiteter. | 62e90394-69f5-4237-9190-012177145e10 |
> | [Global läsare](#global-reader) | Kan läsa allt som en global administratör kan, men inte uppdatera något. | f2ef992c-3afb-46b9-b7cf-a126ee74c451 |
> | [Grupp administratör](#groups-administrator) | Medlemmar i den här rollen kan skapa/hantera grupper, skapa/hantera grupper inställningar som namngivnings-och förfallo principer och Visa grupper-aktivitet och gransknings rapporter. | fdd7a751-b60b-444a-984c-02652fe8fa1c |
> | [Gäst deltagare](#guest-inviter) | Kan bjuda in gäst användare oberoende av inställningen medlemmar kan bjuda in gäster. | 95e79109-95c0-4d8e-aee3-d01accf2d47b |
> | [Support administratör](#helpdesk-administrator) | Kan återställa lösen ord för administratörer som inte har administratörer och supportavdelningen. | 729827e3-9c14-49f7-bb1b-9608f156bbb8 |
> | [Hybrid identitets administratör](#hybrid-identity-administrator) | Kan hantera AD till moln etablering och Federations inställningar i Azure AD. | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2 |
> | [Insights-administratör](#insights-administrator) | Har administrativ åtkomst i Microsoft 365 Insights-appen. | eb1f4a8d-243a-41f0-9fbd-c7cdf6c5ef7c |
> | [Affärs ledare för insikter](#insights-business-leader) | Kan visa och dela instrument paneler och insikter via M365 Insights-appen. | 31e939ad-9672-4796-9c2e-873181342d2d |
> | [Intune-administratör](#intune-administrator) | Kan hantera alla aspekter av Intune-produkten. | 3a2c62db-5318-420d-8d74-23affee5d9d5 |
> | [Kaizala-administratör](#kaizala-administrator) | Kan hantera inställningar för Microsoft-Kaizala. | 74ef975b-6605-40af-a5d2-b9539d836353 |
> | [Kunskaps administratör](#knowledge-administrator) | Kan konfigurera kunskap, inlärning och andra intelligenta funktioner. | b5a8dcf3-09d5-43a9-a639-8e29ef291470 |
> | [Licens administratör](#license-administrator) | Kan hantera produkt licenser för användare och grupper. | 4d6ac14f-3453-41d0-bef9-a3e0c569773a |
> | [Meddelande Center sekretess läsare](#message-center-privacy-reader) | Kan bara läsa säkerhets meddelanden och uppdateringar i meddelande Center för Office 365. | ac16e43d-7b2d-40e0-ac05-243ff356ab5b |
> | [Meddelande Center läsare](#message-center-reader) | Kan endast läsa meddelanden och uppdateringar för sin organisation i meddelande Center för Office 365. | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b |
> | [Modern Commerce-användare](#modern-commerce-user) | Kan hantera kommersiella inköp för ett företag, en avdelning eller ett team. | d24aef57-1500-4070-84db-2666f29cf966 |
> | [Nätverks administratör](#network-administrator) | Kan hantera nätverks platser och granska Enterprise Network design Insights för Microsoft 365 program vara som tjänst program. | d37c8bed-0711-4417-ba38-b4abe66ce4c2 |
> | [Office Apps-administratör](#office-apps-administrator) | Kan hantera Office-appars moln tjänster, inklusive hantering av principer och inställningar, och hantera möjligheten att välja, avmarkera och publicera "What ' s New"-funktions innehåll till slutanvändarens enheter. | 2b745bdf-0803-4d80-aa65-822c4493daac |
> | [Support för partner 1](#partner-tier1-support) | Använd inte – inte avsedd för allmän användning. | 4ba39ca4-527c-499a-b93d-d9b492c50246 |
> | [Support för partner – nivå 2](#partner-tier2-support) | Använd inte – inte avsedd för allmän användning. | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8 |
> | [Lösen ords administratör](#password-administrator) | Kan återställa lösen ord för icke-administratörer och lösen ords administratörer. | 966707d0-3269-4727-9be2-8c3a10f19b9d |
> | [Power BI administratör](#power-bi-administrator) | Kan hantera alla aspekter av den Power BI produkten. | a9ea8996-122f-4c74-9520-8edcd192826c |
> | [Power Platform-administratör](#power-platform-administrator) | Kan skapa och hantera alla aspekter av Microsoft Dynamics 365, PowerApps och Microsoft Flow. | 11648597-926c-4cf3-9c36-bcebb0ba8dcc |
> | [Skrivar administratör](#printer-administrator) | Kan hantera alla aspekter av skrivare och skrivar anslutningar. | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f |
> | [Skrivar tekniker](#printer-technician) | Kan registrera och avregistrera skrivare och uppdatera skrivar status. | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477 |
> | [Administratör för privilegie rad autentisering](#privileged-authentication-administrator) | Har åtkomst till att visa, ange och återställa information om autentiseringsmetoder för alla användare (administratörer eller icke-administratörer). | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13 |
> | [Privilegie rad roll administratör](#privileged-role-administrator) | Kan hantera roll tilldelningar i Azure AD och alla aspekter av Privileged Identity Management. | e8611ab8-c189-46e8-94e1-60213ab1f814 |
> | [Rapportläsare](#reports-reader) | Kan läsa inloggnings-och gransknings rapporter. | 4a5d8f65-41da-4de4-8968-e035b65339cf |
> | [Sök administratör](#search-administrator) | Kan skapa och hantera alla aspekter av Microsofts Sök inställningar. | 0964bb5e-9bdb-4d7b-ac29-58e794862a40 |
> | [Sök redigerare](#search-editor) | Kan skapa och hantera redaktionellt innehåll, till exempel bok märken, Q och as, locations, floorplan. | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9 |
> | [Säkerhetsadministratör](#security-administrator) | Kan läsa säkerhets information och rapporter och hantera konfigurationen i Azure AD och Office 365. | 194ae4cb-b126-40b2-bd5b-6091b380977d |
> | [Säkerhetsoperatör](#security-operator) | Skapar och hanterar säkerhets händelser. | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f |
> | [Säkerhetsläsare](#security-reader) | Kan läsa säkerhets information och rapporter i Azure AD och Office 365. | 5d6b6bb7-de71-4623-b4af-96380a352509 |
> | [Tjänst support administratör](#service-support-administrator) | Kan läsa information om tjänst hälsa och hantera support ärenden. | f023fd81-a637-4b56-95fd-791ac0226033 |
> | [SharePoint-administratör](#sharepoint-administrator) | Kan hantera alla aspekter av SharePoint-tjänsten. | f28a1f50-f6e7-4571-818b-6a12f2af6b6c |
> | [Skype för företag-administratör](#skype-for-business-administrator) | Kan hantera alla aspekter av Skype för företag-produkten. | 75941009-915a-4869-abe7-691bff18279e |
> | [Team administratör](#teams-administrator) | Kan hantera Microsoft Teams-tjänsten. | 69091246-20e8-4a56-aa4d-066075b2a7a8 |
> | [Team kommunikations administratör](#teams-communications-administrator) | Kan hantera uppringnings-och mötes funktioner i Microsoft Teams-tjänsten. | baf37b3a-610e-45da-9e62-d9d1e5e8914b |
> | [Support tekniker för Teams kommunikation](#teams-communications-support-engineer) | Kan felsöka kommunikations problem inom team med hjälp av avancerade verktyg. | f70938a0-fc10-4177-9e90-2178f8765737 |
> | [Support specialist för Teams kommunikation](#teams-communications-support-specialist) | Kan felsöka kommunikations problem inom team med hjälp av grundläggande verktyg. | fcf91098-03e3-41a9-b5ba-6f0ec8188a12 |
> | [Team enhets administratör](#teams-devices-administrator) | Kan utföra hantering av relaterade uppgifter på Team-certifierade enheter. | 3d762c5a-1b6c-493f-843e-55a3b42923d4 |
> | [Rapport läsare för användnings Sammanfattning](#usage-summary-reports-reader) | Kan bara se sammanslagningar på klient nivå i Microsoft 365 användnings analys och produktivitets poäng. | 75934031-6c7e-415a-99d7-48dbd49e875e |
> | [Användar administratör](#user-administrator) | Kan hantera alla aspekter av användare och grupper, inklusive att återställa lösen ord för begränsade administratörer. | fe930be7-5e62-47db-91af-98c3a49a38b1 |

## <a name="application-administrator"></a>Programadministratör

Användare med den här rollen kan skapa och hantera alla aspekter av företags program, program registreringar och programproxy-inställningar. Observera att användare som har tilldelats den här rollen inte har lagts till som ägare när de skapar nya program registreringar eller företags program.

Den här rollen ger också möjlighet att godkänna delegerade behörigheter och program behörigheter, med undantag för program behörigheter för både Microsoft Graph och Azure AD Graph.

> [!IMPORTANT]
> Detta undantag innebär att du fortfarande kan godkänna program behörigheter för _andra_ appar (till exempel appar som inte kommer från Microsoft eller appar som du har registrerat). Du kan fortfarande _begära_ dessa behörigheter som en del av appens registrering, men _beviljar_ (som godkänner) dessa behörigheter kräver en mer privilegie rad administratör, t. ex. global administratör.
>
>Den här rollen ger möjlighet att hantera programautentiseringsuppgifter. Användare som har tilldelats den här rollen kan lägga till autentiseringsuppgifter i ett program och använda dessa autentiseringsuppgifter för att personifiera programmets identitet. Om programmets identitet har beviljats åtkomst till en resurs, till exempel möjligheten att skapa eller uppdatera användare eller andra objekt, kan en användare som tilldelats rollen utföra dessa åtgärder samtidigt som programmet personifieras. Den här möjligheten att personifiera programmets identitet kan vara en höjning av privilegier över vad användaren kan göra via sina roll tilldelningar. Det är viktigt att förstå att tilldelning av en användare till rollen program administratör ger dem möjlighet att personifiera ett programs identitet.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/program/skapa | Skapa alla typer av program |
> | Microsoft. Directory/Applications/Delete | Ta bort alla typer av program |
> | Microsoft. Directory/Applications/applicationProxy/Read | Läsa alla egenskaper för program-proxy |
> | Microsoft. Directory/Applications/applicationProxy/Update | Uppdatera alla programproxy-egenskaper |
> | Microsoft. Directory/Applications/applicationProxyAuthentication/Update | Uppdatera egenskaper för autentisering av programproxy |
> | Microsoft. Directory/Applications/applicationProxySslCertificate/Update | Uppdatera anpassade programproxy-domäner |
> | Microsoft. Directory/Applications/applicationProxyUrlSettings/Update | Uppdatera interna och externa URL: er för programproxy |
> | Microsoft. Directory/Applications/appRoles/Update | Uppdatera egenskapen appRoles för alla typer av program |
> | Microsoft. Directory/program/mål grupp/uppdatering | Uppdatera egenskapen Audience för program |
> | Microsoft. Directory/program/autentisering/uppdatering | Uppdatera autentiseringen för alla typer av program |
> | Microsoft. Directory/Applications/Basic/Update | Uppdatera grundläggande egenskaper för program |
> | Microsoft. Directory/program/autentiseringsuppgifter/uppdatera | Uppdatera programautentiseringsuppgifter |
> | Microsoft. Directory/program/ägare/uppdatering | Uppdatera ägare av program |
> | Microsoft. Directory/program/behörigheter/uppdatera | Uppdatera exponerade behörigheter och nödvändiga behörigheter för alla typer av program |
> | Microsoft. Directory/Applications/policies/Update | Uppdatera principer för program |
> | Microsoft. Directory/program/verifiering/uppdatering | Uppdatera applicationsverification-egenskap |
> | Microsoft. Directory/program/synkronisering/standard/Read | Läs etablerings inställningar som är associerade med programobjektet |
> | Microsoft. Directory/applicationTemplates/instansiera | Instansiera Galleri program från programmallar |
> | Microsoft. Directory/auditLogs/allProperties/Read | Läsa alla egenskaper för gransknings loggar, inklusive privilegierade egenskaper |
> | Microsoft. Directory/Connectors/skapa | Skapa Application Proxy-kopplingar |
> | Microsoft. Directory/Connectors/allProperties/Read | Läsa alla egenskaper för Application Proxy-kopplingar |
> | Microsoft. Directory/connectorGroups/Create | Skapa anslutnings grupper för Application Proxy |
> | Microsoft. Directory/connectorGroups/Delete | Ta bort kopplings grupper för Application Proxy |
> | Microsoft. Directory/connectorGroups/allProperties/Read | Läs alla egenskaper för kopplings grupper för Application Proxy |
> | Microsoft. Directory/connectorGroups/allProperties/Update | Uppdatera alla egenskaper för Application Proxy Connector-grupper |
> | Microsoft. Directory/oAuth2PermissionGrants/allProperties/allTasks | Skapa och ta bort OAuth 2,0-behörighet bidrag och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/applicationPolicies/Create | Skapa användnings principer |
> | Microsoft. Directory/applicationPolicies/Delete | Ta bort användnings principer |
> | Microsoft. Directory/applicationPolicies/standard/Read | Läsa standard egenskaper för användnings principer |
> | Microsoft. Directory/applicationPolicies/Owners/Read | Läs ägare för användnings principer |
> | Microsoft. Directory/applicationPolicies/policyAppliedTo/Read | Läsa användnings principer tillämpade på objekt listan |
> | Microsoft. Directory/applicationPolicies/Basic/Update | Uppdatera standard egenskaper för användnings principer |
> | Microsoft. Directory/applicationPolicies/Owners/Update | Uppdatera egenskapen owner för användnings principer |
> | Microsoft. Directory/provisioningLogs/allProperties/Read | Läs alla egenskaper för etablerings loggar |
> | Microsoft. Directory/Service princip ALS/Create | Skapa tjänsthuvudnamn |
> | Microsoft. Directory/Service princip ALS/Delete | Ta bort tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Disable | Inaktivera tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Enable | Aktivera tjänstens huvudnamn |
> | Microsoft. Directory/Service princip ALS/getPasswordSingleSignOnCredentials | Hantera autentiseringsuppgifter för enkel inloggning med lösen ord för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/synchronizationCredentials/Manage | Hantera program etablerings hemligheter och autentiseringsuppgifter |
> | Microsoft. Directory/Service princip ALS/synchronizationJobs/Manage | Starta, starta om och pausa Application Provisioning synkroniseringen-jobb |
> | Microsoft. Directory/Service princip ALS/synchronizationSchema/Manage | Skapa och hantera synkroniseringen-jobb och-schema för program etablering |
> | Microsoft. Directory/Service princip ALS/managePasswordSingleSignOnCredentials | Läs inloggnings uppgifter för enkel inloggning för lösen ord för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/managePermissionGrantsForAll. Microsoft-Application-admin | Bevilja medgivande för program behörigheter och delegerade behörigheter åt alla användare eller alla användare, förutom program behörigheter för Microsoft Graph och Azure AD Graph |
> | Microsoft. Directory/Service princip ALS/appRoleAssignedTo/Update | Uppdatera roll tilldelningar för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/målgrupp/uppdatera | Uppdatera mål egenskaper för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Authentication/Update | Uppdatera autentiserings egenskaper för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Basic/Update | Uppdatera grundläggande egenskaper för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/credentials/Update | Uppdatera autentiseringsuppgifter för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Owners/Update | Uppdatera ägare till tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Permissions/Update | Uppdatera behörigheter för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/policies/Update | Uppdatera principer för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/tag/Update | Uppdatera egenskapen tag för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Synchronization/standard/Read | Läs etablerings inställningar som är associerade med tjänstens huvud namn |
> | Microsoft. Directory/signInReports/allProperties/Read | Läs alla egenskaper för inloggnings rapporter, inklusive privilegierade egenskaper |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="application-developer"></a>Programutvecklare

Användare med den här rollen kan skapa program registreringar när inställningen "användare kan registrera program" är inställd på Nej. Den här rollen beviljar också behörighet för ett eget ställe när inställningen "användare kan godkänna att appar som har åtkomst till företags information för deras räkning" är inställd på Nej. Användare som har tilldelats rollen läggs till som ägare när de skapar nya program registreringar eller företags program.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/Applications/createAsOwner | Skapa alla typer av program och skapare läggs till som första ägare |
> | Microsoft. Directory/appRoleAssignments/createAsOwner | Skapa program roll tilldelningar, med Creator som första ägare |
> | Microsoft. Directory/oAuth2PermissionGrants/createAsOwner | Skapa behörighets bidrag för OAuth 2,0, med skaparen som första ägare |
> | Microsoft. Directory/Service princip ALS/createAsOwner | Skapa tjänstens huvud namn, med Creator som första ägare |

## <a name="attack-payload-author"></a>Författare för angrepps nytto Last

Användare med den här rollen kan skapa nytto laster, men inte starta eller schemalägga angrepp. Angrepps nytto lasterna är sedan tillgängliga för alla administratörer i klienten som kan använda dem för att skapa en simulering.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Office365. protectionCenter/attackSimulator/nytto Last/allProperties/allTasks | Skapa och hantera angrepps nytto laster i angrepps Simulator |
> | Microsoft. Office365. protectionCenter/attackSimulator/Reports/allProperties/Read | Läs rapporter om attack simulering, svar och tillhör ande utbildning |

## <a name="attack-simulation-administrator"></a>Administratör för attack simulering

Användare med den här rollen kan skapa och hantera alla aspekter av genereringen av attack simulering, start/schemaläggning av en simulering och granskning av simulerings resultat. Medlemmar i den här rollen har den här åtkomsten för alla simuleringar i klienten.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Office365. protectionCenter/attackSimulator/nytto Last/allProperties/allTasks | Skapa och hantera angrepps nytto laster i angrepps Simulator |
> | Microsoft. Office365. protectionCenter/attackSimulator/Reports/allProperties/Read | Läs rapporter om attack simulering, svar och tillhör ande utbildning |
> | Microsoft. Office365. protectionCenter/attackSimulator/simulering/allProperties/allTasks | Skapa och hantera mallar för attack simulering i angrepps Simulator |

## <a name="authentication-administrator"></a>Administratör för autentisering

Användare med den här rollen kan ange eller återställa valfri autentiseringsmetod (inklusive lösen ord) för icke-administratörer och vissa roller. Autentiserings administratörer kan kräva att användare som inte är administratörer eller som har tilldelats vissa roller omregistrerar sig mot befintliga autentiseringsuppgifter som inte är lösen ord (till exempel MFA eller FIDO) och kan även återkalla att **Spara MFA på enheten**, vilket gör att MFA används vid nästa inloggning. En lista över de roller som en autentiserings administratör kan läsa eller uppdatera autentiseringsmetoder finns i [behörigheter för lösen ords återställning](#password-reset-permissions).

Rollen [Privileged Authentication Administrator](#privileged-authentication-administrator) har behörighet att framtvinga omregistrering och Multi-Factor Authentication för alla användare.

[Administratörs rollen autentiseringsprincip](#authentication-policy-administrator) har behörighet att ange innehavarens autentiseringsmetod för autentisering som avgör vilka metoder som varje användare kan registrera sig och använda.

| Roll | Hantera användarens autentiseringsmetoder | Hantera MFA per användare | Hantera MFA-inställningar | Hantera principen för auth-metoden | Hantera lösen ords skydds princip |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Administratör för autentisering | Ja för vissa användare (se ovan) | Ja för vissa användare (se ovan) | Inga | Inga | Inga |
| Administratör för privilegie rad autentisering| Ja för alla användare | Ja för alla användare | Inga | Inga | Inga |
| Administratör för autentiseringsprincip | Inga |Inga | Ja | Ja | Ja |

> [!IMPORTANT]
> Användare med den här rollen kan ändra autentiseringsuppgifter för personer som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i och utanför Azure Active Directory. Att ändra autentiseringsuppgifterna för en användare kan innebära att du förutsätter att användarens identitet och behörigheter. Exempel:
>
>* Program registrering och företags program ägare, som kan hantera autentiseringsuppgifter för appar som de äger. De här apparna kan ha privilegierade behörigheter i Azure AD och inte beviljas till autentiserings administratörer. Via den här sökvägen kan en administratörs administratör anta identiteten för en program ägare och sedan ytterligare utgå från identiteten för ett privilegie rad program genom att uppdatera autentiseringsuppgifterna för programmet.
>* Azure-Prenumerationens ägare, som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i Azure.
>* Säkerhets grupp och Microsoft 365 grupp ägare, som kan hantera grupp medlemskap. Dessa grupper kan ge åtkomst till känslig eller privat information eller kritisk konfiguration i Azure AD och någon annan stans.
>* Administratörer i andra tjänster utanför Azure AD, till exempel Exchange Online, Office Security och Compliance Center och personal system.
>* Icke-administratörer som chefer, juridiska konsulter och personal anställda som kan ha till gång till känslig eller privat information.

> [!IMPORTANT]
> Den här rollen kan för närvarande inte hantera MFA per användare i den äldre hanterings portalen för MFA. Samma funktioner kan utföras med hjälp av Azure AD PowerShell [-modulen set-MsolUser](/powershell/module/msonline/set-msoluser) kommandot.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/Users/invalidateAllRefreshTokens | Framtvinga utloggning genom att invalidera token för användar uppdatering |
> | Microsoft. Directory/Users/strongAuthentication/Update | Uppdatera egenskapen för stark autentisering för användare |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="authentication-policy-administrator"></a>Administratör för autentiseringsprincip

Användare med den här rollen kan konfigurera principen för autentiserings metoder, MFA MFA-inställningar och lösen ords skydds princip. Den här rollen ger behörighet att hantera inställningar för lösen ords skydd: konfigurationer för smart utelåsning och uppdatering av listan med anpassade förbjudna lösen ord.

Administratörs rollerna för [autentisering administratör](#authentication-administrator) och [privilegie rad autentisering](#privileged-authentication-administrator) har behörighet att hantera registrerade autentiseringsmetoder för användare och kan framtvinga omregistrering och Multi-Factor Authentication för alla användare.

| Roll | Hantera användarens autentiseringsmetoder | Hantera MFA per användare | Hantera MFA-inställningar | Hantera principen för auth-metoden | Hantera lösen ords skydds princip |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Administratör för autentisering | Ja för vissa användare (se ovan) | Ja för vissa användare (se ovan) | Inga | Inga | Inga |
| Administratör för privilegie rad autentisering| Ja för alla användare | Ja för alla användare | Inga | Inga | Inga |
| Administratör för autentiseringsprincip | Inga | Inga | Ja | Ja | Ja |

> [!IMPORTANT]
> Den här rollen kan för närvarande inte hantera MFA-inställningar i den äldre hanterings portalen för MFA.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/Organization/strongAuthentication/Update | Uppdatera egenskaper för starkt auth för en organisation |
> | Microsoft. Directory/userCredentialPolicies/Create | Skapa principer för autentiseringsuppgifter för användare |
> | Microsoft. Directory/userCredentialPolicies/Delete | Ta bort Credential-principer för användare |
> | Microsoft. Directory/userCredentialPolicies/standard/Read | Läsa standard egenskaper för principer för autentiseringsuppgifter för användare |
> | Microsoft. Directory/userCredentialPolicies/Owners/Read | Läs ägare till principer för autentiseringsuppgifter för användare |
> | Microsoft. Directory/userCredentialPolicies/policyAppliedTo/Read | Läs princip. navigerings länk för appliesTo |
> | Microsoft. Directory/userCredentialPolicies/Basic/Update | Uppdatera Basic-principer för användare |
> | Microsoft. Directory/userCredentialPolicies/Owners/Update | Uppdatera ägare till principer för autentiseringsuppgifter för användare |
> | Microsoft. Directory/userCredentialPolicies/tenantDefault/Update | Uppdatera princip. isOrganizationDefault-egenskap |

## <a name="azure-ad-joined-device-local-administrator"></a>Lokal administratör för Azure AD-ansluten enhet

Den här rollen är endast tillgänglig för tilldelning som en ytterligare lokal administratör i [enhets inställningar](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Användare med den här rollen blir lokala dator administratörer på alla Windows 10-enheter som är anslutna till Azure Active Directory. De har inte möjlighet att hantera enheters objekt i Azure Active Directory.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/groupSettings/standard/Read | Läsa grundläggande egenskaper för grupp inställningar |
> | Microsoft. Directory/groupSettingTemplates/standard/Read | Läsa grundläggande egenskaper för mallar för grupp inställningar |

## <a name="azure-devops-administrator"></a>Azure DevOps-administratör

Användare med den här rollen kan hantera Azure DevOps-principen för att begränsa skapandet av nya Azure DevOps-organisationer till en uppsättning konfigurerbara användare eller grupper. Användare med den här rollen kan hantera den här principen via valfri Azure DevOps-organisation som backas upp av företagets Azure AD-organisation. Den här rollen beviljar inga andra Azure DevOps-särskilda behörigheter (till exempel projekt samlings administratörer) i någon av Azure DevOps-organisationerna som backas upp av företagets Azure AD-organisation.

Alla Enterprise Azure DevOps-principer kan hanteras av användare i den här rollen.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Azure. devOps/upplånade/allTasks | Läsa och konfigurera Azure-DevOps |

## <a name="azure-information-protection-administrator"></a>Azure Information Protection administratör

Användare med den här rollen har alla behörigheter i Azure Information Protections tjänsten. Med den här rollen kan du konfigurera etiketter för Azure Information Protection principen, hantera skydds mallar och aktivera skydd. Den här rollen beviljar inte några behörigheter i identitets skydds centret, Privileged Identity Management, övervaka Microsoft 365 Service Health eller Office 365 Security & Compliance Center.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Azure. informationProtection/upplånade/allTasks | Hantera alla aspekter av Azure Information Protection |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="b2c-ief-keyset-administrator"></a>B2C IEF Keys-administratör

Användaren kan skapa och hantera princip nycklar och hemligheter för token-kryptering, token-signaturer och anspråks kryptering/dekryptering. Genom att lägga till nya nycklar i befintliga nyckel behållare kan denna begränsade administratör förnya hemligheter vid behov utan att påverka befintliga program. Den här användaren kan se det fullständiga innehållet i dessa hemligheter och deras förfallo datum även efter att de har skapats.

> [!IMPORTANT]
> Detta är en känslig roll. Administratörs rollen för nyckel uppsättningen bör granskas noggrant och tilldelas till vård under för produktion och produktion.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/b2cTrustFrameworkKeySet/allProperties/allTasks | Läsa och uppdatera alla egenskaper för Auktoriseringsprinciper |

## <a name="b2c-ief-policy-administrator"></a>B2C IEF-princip administratör

Användare med den här rollen kan skapa, läsa, uppdatera och ta bort alla anpassade principer i Azure AD B2C och har därför fullständig kontroll över identitets miljö ramverket i relevant Azure AD B2C organisation. Genom att redigera principer kan den här användaren upprätta direkt Federation med externa identitets leverantörer, ändra katalog schema, ändra allt innehåll som riktas mot användare (HTML, CSS, Java Script), ändra kraven för att slutföra en autentisering, skapa nya användare, skicka användar data till externa system, inklusive fullständiga migreringar och redigera all användar information, inklusive känsliga fält som lösen ord och telefonnummer. Den här rollen kan däremot inte ändra krypterings nycklarna eller redigera hemligheterna som används för federation i organisationen.

> [!IMPORTANT]
> IEF princip administratör för B2 är en mycket känslig roll som bör tilldelas mycket begränsad till organisationer i produktion. Aktiviteter av dessa användare bör granskas noga, särskilt för organisationer i produktion.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/b2cTrustFrameworkPolicy/allProperties/allTasks | Läsa och konfigurera nyckel uppsättningar i Azure Active Directory B2C |

## <a name="billing-administrator"></a>Faktureringsadministratör

Gör inköp, hanterar prenumerationer, hanterar supportärenden och övervakar tjänstens hälsa.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/Organization/Basic/Update | Uppdatera grundläggande egenskaper för organisation |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Commerce. debitering/allTasks | Hantera alla aspekter av Office 365-fakturering |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="cloud-application-administrator"></a>Molnprogramadministratör

Användare i den här rollen har samma behörigheter som program administratörs rollen, förutom möjligheten att hantera programproxyn. Den här rollen ger möjlighet att skapa och hantera alla aspekter av företags program och program registreringar. Användare som har tilldelats den här rollen läggs inte till som ägare när de skapar nya program registreringar eller företags program.

Den här rollen ger också möjlighet att godkänna delegerade behörigheter och program behörigheter, med undantag för program behörigheter för både Microsoft Graph och Azure AD Graph.

> [!IMPORTANT]
> Detta undantag innebär att du fortfarande kan godkänna program behörigheter för _andra_ appar (till exempel appar som inte kommer från Microsoft eller appar som du har registrerat). Du kan fortfarande _begära_ dessa behörigheter som en del av appens registrering, men _beviljar_ (som godkänner) dessa behörigheter kräver en mer privilegie rad administratör, t. ex. global administratör.
>
>Den här rollen ger möjlighet att hantera programautentiseringsuppgifter. Användare som har tilldelats den här rollen kan lägga till autentiseringsuppgifter i ett program och använda dessa autentiseringsuppgifter för att personifiera programmets identitet. Om programmets identitet har beviljats åtkomst till en resurs, till exempel möjligheten att skapa eller uppdatera användare eller andra objekt, kan en användare som tilldelats rollen utföra dessa åtgärder samtidigt som programmet personifieras. Den här möjligheten att personifiera programmets identitet kan vara en höjning av privilegier över vad användaren kan göra via sina roll tilldelningar. Det är viktigt att förstå att tilldelning av en användare till rollen program administratör ger dem möjlighet att personifiera ett programs identitet.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/program/skapa | Skapa alla typer av program |
> | Microsoft. Directory/Applications/Delete | Ta bort alla typer av program |
> | Microsoft. Directory/Applications/appRoles/Update | Uppdatera egenskapen appRoles för alla typer av program |
> | Microsoft. Directory/program/mål grupp/uppdatering | Uppdatera egenskapen Audience för program |
> | Microsoft. Directory/program/autentisering/uppdatering | Uppdatera autentiseringen för alla typer av program |
> | Microsoft. Directory/Applications/Basic/Update | Uppdatera grundläggande egenskaper för program |
> | Microsoft. Directory/program/autentiseringsuppgifter/uppdatera | Uppdatera programautentiseringsuppgifter |
> | Microsoft. Directory/program/ägare/uppdatering | Uppdatera ägare av program |
> | Microsoft. Directory/program/behörigheter/uppdatera | Uppdatera exponerade behörigheter och nödvändiga behörigheter för alla typer av program |
> | Microsoft. Directory/Applications/policies/Update | Uppdatera principer för program |
> | Microsoft. Directory/program/verifiering/uppdatering | Uppdatera applicationsverification-egenskap |
> | Microsoft. Directory/program/synkronisering/standard/Read | Läs etablerings inställningar som är associerade med programobjektet |
> | Microsoft. Directory/applicationTemplates/instansiera | Instansiera Galleri program från programmallar |
> | Microsoft. Directory/auditLogs/allProperties/Read | Läsa alla egenskaper för gransknings loggar, inklusive privilegierade egenskaper |
> | Microsoft. Directory/oAuth2PermissionGrants/allProperties/allTasks | Skapa och ta bort OAuth 2,0-behörighet bidrag och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/applicationPolicies/Create | Skapa användnings principer |
> | Microsoft. Directory/applicationPolicies/Delete | Ta bort användnings principer |
> | Microsoft. Directory/applicationPolicies/standard/Read | Läsa standard egenskaper för användnings principer |
> | Microsoft. Directory/applicationPolicies/Owners/Read | Läs ägare för användnings principer |
> | Microsoft. Directory/applicationPolicies/policyAppliedTo/Read | Läsa användnings principer tillämpade på objekt listan |
> | Microsoft. Directory/applicationPolicies/Basic/Update | Uppdatera standard egenskaper för användnings principer |
> | Microsoft. Directory/applicationPolicies/Owners/Update | Uppdatera egenskapen owner för användnings principer |
> | Microsoft. Directory/provisioningLogs/allProperties/Read | Läs alla egenskaper för etablerings loggar |
> | Microsoft. Directory/Service princip ALS/Create | Skapa tjänsthuvudnamn |
> | Microsoft. Directory/Service princip ALS/Delete | Ta bort tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Disable | Inaktivera tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Enable | Aktivera tjänstens huvudnamn |
> | Microsoft. Directory/Service princip ALS/getPasswordSingleSignOnCredentials | Hantera autentiseringsuppgifter för enkel inloggning med lösen ord för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/synchronizationCredentials/Manage | Hantera program etablerings hemligheter och autentiseringsuppgifter |
> | Microsoft. Directory/Service princip ALS/synchronizationJobs/Manage | Starta, starta om och pausa Application Provisioning synkroniseringen-jobb |
> | Microsoft. Directory/Service princip ALS/synchronizationSchema/Manage | Skapa och hantera synkroniseringen-jobb och-schema för program etablering |
> | Microsoft. Directory/Service princip ALS/managePasswordSingleSignOnCredentials | Läs inloggnings uppgifter för enkel inloggning för lösen ord för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/managePermissionGrantsForAll. Microsoft-Application-admin | Bevilja medgivande för program behörigheter och delegerade behörigheter åt alla användare eller alla användare, förutom program behörigheter för Microsoft Graph och Azure AD Graph |
> | Microsoft. Directory/Service princip ALS/appRoleAssignedTo/Update | Uppdatera roll tilldelningar för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/målgrupp/uppdatera | Uppdatera mål egenskaper för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Authentication/Update | Uppdatera autentiserings egenskaper för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Basic/Update | Uppdatera grundläggande egenskaper för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/credentials/Update | Uppdatera autentiseringsuppgifter för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Owners/Update | Uppdatera ägare till tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Permissions/Update | Uppdatera behörigheter för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/policies/Update | Uppdatera principer för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/tag/Update | Uppdatera egenskapen tag för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Synchronization/standard/Read | Läs etablerings inställningar som är associerade med tjänstens huvud namn |
> | Microsoft. Directory/signInReports/allProperties/Read | Läs alla egenskaper för inloggnings rapporter, inklusive privilegierade egenskaper |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="cloud-device-administrator"></a>Moln enhets administratör

Användare med den här rollen kan aktivera, inaktivera och ta bort enheter i Azure AD och läsa Windows 10 BitLocker-nycklar (om sådana finns) i Azure Portal. Rollen ger inte behörighet att hantera andra egenskaper på enheten.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/auditLogs/allProperties/Read | Läsa alla egenskaper för gransknings loggar, inklusive privilegierade egenskaper |
> | Microsoft. Directory/bitlockerKeys/Key/Read | Läs BitLocker-metadata och-nyckel på enheter |
> | Microsoft. Directory/Devices/Delete | Ta bort enheter från Azure AD |
> | Microsoft. Directory/enheter/inaktivera | Inaktivera enheter i Azure AD |
> | Microsoft. Directory/Devices/Enable | Aktivera enheter i Azure AD |
> | Microsoft. Directory/Devices/extensionAttributes/Update | Uppdatera alla värden för egenskapen Devices. extensionAttributes |
> | Microsoft. Directory/deviceManagementPolicies/standard/Read | Läsa standard egenskaper för användnings principer för enhets hantering |
> | Microsoft. Directory/deviceManagementPolicies/Basic/Update | Uppdatera grundläggande egenskaper för användnings principer för enhets hantering |
> | Microsoft. Directory/deviceRegistrationPolicy/standard/Read | Läsa standard egenskaper för enhets registrerings principer |
> | Microsoft. Directory/deviceRegistrationPolicy/Basic/Update | Uppdatera grundläggande egenskaper för enhets registrerings principer |
> | Microsoft. Directory/signInReports/allProperties/Read | Läs alla egenskaper för inloggnings rapporter, inklusive privilegierade egenskaper |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |

## <a name="compliance-administrator"></a>Efterlevnadsadministratör

Användare med den här rollen har behörighet att hantera kompatibla funktioner i Microsoft 365 Compliance Center, Microsoft 365 administrations Center, Azure och Office 365 Security & Compliance Center. Tilldelningar kan också hantera alla funktioner i administrations centret för Exchange och team & Skype för företag-administratörer och skapa support biljetter för Azure och Microsoft 365. Mer information finns på [Microsoft 365 admin-roller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

I | Kan göra
----- | ----------
[Microsoft 365 Compliance Center](https://protection.office.com) | Skydda och hantera din organisations data mellan Microsoft 365 tjänster<br>Hantera aviseringar om efterlevnad
[Compliance Manager (Efterlevnadshanteraren)](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Spåra, tilldela och verifiera organisationens uppgifter för regelefterlevnad
[Office 365 säkerhets & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Hantera data styrning<br>Utföra juridisk information och data undersökning<br>Hantera begäran om data subjekt<br><br>Den här rollen har samma behörigheter som [mängdsredigeraren](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) i Office 365 Security & rollbaserade åtkomst kontroll för Compliance Center.
[Intune](/intune/role-based-access-control) | Visa alla gransknings data i Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Har Läs behörighet och kan hantera aviseringar<br>Kan skapa och ändra fil principer och tillåta fil styrnings åtgärder<br>Kan visa alla inbyggda rapporter under Datahantering

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Directory/entitlementManagement/allProperties/Read | Läsa alla egenskaper i hantering av Azure AD-berättigande |
> | Microsoft. Office365. complianceManager/uplånar/allTasks | Hantera alla aspekter av Compliance Manager för Office 365 |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="compliance-data-administrator"></a>Efterlevnadsdataadministratör

Användare med den här rollen har behörighet att spåra data i Microsoft 365 Compliance Center, Microsoft 365 administrations Center och Azure. Användare kan också spåra efterlevnadsprinciper i administrations centret för Exchange, Compliance Manager och Teams & Skype för företag administrations Center och skapa support biljetter för Azure och Microsoft 365. [Den här dokumentationen](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) innehåller information om skillnaderna mellan kompatibilitetens administratör och kompatibilitets data administratör.

I | Kan göra
----- | ----------
[Microsoft 365 Compliance Center](https://protection.office.com) | Övervaka efterlevnadsprinciper relaterade principer i Microsoft 365 Services<br>Hantera aviseringar om efterlevnad
[Compliance Manager (Efterlevnadshanteraren)](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Spåra, tilldela och verifiera organisationens uppgifter för regelefterlevnad
[Office 365 säkerhets & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Hantera data styrning<br>Utföra juridisk information och data undersökning<br>Hantera begäran om data subjekt<br><br>Den här rollen har samma behörigheter som [mängdsredigeraren för administratörs](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) behörighet i Office 365 säkerhets & rollbaserade åtkomst kontroll för Compliance Center.
[Intune](/intune/role-based-access-control) | Visa alla gransknings data i Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Har Läs behörighet och kan hantera aviseringar<br>Kan skapa och ändra fil principer och tillåta fil styrnings åtgärder<br>Kan visa alla inbyggda rapporter under Datahantering

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/cloudAppSecurity/allProperties/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standard egenskaper i Microsoft Cloud App Security |
> | Microsoft. Azure. informationProtection/upplånade/allTasks | Hantera alla aspekter av Azure Information Protection |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Office365. complianceManager/uplånar/allTasks | Hantera alla aspekter av Compliance Manager för Office 365 |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="conditional-access-administrator"></a>Administratör för villkorsstyrd åtkomst

Användare med den här rollen kan hantera inställningar för Azure Active Directory villkorlig åtkomst.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/conditionalAccessPolicies/Create | Skapa principer för villkorsstyrd åtkomst |
> | Microsoft. Directory/conditionalAccessPolicies/Delete | Ta bort principer för villkorlig åtkomst |
> | Microsoft. Directory/conditionalAccessPolicies/standard/Read | Läs policies. conditionalAccess-egenskap |
> | Microsoft. Directory/conditionalAccessPolicies/Owners/Read | Läs policies. conditionalAccess-egenskap |
> | Microsoft. Directory/conditionalAccessPolicies/policyAppliedTo/Read | Läs policies. conditionalAccess-egenskap |
> | Microsoft. Directory/conditionalAccessPolicies/Basic/Update | Uppdatera grundläggande egenskaper för principer för villkorlig åtkomst |
> | Microsoft. Directory/conditionalAccessPolicies/Owners/Update | Uppdatera policys. conditionalAccess-egenskapen |
> | Microsoft. Directory/conditionalAccessPolicies/tenantDefault/Update | Uppdatera policys. conditionalAccess-egenskapen |
> | Microsoft. Directory/crossTenantAccessPolicies/Create | Skapa åtkomst principer för flera innehavare |
> | Microsoft. Directory/crossTenantAccessPolicies/Delete | Ta bort åtkomst principer för flera innehavare |
> | Microsoft. Directory/crossTenantAccessPolicies/standard/Read | Läs grundläggande egenskaper för åtkomst principer för flera innehavare |
> | Microsoft. Directory/crossTenantAccessPolicies/Owners/Read | Läs ägare till åtkomst principer för flera innehavare |
> | Microsoft. Directory/crossTenantAccessPolicies/policyAppliedTo/Read | Läs egenskapen policyAppliedTo för åtkomst principer för flera innehavare |
> | Microsoft. Directory/crossTenantAccessPolicies/Basic/Update | Uppdatera grundläggande egenskaper för åtkomst principer för flera innehavare |
> | Microsoft. Directory/crossTenantAccessPolicies/Owners/Update | Uppdatera ägare av åtkomst principer för flera innehavare |
> | Microsoft. Directory/crossTenantAccessPolicies/tenantDefault/Update | Uppdatera standard klient organisationen för åtkomst principer mellan klienter |

## <a name="customer-lockbox-access-approver"></a>Åtkomst god kännare för kund lås

Hanterar [Customer lockbox begär Anden](/office365/admin/manage/customer-lockbox-requests) i din organisation. De får e-postaviseringar för Customer Lockbox begär Anden och kan godkänna och Neka förfrågningar från Microsoft 365 administrations Center. De kan också aktivera eller inaktivera funktionen för Customer Lockbox. Endast globala administratörer kan återställa lösen orden för personer som har tilldelats den här rollen.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Office365. lock/upplånade/allTasks | Hantera alla aspekter av Customer Lockbox |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="desktop-analytics-administrator"></a>Administratör för Skriv bords analys

Användare med den här rollen kan hantera Desktop Analytics-och Office-anpassning & princip tjänster. För Skriv bords analys omfattar detta möjlighet att visa till gångs lager, skapa distributions planer, Visa distribution och hälso status. Den här rollen ger användare möjlighet att hantera Office-principer för tjänsten för anpassning av Office-&.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Office365. desktopAnalytics/uplånar/allTasks | Hantera alla aspekter av Skriv bords analys |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="directory-readers"></a>Katalog läsare

Användare med den här rollen kan läsa grundläggande katalog information. Den här rollen ska användas för:

* Att bevilja en speciell uppsättning gäst användare Läs åtkomst i stället för att ge dem till alla gäst användare.
* Att bevilja en särskild uppsättning användare som inte är administratörs åtkomst till Azure Portal när "begränsa åtkomst till Azure AD-portalen enbart till administratörer" är inställd på "Ja".
* Beviljar tjänstens huvud namn åtkomst till katalogen där Directory. Read. all inte är ett alternativ.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/administrativeUnits/standard/Read | Läsa grundläggande egenskaper för administrativa enheter |
> | Microsoft. Directory/administrativeUnits/members/Read | Läsa medlemmar i administrativa enheter |
> | Microsoft. Directory/program/standard/Read | Läsa standard egenskaper för program |
> | Microsoft. Directory/program/ägare/läsa | Läs program ägare |
> | Microsoft. Directory/Applications/policies/Read | Läsa principer för program |
> | Microsoft. Directory/Contacts/standard/Read | Läsa grundläggande egenskaper för kontakter i Azure AD |
> | Microsoft. Directory/Contacts/memberOf/läsa | Läs grupp medlemskapet för alla kontakter i Azure AD |
> | Microsoft. Directory/kontrakt/standard/Read | Läsa grundläggande egenskaper för partner kontrakt |
> | Microsoft. Directory/enheter/standard/Read | Läsa grundläggande egenskaper för enheter |
> | Microsoft. Directory/Devices/memberOf/Read | Läsa enhets medlemskap |
> | Microsoft. Directory/Devices/registeredOwners/Read | Läs registrerade ägare av enheter |
> | Microsoft. Directory/Devices/registeredUsers/Read | Läs registrerade användare av enheter |
> | Microsoft. Directory/directoryRoles/standard/Read | Uppdatera grundläggande egenskaper i Azure AD-roller |
> | Microsoft. Directory/directoryRoles/eligibleMembers/Read | Läs de berättigade medlemmarna i Azure AD-roller |
> | Microsoft. Directory/directoryRoles/members/Read | Läs alla medlemmar i Azure AD-roller |
> | Microsoft. Directory/Domains/standard/Read | Läsa grundläggande egenskaper för domäner |
> | Microsoft. Directory/grupper/standard/Read | Läsa grundläggande egenskaper för grupper |
> | Microsoft. Directory/grupper/appRoleAssignments/Read | Läs program roll tilldelningar för grupper |
> | Microsoft. Directory/grupper/memberOf/läsa | Läs de grupper där en grupp är medlem i Azure AD |
> | Microsoft. Directory/grupper/medlemmar/läsa | Läsa medlemmar i grupper |
> | Microsoft. Directory/grupper/ägare/läsa | Läs grupp ägare |
> | Microsoft. Directory/grupper/inställningar/Läs | Läs inställningar för grupper |
> | Microsoft. Directory/groupSettings/standard/Read | Läsa grundläggande egenskaper för grupp inställningar |
> | Microsoft. Directory/groupSettingTemplates/standard/Read | Läsa grundläggande egenskaper för mallar för grupp inställningar |
> | Microsoft. Directory/oAuth2PermissionGrants/standard/Read | Läsa grundläggande egenskaper för behörighets bidrag för OAuth 2,0 |
> | Microsoft. Directory/Organization/standard/Read | Läsa grundläggande egenskaper för en organisation |
> | Microsoft. Directory/Organization/trustedCAsForPasswordlessAuth/Read | Läsa betrodda certifikat utfärdare för autentisering utan lösen ord |
> | Microsoft. Directory/applicationPolicies/standard/Read | Läsa standard egenskaper för användnings principer |
> | Microsoft. Directory/roleAssignments/standard/Read | Läsa grundläggande egenskaper för roll tilldelningar |
> | Microsoft. Directory/roleDefinitions/standard/Read | Läsa grundläggande egenskaper för roll definitioner |
> | Microsoft. Directory/Service princip ALS/appRoleAssignedTo/Read | Läs roll tilldelningar för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/appRoleAssignments/Read | Läs roll tilldelningar som tilldelats tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/standard/Read | Läs grundläggande egenskaper för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/memberOf/läsa | Läs grupp medlemskapen för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/oAuth2PermissionGrants/Read | Läs delegerad behörighet beviljar för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Owners/Read | Läs ägare till tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/ownedObjects/Read | Läsa ägda objekt av tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/policies/Read | Läs principer för tjänstens huvud namn |
> | Microsoft. Directory/subscribedSkus/standard/Read | Läsa grundläggande egenskaper för prenumerationer |
> | Microsoft. Directory/Users/standard/Read | Läsa grundläggande egenskaper för användare |
> | Microsoft. Directory/Users/appRoleAssignments/Read | Läs program roll tilldelningar för användare |
> | Microsoft. Directory/Users/directReports/Read | Läs direkt rapporterna för användare |
> | Microsoft. Directory/Users/Manager/Read | Läs chef för användare |
> | Microsoft. Directory/Users/memberOf/Read | Läs grupp medlemskap för användare |
> | Microsoft. Directory/Users/oAuth2PermissionGrants/Read | Läs delegerade behörighets bidrag för användare |
> | Microsoft. Directory/Users/ownedDevices/Read | Läsa ägda enheter av användare |
> | Microsoft. Directory/Users/ownedObjects/Read | Läsa ägda objekt av användare |
> | Microsoft. Directory/Users/registeredDevices/Read | Läsa registrerade enheter med användare |

## <a name="directory-synchronization-accounts"></a>Konton för katalog synkronisering

Använd inte. Den här rollen tilldelas automatiskt till den Azure AD Connect tjänsten och är inte avsedd eller stöds inte för någon annan användning.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/program/skapa | Skapa alla typer av program |
> | Microsoft. Directory/Applications/Delete | Ta bort alla typer av program |
> | Microsoft. Directory/Applications/appRoles/Update | Uppdatera egenskapen appRoles för alla typer av program |
> | Microsoft. Directory/program/mål grupp/uppdatering | Uppdatera egenskapen Audience för program |
> | Microsoft. Directory/program/autentisering/uppdatering | Uppdatera autentiseringen för alla typer av program |
> | Microsoft. Directory/Applications/Basic/Update | Uppdatera grundläggande egenskaper för program |
> | Microsoft. Directory/program/autentiseringsuppgifter/uppdatera | Uppdatera programautentiseringsuppgifter |
> | Microsoft. Directory/program/ägare/uppdatering | Uppdatera ägare av program |
> | Microsoft. Directory/program/behörigheter/uppdatera | Uppdatera exponerade behörigheter och nödvändiga behörigheter för alla typer av program |
> | Microsoft. Directory/Applications/policies/Update | Uppdatera principer för program |
> | Microsoft. Directory/Organization/dirSync/Update | Uppdatera egenskapen för organisations katalogens synkronisering |
> | Microsoft. Directory/principer/skapa | Skapa principer i Azure AD |
> | Microsoft. Directory/principer/Delete | Ta bort principer i Azure AD |
> | Microsoft. Directory/policys/standard/Read | Läsa grundläggande egenskaper för principer |
> | Microsoft. Directory/principer/ägare/läsa | Läs ägare till principer |
> | Microsoft. Directory/policys/policyAppliedTo/Read | Läs policies. policyAppliedTo-egenskap |
> | Microsoft. Directory/policies/Basic/Update | Uppdatera grundläggande egenskaper för principer |
> | Microsoft. Directory/principer/ägare/uppdatering | Uppdatera ägare av principer |
> | Microsoft. Directory/policys/tenantDefault/Update | Uppdatera standard organisations principer |
> | Microsoft. Directory/Service princip ALS/Create | Skapa tjänsthuvudnamn |
> | Microsoft. Directory/Service princip ALS/Delete | Ta bort tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Enable | Aktivera tjänstens huvudnamn |
> | Microsoft. Directory/Service princip ALS/Disable | Inaktivera tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/getPasswordSingleSignOnCredentials | Hantera autentiseringsuppgifter för enkel inloggning med lösen ord för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/managePasswordSingleSignOnCredentials | Läs inloggnings uppgifter för enkel inloggning för lösen ord för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/appRoleAssignedTo/Read | Läs roll tilldelningar för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/appRoleAssignments/Read | Läs roll tilldelningar som tilldelats tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/standard/Read | Läs grundläggande egenskaper för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/memberOf/läsa | Läs grupp medlemskapen för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/oAuth2PermissionGrants/Read | Läs delegerad behörighet beviljar för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Owners/Read | Läs ägare till tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/ownedObjects/Read | Läsa ägda objekt av tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/policies/Read | Läs principer för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/appRoleAssignedTo/Update | Uppdatera roll tilldelningar för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/målgrupp/uppdatera | Uppdatera mål egenskaper för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Authentication/Update | Uppdatera autentiserings egenskaper för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Basic/Update | Uppdatera grundläggande egenskaper för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/credentials/Update | Uppdatera autentiseringsuppgifter för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Owners/Update | Uppdatera ägare till tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Permissions/Update | Uppdatera behörigheter för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/policies/Update | Uppdatera principer för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/tag/Update | Uppdatera egenskapen tag för tjänstens huvud namn |

## <a name="directory-writers"></a>Katalog skrivare

Användare med den här rollen kan läsa och uppdatera grundläggande information om användare, grupper och tjänstens huvud namn. Tilldela endast den här rollen till program som inte stöder [medgivande ramverket](../develop/quickstart-register-app.md). De ska inte tilldelas till några användare.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/grupper/assignLicense | Tilldela produkt licenser till grupper för gruppbaserad licensiering |
> | Microsoft. Directory/grupper/skapa | Skapa grupper med undantag för roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper/reprocessLicenseAssignment | Ombearbeta licens tilldelningar för gruppbaserad licensiering |
> | Microsoft. Directory/grupper/Basic/Update | Uppdatera grundläggande egenskaper för grupper, exklusive roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper/klassificering/uppdatera | Uppdatera klassificerings egenskapen för grupper, exklusive roll tilldelnings bara grupper |
> | Microsoft. Directory/Groups/dynamicMembershipRule/Update | Uppdatera regel för dynamiskt medlemskap i grupper, exklusive roll tilldelnings bara grupper |
> | Microsoft. Directory/Groups/groupType/Update | Uppdatera egenskapen groupType för en grupp |
> | Microsoft. Directory/grupper/medlemmar/uppdatera | Uppdatera medlemmar i grupper, exklusive roll tilldelnings bara grupper |
> | Microsoft. Directory/Groups/onPremWriteBack/Update | Uppdatera Azure AD-grupper så att de skrivs tillbaka till lokalt |
> | Microsoft. Directory/grupper/ägare/uppdatera | Uppdatera ägare av grupper, exklusive roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper/inställningar/uppdatera | Uppdatera inställningar för grupper |
> | Microsoft. Directory/grupper/synlighet/uppdatera | Uppdatera Synlighets egenskapen för grupper |
> | Microsoft. Directory/groupSettings/Create | Skapa gruppinställningar |
> | Microsoft. Directory/groupSettings/Delete | Ta bort gruppinställningar |
> | Microsoft. Directory/groupSettings/Basic/Update | Uppdatera grundläggande egenskaper för grupp inställningar |
> | Microsoft. Directory/oAuth2PermissionGrants/Create | Skapa behörighets bidrag för OAuth 2,0 |
> | Microsoft. Directory/oAuth2PermissionGrants/Basic/Update | Uppdatera behörighets bidrag för OAuth 2,0 |
> | Microsoft. Directory/Service princip ALS/synchronizationCredentials/Manage | Hantera program etablerings hemligheter och autentiseringsuppgifter |
> | Microsoft. Directory/Service princip ALS/synchronizationJobs/Manage | Starta, starta om och pausa Application Provisioning synkroniseringen-jobb |
> | Microsoft. Directory/Service princip ALS/synchronizationSchema/Manage | Skapa och hantera synkroniseringen-jobb och-schema för program etablering |
> | Microsoft. Directory/Service princip ALS/managePermissionGrantsForGroup. Microsoft-all-Application-Permissions | Bevilja en tjänst Principal direkt åtkomst till en grupps data |
> | Microsoft. Directory/Service princip ALS/appRoleAssignedTo/Update | Uppdatera roll tilldelningar för tjänstens huvud namn |
> | Microsoft. Directory/Users/assignLicense | Hantera användar licenser |
> | Microsoft. Directory/Users/Create | Lägga till användare |
> | Microsoft. Directory/användare/inaktivera | Inaktivera användare |
> | Microsoft. Directory/Users/Enable | Aktivera användare |
> | Microsoft. Directory/Users/invalidateAllRefreshTokens | Framtvinga utloggning genom att invalidera token för användar uppdatering |
> | Microsoft. Directory/Users/reprocessLicenseAssignment | Ombearbeta licens tilldelningar för användare |
> | Microsoft. Directory/Users/Basic/Update | Uppdatera grundläggande egenskaper för användare |
> | Microsoft. Directory/Users/Manager/Update | Uppdaterings hanteraren för användare |
> | Microsoft. Directory/Users/userPrincipalName/Update | Uppdatera användarens huvud namn |

## <a name="domain-name-administrator"></a>Domän namns administratör

Användare med den här rollen kan hantera (läsa, lägga till, verifiera, uppdatera och ta bort) domän namn. De kan också läsa katalog information om användare, grupper och program, eftersom dessa objekt har domän beroenden. För lokala miljöer kan användare med den här rollen konfigurera domän namn för Federation så att associerade användare alltid autentiseras lokalt. Dessa användare kan sedan logga in på Azure AD-baserade tjänster med sina lokala lösen ord via enkel inloggning. Federations inställningarna måste synkroniseras via Azure AD Connect, så användare har även behörighet att hantera Azure AD Connect.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/Domains/allProperties/allTasks | Skapa och ta bort domäner och läsa och uppdatera alla egenskaper |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |

## <a name="dynamics-365-administrator"></a>Dynamics 365-administratör

Användare med den här rollen har globala behörigheter inom Microsoft Dynamics 365 online när tjänsten finns, samt möjligheten att hantera support biljetter och övervaka tjänstens hälsa. Mer information finns i [använda rollen tjänst administratör för att hantera din Azure AD-organisation](/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

> [!NOTE]
> I Microsoft Graph API och Azure AD PowerShell identifieras den här rollen som "Dynamics 365 service Administrator". Det är "Dynamics 365-administratör" i [Azure Portal](https://portal.azure.com).

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. dynamics365/upplånade/allTasks | Hantera alla aspekter av Dynamics 365 |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="exchange-administrator"></a>Exchange-administratör

Användare med den här rollen har globala behörigheter inom Microsoft Exchange Online när tjänsten finns. Har också möjlighet att skapa och hantera alla Microsoft 365 grupper, hantera support biljetter och övervaka tjänstens hälsa. Mer information finns på [Microsoft 365 admin-roller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> I Microsoft Graph API och Azure AD PowerShell identifieras den här rollen som "Exchange Service Administrator". Det är "Exchange-administratör" i [Azure Portal](https://portal.azure.com). Det är "Exchange Online Administrator" i [administrations centret för Exchange](https://go.microsoft.com/fwlink/p/?LinkID=529144).

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/grupper/hiddenMembers/Read | Läsa dolda medlemmar i en grupp |
> | Microsoft. Directory/grupper. Unified/Create | Skapa Microsoft 365 grupper med undantag för roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper. Unified/Delete | Ta bort Microsoft 365 grupper med undantag av roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper. Unified/Restore | Återställ Microsoft 365 grupper |
> | Microsoft. Directory/Groups. Unified/Basic/Update | Uppdatera grundläggande egenskaper för Microsoft 365 grupper med undantag för roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper. Unified/members/Update | Uppdatera medlemmar i Microsoft 365 grupper med undantag av roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper. Unified/Owners/Update | Uppdatera ägare av Microsoft 365 grupper med undantag för roll tilldelnings bara grupper |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Office365. Exchange/uplånar/Basic/allTasks | Hantera alla aspekter av Exchange Online |
> | Microsoft. Office365. Network/Performance/allProperties/Read | Läs alla egenskaper för nätverks prestanda i Microsoft 365 administrations centret |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. usageReports/uplånar/allProperties/Read | Läs Office 365-användnings rapporter |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="external-id-user-flow-administrator"></a>Användar flödes administratör för externt ID

Användare med den här rollen kan skapa och hantera användar flöden (kallas även "inbyggda" principer) i Azure Portal. Dessa användare kan anpassa HTML/CSS/JavaScript-innehåll, ändra MFA-krav, välja anspråk i token, hantera API-kopplingar och konfigurera sessionsinställningar för alla användar flöden i Azure AD-organisationen. Den här rollen omfattar å andra sidan inte möjligheten att granska användar data eller göra ändringar i de attribut som ingår i organisationens schema. Ändringar av Identity Experience Framework-principer (även kallade anpassade principer) är också utanför den här rollens omfång.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/b2cUserFlow/allProperties/allTasks | Läsa och konfigurera användarattribut i Azure Active Directory B2C |

## <a name="external-id-user-flow-attribute-administrator"></a>Administratör för externt ID-attribut för användar flöde

Användare med den här rollen lägger till eller tar bort anpassade attribut som är tillgängliga för alla användar flöden i Azure AD-organisationen. Det innebär att användare med den här rollen kan ändra eller lägga till nya element i slutanvändarens schema och påverka beteendet för alla användar flöden och indirekt leda till ändringar i vilka data som kan tillfrågas slutanvändare och slutligen skickas som anspråk till program. Den här rollen kan inte redigera användar flöden.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/b2cUserAttribute/allProperties/allTasks | Läsa och konfigurera anpassade principer i Azure Active Directory B2C |

## <a name="external-identity-provider-administrator"></a>Administratör för extern identitetsprovider

Den här administratören hanterar federationen mellan Azure AD-organisationer och externa identitets leverantörer. Med den här rollen kan användarna lägga till nya identitets leverantörer och konfigurera alla tillgängliga inställningar (t. ex. autentiserings Sök väg, tjänst-ID, tilldelade nyckel behållare). Den här användaren kan göra det möjligt för Azure AD-organisationen att lita på autentiseringar från externa identitets leverantörer. Den resulterande påverkan på slut användar upplevelsen beror på organisationens typ:

* Azure AD-organisationer för anställda och partner: tillägget av en Federation (t. ex. med Gmail) påverkar omedelbart alla gäst inbjudningar som ännu inte har lösts in. Se [lägga till Google som en identitets leverantör för B2B-gäst användare](../external-identities/google-federation.md).
* Azure Active Directory B2C organisationer: att lägga till en Federation (till exempel med Facebook eller en annan Azure AD-organisation) påverkar inte omedelbart slut användar flöden förrän identitets leverantören läggs till som ett alternativ i ett användar flöde (även kallat en inbyggd princip). Se [Konfigurera en Microsoft-konto som en identitets leverantör](../../active-directory-b2c/identity-provider-microsoft-account.md) för ett exempel. Om du vill ändra användar flöden krävs den begränsade rollen "B2C User Flow Administrator".

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/Identityprovider/allProperties/allTasks | Läsa och konfigurera identitets leverantörer i Azure Active Directory B2C |

## <a name="global-administrator"></a>Global administratör

Användare med den här rollen har åtkomst till alla administrativa funktioner i Azure Active Directory, samt tjänster som använder Azure Active Directory identiteter som Microsoft 365 Security Center, Microsoft 365 Compliance Center, Exchange Online, SharePoint Online och Skype för företag – online. Dessutom kan globala administratörer [öka sin åtkomst](../../role-based-access-control/elevate-access-global-admin.md) för att hantera alla Azure-prenumerationer och hanterings grupper. Detta gör det möjligt för globala administratörer att få fullständig åtkomst till alla Azure-resurser med respektive Azure AD-klient. Den person som registrerar sig för Azure AD-organisationen blir global administratör. Det kan finnas mer än en global administratör på ditt företag. Globala administratörer kan återställa lösen ordet för alla användare och alla andra administratörer.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/accessReviews/allProperties/allTasks | Skapa och ta bort åtkomst granskningar och läsa och uppdatera alla egenskaper för åtkomst granskningar i Azure AD |
> | Microsoft. Directory/administrativeUnits/allProperties/allTasks | Skapa och hantera administrativa enheter (inklusive medlemmar) |
> | Microsoft. Directory/Applications/allProperties/allTasks | Skapa och ta bort program och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/program/synkronisering/standard/Read | Läs etablerings inställningar som är associerade med programobjektet |
> | Microsoft. Directory/applicationTemplates/instansiera | Instansiera Galleri program från programmallar |
> | Microsoft. Directory/appRoleAssignments/allProperties/allTasks | Skapa och ta bort appRoleAssignments och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/auditLogs/allProperties/Read | Läsa alla egenskaper för gransknings loggar, inklusive privilegierade egenskaper |
> | Microsoft. Directory/authorizationPolicy/allProperties/allTasks | Hantera alla aspekter av Auktoriseringsprinciper |
> | Microsoft. Directory/bitlockerKeys/Key/Read | Läs BitLocker-metadata och-nyckel på enheter |
> | Microsoft. Directory/cloudAppSecurity/allProperties/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standard egenskaper i Microsoft Cloud App Security |
> | Microsoft. Directory/Connectors/skapa | Skapa Application Proxy-kopplingar |
> | Microsoft. Directory/Connectors/allProperties/Read | Läsa alla egenskaper för Application Proxy-kopplingar |
> | Microsoft. Directory/connectorGroups/Create | Skapa anslutnings grupper för Application Proxy |
> | Microsoft. Directory/connectorGroups/Delete | Ta bort kopplings grupper för Application Proxy |
> | Microsoft. Directory/connectorGroups/allProperties/Read | Läs alla egenskaper för kopplings grupper för Application Proxy |
> | Microsoft. Directory/connectorGroups/allProperties/Update | Uppdatera alla egenskaper för Application Proxy Connector-grupper |
> | Microsoft. Directory/Contacts/allProperties/allTasks | Skapa och ta bort kontakter och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/kontrakt/allProperties/allTasks | Skapa och ta bort partner kontrakt och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/Devices/allProperties/allTasks | Skapa och ta bort enheter och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/deviceManagementPolicies/standard/Read | Läsa standard egenskaper för användnings principer för enhets hantering |
> | Microsoft. Directory/deviceManagementPolicies/Basic/Update | Uppdatera grundläggande egenskaper för användnings principer för enhets hantering |
> | Microsoft. Directory/deviceRegistrationPolicy/standard/Read | Läsa standard egenskaper för enhets registrerings principer |
> | Microsoft. Directory/deviceRegistrationPolicy/Basic/Update | Uppdatera grundläggande egenskaper för enhets registrerings principer |
> | Microsoft. Directory/directoryRoles/allProperties/allTasks | Skapa och ta bort katalog roller och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/directoryRoleTemplates/allProperties/allTasks | Skapa och ta bort mallar för Azure AD-roller och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/Domains/allProperties/allTasks | Skapa och ta bort domäner och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/entitlementManagement/allProperties/allTasks | Skapa och ta bort resurser och läsa och uppdatera alla egenskaper i hantering av Azure AD-rättigheter |
> | Microsoft. Directory/Groups/allProperties/allTasks | Skapa och ta bort grupper och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/groupsAssignableToRoles/Create | Skapa rolltilldelningsbara grupper |
> | Microsoft. Directory/groupsAssignableToRoles/Delete | Ta bort roll tilldelnings bara grupper |
> | Microsoft. Directory/groupsAssignableToRoles/Restore | Återställa roll tilldelnings bara grupper |
> | Microsoft. Directory/groupsAssignableToRoles/allProperties/Update | Uppdatera roll tilldelnings bara grupper |
> | Microsoft. Directory/groupSettings/allProperties/allTasks | Skapa och ta bort grupp inställningar och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/groupSettingTemplates/allProperties/allTasks | Skapa och ta bort mallar för grupp inställningar och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/identityProtection/allProperties/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standard egenskaper i Azure AD Identity Protection |
> | Microsoft. Directory/loginOrganizationBranding/allProperties/allTasks | Skapa och ta bort loginTenantBranding och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/oAuth2PermissionGrants/allProperties/allTasks | Skapa och ta bort OAuth 2,0-behörighet bidrag och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/Organization/allProperties/allTasks | Skapa och ta bort organisationer och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/policys/allProperties/allTasks | Skapa och ta bort principer och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/conditionalAccessPolicies/allProperties/allTasks | Hantera alla egenskaper för principer för villkorlig åtkomst |
> | Microsoft. Directory/privilegedIdentityManagement/allProperties/Read | Läs alla resurser i Privileged Identity Management |
> | Microsoft. Directory/provisioningLogs/allProperties/Read | Läs alla egenskaper för etablerings loggar |
> | Microsoft. Directory/roleAssignments/allProperties/allTasks | Skapa och ta bort roll tilldelningar och läsa och uppdatera alla Roll tilldelnings egenskaper |
> | Microsoft. Directory/roleDefinitions/allProperties/allTasks | Skapa och ta bort roll definitioner och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/scopedRoleMemberships/allProperties/allTasks | Skapa och ta bort scopedRoleMemberships och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/serviceAction/activateService | Kan utföra åtgärden "Aktivera tjänst" för en tjänst |
> | Microsoft. Directory/serviceAction/disableDirectoryFeature | Kan utföra tjänst åtgärden "Inaktivera katalog funktion" |
> | Microsoft. Directory/serviceAction/enableDirectoryFeature | Kan utföra tjänst åtgärden "Aktivera katalog funktion" |
> | Microsoft. Directory/serviceAction/getAvailableExtentionProperties | Kan utföra Getavailableextentionproperties-tjänstens åtgärd |
> | Microsoft. Directory/Service princip ALS/allProperties/allTasks | Skapa och ta bort tjänstens huvud namn och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/Service princip ALS/managePermissionGrantsForAll. Microsoft-Company-Admin | Bevilja medgivande för alla behörigheter till program |
> | Microsoft. Directory/Service princip ALS/managePermissionGrantsForGroup. Microsoft-all-Application-Permissions | Bevilja en tjänst Principal direkt åtkomst till en grupps data |
> | Microsoft. Directory/Service princip ALS/Synchronization/standard/Read | Läs etablerings inställningar som är associerade med tjänstens huvud namn |
> | Microsoft. Directory/signInReports/allProperties/Read | Läs alla egenskaper för inloggnings rapporter, inklusive privilegierade egenskaper |
> | Microsoft. Directory/subscribedSkus/allProperties/allTasks | Köp och hantera prenumerationer och ta bort prenumerationer |
> | Microsoft. Directory/Users/allProperties/allTasks | Skapa och ta bort användare och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/permissionGrantPolicies/Create | Skapa principer för behörighets beviljande |
> | Microsoft. Directory/permissionGrantPolicies/Delete | Ta bort principer för behörighets beviljande |
> | Microsoft. Directory/permissionGrantPolicies/standard/Read | Läsa standard egenskaper för principer för behörighets beviljande |
> | Microsoft. Directory/permissionGrantPolicies/Basic/Update | Uppdatera grundläggande egenskaper för principer för behörighets beviljande |
> | Microsoft. Directory/servicePrincipalCreationPolicies/Create | Skapa principer för att skapa tjänstens huvud namn |
> | Microsoft. Directory/servicePrincipalCreationPolicies/Delete | Ta bort principer för att skapa tjänstens huvud namn |
> | Microsoft. Directory/servicePrincipalCreationPolicies/standard/Read | Läsa standard egenskaper för skapande principer för tjänstens huvud namn |
> | Microsoft. Directory/servicePrincipalCreationPolicies/Basic/Update | Uppdatera grundläggande egenskaper för skapande principer för tjänstens huvud namn |
> | Microsoft. Azure. advancedThreatProtection/upplånade/allTasks | Hantera alla aspekter av Azure Advanced Threat Protection |
> | Microsoft. Azure. informationProtection/upplånade/allTasks | Hantera alla aspekter av Azure Information Protection |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Commerce. debitering/allTasks | Hantera alla aspekter av Office 365-fakturering |
> | Microsoft. dynamics365/upplånade/allTasks | Hantera alla aspekter av Dynamics 365 |
> | Microsoft. Flow/upplånade/allTasks | Hantera alla aspekter av Microsoft Power automatisering |
> | Microsoft. Intune/upplånade/allTasks | Hantera alla aspekter av Microsoft Intune |
> | Microsoft. Office365. complianceManager/uplånar/allTasks | Hantera alla aspekter av Compliance Manager för Office 365 |
> | Microsoft. Office365. desktopAnalytics/uplånar/allTasks | Hantera alla aspekter av Skriv bords analys |
> | Microsoft. Office365. Exchange/uplånar/Basic/allTasks | Hantera alla aspekter av Exchange Online |
> | Microsoft. Office365. lock/upplånade/allTasks | Hantera alla aspekter av Customer Lockbox |
> | Microsoft. Office365. messageCenter/meddelanden/läsa | Läsa meddelanden i meddelande Center i Microsoft 365 administrations centret, förutom säkerhets meddelanden |
> | Microsoft. Office365. messageCenter/securityMessages/Read | Läsa säkerhets meddelanden i meddelande Center i Microsoft 365 administrations Center |
> | Microsoft. Office365. Network/Performance/allProperties/Read | Läs alla egenskaper för nätverks prestanda i Microsoft 365 administrations centret |
> | Microsoft. Office365. protectionCenter/uplånar/allProperties/allTasks | Hantera alla aspekter av centret för säkerhet och efterlevnad |
> | Microsoft. Office365. search/Content/Manage | Skapa och ta bort innehåll och läsa och uppdatera alla egenskaper i Microsoft Search |
> | Microsoft. Office365. securityComplianceCenter/uplånar/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standard egenskaper i Microsoft 365 Security and Compliance Center |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. sharePoint/upplånade/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standard egenskaper i SharePoint |
> | Microsoft. Office365. skypeForBusiness/uplånar/allTasks | Hantera alla aspekter av Skype för företag – Online |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. usageReports/uplånar/allProperties/Read | Läs Office 365-användnings rapporter |
> | Microsoft. Office365. userCommunication/uplånar/allTasks | Läsa och uppdatera visningen av nya meddelanden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |
> | Microsoft. powerApps/upplånade/allTasks | Hantera alla aspekter av Power Apps |
> | Microsoft. powerApps. powerBI/uplånar/allTasks | Hantera alla aspekter av Power BI |
> | Microsoft. Windows. defenderAdvancedThreatProtection/upplånade/allTasks | Hantera alla aspekter av Microsoft Defender för slut punkt |

## <a name="global-reader"></a>Global läsare

Användare med den här rollen kan läsa inställningar och administrativ information mellan Microsoft 365 tjänster, men kan inte utföra hanterings åtgärder. Global läsare är den skrivskyddade motsvarigheten till global administratör. Tilldela global läsare i stället för global administratör för planering, granskningar och undersökningar. Använd global läsare i kombination med andra begränsade administratörs roller som Exchange-administratör för att göra det enklare att få jobbet gjort utan att tilldela rollen global administratör. Global läsare fungerar med Microsoft 365 administrations Center, administrations Center för SharePoint, administrations Center för team, team administrations Center, Security Center, Compliance Center, Azure AD administrations Center och administrations Center för enhets hantering.

> [!NOTE]
> Global läsar roll har några begränsningar just nu –
>
>- [Administrations Center för OneDrive](https://admin.onedrive.com/) – administrations Center för OneDrive stöder inte rollen global läsare
>- [M365 administrations Center](https://admin.microsoft.com/Adminportal/Home#/homepage) – den globala läsaren kan inte läsa förfrågningar om kundsäkerhet. Du hittar inte fliken **Customer Locked-begäranden** under **stöd** i den vänstra rutan i M365 administrations Center.
>- [Office-säkerhet & Compliance Center](https://sip.protection.office.com/homepage) – den globala läsaren kan inte läsa SCC gransknings loggar, söka efter innehåll eller se säkra poäng.
>- [Team administrations Center](https://admin.teams.microsoft.com) – den globala läsaren kan inte läsa **team livs cykeln**, analysera **& rapporter**, **hantering av IP-telefon enheter** och **program katalog**.
>- [Privileged Access Management (PAM)](/office365/securitycompliance/privileged-access-management-overview) stöder inte rollen global läsare.
>- [Azure information Protection](/azure/information-protection/what-is-information-protection) – global läsare stöds endast [för central rapportering](/azure/information-protection/reports-aip) , och när din Azure AD-organisation inte finns på den [enhetliga etiketterande plattformen](/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform).
>
> Dessa funktioner håller på att utvecklas.
>

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/Applications/applicationProxy/Read | Läsa alla egenskaper för program-proxy |
> | Microsoft. Directory/program/synkronisering/standard/Read | Läs etablerings inställningar som är associerade med programobjektet |
> | Microsoft. Directory/auditLogs/allProperties/Read | Läsa alla egenskaper för gransknings loggar, inklusive privilegierade egenskaper |
> | Microsoft. Directory/bitlockerKeys/Key/Read | Läs BitLocker-metadata och-nyckel på enheter |
> | Microsoft. Directory/Connectors/allProperties/Read | Läsa alla egenskaper för Application Proxy-kopplingar |
> | Microsoft. Directory/connectorGroups/allProperties/Read | Läs alla egenskaper för kopplings grupper för Application Proxy |
> | Microsoft. Directory/entitlementManagement/allProperties/Read | Läsa alla egenskaper i hantering av Azure AD-berättigande |
> | Microsoft. Directory/deviceManagementPolicies/standard/Read | Läsa standard egenskaper för användnings principer för enhets hantering |
> | Microsoft. Directory/deviceRegistrationPolicy/standard/Read | Läsa standard egenskaper för enhets registrerings principer |
> | Microsoft. Directory/grupper/hiddenMembers/Read | Läsa dolda medlemmar i en grupp |
> | Microsoft. Directory/policys/standard/Read | Läsa grundläggande egenskaper för principer |
> | Microsoft. Directory/principer/ägare/läsa | Läs ägare till principer |
> | Microsoft. Directory/policys/policyAppliedTo/Read | Läs policies. policyAppliedTo-egenskap |
> | Microsoft. Directory/conditionalAccessPolicies/standard/Read | Läs policies. conditionalAccess-egenskap |
> | Microsoft. Directory/conditionalAccessPolicies/Owners/Read | Läs policies. conditionalAccess-egenskap |
> | Microsoft. Directory/conditionalAccessPolicies/policyAppliedTo/Read | Läs policies. conditionalAccess-egenskap |
> | Microsoft. Directory/provisioningLogs/allProperties/Read | Läs alla egenskaper för etablerings loggar |
> | Microsoft. Directory/Service princip ALS/Authentication/Read | Läsa egenskaper för autentisering för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Synchronization/standard/Read | Läs etablerings inställningar som är associerade med tjänstens huvud namn |
> | Microsoft. Directory/signInReports/allProperties/Read | Läs alla egenskaper för inloggnings rapporter, inklusive privilegierade egenskaper |
> | Microsoft. Directory/Users/strongAuthentication/Read | Läs egenskapen för stark autentisering för användare |
> | Microsoft. Commerce. fakturering/utlånare/Läs | Läs alla resurser för Office 365-fakturering |
> | Microsoft. Office365. Exchange/uplånar/standard/Read | Läs alla resurser för Exchange Online |
> | Microsoft. Office365. messageCenter/meddelanden/läsa | Läsa meddelanden i meddelande Center i Microsoft 365 administrations centret, förutom säkerhets meddelanden |
> | Microsoft. Office365. messageCenter/securityMessages/Read | Läsa säkerhets meddelanden i meddelande Center i Microsoft 365 administrations Center |
> | Microsoft. Office365. Network/Performance/allProperties/Read | Läs alla egenskaper för nätverks prestanda i Microsoft 365 administrations centret |
> | Microsoft. Office365. protectionCenter/uplånar/allProperties/Read | Läsa alla egenskaper i Center för säkerhet och efterlevnad |
> | Microsoft. Office365. securityComplianceCenter/upplånade/upplånade/lästa | Läsa standard egenskaper i Microsoft 365 Security and Compliance Center |
> | Microsoft. Office365. usageReports/uplånar/allProperties/Read | Läs Office 365-användnings rapporter |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="groups-administrator"></a>Grupp administratör

Användare med den här rollen kan skapa/hantera grupper och dess inställningar, t. ex. namn och förfallo principer. Det är viktigt att förstå att tilldela en användare till den här rollen och ger dem möjlighet att hantera alla grupper i organisationen över olika arbets belastningar som team, SharePoint, Yammer, förutom Outlook. Användaren kan också hantera de olika grupp inställningarna över olika administrations portaler som Microsoft administrations Center, Azure Portal, och även arbets belastningar som team och administrations Center för SharePoint.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/grupper/assignLicense | Tilldela produkt licenser till grupper för gruppbaserad licensiering |
> | Microsoft. Directory/grupper/skapa | Skapa grupper med undantag för roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper/ta bort | Ta bort grupper, exklusive roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper/hiddenMembers/Read | Läsa dolda medlemmar i en grupp |
> | Microsoft. Directory/grupper/reprocessLicenseAssignment | Ombearbeta licens tilldelningar för gruppbaserad licensiering |
> | Microsoft. Directory/grupper/Återställ | Återställa borttagna grupper |
> | Microsoft. Directory/grupper/Basic/Update | Uppdatera grundläggande egenskaper för grupper, exklusive roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper/klassificering/uppdatera | Uppdatera klassificerings egenskapen för grupper, exklusive roll tilldelnings bara grupper |
> | Microsoft. Directory/Groups/dynamicMembershipRule/Update | Uppdatera regel för dynamiskt medlemskap i grupper, exklusive roll tilldelnings bara grupper |
> | Microsoft. Directory/Groups/groupType/Update | Uppdatera egenskapen groupType för en grupp |
> | Microsoft. Directory/grupper/medlemmar/uppdatera | Uppdatera medlemmar i grupper, exklusive roll tilldelnings bara grupper |
> | Microsoft. Directory/Groups/onPremWriteBack/Update | Uppdatera Azure AD-grupper så att de skrivs tillbaka till lokalt |
> | Microsoft. Directory/grupper/ägare/uppdatera | Uppdatera ägare av grupper, exklusive roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper/inställningar/uppdatera | Uppdatera inställningar för grupper |
> | Microsoft. Directory/grupper/synlighet/uppdatera | Uppdatera Synlighets egenskapen för grupper |
> | Microsoft. Directory/Service princip ALS/managePermissionGrantsForGroup. Microsoft-all-Application-Permissions | Bevilja en tjänst Principal direkt åtkomst till en grupps data |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="guest-inviter"></a>Gäst deltagare

Användare med den här rollen kan hantera Azure Active Directory B2B-gäst användare om **medlemmar kan bjuda** in användar inställningen till Nej. Mer information om B2B-samarbete vid [Azure AD B2B-samarbete](../external-identities/what-is-b2b.md). Den innehåller inga andra behörigheter.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/Users/inviteGuest | Bjud in gästanvändare |
> | Microsoft. Directory/Users/standard/Read | Läsa grundläggande egenskaper för användare |
> | Microsoft. Directory/Users/appRoleAssignments/Read | Läs program roll tilldelningar för användare |
> | Microsoft. Directory/Users/directReports/Read | Läs direkt rapporterna för användare |
> | Microsoft. Directory/Users/Manager/Read | Läs chef för användare |
> | Microsoft. Directory/Users/memberOf/Read | Läs grupp medlemskap för användare |
> | Microsoft. Directory/Users/oAuth2PermissionGrants/Read | Läs delegerade behörighets bidrag för användare |
> | Microsoft. Directory/Users/ownedDevices/Read | Läsa ägda enheter av användare |
> | Microsoft. Directory/Users/ownedObjects/Read | Läsa ägda objekt av användare |
> | Microsoft. Directory/Users/registeredDevices/Read | Läsa registrerade enheter med användare |

## <a name="helpdesk-administrator"></a>Support administratör

Användare med den här rollen kan ändra lösen ord, ogiltig uppdatera tokens, hantera tjänst begär Anden och övervaka tjänstens hälsa. Om en uppdateringstoken inverifieras tvingar användaren att logga in igen. Om en helpdesk-administratör kan återställa en användares lösen ord och ogiltig verifiering av uppdateringstoken beror på vilken roll användaren tilldelas. En lista över de roller som en supportavdelningen-administratör kan återställa lösen ord för och inte validera uppdateringstoken finns i [behörigheter för lösen ords återställning](#password-reset-permissions).

> [!IMPORTANT]
> Användare med den här rollen kan ändra lösen ord för personer som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i och utanför Azure Active Directory. Att ändra lösen ordet för en användare kan innebära att du antar användarens identitet och behörigheter. Exempel:
>
>- Program registrering och företags program ägare, som kan hantera autentiseringsuppgifter för appar som de äger. De här apparna kan ha privilegierade behörigheter i Azure AD och beviljas ingen annan support administratör. Med hjälp av den här sökvägen kan IT-administratören kunna ta hänsyn till identiteten för en program ägare och sedan ytterligare utgå från identiteten för ett privilegierat program genom att uppdatera autentiseringsuppgifterna för programmet.
>- Azure-Prenumerationens ägare, som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i Azure.
>- Säkerhets grupp och Microsoft 365 grupp ägare, som kan hantera grupp medlemskap. Dessa grupper kan ge åtkomst till känslig eller privat information eller kritisk konfiguration i Azure AD och någon annan stans.
>- Administratörer i andra tjänster utanför Azure AD, till exempel Exchange Online, Office Security och Compliance Center och personal system.
>- Icke-administratörer som chefer, juridiska konsulter och personal anställda som kan ha till gång till känslig eller privat information.

Det kan vara möjligt att delegera administrativa behörigheter över del mängder av användare och tillämpa principer på en delmängd användare med [administrativa enheter](administrative-units.md).

Den här rollen kallades tidigare för "lösen ords administratör" i [Azure Portal](https://portal.azure.com/). Namnet "supportavdelningen administratör" i Azure AD matchar nu namnet i Azure AD PowerShell och Microsoft Graph API.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/bitlockerKeys/Key/Read | Läs BitLocker-metadata och-nyckel på enheter |
> | Microsoft. Directory/Users/invalidateAllRefreshTokens | Framtvinga utloggning genom att invalidera token för användar uppdatering |
> | Microsoft. Directory/Users/Password/Update | Återställ lösen ord för alla användare |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="hybrid-identity-administrator"></a>Hybrid identitets administratör

Användare med den här rollen kan skapa, hantera och distribuera konfigurations konfigurations inställningar från AD till Azure AD med hjälp av moln etablering och hantera Federations inställningar. Användare kan också felsöka och övervaka loggar med den här rollen.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/program/skapa | Skapa alla typer av program |
> | Microsoft. Directory/Applications/Delete | Ta bort alla typer av program |
> | Microsoft. Directory/Applications/appRoles/Update | Uppdatera egenskapen appRoles för alla typer av program |
> | Microsoft. Directory/program/mål grupp/uppdatering | Uppdatera egenskapen Audience för program |
> | Microsoft. Directory/program/autentisering/uppdatering | Uppdatera autentiseringen för alla typer av program |
> | Microsoft. Directory/Applications/Basic/Update | Uppdatera grundläggande egenskaper för program |
> | Microsoft. Directory/program/autentiseringsuppgifter/uppdatera | Uppdatera programautentiseringsuppgifter |
> | Microsoft. Directory/program/ägare/uppdatering | Uppdatera ägare av program |
> | Microsoft. Directory/program/behörigheter/uppdatera | Uppdatera exponerade behörigheter och nödvändiga behörigheter för alla typer av program |
> | Microsoft. Directory/Applications/policies/Update | Uppdatera principer för program |
> | Microsoft. Directory/program/synkronisering/standard/Read | Läs etablerings inställningar som är associerade med programobjektet |
> | Microsoft. Directory/applicationTemplates/instansiera | Instansiera Galleri program från programmallar |
> | Microsoft. Directory/auditLogs/allProperties/Read | Läsa alla egenskaper för gransknings loggar, inklusive privilegierade egenskaper |
> | Microsoft. Directory/cloudProvisioning/allProperties/allTasks | Läsa och konfigurera alla egenskaper för Azure AD Cloud Provisioning-tjänsten. |
> | Microsoft. Directory/Domains/allProperties/Read | Läsa alla egenskaper för domäner |
> | Microsoft. Directory/Domains/Federation/Update | Uppdatera Federations egenskapen för domäner |
> | Microsoft. Directory/Organization/dirSync/Update | Uppdatera egenskapen för organisations katalogens synkronisering |
> | Microsoft. Directory/provisioningLogs/allProperties/Read | Läs alla egenskaper för etablerings loggar |
> | Microsoft. Directory/Service princip ALS/Create | Skapa tjänsthuvudnamn |
> | Microsoft. Directory/Service princip ALS/Delete | Ta bort tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Disable | Inaktivera tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Enable | Aktivera tjänstens huvudnamn |
> | Microsoft. Directory/Service princip ALS/synchronizationCredentials/Manage | Hantera program etablerings hemligheter och autentiseringsuppgifter |
> | Microsoft. Directory/Service princip ALS/synchronizationJobs/Manage | Starta, starta om och pausa Application Provisioning synkroniseringen-jobb |
> | Microsoft. Directory/Service princip ALS/synchronizationSchema/Manage | Skapa och hantera synkroniseringen-jobb och-schema för program etablering |
> | Microsoft. Directory/Service princip ALS/målgrupp/uppdatera | Uppdatera mål egenskaper för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Authentication/Update | Uppdatera autentiserings egenskaper för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Basic/Update | Uppdatera grundläggande egenskaper för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/credentials/Update | Uppdatera autentiseringsuppgifter för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Owners/Update | Uppdatera ägare till tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Permissions/Update | Uppdatera behörigheter för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/policies/Update | Uppdatera principer för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/tag/Update | Uppdatera egenskapen tag för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Synchronization/standard/Read | Läs etablerings inställningar som är associerade med tjänstens huvud namn |
> | Microsoft. Directory/signInReports/allProperties/Read | Läs alla egenskaper för inloggnings rapporter, inklusive privilegierade egenskaper |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Office365. messageCenter/meddelanden/läsa | Läsa meddelanden i meddelande Center i Microsoft 365 administrations centret, förutom säkerhets meddelanden |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="insights-administrator"></a>Insights-administratör

Användare med den här rollen har åtkomst till en fullständig uppsättning administrativa funktioner i [M365 Insights-programmet](https://go.microsoft.com/fwlink/?linkid=2129521). Den här rollen har möjlighet att läsa katalog information, övervaka tjänstens hälsa, stöd för fil support och få åtkomst till Insikternas administrations inställningar.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Insights/upplånade/allTasks | Hantera alla aspekter av Insights-appen |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="insights-business-leader"></a>Affärs ledare för insikter

Användare med den här rollen har åtkomst till en uppsättning instrument paneler och insikter via [M365 Insights-programmet](https://go.microsoft.com/fwlink/?linkid=2129521). Detta omfattar fullständig åtkomst till alla instrument paneler och visade insikter och data utforsknings funktioner. Användare med den här rollen har inte åtkomst till produkt konfigurations inställningar, vilket är ansvaret för rollen insikter-administratör.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Insights/rapporter/läsa | Visa rapporter och instrument paneler i insikter-appen |
> | Microsoft. Insights/program/uppdatera | Distribuera och hantera program i insikter-appen |

## <a name="intune-administrator"></a>Intune-administratör

Användare med den här rollen har globala behörigheter inom Microsoft Intune online när tjänsten finns. Dessutom innehåller den här rollen möjligheten att hantera användare och enheter för att associera principer, samt skapa och hantera grupper. Mer information finns i [rollbaserad administrations kontroll (RBAC) med Microsoft Intune](/intune/role-based-access-control).

Den här rollen kan skapa och hantera alla säkerhets grupper. Intune-administratören har dock inte administratörs behörighet för Office-grupper. Det innebär att administratören inte kan uppdatera ägare eller medlemskap i alla Office-grupper i organisationen. Han/hon kan dock hantera Office-gruppen som han skapar och som ingår i hans/hennes slut användar behörighet. I så fall ska alla Office-grupper (inte säkerhets grupper) som han/hon skapar räknas mot kvoten på 250.

> [!NOTE]
> I Microsoft Graph API och Azure AD PowerShell identifieras den här rollen som "Intune service Administrator". Det är "Intune-administratör" i [Azure Portal](https://portal.azure.com).

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/bitlockerKeys/Key/Read | Läs BitLocker-metadata och-nyckel på enheter |
> | Microsoft. Directory/kontakter/skapa | Skapa kontakter |
> | Microsoft. Directory/Contacts/Delete | Ta bort kontakter |
> | Microsoft. Directory/Contacts/Basic/Update | Uppdatera grundläggande egenskaper för kontakter |
> | Microsoft. Directory/Devices/Create | Skapa enheter (registrera i Azure AD) |
> | Microsoft. Directory/Devices/Delete | Ta bort enheter från Azure AD |
> | Microsoft. Directory/enheter/inaktivera | Inaktivera enheter i Azure AD |
> | Microsoft. Directory/Devices/Enable | Aktivera enheter i Azure AD |
> | Microsoft. Directory/Devices/Basic/Update | Uppdatera grundläggande egenskaper för enheter |
> | Microsoft. Directory/Devices/extensionAttributes/Update | Uppdatera alla värden för egenskapen Devices. extensionAttributes |
> | Microsoft. Directory/Devices/registeredOwners/Update | Uppdatera registrerade ägare av enheter |
> | Microsoft. Directory/Devices/registeredUsers/Update | Uppdatera registrerade användare av enheter |
> | Microsoft. Directory/deviceManagementPolicies/standard/Read | Läsa standard egenskaper för användnings principer för enhets hantering |
> | Microsoft. Directory/deviceRegistrationPolicy/standard/Read | Läsa standard egenskaper för enhets registrerings principer |
> | Microsoft. Directory/grupper/hiddenMembers/Read | Läsa dolda medlemmar i en grupp |
> | Microsoft. Directory/grupper. säkerhet/skapa | Skapa säkerhets grupper med undantag för roll tilldelnings bara grupper |
> | Microsoft. Directory/Groups. Security/Delete | Ta bort säkerhets grupper med undantag för roll tilldelnings bara grupper |
> | Microsoft. Directory/Groups. Security/Basic/Update | Uppdatera grundläggande egenskaper för säkerhets grupper med undantag för roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper. säkerhet/klassificering/uppdatera | Uppdatera klassificerings egenskapen för säkerhets grupperna med undantag av roll tilldelnings bara grupper |
> | Microsoft. Directory/Groups. Security/dynamicMembershipRule/Update | Uppdatera dynamicMembershipRule-egenskapen för säkerhets grupperna med undantag av roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper. säkerhet/medlemmar/uppdatera | Uppdatera medlemmar i säkerhets grupper med undantag för roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper. säkerhet/ägare/uppdatera | Uppdatera ägare av säkerhets grupper med undantag för roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper. säkerhet/synlighet/uppdatera | Uppdatera Synlighets egenskapen för säkerhets grupperna med undantag av roll tilldelnings bara grupper |
> | Microsoft. Directory/Users/Basic/Update | Uppdatera grundläggande egenskaper för användare |
> | Microsoft. Directory/Users/Manager/Update | Uppdaterings hanteraren för användare |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Intune/upplånade/allTasks | Hantera alla aspekter av Microsoft Intune |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="kaizala-administrator"></a>Kaizala-administratör

Användare med den här rollen har globala behörigheter för att hantera inställningar inom Microsoft Kaizala när tjänsten finns, samt möjligheten att hantera support biljetter och övervaka tjänstens hälsa. Dessutom kan användaren komma åt rapporter som är relaterade till antagande & användningen av Kaizala av organisations medlemmar och affärs rapporter som genereras med hjälp av Kaizala-åtgärder.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="knowledge-administrator"></a>Kunskaps administratör

Användare i den här rollen har fullständig åtkomst till alla inställningar för kunskap, inlärning och intelligenta funktioner i Microsoft 365 administrations centret. De har en allmän förståelse för produkt serien, licens information och har ansvar för att kontrol lera åtkomst. Kunskaps administratören kan skapa och hantera innehåll, t. ex. ämnen, akronymer och utbildnings resurser. Dessa användare kan dessutom skapa innehålls Center, övervaka tjänstens hälsa och skapa tjänst begär Anden.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/grupper. säkerhet/skapa | Skapa säkerhets grupper med undantag för roll tilldelnings bara grupper |
> | Microsoft. Directory/Groups. Security/createAsOwner | Skapa säkerhets grupper med undantag för roll tilldelnings bara grupper och skapare läggs till som första ägare |
> | Microsoft. Directory/Groups. Security/Delete | Ta bort säkerhets grupper med undantag för roll tilldelnings bara grupper |
> | Microsoft. Directory/Groups. Security/Basic/Update | Uppdatera grundläggande egenskaper för säkerhets grupper med undantag för roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper. säkerhet/medlemmar/uppdatera | Uppdatera medlemmar i säkerhets grupper med undantag för roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper. säkerhet/ägare/uppdatera | Uppdatera ägare av säkerhets grupper med undantag för roll tilldelnings bara grupper |
> | Microsoft. Office365. Knowledge/contentUnderstanding/allProperties/allTasks | Läsa och uppdatera alla egenskaper för innehålls förståelse i Microsoft 365 administrations Center |
> | Microsoft. Office365. Knowledge/knowledgeNetwork/allProperties/allTasks | Läsa och uppdatera alla egenskaper för kunskaps nätverket i Microsoft 365 administrations Center |
> | Microsoft. Office365. protectionCenter/sensitivityLabels/allProperties/Read | Läsa känslighets etiketter i Center för säkerhet och efterlevnad |
> | Microsoft. Office365. sharePoint/upplånade/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standard egenskaper i SharePoint |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="license-administrator"></a>Licens administratör

Användare med den här rollen kan lägga till, ta bort och uppdatera licens tilldelningar för användare, grupper (med gruppbaserad licensiering) och hantera användnings platsen för användare. Rollen ger inte möjlighet att köpa eller hantera prenumerationer, skapa eller hantera grupper eller skapa eller hantera användare bortom användnings platsen. Den här rollen har ingen åtkomst för att visa, skapa eller hantera support biljetter.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/grupper/assignLicense | Tilldela produkt licenser till grupper för gruppbaserad licensiering |
> | Microsoft. Directory/grupper/reprocessLicenseAssignment | Ombearbeta licens tilldelningar för gruppbaserad licensiering |
> | Microsoft. Directory/Users/assignLicense | Hantera användar licenser |
> | Microsoft. Directory/Users/reprocessLicenseAssignment | Ombearbeta licens tilldelningar för användare |
> | Microsoft. Directory/Users/usageLocation/Update | Uppdatera användnings plats för användare |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="message-center-privacy-reader"></a>Meddelande Center sekretess läsare

Användare med den här rollen kan övervaka alla meddelanden i meddelande centret, inklusive data sekretess meddelanden. Meddelande Center sekretess läsare får e-postaviseringar, inklusive de som är relaterade till data sekretess och de kan avbryta prenumerationen med hjälp av meddelande Center inställningar. Endast global administratör och meddelande Center sekretess läsare kan läsa data sekretess meddelanden. Dessutom innehåller den här rollen möjligheten att Visa grupper, domäner och prenumerationer. Den här rollen har ingen behörighet att visa, skapa eller hantera tjänst begär Anden.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Office365. messageCenter/meddelanden/läsa | Läsa meddelanden i meddelande Center i Microsoft 365 administrations centret, förutom säkerhets meddelanden |
> | Microsoft. Office365. messageCenter/securityMessages/Read | Läsa säkerhets meddelanden i meddelande Center i Microsoft 365 administrations Center |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="message-center-reader"></a>Meddelande Center läsare

Användare i den här rollen kan övervaka meddelanden och råd om hälso uppdateringar i [meddelande Center](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) för deras organisation på konfigurerade tjänster som Exchange, Intune och Microsoft Teams. Meddelande Center läsare får veckovis e-postsammandrag av inlägg, uppdateringar och kan dela meddelande Center inlägg i Microsoft 365. I Azure AD kommer användare som tilldelats den här rollen bara att ha skrivskyddad åtkomst till Azure AD-tjänster, till exempel användare och grupper. Den här rollen har ingen åtkomst för att visa, skapa eller hantera support biljetter.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Office365. messageCenter/meddelanden/läsa | Läsa meddelanden i meddelande Center i Microsoft 365 administrations centret, förutom säkerhets meddelanden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="modern-commerce-user"></a>Modern Commerce-användare

Använd inte. Den här rollen tilldelas automatiskt från Commerce och är inte avsedd eller stöds inte för någon annan användning. Se informationen nedan.

Den moderna Commerce användar rollen ger vissa användare behörighet att komma åt Microsoft 365 administrations Center och se de vänstra navigerings posterna för **Start**, **fakturering** och **support**. Innehållet som är tillgängligt i dessa områden styrs av handelstilldelade [roller](../../cost-management-billing/manage/understand-mca-roles.md) som har tilldelats användare för att hantera produkter som de har köpt för sig själva eller din organisation. Detta kan omfatta uppgifter som att betala fakturor, eller för åtkomst till fakturerings konton och fakturerings profiler.

Användare med en modern Commerce-användargrupp har vanligt vis administratörs behörighet i andra Microsoft-inköps system, men har inte rollen som global administratör eller fakturerings administratör för åtkomst till administrations centret.

**När tilldelas den moderna Commerce användar rollen?**

* **Självbetjänings köp i Microsoft 365 administrations Center** – självbetjänings köp ger användarna möjlighet att prova nya produkter genom att köpa eller registrera sig för dem på egen hand. Dessa produkter hanteras i administrations centret. Användare som gör ett självbetjänings köp tilldelas en roll i handels systemet och den ständiga Commerce användar rollen så att de kan hantera sina inköp i administrations centret. Administratörer kan blockera självbetjänings köp (för Power BI, Power Apps, energi automatisering) via [PowerShell](/microsoft-365/commerce/subscriptions/allowselfservicepurchase-powershell). Mer information finns i [Vanliga frågor och svar om självbetjäningsköp](/microsoft-365/commerce/subscriptions/self-service-purchase-faq).
* **Köp från Microsofts kommersiella marknads platser** – på liknande sätt som självbetjänings köp, när en användare köper en produkt eller tjänst från Microsoft AppSource eller Azure Marketplace, tilldelas den ständiga handels användar rollen om de inte har rollen som global administratör eller fakturerings administratör. I vissa fall kan användarna blockeras från att göra dessa inköp. Mer information finns på [Microsofts kommersiella marknads platser](../../marketplace/marketplace-faq-publisher-guide.md#what-could-block-a-customer-from-completing-a-purchase).
* **Förslag från Microsoft** – ett förslag är ett formellt erbjudande från Microsoft för din organisation att köpa produkter och tjänster från Microsoft. När den person som accepterar förslaget inte har rollen som global administratör eller fakturerings administratör i Azure AD, tilldelas de både en handelsbestämd roll för att slutföra förslaget och den moderna Commerce-rollen för att få åtkomst till administrations centret. När de får åtkomst till administrations centret kan de endast använda funktioner som har godkänts av sin handelsbestämd roll.
* **Handelsrelaterade roller** – vissa användare har tilldelats handelsrelaterade roller. Om en användare inte är en global administratör eller fakturerings administratör får de en modern Commerce-användar roll så att de kan komma åt administrations centret.

Om den moderna Commerce-rollen är otilldelad från en användare förlorar han eller hon åtkomst till Microsoft 365 administrations Center. Om de hanterade produkter, antingen för sig själva eller för din organisation, kan de inte hantera dem. Detta kan inkludera att tilldela licenser, ändra betalnings metoder, betala fakturor eller andra aktiviteter för att hantera prenumerationer.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Commerce. fakturering/partners/Läs | Läsa partner egenskap för Microsoft 365 fakturering |
> | Microsoft. Commerce. volumeLicenseServiceCenter/upplånade/allTasks | Hantera alla aspekter av Volume Licensing Service Center |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. webports/-upplånare/grundläggande/lästa | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="network-administrator"></a>Nätverks administratör

Användare i den här rollen kan granska rekommendationer för nätverks perimeter arkitektur från Microsoft som baseras på telemetri från användarens platser. Nätverks prestanda för Microsoft 365 är beroende av en noggrann nätverks gräns arkitektur för företags nätverk som vanligt vis är en specifik användar plats. Med den här rollen kan du redigera identifierade användar platser och konfigurera nätverks parametrar för de platserna för att under lätta förbättrad telemetri och design rekommendationer

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Office365. Network/locations/allProperties/allTasks | Hantera alla aspekter av nätverks platser |
> | Microsoft. Office365. Network/Performance/allProperties/Read | Läs alla egenskaper för nätverks prestanda i Microsoft 365 administrations centret |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="office-apps-administrator"></a>Office Apps-administratör

Användare med den här rollen kan hantera moln inställningar för Microsoft 365 Apps. Detta omfattar hantering av moln principer, hantering av självbetjänings hantering och möjligheten att visa Office apps-relaterade rapporter. Den här rollen ger dessutom möjlighet att hantera support biljetter och övervaka tjänstens hälsa i huvud administrations centret. Användare som har tilldelats den här rollen kan även hantera kommunikation av nya funktioner i Office-appar.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Office365. messageCenter/meddelanden/läsa | Läsa meddelanden i meddelande Center i Microsoft 365 administrations centret, förutom säkerhets meddelanden |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. userCommunication/uplånar/allTasks | Läsa och uppdatera visningen av nya meddelanden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="partner-tier1-support"></a>Support för partner 1

Använd inte. Den här rollen är inaktuell och kommer att tas bort från Azure AD i framtiden. Den här rollen är avsedd att användas av ett litet antal Microsoft-partner och är inte avsedd för allmän användning.

> [!IMPORTANT]
> Den här rollen kan återställa lösen ord och ogiltig verifiering av uppdateringstoken för icke-administratörer. Den här rollen ska inte användas eftersom den är föråldrad och den kommer inte längre att returneras i API.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/Applications/appRoles/Update | Uppdatera egenskapen appRoles för alla typer av program |
> | Microsoft. Directory/program/mål grupp/uppdatering | Uppdatera egenskapen Audience för program |
> | Microsoft. Directory/program/autentisering/uppdatering | Uppdatera autentiseringen för alla typer av program |
> | Microsoft. Directory/Applications/Basic/Update | Uppdatera grundläggande egenskaper för program |
> | Microsoft. Directory/program/autentiseringsuppgifter/uppdatera | Uppdatera programautentiseringsuppgifter |
> | Microsoft. Directory/program/ägare/uppdatering | Uppdatera ägare av program |
> | Microsoft. Directory/program/behörigheter/uppdatera | Uppdatera exponerade behörigheter och nödvändiga behörigheter för alla typer av program |
> | Microsoft. Directory/Applications/policies/Update | Uppdatera principer för program |
> | Microsoft. Directory/kontakter/skapa | Skapa kontakter |
> | Microsoft. Directory/Contacts/Delete | Ta bort kontakter |
> | Microsoft. Directory/Contacts/Basic/Update | Uppdatera grundläggande egenskaper för kontakter |
> | Microsoft. Directory/grupper/skapa | Skapa grupper med undantag för roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper/ta bort | Ta bort grupper, exklusive roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper/Återställ | Återställa borttagna grupper |
> | Microsoft. Directory/grupper/medlemmar/uppdatera | Uppdatera medlemmar i grupper, exklusive roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper/ägare/uppdatera | Uppdatera ägare av grupper, exklusive roll tilldelnings bara grupper |
> | Microsoft. Directory/oAuth2PermissionGrants/allProperties/allTasks | Skapa och ta bort OAuth 2,0-behörighet bidrag och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/Service princip ALS/appRoleAssignedTo/Update | Uppdatera roll tilldelningar för tjänstens huvud namn |
> | Microsoft. Directory/Users/assignLicense | Hantera användar licenser |
> | Microsoft. Directory/Users/Create | Lägga till användare |
> | Microsoft. Directory/Users/Delete | Ta bort användare |
> | Microsoft. Directory/användare/inaktivera | Inaktivera användare |
> | Microsoft. Directory/Users/Enable | Aktivera användare |
> | Microsoft. Directory/Users/invalidateAllRefreshTokens | Framtvinga utloggning genom att invalidera token för användar uppdatering |
> | Microsoft. Directory/Users/Restore | Återställ borttagna användare |
> | Microsoft. Directory/Users/Basic/Update | Uppdatera grundläggande egenskaper för användare |
> | Microsoft. Directory/Users/Manager/Update | Uppdaterings hanteraren för användare |
> | Microsoft. Directory/Users/Password/Update | Återställ lösen ord för alla användare |
> | Microsoft. Directory/Users/userPrincipalName/Update | Uppdatera användarens huvud namn |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="partner-tier2-support"></a>Support för partner – nivå 2

Använd inte. Den här rollen är inaktuell och kommer att tas bort från Azure AD i framtiden. Den här rollen är avsedd att användas av ett litet antal Microsoft-partner och är inte avsedd för allmän användning.

> [!IMPORTANT]
> Den här rollen kan återställa lösen ord och göra en ogiltig verifiering av uppdateringstoken för alla icke-administratörer och administratörer (inklusive globala administratörer). Den här rollen ska inte användas eftersom den är föråldrad och den kommer inte längre att returneras i API.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/Applications/appRoles/Update | Uppdatera egenskapen appRoles för alla typer av program |
> | Microsoft. Directory/program/mål grupp/uppdatering | Uppdatera egenskapen Audience för program |
> | Microsoft. Directory/program/autentisering/uppdatering | Uppdatera autentiseringen för alla typer av program |
> | Microsoft. Directory/Applications/Basic/Update | Uppdatera grundläggande egenskaper för program |
> | Microsoft. Directory/program/autentiseringsuppgifter/uppdatera | Uppdatera programautentiseringsuppgifter |
> | Microsoft. Directory/program/ägare/uppdatering | Uppdatera ägare av program |
> | Microsoft. Directory/program/behörigheter/uppdatera | Uppdatera exponerade behörigheter och nödvändiga behörigheter för alla typer av program |
> | Microsoft. Directory/Applications/policies/Update | Uppdatera principer för program |
> | Microsoft. Directory/kontakter/skapa | Skapa kontakter |
> | Microsoft. Directory/Contacts/Delete | Ta bort kontakter |
> | Microsoft. Directory/Contacts/Basic/Update | Uppdatera grundläggande egenskaper för kontakter |
> | Microsoft. Directory/Domains/Basic/allTasks | Skapa och ta bort domäner och läsa och uppdatera standard egenskaper |
> | Microsoft. Directory/grupper/skapa | Skapa grupper med undantag för roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper/ta bort | Ta bort grupper, exklusive roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper/Återställ | Återställa borttagna grupper |
> | Microsoft. Directory/grupper/medlemmar/uppdatera | Uppdatera medlemmar i grupper, exklusive roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper/ägare/uppdatera | Uppdatera ägare av grupper, exklusive roll tilldelnings bara grupper |
> | Microsoft. Directory/oAuth2PermissionGrants/allProperties/allTasks | Skapa och ta bort OAuth 2,0-behörighet bidrag och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/Organization/Basic/Update | Uppdatera grundläggande egenskaper för organisation |
> | Microsoft. Directory/roleAssignments/allProperties/allTasks | Skapa och ta bort roll tilldelningar och läsa och uppdatera alla Roll tilldelnings egenskaper |
> | Microsoft. Directory/roleDefinitions/allProperties/allTasks | Skapa och ta bort roll definitioner och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/scopedRoleMemberships/allProperties/allTasks | Skapa och ta bort scopedRoleMemberships och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/Service princip ALS/appRoleAssignedTo/Update | Uppdatera roll tilldelningar för tjänstens huvud namn |
> | Microsoft. Directory/subscribedSkus/standard/Read | Läsa grundläggande egenskaper för prenumerationer |
> | Microsoft. Directory/Users/assignLicense | Hantera användar licenser |
> | Microsoft. Directory/Users/Create | Lägga till användare |
> | Microsoft. Directory/Users/Delete | Ta bort användare |
> | Microsoft. Directory/användare/inaktivera | Inaktivera användare |
> | Microsoft. Directory/Users/Enable | Aktivera användare |
> | Microsoft. Directory/Users/invalidateAllRefreshTokens | Framtvinga utloggning genom att invalidera token för användar uppdatering |
> | Microsoft. Directory/Users/Restore | Återställ borttagna användare |
> | Microsoft. Directory/Users/Basic/Update | Uppdatera grundläggande egenskaper för användare |
> | Microsoft. Directory/Users/Manager/Update | Uppdaterings hanteraren för användare |
> | Microsoft. Directory/Users/Password/Update | Återställ lösen ord för alla användare |
> | Microsoft. Directory/Users/userPrincipalName/Update | Uppdatera användarens huvud namn |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="password-administrator"></a>Lösen ords administratör

Användare med den här rollen har begränsad möjlighet att hantera lösen ord. Den här rollen ger inte möjlighet att hantera tjänst begär Anden eller övervaka tjänstens hälsa. Om en lösen ords administratör kan återställa en användares lösen ord beror på vilken roll användaren har tilldelats. En lista över de roller som en lösen ords administratör kan återställa lösen ord för finns i [behörigheter för lösen ords återställning](#password-reset-permissions).

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/Users/Password/Update | Återställ lösen ord för alla användare |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="power-bi-administrator"></a>Power BI administratör

Användare med den här rollen har globala behörigheter inom Microsoft Power BI när tjänsten finns, samt möjligheten att hantera support biljetter och övervaka tjänstens hälsa. Mer information finns i [förstå Power BI admin-rollen](/power-bi/service-admin-role).

> [!NOTE]
> I Microsoft Graph API och Azure AD PowerShell identifieras rollen som "Power BI tjänst administratör". Det är "Power BI administratör" i [Azure Portal](https://portal.azure.com).

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |
> | Microsoft. powerApps. powerBI/uplånar/allTasks | Hantera alla aspekter av Power BI |

## <a name="power-platform-administrator"></a>Power Platform-administratör

Användare med den här rollen kan skapa och hantera alla aspekter av miljöer, PowerApps, flöden och principer för data förlust skydd. Dessutom har användare med den här rollen möjlighet att hantera support biljetter och övervaka tjänstens hälsa.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. dynamics365/upplånade/allTasks | Hantera alla aspekter av Dynamics 365 |
> | Microsoft. Flow/upplånade/allTasks | Hantera alla aspekter av Microsoft Power automatisering |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |
> | Microsoft. powerApps/upplånade/allTasks | Hantera alla aspekter av Power Apps |

## <a name="printer-administrator"></a>Skrivar administratör

Användare med den här rollen kan registrera skrivare och hantera alla aspekter av alla skrivar konfigurationer i Microsofts Universal Print-lösning, inklusive inställningar för Universal Print Connector. De kan godkänna alla delegerade utskrifts behörighets begär Anden. Skrivar administratörer har även åtkomst till utskrifts rapporter.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Azure. Print/uplånar/allProperties/allTasks | Skapa och ta bort skrivare och anslutningar och läsa och uppdatera alla egenskaper i Microsoft Print |

## <a name="printer-technician"></a>Skrivar tekniker

Användare med den här rollen kan registrera skrivare och hantera skrivar status i Microsoft Universal Print-lösningen. De kan också läsa all anslutnings information. Nyckel aktivitet en skrivare tekniker kan inte ange användar behörigheter för skrivare och dela skrivare.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Azure. Print/Connectors/allProperties/Read | Läs alla egenskaper för kopplingar i Microsoft Print |
> | Microsoft. Azure. Print/Printers/allProperties/Read | Läs alla egenskaper för skrivare i Microsoft Print |
> | Microsoft. Azure. Print/Printers/registrera | Registrera skrivare i Microsoft Print |
> | Microsoft. Azure. Print/Printers/avregistrera | Avregistrera skrivare i Microsoft Print |
> | Microsoft. Azure. Print/Printers/Basic/Update | Uppdatera grundläggande egenskaper för skrivare i Microsoft Print |

## <a name="privileged-authentication-administrator"></a>Administratör för privilegie rad autentisering

Användare med den här rollen kan ange eller återställa valfri autentiseringsmetod (inklusive lösen ord) för alla användare, inklusive globala administratörer. Administratörer med privilegie rad autentisering kan tvinga användare att omregistrera sig mot befintliga autentiseringsuppgifter som inte är lösen ord (t. ex. MFA eller FIDO) och återkalla "MFA på enheten" och fråga om MFA vid nästa inloggning av alla användare.

Rollen som [administratör för autentisering](#authentication-administrator) har behörighet att framtvinga omregistrering och Multi-Factor Authentication för standard användare och användare med vissa administratörs roller.

[Administratörs rollen autentiseringsprincip](#authentication-policy-administrator) har behörighet att ange innehavarens autentiseringsmetod för autentisering som avgör vilka metoder som varje användare kan registrera sig och använda.

| Roll | Hantera användarens autentiseringsmetoder | Hantera MFA per användare | Hantera MFA-inställningar | Hantera principen för auth-metoden | Hantera lösen ords skydds princip |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Administratör för autentisering | Ja för vissa användare (se ovan) | Ja för vissa användare (se ovan) | Inga | Inga | Inga |
| Administratör för privilegie rad autentisering| Ja för alla användare | Ja för alla användare | Inga | Inga | Inga |
| Administratör för autentiseringsprincip | Inga | Inga | Ja | Ja | Ja |

> [!IMPORTANT]
> Användare med den här rollen kan ändra autentiseringsuppgifter för personer som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i och utanför Azure Active Directory. Att ändra autentiseringsuppgifterna för en användare kan innebära att du förutsätter att användarens identitet och behörigheter. Exempel:
>
>* Program registrering och företags program ägare, som kan hantera autentiseringsuppgifter för appar som de äger. De här apparna kan ha privilegierade behörigheter i Azure AD och inte beviljas till autentiserings administratörer. Via den här sökvägen kan en administratörs administratör anta identiteten för en program ägare och sedan ytterligare utgå från identiteten för ett privilegie rad program genom att uppdatera autentiseringsuppgifterna för programmet.
>* Azure-Prenumerationens ägare, som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i Azure.
>* Säkerhets grupp och Microsoft 365 grupp ägare, som kan hantera grupp medlemskap. Dessa grupper kan ge åtkomst till känslig eller privat information eller kritisk konfiguration i Azure AD och någon annan stans.
>* Administratörer i andra tjänster utanför Azure AD, till exempel Exchange Online, Office Security och Compliance Center och personal system.
>* Icke-administratörer som chefer, juridiska konsulter och personal anställda som kan ha till gång till känslig eller privat information.


> [!IMPORTANT]
> Den här rollen kan för närvarande inte hantera MFA per användare i den äldre hanterings portalen för MFA. Samma funktioner kan utföras med hjälp av Azure AD PowerShell [-modulen set-MsolUser](https://docs.microsoft.com/powershell/module/msonline/set-msoluser) kommandot.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/Users/invalidateAllRefreshTokens | Framtvinga utloggning genom att invalidera token för användar uppdatering |
> | Microsoft. Directory/Users/strongAuthentication/Update | Uppdatera egenskapen för stark autentisering för användare |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="privileged-role-administrator"></a>Privilegie rad roll administratör

Användare med den här rollen kan hantera roll tilldelningar i Azure Active Directory, samt i Azure AD Privileged Identity Management. De kan skapa och hantera grupper som kan tilldelas till Azure AD-roller. Dessutom tillåter den här rollen hantering av alla aspekter av Privileged Identity Management och administrativa enheter.

> [!IMPORTANT]
> Den här rollen ger möjlighet att hantera tilldelningar för alla Azure AD-roller, inklusive rollen global administratör. Den här rollen omfattar inte andra privilegierade förmågor i Azure AD som att skapa eller uppdatera användare. Användare som har tilldelats den här rollen kan dock ge sig själva eller andra ytterligare behörigheter genom att tilldela ytterligare roller.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/administrativeUnits/allProperties/allTasks | Skapa och hantera administrativa enheter (inklusive medlemmar) |
> | Microsoft. Directory/appRoleAssignments/allProperties/allTasks | Skapa och ta bort appRoleAssignments och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/authorizationPolicy/allProperties/allTasks | Hantera alla aspekter av Auktoriseringsprinciper |
> | Microsoft. Directory/directoryRoles/allProperties/allTasks | Skapa och ta bort katalog roller och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/groupsAssignableToRoles/Create | Skapa rolltilldelningsbara grupper |
> | Microsoft. Directory/groupsAssignableToRoles/Delete | Ta bort roll tilldelnings bara grupper |
> | Microsoft. Directory/groupsAssignableToRoles/Restore | Återställa roll tilldelnings bara grupper |
> | Microsoft. Directory/groupsAssignableToRoles/allProperties/Update | Uppdatera roll tilldelnings bara grupper |
> | Microsoft. Directory/oAuth2PermissionGrants/allProperties/allTasks | Skapa och ta bort OAuth 2,0-behörighet bidrag och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/privilegedIdentityManagement/allProperties/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standard egenskaper i Privileged Identity Management |
> | Microsoft. Directory/roleAssignments/allProperties/allTasks | Skapa och ta bort roll tilldelningar och läsa och uppdatera alla Roll tilldelnings egenskaper |
> | Microsoft. Directory/roleDefinitions/allProperties/allTasks | Skapa och ta bort roll definitioner och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/scopedRoleMemberships/allProperties/allTasks | Skapa och ta bort scopedRoleMemberships och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/Service princip ALS/appRoleAssignedTo/Update | Uppdatera roll tilldelningar för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/Permissions/Update | Uppdatera behörigheter för tjänstens huvud namn |
> | Microsoft. Directory/Service princip ALS/managePermissionGrantsForAll. Microsoft-Company-Admin | Bevilja medgivande för alla behörigheter till program |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="reports-reader"></a>Rapportläsare

Användare med den här rollen kan visa användnings rapporterings data och instrument panelen rapporter i Microsoft 365 administrations Center och implementerings kontext paketet i Power BI. Dessutom ger rollen åtkomst till inloggnings rapporter och aktiviteter i Azure AD och data som returneras av Microsoft Graph repor ting API. En användare som tilldelats rollen rapport läsare kan bara komma åt relevanta användnings-och implementerings mått. De har inte någon administratörs behörighet för att konfigurera inställningar eller komma åt de produktspecifik administrations Center som Exchange. Den här rollen har ingen åtkomst för att visa, skapa eller hantera support biljetter.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/auditLogs/allProperties/Read | Läsa alla egenskaper för gransknings loggar, inklusive privilegierade egenskaper |
> | Microsoft. Directory/provisioningLogs/allProperties/Read | Läs alla egenskaper för etablerings loggar |
> | Microsoft. Directory/signInReports/allProperties/Read | Läs alla egenskaper för inloggnings rapporter, inklusive privilegierade egenskaper |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Office365. Network/Performance/allProperties/Read | Läs alla egenskaper för nätverks prestanda i Microsoft 365 administrations centret |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. usageReports/uplånar/allProperties/Read | Läs Office 365-användnings rapporter |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="search-administrator"></a>Sök administratör

Användare med den här rollen har fullständig åtkomst till alla funktioner för Microsoft Search-hantering i Microsoft 365 administrations centret. Dessa användare kan dessutom Visa meddelande Center, övervaka tjänstens hälsa och skapa tjänst begär Anden.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Office365. messageCenter/meddelanden/läsa | Läsa meddelanden i meddelande Center i Microsoft 365 administrations centret, förutom säkerhets meddelanden |
> | Microsoft. Office365. search/Content/Manage | Skapa och ta bort innehåll och läsa och uppdatera alla egenskaper i Microsoft Search |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="search-editor"></a>Sök redigerare

Användare med den här rollen kan skapa, hantera och ta bort innehåll för Microsoft Search i Microsoft 365 administrations Center, inklusive bok märken, Q&som och platser.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Office365. messageCenter/meddelanden/läsa | Läsa meddelanden i meddelande Center i Microsoft 365 administrations centret, förutom säkerhets meddelanden |
> | Microsoft. Office365. search/Content/Manage | Skapa och ta bort innehåll och läsa och uppdatera alla egenskaper i Microsoft Search |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="security-administrator"></a>Säkerhetsadministratör

Användare med den här rollen har behörighet att hantera säkerhetsrelaterade funktioner i Microsoft 365 Security Center, Azure Active Directory Identity Protection, Azure Active Directory autentisering, Azure Information Protection och Office 365 Security & Compliance Center. Mer information om Office 365-behörigheter finns på [behörigheter i säkerhets & Compliance Center](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

I | Kan göra
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | Övervaka säkerhetsrelaterade principer i Microsoft 365 Services<br>Hantera säkerhetshot och aviseringar<br>Visa rapporter
Identitets skydds Center | Alla behörigheter för säkerhets läsar rollen<br>Dessutom kan du utföra alla åtgärder för identitets skydds Center förutom att återställa lösen ord
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Alla behörigheter för säkerhets läsar rollen<br>**Det går inte att** hantera roll tilldelningar eller inställningar för Azure AD
[Office 365 säkerhets & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Hantera säkerhetsprinciper<br>Visa, undersöka och reagera på säkerhetshot<br>Visa rapporter
Azure Advanced Threat Protection | Övervaka och svara på misstänkt säkerhets aktivitet
Windows Defender ATP och EDR | Tilldela roller<br>Hantera dator grupper<br>Konfigurera identifiering av slut punkts hot och automatiserad reparation<br>Visa, undersöka och svara på aviseringar
[Intune](/intune/role-based-access-control) | Visar information om användare, enhet, registrering, konfiguration och program<br>Det går inte att göra ändringar i Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Lägg till administratörer, lägga till principer och inställningar, ladda upp loggar och utföra styrnings åtgärder
[Azure Security Center](../../key-vault/managed-hsm/built-in-roles.md) | Kan visa säkerhets principer, Visa säkerhets tillstånd, redigera säkerhets principer, Visa aviseringar och rekommendationer, ignorera aviseringar och rekommendationer
[Microsoft 365 tjänstens hälsa](/office365/enterprise/view-service-health) | Visa hälso tillståndet för Microsoft 365 Services
[Smart utlåsning](../authentication/howto-password-smart-lockout.md) | Definiera tröskelvärde och varaktighet för utelåsning vid misslyckade inloggnings händelser.
[Lösen ords skydd](../authentication/concept-password-ban-bad.md) | Konfigurera anpassad blockerad lösen ords lista eller lokalt lösen ords skydd.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/Applications/policies/Update | Uppdatera principer för program |
> | Microsoft. Directory/auditLogs/allProperties/Read | Läsa alla egenskaper för gransknings loggar, inklusive privilegierade egenskaper |
> | Microsoft. Directory/bitlockerKeys/Key/Read | Läs BitLocker-metadata och-nyckel på enheter |
> | Microsoft. Directory/entitlementManagement/allProperties/Read | Läsa alla egenskaper i hantering av Azure AD-berättigande |
> | Microsoft. Directory/identityProtection/allProperties/Read | Läs alla resurser i Azure AD Identity Protection |
> | Microsoft. Directory/identityProtection/allProperties/Update | Uppdatera alla resurser i Azure AD Identity Protection |
> | Microsoft. Directory/principer/skapa | Skapa principer i Azure AD |
> | Microsoft. Directory/principer/Delete | Ta bort principer i Azure AD |
> | Microsoft. Directory/policies/Basic/Update | Uppdatera grundläggande egenskaper för principer |
> | Microsoft. Directory/principer/ägare/uppdatering | Uppdatera ägare av principer |
> | Microsoft. Directory/policys/tenantDefault/Update | Uppdatera standard organisations principer |
> | Microsoft. Directory/conditionalAccessPolicies/Create | Skapa principer för villkorsstyrd åtkomst |
> | Microsoft. Directory/conditionalAccessPolicies/Delete | Ta bort principer för villkorlig åtkomst |
> | Microsoft. Directory/conditionalAccessPolicies/standard/Read | Läs policies. conditionalAccess-egenskap |
> | Microsoft. Directory/conditionalAccessPolicies/Owners/Read | Läs policies. conditionalAccess-egenskap |
> | Microsoft. Directory/conditionalAccessPolicies/policyAppliedTo/Read | Läs policies. conditionalAccess-egenskap |
> | Microsoft. Directory/conditionalAccessPolicies/Basic/Update | Uppdatera grundläggande egenskaper för principer för villkorlig åtkomst |
> | Microsoft. Directory/conditionalAccessPolicies/Owners/Update | Uppdatera policys. conditionalAccess-egenskapen |
> | Microsoft. Directory/conditionalAccessPolicies/tenantDefault/Update | Uppdatera policys. conditionalAccess-egenskapen |
> | Microsoft. Directory/privilegedIdentityManagement/allProperties/Read | Läs alla resurser i Privileged Identity Management |
> | Microsoft. Directory/provisioningLogs/allProperties/Read | Läs alla egenskaper för etablerings loggar |
> | Microsoft. Directory/Service princip ALS/policies/Update | Uppdatera principer för tjänstens huvud namn |
> | Microsoft. Directory/signInReports/allProperties/Read | Läs alla egenskaper för inloggnings rapporter, inklusive privilegierade egenskaper |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Office365. protectionCenter/upplånare/standard/Read | Läsa standard egenskaper för alla resurser i Center för säkerhet och efterlevnad |
> | Microsoft. Office365. protectionCenter/utlånare/grundläggande/uppdatera | Uppdatera grundläggande egenskaper för alla resurser i Center för säkerhet och efterlevnad |
> | Microsoft. Office365. protectionCenter/attackSimulator/nytto Last/allProperties/allTasks | Skapa och hantera angrepps nytto laster i angrepps Simulator |
> | Microsoft. Office365. protectionCenter/attackSimulator/Reports/allProperties/Read | Läs rapporter om attack simulering, svar och tillhör ande utbildning |
> | Microsoft. Office365. protectionCenter/attackSimulator/simulering/allProperties/allTasks | Skapa och hantera mallar för attack simulering i angrepps Simulator |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="security-operator"></a>Säkerhetsoperatör

Användare med den här rollen kan hantera aviseringar och ha global Läs behörighet för säkerhetsrelaterade funktioner, inklusive all information i Microsoft 365 Security Center, Azure Active Directory, identitets skydd, Privileged Identity Management och Office 365 Security & Compliance Center. Mer information om Office 365-behörigheter finns på [behörigheter i säkerhets & Compliance Center](/office365/securitycompliance/permissions-in-the-security-and-compliance-center).

I | Kan göra
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | Alla behörigheter för säkerhets läsar rollen<br>Visa, undersöka och svara på säkerhetshot
Azure AD Identity Protection | Alla behörigheter för säkerhets läsar rollen<br>Dessutom kan du utföra alla åtgärder för identitets skydds Center förutom att återställa lösen ord och konfigurera e-postaviseringar.
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Alla behörigheter för säkerhets läsar rollen
[Office 365 säkerhets & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Alla behörigheter för säkerhets läsar rollen<br>Visa, undersöka och svara på säkerhets aviseringar
Windows Defender ATP och EDR | Alla behörigheter för säkerhets läsar rollen<br>Visa, undersöka och svara på säkerhets aviseringar
[Intune](/intune/role-based-access-control) | Alla behörigheter för säkerhets läsar rollen
[Cloud App Security](/cloud-app-security/manage-admins) | Alla behörigheter för säkerhets läsar rollen
[Microsoft 365 tjänstens hälsa](/office365/enterprise/view-service-health) | Visa hälso tillståndet för Microsoft 365 Services

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/auditLogs/allProperties/Read | Läsa alla egenskaper för gransknings loggar, inklusive privilegierade egenskaper |
> | Microsoft. Directory/cloudAppSecurity/allProperties/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standard egenskaper i Microsoft Cloud App Security |
> | Microsoft. Directory/identityProtection/allProperties/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standard egenskaper i Azure AD Identity Protection |
> | Microsoft. Directory/privilegedIdentityManagement/allProperties/Read | Läs alla resurser i Privileged Identity Management |
> | Microsoft. Directory/provisioningLogs/allProperties/Read | Läs alla egenskaper för etablerings loggar |
> | Microsoft. Directory/signInReports/allProperties/Read | Läs alla egenskaper för inloggnings rapporter, inklusive privilegierade egenskaper |
> | Microsoft. Azure. advancedThreatProtection/upplånade/allTasks | Hantera alla aspekter av Azure Advanced Threat Protection |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Intune/upplånade/Läs | Läs alla resurser i Microsoft Intune |
> | Microsoft. Office365. securityComplianceCenter/uplånar/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standard egenskaper i Microsoft 365 Security and Compliance Center |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Windows. defenderAdvancedThreatProtection/upplånade/allTasks | Hantera alla aspekter av Microsoft Defender för slut punkt |

## <a name="security-reader"></a>Säkerhetsläsare

Användare med den här rollen har global skrivskyddad åtkomst till säkerhetsrelaterad funktion, inklusive all information i Microsoft 365 Security Center, Azure Active Directory, identitets skydd, Privileged Identity Management, samt möjligheten att läsa Azure Active Directory inloggnings rapporter och gransknings loggar och i Office 365 Security & Compliance Center. Mer information om Office 365-behörigheter finns på [behörigheter i säkerhets & Compliance Center](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

I | Kan göra
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | Visa säkerhetsrelaterade principer i Microsoft 365 Services<br>Visa säkerhetshot och aviseringar<br>Visa rapporter
Identitets skydds Center | Läs alla säkerhets rapporter och inställnings information för säkerhetsfunktioner<br><ul><li>Anti-spam<li>Kryptering<li>Skydd mot data förlust<li>Program mot skadlig kod<li>Advanced Threat Protection<li>Skydd mot nätfiske<li>Regler för e-postflöde
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Har skrivskyddad åtkomst till all information som finns i Azure AD Privileged Identity Management: principer och rapporter för roll tilldelningar i Azure AD och säkerhets granskningar.<br>**Det går inte att** registrera dig för Azure AD Privileged Identity Management eller göra ändringar i den. I Privileged Identity Management-portalen eller via PowerShell kan någon i den här rollen aktivera ytterligare roller (till exempel global administratör eller privilegie rad roll administratör), om användaren är berättigad till dem.
[Office 365 säkerhets & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Visa säkerhetsprinciper<br>Visa och undersök säkerhetshot<br>Visa rapporter
Windows Defender ATP och EDR | Visa och undersök aviseringar. När du aktiverar rollbaserad åtkomst kontroll i Windows Defender ATP kan användare med Läs behörighet som rollen Azure AD Security Reader förlora åtkomst tills de tilldelas till en Windows Defender ATP-roll.
[Intune](/intune/role-based-access-control) | Visar information om användare, enhet, registrering, konfiguration och programmet. Kan inte göra ändringar i Intune.
[Cloud App Security](/cloud-app-security/manage-admins) | Har Läs behörighet och kan hantera aviseringar
[Azure Security Center](../../key-vault/managed-hsm/built-in-roles.md) | Kan visa rekommendationer och aviseringar, Visa säkerhets principer, Visa säkerhets tillstånd, men kan inte göra ändringar
[Microsoft 365 tjänstens hälsa](/office365/enterprise/view-service-health) | Visa hälso tillståndet för Microsoft 365 Services

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/auditLogs/allProperties/Read | Läsa alla egenskaper för gransknings loggar, inklusive privilegierade egenskaper |
> | Microsoft. Directory/bitlockerKeys/Key/Read | Läs BitLocker-metadata och-nyckel på enheter |
> | Microsoft. Directory/entitlementManagement/allProperties/Read | Läsa alla egenskaper i hantering av Azure AD-berättigande |
> | Microsoft. Directory/identityProtection/allProperties/Read | Läs alla resurser i Azure AD Identity Protection |
> | Microsoft. Directory/policys/standard/Read | Läsa grundläggande egenskaper för principer |
> | Microsoft. Directory/principer/ägare/läsa | Läs ägare till principer |
> | Microsoft. Directory/policys/policyAppliedTo/Read | Läs policies. policyAppliedTo-egenskap |
> | Microsoft. Directory/conditionalAccessPolicies/standard/Read | Läs policies. conditionalAccess-egenskap |
> | Microsoft. Directory/conditionalAccessPolicies/Owners/Read | Läs policies. conditionalAccess-egenskap |
> | Microsoft. Directory/conditionalAccessPolicies/policyAppliedTo/Read | Läs policies. conditionalAccess-egenskap |
> | Microsoft. Directory/privilegedIdentityManagement/allProperties/Read | Läs alla resurser i Privileged Identity Management |
> | Microsoft. Directory/provisioningLogs/allProperties/Read | Läs alla egenskaper för etablerings loggar |
> | Microsoft. Directory/signInReports/allProperties/Read | Läs alla egenskaper för inloggnings rapporter, inklusive privilegierade egenskaper |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Office365. protectionCenter/upplånare/standard/Read | Läsa standard egenskaper för alla resurser i Center för säkerhet och efterlevnad |
> | Microsoft. Office365. protectionCenter/attackSimulator/nytto Last/allProperties/Read | Läs alla egenskaper för angrepps nytto laster i angrepps Simulator |
> | Microsoft. Office365. protectionCenter/attackSimulator/Reports/allProperties/Read | Läs rapporter om attack simulering, svar och tillhör ande utbildning |
> | Microsoft. Office365. protectionCenter/attackSimulator/simulering/allProperties/Read | Läs alla egenskaper för mallar för attack simulering i angrepps Simulator |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="service-support-administrator"></a>Tjänst support administratör

Användare med den här rollen kan öppna support förfrågningar med Microsoft för Azure och Microsoft 365-tjänster och Visa instrument panelen och meddelande centret i [Azure Portal](https://portal.azure.com) och [Microsoft 365 administrations Center](https://admin.microsoft.com). Mer information [om administratörs roller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Tidigare kallades den här rollen "tjänst administratör" i [Azure Portal](https://portal.azure.com) och [Microsoft 365 administrations Center](https://admin.microsoft.com). Vi har bytt namn till "service support Administrator" för att överensstämma med befintlig-namnet i Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Office365. Network/Performance/allProperties/Read | Läs alla egenskaper för nätverks prestanda i Microsoft 365 administrations centret |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="sharepoint-administrator"></a>SharePoint-administratör

Användare med den här rollen har globala behörigheter inom Microsoft SharePoint Online när tjänsten finns, samt möjligheten att skapa och hantera alla Microsoft 365 grupper, hantera support biljetter och övervaka tjänstens hälsa. Mer information [om administratörs roller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Den här rollen identifieras som "SharePoint-tjänsteadministratör" i Microsoft Graph API och Azure AD PowerShell. Det är "SharePoint-administratör" i [Azure Portal](https://portal.azure.com).

> [!NOTE]
> Den här rollen beviljar även begränsade behörigheter till Microsoft Graph API för Microsoft Intune, vilket möjliggör hantering och konfiguration av principer som rör SharePoint-och OneDrive-resurser.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/grupper. Unified/Create | Skapa Microsoft 365 grupper med undantag för roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper. Unified/Delete | Ta bort Microsoft 365 grupper med undantag av roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper. Unified/Restore | Återställ Microsoft 365 grupper |
> | Microsoft. Directory/Groups. Unified/Basic/Update | Uppdatera grundläggande egenskaper för Microsoft 365 grupper med undantag för roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper. Unified/members/Update | Uppdatera medlemmar i Microsoft 365 grupper med undantag av roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper. Unified/Owners/Update | Uppdatera ägare av Microsoft 365 grupper med undantag för roll tilldelnings bara grupper |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Office365. Network/Performance/allProperties/Read | Läs alla egenskaper för nätverks prestanda i Microsoft 365 administrations centret |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. sharePoint/upplånade/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standard egenskaper i SharePoint |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. usageReports/uplånar/allProperties/Read | Läs Office 365-användnings rapporter |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="skype-for-business-administrator"></a>Skype för företag-administratör

Användare med den här rollen har globala behörigheter inom Microsoft Skype för företag, när tjänsten finns och hantera Skype-specifika användarattribut i Azure Active Directory. Dessutom ger den här rollen möjlighet att hantera support biljetter och övervaka tjänstens hälsa och få åtkomst till team och Skype för Business administrations Center. Kontot måste också vara licensierat för team, eller så kan det inte köra team PowerShell-cmdletar. Mer information [om administrations rollen för Skype för företag](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) och team licens information finns på [Skype för företag och Microsoft Teams tilläggs licensiering](/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

> [!NOTE]
> I Microsoft Graph API och Azure AD PowerShell identifieras rollen som "Lync-tjänsteadministratör". Det är "Skype för företag-administratör" i [Azure Portal](https://portal.azure.com/).

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. skypeForBusiness/uplånar/allTasks | Hantera alla aspekter av Skype för företag – Online |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. usageReports/uplånar/allProperties/Read | Läs Office 365-användnings rapporter |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="teams-administrator"></a>Team administratör

Användare med den här rollen kan hantera alla aspekter av arbets belastningen Microsoft Teams via Microsoft Teams & Skype för Business administrations Center och respektive PowerShell-moduler. Detta omfattar bland annat alla hanterings verktyg som rör telefoni, meddelanden, möten och själva teamet. Den här rollen ger dessutom möjlighet att skapa och hantera alla Microsoft 365 grupper, hantera support biljetter och övervaka tjänstens hälsa.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/grupper/hiddenMembers/Read | Läsa dolda medlemmar i en grupp |
> | Microsoft. Directory/grupper. Unified/Create | Skapa Microsoft 365 grupper med undantag för roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper. Unified/Delete | Ta bort Microsoft 365 grupper med undantag av roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper. Unified/Restore | Återställ Microsoft 365 grupper |
> | Microsoft. Directory/Groups. Unified/Basic/Update | Uppdatera grundläggande egenskaper för Microsoft 365 grupper med undantag för roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper. Unified/members/Update | Uppdatera medlemmar i Microsoft 365 grupper med undantag av roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper. Unified/Owners/Update | Uppdatera ägare av Microsoft 365 grupper med undantag för roll tilldelnings bara grupper |
> | Microsoft. Directory/Service princip ALS/managePermissionGrantsForGroup. Microsoft-all-Application-Permissions | Bevilja en tjänst Principal direkt åtkomst till en grupps data |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Office365. Network/Performance/allProperties/Read | Läs alla egenskaper för nätverks prestanda i Microsoft 365 administrations centret |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. skypeForBusiness/uplånar/allTasks | Hantera alla aspekter av Skype för företag – Online |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. usageReports/uplånar/allProperties/Read | Läs Office 365-användnings rapporter |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |
> | Microsoft. Teams/utlånare/allProperties/allTasks | Hantera alla resurser i team |

## <a name="teams-communications-administrator"></a>Team kommunikations administratör

Användare med den här rollen kan hantera delar av arbets belastningen Microsoft Teams som rör röst & telefoni. Här ingår hanterings verktyg för tilldelning av telefonnummer, röst-och mötes principer och fullständig åtkomst till verktygen för anrops analys.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. skypeForBusiness/uplånar/allTasks | Hantera alla aspekter av Skype för företag – Online |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. usageReports/uplånar/allProperties/Read | Läs Office 365-användnings rapporter |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |
> | Microsoft. Teams/callQuality/allProperties/Read | Läs alla data på instrument panelen för samtals kvalitet (CQD) |
> | Microsoft. Teams/möten/allProperties/allTasks | Hantera möten, inklusive Mötes principer, konfigurationer och konferens bryggor |
> | Microsoft. Teams/Voice/allProperties/allTasks | Hantera rösten inklusive anrops principer och telefonnummer och tilldelning |

## <a name="teams-communications-support-engineer"></a>Support tekniker för Teams kommunikation

Användare i den här rollen kan felsöka kommunikations problem i Microsoft Teams & Skype för företag med hjälp av fel söknings verktygen för användar anrop i Microsoft Teams & Skype för företag administrations Center. Användare med den här rollen kan Visa fullständig information om anrops poster för alla berörda deltagare. Den här rollen har ingen åtkomst för att visa, skapa eller hantera support biljetter.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. skypeForBusiness/uplånar/allTasks | Hantera alla aspekter av Skype för företag – Online |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |
> | Microsoft. Teams/callQuality/allProperties/Read | Läs alla data på instrument panelen för samtals kvalitet (CQD) |

## <a name="teams-communications-support-specialist"></a>Support specialist för Teams kommunikation

Användare i den här rollen kan felsöka kommunikations problem i Microsoft Teams & Skype för företag med hjälp av fel söknings verktygen för användar anrop i Microsoft Teams & Skype för företag administrations Center. Användare med den här rollen kan bara Visa användar information i anropet för den aktuella användaren som de har sökt efter. Den här rollen har ingen åtkomst för att visa, skapa eller hantera support biljetter.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. skypeForBusiness/uplånar/allTasks | Hantera alla aspekter av Skype för företag – Online |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |
> | Microsoft. Teams/callQuality/standard/Read | Läs grundläggande data på instrument panelen för samtals kvalitet (CQD) |

## <a name="teams-devices-administrator"></a>Team enhets administratör

Användare med den här rollen kan hantera [team-certifierade enheter](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) från Team administrations centret. Med den här rollen kan du snabbt och enkelt se alla enheter med möjlighet att söka efter och filtrera enheter. Användaren kan kontrol lera information om varje enhet, inklusive inloggat konto, märke och modell för enheten. Användaren kan ändra inställningarna på enheten och uppdatera program varu versionerna. Den här rollen beviljar inte behörigheter för att kontrol lera team aktiviteter och enhetens kvalitet.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |
> | Microsoft. Teams/Devices/standard/Read | Hantera alla aspekter av team – certifierade enheter, inklusive konfigurations principer |

## <a name="usage-summary-reports-reader"></a>Rapport läsare för användnings Sammanfattning

Användare med den här rollen har åtkomst till sammanställda data för klient nivå och associerade insikter i Microsoft 365 administrations Center för användning och produktivitets poäng, men kan inte komma åt information om användar nivå eller insikter. I Microsoft 365 administrations Center för de två rapporterna skiljer vi mellan information om sammanställda data på klient nivå och information på användar nivå. Den här rollen ger ett extra skydds lager för enskilda användares identifierbara data som har begärts av både kunder och juridiska team.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Office365. Network/Performance/allProperties/Read | Läs alla egenskaper för nätverks prestanda i Microsoft 365 administrations centret |
> | Microsoft. Office365. usageReports/upplånare/standard/Read | Läs användnings rapporter för samlad Office 365-klient på klient nivå |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="user-administrator"></a>Användaradministratör

Användare med den här rollen kan skapa användare och hantera alla aspekter av användare med vissa begränsningar (se tabellen) och kan uppdatera principer för lösen ordets giltighets tid. Dessutom kan användare med den här rollen skapa och hantera alla grupper. Den här rollen omfattar även möjligheten att skapa och hantera användar visningar, hantera support biljetter och övervaka tjänstens hälsa. Användar administratörer har inte behörighet att hantera vissa användar egenskaper för användare i de flesta administratörs roller. Användare med den här rollen har inte behörighet att hantera MFA. Rollerna som är undantag till den här begränsningen visas i följande tabell.

| Användar administratörs behörighet | Anteckningar |
| --- | --- |
| Skapa användare och grupper<br/>Skapa och hantera användarvyer<br/>Hantera Office-support biljetter<br/>Uppdatera principer för förfallo datum för lösen ord |  |
| Hantera licenser<br/>Hantera alla användar egenskaper utom användarens huvud namn | Gäller alla användare, inklusive alla administratörer |
| Ta bort och Återställ<br/>Inaktivera och aktivera<br/>Hantera alla användar egenskaper inklusive användarens huvud namn<br/>Uppdatera (FIDO) enhets nycklar | Gäller för användare som inte är administratörer eller i någon av följande roller:<ul><li>Support administratör</li><li>Användare utan roll</li><li>Användaradministratör</li></ul> |
| Invalidera uppdateringstoken<br/>Återställa lösenord | En lista över de roller som en användar administratör kan återställa lösen ord för och ogiltig uppdateringstoken finns i [behörigheter för lösen ords återställning](#password-reset-permissions). |

> [!IMPORTANT]
> Användare med den här rollen kan ändra lösen ord för personer som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i och utanför Azure Active Directory. Att ändra lösen ordet för en användare kan innebära att du antar användarens identitet och behörigheter. Exempel:
>
>- Program registrering och företags program ägare, som kan hantera autentiseringsuppgifter för appar som de äger. Dessa appar kan ha privilegierade behörigheter i Azure AD och inte beviljas till användar administratörer. Genom den här sökvägen kan en användar administratör kunna ta hänsyn till identiteten för en program ägare och sedan ytterligare anta identiteten för ett privilegierat program genom att uppdatera autentiseringsuppgifterna för programmet.
>- Azure-Prenumerationens ägare, som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i Azure.
>- Säkerhets grupp och Microsoft 365 grupp ägare, som kan hantera grupp medlemskap. Dessa grupper kan ge åtkomst till känslig eller privat information eller kritisk konfiguration i Azure AD och någon annan stans.
>- Administratörer i andra tjänster utanför Azure AD, till exempel Exchange Online, Office Security och Compliance Center och personal system.
>- Icke-administratörer som chefer, juridiska konsulter och personal anställda som kan ha till gång till känslig eller privat information.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft. Directory/appRoleAssignments/Create | Skapa program roll tilldelningar |
> | Microsoft. Directory/appRoleAssignments/Delete | Ta bort program roll tilldelningar |
> | Microsoft. Directory/appRoleAssignments/Basic/Update | Uppdatera grundläggande egenskaper för program roll tilldelningar |
> | Microsoft. Directory/kontakter/skapa | Skapa kontakter |
> | Microsoft. Directory/Contacts/Delete | Ta bort kontakter |
> | Microsoft. Directory/Contacts/Basic/Update | Uppdatera grundläggande egenskaper för kontakter |
> | Microsoft. Directory/entitlementManagement/allProperties/allTasks | Skapa och ta bort resurser och läsa och uppdatera alla egenskaper i hantering av Azure AD-rättigheter |
> | Microsoft. Directory/grupper/assignLicense | Tilldela produkt licenser till grupper för gruppbaserad licensiering |
> | Microsoft. Directory/grupper/skapa | Skapa grupper med undantag för roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper/ta bort | Ta bort grupper, exklusive roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper/hiddenMembers/Read | Läsa dolda medlemmar i en grupp |
> | Microsoft. Directory/grupper/reprocessLicenseAssignment | Ombearbeta licens tilldelningar för gruppbaserad licensiering |
> | Microsoft. Directory/grupper/Återställ | Återställa borttagna grupper |
> | Microsoft. Directory/grupper/Basic/Update | Uppdatera grundläggande egenskaper för grupper, exklusive roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper/klassificering/uppdatera | Uppdatera klassificerings egenskapen för grupper, exklusive roll tilldelnings bara grupper |
> | Microsoft. Directory/Groups/dynamicMembershipRule/Update | Uppdatera regel för dynamiskt medlemskap i grupper, exklusive roll tilldelnings bara grupper |
> | Microsoft. Directory/Groups/groupType/Update | Uppdatera egenskapen groupType för en grupp |
> | Microsoft. Directory/grupper/medlemmar/uppdatera | Uppdatera medlemmar i grupper, exklusive roll tilldelnings bara grupper |
> | Microsoft. Directory/Groups/onPremWriteBack/Update | Uppdatera Azure AD-grupper så att de skrivs tillbaka till lokalt |
> | Microsoft. Directory/grupper/ägare/uppdatera | Uppdatera ägare av grupper, exklusive roll tilldelnings bara grupper |
> | Microsoft. Directory/grupper/inställningar/uppdatera | Uppdatera inställningar för grupper |
> | Microsoft. Directory/grupper/synlighet/uppdatera | Uppdatera Synlighets egenskapen för grupper |
> | Microsoft. Directory/oAuth2PermissionGrants/allProperties/allTasks | Skapa och ta bort OAuth 2,0-behörighet bidrag och läsa och uppdatera alla egenskaper |
> | Microsoft. Directory/Service princip ALS/appRoleAssignedTo/Update | Uppdatera roll tilldelningar för tjänstens huvud namn |
> | Microsoft. Directory/Users/assignLicense | Hantera användar licenser |
> | Microsoft. Directory/Users/Create | Lägga till användare |
> | Microsoft. Directory/Users/Delete | Ta bort användare |
> | Microsoft. Directory/användare/inaktivera | Inaktivera användare |
> | Microsoft. Directory/Users/Enable | Aktivera användare |
> | Microsoft. Directory/Users/inviteGuest | Bjud in gästanvändare |
> | Microsoft. Directory/Users/invalidateAllRefreshTokens | Framtvinga utloggning genom att invalidera token för användar uppdatering |
> | Microsoft. Directory/Users/reprocessLicenseAssignment | Ombearbeta licens tilldelningar för användare |
> | Microsoft. Directory/Users/Restore | Återställ borttagna användare |
> | Microsoft. Directory/Users/Basic/Update | Uppdatera grundläggande egenskaper för användare |
> | Microsoft. Directory/Users/Manager/Update | Uppdaterings hanteraren för användare |
> | Microsoft. Directory/Users/Password/Update | Återställ lösen ord för alla användare |
> | Microsoft. Directory/Users/userPrincipalName/Update | Uppdatera användarens huvud namn |
> | Microsoft. Azure. serviceHealth/upplånade/allTasks | Läsa och konfigurera Azure Service Health |
> | Microsoft. Azure. supportTickets/upplånade/allTasks | Skapa och hantera support biljetter för Azure |
> | Microsoft. Office365. serviceHealth/uplånar/allTasks | Läsa och konfigurera Service Health i Microsoft 365 administrations Center |
> | Microsoft. Office365. supportTickets/uplånar/allTasks | Skapa och hantera Microsoft 365 tjänst begär Anden |
> | Microsoft. Office365. webports/-upplånare/standard/Read | Läsa grundläggande egenskaper för alla resurser i Microsoft 365 administrations Center |

## <a name="deprecated-roles"></a>Föråldrade roller

Följande roller ska inte användas. De är inaktuella och kommer att tas bort från Azure AD i framtiden.

* AdHoc-licens administratör
* Enhets anslutning
* Enhets hanterare
* Enhets användare
* E-postverifierad användar skapare
* Mailbox-administratör
* Anslutning till arbets plats enhet

## <a name="roles-not-shown-in-the-portal"></a>Roller som inte visas i portalen

Alla roller som returneras av PowerShell eller MS Graph API visas i Azure Portal. I följande tabell ordnas skillnaderna.

API-namn | Azure Portal namn | Anteckningar
-------- | ------------------- | -------------
Enhets anslutning | Inaktuell | [Dokumentation om föråldrade roller](#deprecated-roles)
Enhets hanterare | Inaktuell | [Dokumentation om föråldrade roller](#deprecated-roles)
Enhets användare | Inaktuell | [Dokumentation om föråldrade roller](#deprecated-roles)
Konton för katalog synkronisering | Visas inte eftersom det inte ska användas | [Dokumentation om Directory Synchronization-konton](#directory-synchronization-accounts)
Gästanvändare | Visas inte eftersom det inte kan användas | NA
Support på partner nivå 1 | Visas inte eftersom det inte ska användas | [Support dokumentation för partner 1](#partner-tier1-support)
Support på partner nivå 2 | Visas inte eftersom det inte ska användas | [Support dokumentation för partner – nivå 2](#partner-tier2-support)
Begränsad gäst användare | Visas inte eftersom det inte kan användas | NA
User | Visas inte eftersom det inte kan användas | NA
Anslutning till arbets plats enhet | Inaktuell | [Dokumentation om föråldrade roller](#deprecated-roles)

## <a name="password-reset-permissions"></a>Behörigheter för lösen ords återställning

Kolumn rubrikerna representerar de roller som kan återställa lösen ord. Tabell rader innehåller de roller som lösen ordet kan återställas för.

Lösen ordet kan återställas | Lösen ords administratör | Supportavdelningen-administratör | Administratör för autentisering | Användar administratör | Administratör för privilegie rad autentisering | Global administratör
------ | ------ | ------ | ------ | ------ | ------ | ------
Administratör för autentisering | &nbsp; | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Katalog läsare | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Global administratör | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:\*
Grupp administratör | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Gäst deltagare | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Supportavdelningen-administratör | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Meddelande Center läsare | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Lösen ords administratör | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Administratör för privilegie rad autentisering | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Administratör för privilegie rad roll | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Rapportläsare | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Användare (ingen administratörs roll) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Användar administratör | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Rapport läsare för användnings Sammanfattning | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:

\* En global administratör kan inte ta bort sin egen globala administratörs tilldelning. Detta är för att förhindra en situation där en organisation har 0 globala administratörer.

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure AD-roller till grupper](groups-assign-role.md)
- [Förstå de olika rollerna](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Tilldela en användare administratörsbehörighet för en Azure-prenumeration](../../role-based-access-control/role-assignments-portal-subscription-admin.md)
