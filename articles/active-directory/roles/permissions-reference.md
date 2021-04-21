---
title: Inbyggda roller i Azure AD – Azure Active Directory
description: Beskriver Azure Active Directory inbyggda roller och behörigheter.
services: active-directory
author: rolyon
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 04/20/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf614922503212488c822ac020960b0ddb99fc28
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780073"
---
# <a name="azure-ad-built-in-roles"></a>Inbyggda roller i Azure AD

I Azure Active Directory (Azure AD) tilldelar du en annan administratör eller icke-administratör för att hantera Azure AD-resurser en Azure AD-roll som ger dem de behörigheter som de behöver. Du kan till exempel tilldela roller för att lägga till eller ändra användare, återställa användarlösenord, hantera användarlicenser eller hantera domännamn.

Den här artikeln innehåller de inbyggda Roller i Azure AD som du kan tilldela för att tillåta hantering av Azure AD-resurser. Information om hur du tilldelar roller finns i [Tilldela Azure AD-roller till användare.](manage-roles-portal.md)

## <a name="all-roles"></a>Alla roller

> [!div class="mx-tableFixed"]
> | Roll | Beskrivning | Mall-ID |
> | --- | --- | --- |
> | [Programadministratör](#application-administrator) | Kan skapa och hantera alla aspekter av appregistreringar och företagsappar. | 9b895d92-2cd3-44c7-9d02-a6ac2d5ea5c3 |
> | [Programutvecklare](#application-developer) | Kan skapa programregistreringar oberoende av inställningen "Användare kan registrera program". | cf1c38e5-3621-4004-a7cb-879624dced7c |
> | [Attack Payload Author](#attack-payload-author) | Kan skapa attacknyttolaster som en administratör kan initiera senare. | 9c6df0f2-1e7c-4dc3-b195-66dfbd24aa8f |
> | [Administratör för attacksimulering](#attack-simulation-administrator) | Kan skapa och hantera alla aspekter av attacksimuleringskampanjer. | c430b396-e693-46cc-96f3-db01bf8bb62a |
> | [Autentiseringsadministratör](#authentication-administrator) | Har åtkomst till att visa, ange och återställa autentiseringsmetodinformation för alla användare som inte är administratörer. | c4e39bd9-1100-46d3-8c65-fb160da0071f |
> | [Administratör för autentiseringsprincip](#authentication-policy-administrator) | Kan skapa och hantera alla aspekter av autentiseringsmetoder och lösenordsskyddsprinciper. | 0526716b-113d-4c15-b2c8-68e3c22b9f80 |
> | [Lokal administratör för Azure AD-ansluten enhet](#azure-ad-joined-device-local-administrator) | Användare som har tilldelats den här rollen läggs till i den lokala administratörsgruppen på Azure AD-anslutna enheter. | 9f06204d-73c1-4d4c-880a-6edb90606fd8 |
> | [Azure DevOps-administratör](#azure-devops-administrator) | Kan hantera organisationsprinciper och inställningar för Azure DevOps. | e3973bdf-4987-49ae-837a-ba8e231c7286 |
> | [Azure Information Protection administratör](#azure-information-protection-administrator) | Kan hantera alla aspekter av Azure Information Protection produkt. | 7495fdc4-34c4-4d15-a289-98788ce399fd |
> | [B2C IEF-administratör för nyckeluppsättning](#b2c-ief-keyset-administrator) | Kan hantera hemligheter för federation och kryptering i Identity Experience Framework (IEF). | aaf43236-0c0d-4d5f-883a-6955382ac081 |
> | [B2C IEF-principadministratör](#b2c-ief-policy-administrator) | Kan skapa och hantera principer för förtroenderamverk i Identity Experience Framework (IEF). | 3edaf663-341e-4475-9f94-5c398ef6c070 |
> | [Faktureringsadministratör](#billing-administrator) | Kan utföra vanliga faktureringsrelaterade uppgifter som att uppdatera betalningsinformation. | b0f54661-2d74-4c50-afa3-1ec803f12efe |
> | [Molnprogramadministratör](#cloud-application-administrator) | Kan skapa och hantera alla aspekter av appregistreringar och företagsappar utom App Proxy. | 158c047a-c907-4556-b7ef-446551a6b5f7 |
> | [Molnenhetsadministratör](#cloud-device-administrator) | Begränsad åtkomst för att hantera enheter i Azure AD. | 7698a772-787b-4ac8-901f-60d6b08affd2 |
> | [Efterlevnadsadministratör](#compliance-administrator) | Kan läsa och hantera kompatibilitetskonfigurationer och -rapporter i Azure AD och Microsoft 365. | 17315797-102d-40b4-93e0-432062caca18 |
> | [Efterlevnadsdataadministratör](#compliance-data-administrator) | Skapar och hanterar efterlevnadsinnehåll. | e6d1a23a-da11-4be4-9570-befc86d067a7 |
> | [Administratör för villkorsstyrd åtkomst](#conditional-access-administrator) | Kan hantera funktioner för villkorlig åtkomst. | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9 |
> | [Customer LockBox-åtkomst godkännare](#customer-lockbox-access-approver) | Kan godkänna Microsofts supportbegäranden om att få åtkomst till kundens organisationsdata. | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91 |
> | [Desktop Analytics administratör](#desktop-analytics-administrator) | Kan komma åt och hantera verktyg och tjänster för skrivbordshantering. | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4 |
> | [Katalogläsare](#directory-readers) | Kan läsa grundläggande kataloginformation. Används ofta för att bevilja katalogläsningsåtkomst till program och gäster. | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b |
> | [Katalogsynkroniseringskonton](#directory-synchronization-accounts) | Används endast av Azure AD Connect tjänsten. | d29b2b05-8046-44ba-8758-1e26182fcf32 |
> | [Katalogskrivare](#directory-writers) | Kan läsa och skriva grundläggande kataloginformation. För att bevilja åtkomst till program som inte är avsedda för användare. | 9360feb5-f418-4baa-8175-e2a00bac4301 |
> | [Domännamnsadministratör](#domain-name-administrator) | Kan hantera domännamn i molnet och lokalt. | 8329153b-31d0-4727-b945-745eb3bc5f31 |
> | [Dynamics 365-administratör](#dynamics-365-administrator) | Kan hantera alla aspekter av Dynamics 365-produkten. | 44367163-eba1-44c3-98af-f5787879f96a |
> | [Exchange-administratör](#exchange-administrator) | Kan hantera alla aspekter av Exchange-produkten. | 29232cdf-9323-42fd-ade2-1d097af3e4de |
> | [Administratör för Exchange-mottagare](#exchange-recipient-administrator) | Kan skapa eller uppdatera Exchange Online-mottagare i Exchange Online-organisationen. | 31392ffb-586c-42d1-9346-e59415a2cc4e |
> | [Administratör för användarflöde för externt ID](#external-id-user-flow-administrator) | Kan skapa och hantera alla aspekter av användarflöden. | 6e591065-9bad-43ed-90f3-e9424366d2f0 |
> | [Administratör för användarflödesattribut för externt ID](#external-id-user-flow-attribute-administrator) | Kan skapa och hantera attributschemat som är tillgängligt för alla användarflöden. | 0f971eea-41eb-4569-a71e-57bb8a3eff1e |
> | [Administratör för extern identitetsprovider](#external-identity-provider-administrator) | Kan konfigurera identitetsprovidrer för användning i direkt federation. | be2f45a1-457d-42af-a067-6ec1fa63bc45 |
> | [Global administratör](#global-administrator) | Kan hantera alla aspekter av Azure AD och Microsoft-tjänster som använder Azure AD-identiteter. | 62e90394-69f5-4237-9190-012177145e10 |
> | [Global läsare](#global-reader) | Kan läsa allt som en global administratör kan, men inte uppdatera något. | f2ef992c-3afb-46b9-b7cf-a126ee74c451 |
> | [Gruppadministratör](#groups-administrator) | Medlemmar i den här rollen kan skapa/hantera grupper, skapa/hantera gruppinställningar som namngivnings- och förfalloprinciper samt visa gruppers aktivitet och granskningsrapporter. | fdd7a751-b60b-444a-984c-02652fe8fa1c |
> | [Gäst inbjudare](#guest-inviter) | Kan bjuda in gästanvändare oberoende av inställningen "medlemmar kan bjuda in gäster". | 95e79109-95c0-4d8e-aee3-d01accf2d47b |
> | [Supportadministratör](#helpdesk-administrator) | Kan återställa lösenord för icke-administratörer och supportadministratörer. | 729827e3-9c14-49f7-bb1b-9608f156bbb8 |
> | [Hybrididentitetsadministratör](#hybrid-identity-administrator) | Kan hantera AD till Azure AD-molnetablering, Azure AD Connect och federationsinställningar. | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2 |
> | [Insights-administratör](#insights-administrator) | Har administrativ åtkomst i Microsoft 365 Insights-appen. | eb1f4a8d-243a-41f0-9fbd-c7cdf6c5ef7c |
> | [Affärsledare för Insights](#insights-business-leader) | Kan visa och dela instrumentpaneler och insikter via M365 Insights-appen. | 31e939ad-9672-4796-9c2e-873181342d2d |
> | [Intune-administratör](#intune-administrator) | Kan hantera alla aspekter av Intune-produkten. | 3a2c62db-5318-420d-8d74-23affee5d9d5 |
> | [Kaizala-administratör](#kaizala-administrator) | Kan hantera inställningar för Microsoft Kaizala. | 74ef975b-6605-40af-a5d2-b9539d836353 |
> | [Kunskapsadministratör](#knowledge-administrator) | Kan konfigurera kunskap, inlärning och andra intelligenta funktioner. | b5a8dcf3-09d5-43a9-a639-8e29ef291470 |
> | [Licensadministratör](#license-administrator) | Kan hantera produktlicenser för användare och grupper. | 4d6ac14f-3453-41d0-bef9-a3e0c569773a |
> | [Meddelandecenter sekretessläsare](#message-center-privacy-reader) | Kan läsa säkerhetsmeddelanden och uppdateringar i Office 365 Meddelandecenter endast. | ac16e43d-7b2d-40e0-ac05-243ff356ab5b |
> | [Meddelandecenter Läsare](#message-center-reader) | Kan läsa meddelanden och uppdateringar för organisationen i Office 365 Meddelandecenter endast. | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b |
> | [Modern commerce-användare](#modern-commerce-user) | Kan hantera kommersiella inköp för ett företag, en avdelning eller ett team. | d24aef57-1500-4070-84db-2666f29cf966 |
> | [Nätverksadministratör](#network-administrator) | Kan hantera nätverksplatser och granska företagets nätverksdesigninsikter för Microsoft 365 Software as a Service-program. | d37c8bed-0711-4417-ba38-b4abe66ce4c2 |
> | [Administratör för Office-appar](#office-apps-administrator) | Kan hantera molntjänster för Office-appar, inklusive princip- och inställningshantering, och hantera möjligheten att välja, avmarkera och publicera "vad är nytt"-funktionsinnehåll till slutanvändarens enheter. | 2b745bdf-0803-4d80-aa65-822c4493daac |
> | [Support på partnernivå 1](#partner-tier1-support) | Använd inte – inte avsett för allmän användning. | 4ba39ca4-527c-499a-b93d-d9b492c50246 |
> | [Support för partnernivå 2](#partner-tier2-support) | Använd inte – inte avsett för allmän användning. | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8 |
> | [Lösenordsadministratör](#password-administrator) | Kan återställa lösenord för icke-administratörer och lösenordsadministratörer. | 966707d0-3269-4727-9be2-8c3a10f19b9d |
> | [Power BI administratör](#power-bi-administrator) | Kan hantera alla aspekter av Power BI produkt. | a9ea8996-122f-4c74-9520-8edcd192826c |
> | [Power Platform administratör](#power-platform-administrator) | Kan skapa och hantera alla aspekter av Microsoft Dynamics 365, PowerApps och Microsoft Flow. | 11648597-926c-4cf3-9c36-bcebb0ba8dcc |
> | [Skrivaradministratör](#printer-administrator) | Kan hantera alla aspekter av skrivare och skrivaranslutningar. | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f |
> | [Skrivartekniker](#printer-technician) | Kan registrera och avregistrera skrivare och uppdatera skrivarstatus. | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477 |
> | [Privilegierad autentiseringsadministratör](#privileged-authentication-administrator) | Har åtkomst till att visa, ange och återställa autentiseringsmetodinformation för alla användare (administratör eller icke-administratör). | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13 |
> | [Privilegierad rolladministratör](#privileged-role-administrator) | Kan hantera rolltilldelningar i Azure AD och alla aspekter av Privileged Identity Management. | e8611ab8-c189-46e8-94e1-60213ab1f814 |
> | [Rapportläsare](#reports-reader) | Kan läsa inloggnings- och granskningsrapporter. | 4a5d8f65-41da-4de4-8968-e035b65339cf |
> | [Sökadministratör](#search-administrator) | Kan skapa och hantera alla aspekter av Microsoft Search-inställningar. | 0964bb5e-9bdb-4d7b-ac29-58e794862a40 |
> | [Sökredigeraren](#search-editor) | Kan skapa och hantera det redigeringsinnehåll som bokmärken, Q och As, platser, golvplan. | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9 |
> | [Säkerhetsadministratör](#security-administrator) | Kan läsa säkerhetsinformation och rapporter och hantera konfiguration i Azure AD och Office 365. | 194ae4cb-b126-40b2-bd5b-6091b380977d |
> | [Säkerhetsoperatör](#security-operator) | Skapar och hanterar säkerhetshändelser. | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f |
> | [Säkerhetsläsare](#security-reader) | Kan läsa säkerhetsinformation och rapporter i Azure AD och Office 365. | 5d6b6bb7-de71-4623-b4af-96380a352509 |
> | [Tjänstsupportadministratör](#service-support-administrator) | Kan läsa hälsoinformation om tjänsten och hantera supportärenden. | f023fd81-a637-4b56-95fd-791ac0226033 |
> | [SharePoint-administratör](#sharepoint-administrator) | Kan hantera alla aspekter av SharePoint-tjänsten. | f28a1f50-f6e7-4571-818b-6a12f2af6b6c |
> | [Skype för företag-administratör](#skype-for-business-administrator) | Kan hantera alla aspekter av Skype för företag-produkten. | 75941009-915a-4869-abe7-691bff18279e |
> | [Teams-administratör](#teams-administrator) | Kan hantera Microsoft Teams-tjänsten. | 69091246-20e8-4a56-aa4d-066075b2a7a8 |
> | [Teams-kommunikationsadministratör](#teams-communications-administrator) | Kan hantera funktioner för samtal och möten i Microsoft Teams-tjänsten. | baf37b3a-610e-45da-9e62-d9d1e5e8914b |
> | [Teams Communications Support Engineer](#teams-communications-support-engineer) | Kan felsöka kommunikationsproblem i Teams med hjälp av avancerade verktyg. | f70938a0-fc10-4177-9e90-2178f8765737 |
> | [Teams Communications Support Specialist](#teams-communications-support-specialist) | Kan felsöka kommunikationsproblem i Teams med hjälp av grundläggande verktyg. | fcf91098-03e3-41a9-b5ba-6f0ec8188a12 |
> | [Administratör för Teams-enheter](#teams-devices-administrator) | Kan utföra hanteringsrelaterade uppgifter på Teams-certifierade enheter. | 3d762c5a-1b6c-493f-843e-55a3b42923d4 |
> | [Läsare av användningssammanfattningsrapporter](#usage-summary-reports-reader) | Kan endast se aggregeringar på klientnivå Microsoft 365 användningsanalys och produktivitetspoäng. | 75934031-6c7e-415a-99d7-48dbd49e875e |
> | [Användaradministratör](#user-administrator) | Kan hantera alla aspekter av användare och grupper, inklusive återställning av lösenord för begränsade administratörer. | fe930be7-5e62-47db-91af-98c3a49a38b1 |

## <a name="application-administrator"></a>Programadministratör

Användare i den här rollen kan skapa och hantera alla aspekter av företagsprogram, programregistreringar och programproxyinställningar. Observera att användare som tilldelats den här rollen inte läggs till som ägare när du skapar nya programregistreringar eller företagsprogram.

Den här rollen ger också möjlighet att samtycka till delegerade behörigheter och programbehörigheter, med undantag för programbehörigheter för både Microsoft Graph och Azure AD Graph.

> [!IMPORTANT]
> Det här undantaget innebär att du fortfarande kan godkänna programbehörigheter för andra _appar_ (till exempel appar som inte kommer från Microsoft eller appar som du har registrerat). Du kan fortfarande _begära_ dessa behörigheter som en del av appregistreringen, men att _bevilja_ (det vill säga samtycka till) dessa behörigheter kräver en mer privilegierad administratör, till exempel global administratör.
>
>Den här rollen ger möjlighet att hantera programautentiseringsuppgifter. Användare som har tilldelats den här rollen kan lägga till autentiseringsuppgifter i ett program och använda dessa autentiseringsuppgifter för att personifiera programmets identitet. Om programmets identitet har beviljats åtkomst till en resurs, till exempel möjligheten att skapa eller uppdatera användare eller andra objekt, kan en användare som tilldelats den här rollen utföra dessa åtgärder när den personifierar programmet. Den här möjligheten att personifiera programmets identitet kan vara en rättighetsökning över vad användaren kan göra via sina rolltilldelningar. Det är viktigt att förstå att tilldelning av en användare till rollen Programadministratör ger dem möjlighet att personifiera ett programs identitet.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/applications/create | Skapa alla typer av program |
> | microsoft.directory/applications/delete | Ta bort alla typer av program |
> | microsoft.directory/applications/applicationProxy/read | Läsa alla egenskaper för programproxy |
> | microsoft.directory/applications/applicationProxy/update | Uppdatera alla egenskaper för programproxy |
> | microsoft.directory/applications/applicationProxyAuthentication/update | Uppdatera autentisering för alla typer av program |
> | microsoft.directory/applications/applicationProxySslCertificate/update | Uppdatera SSL-certifikatinställningar för programproxy |
> | microsoft.directory/applications/applicationProxyUrlSettings/update | Uppdatera URL-inställningar för programproxy |
> | microsoft.directory/applications/appRoles/update | Uppdatera egenskapen appRoles för alla typer av program |
> | microsoft.directory/applications/audience/update | Uppdatera målgruppsegenskapen för program |
> | microsoft.directory/applications/authentication/update | Uppdatera autentisering för alla typer av program |
> | microsoft.directory/applications/basic/update | Uppdatera grundläggande egenskaper för program |
> | microsoft.directory/applications/credentials/update | Uppdatera programautentiseringsuppgifter |
> | microsoft.directory/applications/owners/update | Uppdatera programägare |
> | microsoft.directory/applications/permissions/update | Uppdatera exponerade behörigheter och nödvändiga behörigheter för alla typer av program |
> | microsoft.directory/applications/policies/update | Uppdatera principer för program |
> | microsoft.directory/applications/verification/update | Uppdatera programverifieringsegenskap |
> | microsoft.directory/applications/synchronization/standard/read | Läsa etableringsinställningar som är associerade med programobjektet |
> | microsoft.directory/applicationTemplates/instantiate | Skapa en instans av galleriprogram från programmallar |
> | microsoft.directory/auditLogs/allProperties/read | Läsa alla egenskaper i granskningsloggar, inklusive privilegierade egenskaper |
> | microsoft.directory/connectors/create | Skapa anslutningsappar för programproxy |
> | microsoft.directory/connectors/allProperties/read | Läsa alla egenskaper för anslutningsappar för programproxy |
> | microsoft.directory/connectorGroups/create | Skapa anslutningsgrupper för programproxy |
> | microsoft.directory/connectorGroups/delete | Ta bort anslutningsgrupper för programproxy |
> | microsoft.directory/connectorGroups/allProperties/read | Läsa alla egenskaper för anslutningsgrupper för programproxy |
> | microsoft.directory/connectorGroups/allProperties/update | Uppdatera alla egenskaper för anslutningsgrupper för programproxy |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Skapa och ta bort OAuth 2.0-behörighetsstipendier och läsa och uppdatera alla egenskaper |
> | microsoft.directory/applicationPolicies/create | Skapa programprinciper |
> | microsoft.directory/applicationPolicies/delete | Ta bort programprinciper |
> | microsoft.directory/applicationPolicies/standard/read | Läsa standardegenskaper för programprinciper |
> | microsoft.directory/applicationPolicies/owners/read | Läsa ägare om programprinciper |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | Läsa programprinciper som tillämpas på objektlistan |
> | microsoft.directory/applicationPolicies/basic/update | Uppdatera standardegenskaper för programprinciper |
> | microsoft.directory/applicationPolicies/owners/update | Uppdatera egenskapen ägare för programprinciper |
> | microsoft.directory/provisioningLogs/allProperties/read | Läsa alla egenskaper för etableringsloggar |
> | microsoft.directory/servicePrincipals/create | Skapa tjänsthuvudnamn |
> | microsoft.directory/servicePrincipals/delete | Ta bort tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/disable | Inaktivera tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/enable | Aktivera tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Hantera autentiseringsuppgifter för enkel inloggning med lösenord för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Hantera programetablering av hemligheter och autentiseringsuppgifter |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Starta, starta om och pausa synkroniseringsjobb för programetablering |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Skapa och hantera synkroniseringsjobb och schema för programetablering |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Läsa autentiseringsuppgifter för enkel inloggning med lösenord för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-application-admin | Bevilja medgivande för programbehörigheter och delegerade behörigheter för alla användares eller alla användares räkning, förutom programbehörigheter för Microsoft Graph och Azure AD Graph |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Uppdatera rolltilldelningar för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/audience/update | Uppdatera målgruppsegenskaper för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/authentication/update | Uppdatera autentiseringsegenskaper för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/basic/update | Uppdatera grundläggande egenskaper för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/credentials/update | Uppdatera autentiseringsuppgifterna för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/owners/update | Uppdatera ägare av tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/permissions/update | Uppdatera behörigheter för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/policies/update | Uppdatera principer för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/tag/update | Uppdatera taggegenskapen för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | Läsa etableringsinställningar som är associerade med tjänstens huvudnamn |
> | microsoft.directory/signInReports/allProperties/read | Läsa alla egenskaper i inloggningsrapporter, inklusive privilegierade egenskaper |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="application-developer"></a>Programutvecklare

Användare i den här rollen kan skapa programregistreringar när inställningen "Användare kan registrera program" är inställd på Nej. Den här rollen ger också behörighet att godkänna för ens räkning när inställningen "Användare kan samtycka till att appar får åtkomst till företagsdata för deras räkning" är inställd på Nej. Användare som tilldelas den här rollen läggs till som ägare när nya programregistreringar eller företagsprogram skapas.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/applications/createAsOwner | Skapa alla typer av program, och skapare läggs till som första ägare |
> | microsoft.directory/appRoleAssignments/createAsOwner | Skapa programrolltilldelningar med skapare som första ägare |
> | microsoft.directory/oAuth2PermissionGrants/createAsOwner | Skapa OAuth 2.0-behörighetsstipendier, med skapare som första ägare |
> | microsoft.directory/servicePrincipals/createAsOwner | Skapa tjänstens huvudnamn, med skaparen som första ägare |

## <a name="attack-payload-author"></a>Attack Payload Author

Användare i den här rollen kan skapa angreppsnyttolaster, men inte starta eller schemalägga dem. Attacknyttolaster är sedan tillgängliga för alla administratörer i klientorganisationen som kan använda dem för att skapa en simulering.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | Skapa och hantera attacknyttolaster i attacksimulatorn |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Läsa rapporter om svar från attacksimulering och tillhörande utbildning |

## <a name="attack-simulation-administrator"></a>Administratör för attacksimulering

Användare i den här rollen kan skapa och hantera alla aspekter av skapande av attacksimulering, start/schemaläggning av en simulering och granskning av simuleringsresultat. Medlemmar i den här rollen har den här åtkomsten för alla simuleringar i klientorganisationen.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | Skapa och hantera attacknyttolaster i attacksimulatorn |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Läs rapporter om svar från attacksimulering och tillhörande utbildning |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/allTasks | Skapa och hantera mallar för attacksimulering i attacksimulatorn |

## <a name="authentication-administrator"></a>Autentiseringsadministratör

Användare med den här rollen kan ange eller återställa valfri autentiseringsmetod (inklusive lösenord) för icke-administratörer och vissa roller. Autentiseringsadministratörer kan kräva att användare som inte är administratörer eller som är tilldelade till vissa roller omregistrerar sig mot befintliga autentiseringsuppgifter som inte är lösenord (till exempel MFA eller FIDO), och kan även återkalla kom ihåg **MFA** på enheten , som frågar efter MFA vid nästa inloggning. En lista över de roller som en autentiseringsadministratör kan läsa eller uppdatera autentiseringsmetoder finns i Behörigheter [för lösenordsåterställning.](#password-reset-permissions)

[Administratörsrollen Privilegierad](#privileged-authentication-administrator) autentisering har behörighet att tvinga omregistrering och multifaktorautentisering för alla användare.

[Administratörsrollen autentiseringsprincip](#authentication-policy-administrator) har behörighet att ange klientens autentiseringsmetodprincip som avgör vilka metoder varje användare kan registrera och använda.

| Roll | Hantera användarens autentiseringsmetoder | Hantera MFA per användare | Hantera MFA-inställningar | Hantera auth-metodprincip | Hantera lösenordsskyddsprincip |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Autentiseringsadministratör | Ja för vissa användare (se ovan) | Ja för vissa användare (se ovan) | Inga | Inga | Inga |
| Administratör för privilegierad autentisering| Ja för alla användare | Ja för alla användare | Inga | Inga | Inga |
| Administratör för autentiseringsprincip | Inga |Inga | Ja | Ja | Ja |

> [!IMPORTANT]
> Användare med den här rollen kan ändra autentiseringsuppgifter för personer som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i och utanför Azure Active Directory. Att ändra autentiseringsuppgifterna för en användare kan innebära möjligheten att anta användarens identitet och behörigheter. Exempel:
>
>* Programregistrering och ägare av företagsprogram, som kan hantera autentiseringsuppgifter för appar som de äger. Dessa appar kan ha privilegierad behörighet i Azure AD och någon annanstans som inte har beviljats till autentiseringsadministratörer. Via den här sökvägen kan en autentiseringsadministratör anta identiteten för en programägare och sedan ytterligare anta identiteten för ett privilegierat program genom att uppdatera autentiseringsuppgifterna för programmet.
>* Azure-prenumerationsägare, som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i Azure.
>* Säkerhetsgrupp och Microsoft 365 gruppägare som kan hantera gruppmedlemskap. Dessa grupper kan bevilja åtkomst till känslig eller privat information eller kritisk konfiguration i Azure AD och någon annanstans.
>* Administratörer i andra tjänster utanför Azure AD som Exchange Online, Säkerhets- och efterlevnadscenter för Office och system för personalavdelningen.
>* Icke-administratörer som chefer, juridiska rådgivare och personalpersonal som kan ha åtkomst till känslig eller privat information.

> [!IMPORTANT]
> Den här rollen kan inte hantera MFA-inställningar i den äldre MFA-hanteringsportalen eller OATH-maskinvarutoken. Samma funktioner kan utföras med hjälp av [Set-MsolUser-kommandoleten](/powershell/module/msonline/set-msoluser) Azure AD Powershell-modulen.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/users/invalidateAllRefreshTokens | Framt tvinga ut ut genom att ogiltigförklara token för användaruppdatering |
> | microsoft.directory/users/strongAuthentication/update | Uppdatera den starka autentiseringsegenskapen för användare |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="authentication-policy-administrator"></a>Administratör för autentiseringsprincip

Användare med den här rollen kan konfigurera principen för autentiseringsmetoder, MFA-inställningar för hela klientorganisationen och lösenordsskyddsprincipen. Den här rollen ger behörighet att hantera inställningar för lösenordsskydd: konfigurationer för smart utelåsning och uppdatering av listan över anpassade förbjudna lösenord.

[Administratörsrollerna](#authentication-administrator) Autentiseringsadministratör [](#privileged-authentication-administrator) privilegierad autentisering har behörighet att hantera registrerade autentiseringsmetoder på användare och kan tvinga omregistrering och multifaktorautentisering för alla användare.

| Roll | Hantera användarens autentiseringsmetoder | Hantera MFA per användare | Hantera MFA-inställningar | Hantera auth-metodprincip | Hantera lösenordsskyddsprincip |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Autentiseringsadministratör | Ja för vissa användare (se ovan) | Ja för vissa användare (se ovan) | Inga | Inga | Inga |
| Administratör för privilegierad autentisering| Ja för alla användare | Ja för alla användare | Inga | Inga | Inga |
| Administratör för autentiseringsprincip | Inga | Inga | Ja | Ja | Ja |

> [!IMPORTANT]
> Den här rollen kan inte hantera MFA-inställningar i den äldre MFA-hanteringsportalen eller OATH-maskinvarutoken.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/organization/strongAuthentication/update | Uppdatera starka autentiseringsegenskaper för en organisation |
> | microsoft.directory/userCredentialPolicies/create | Skapa principer för autentiseringsuppgifter för användare |
> | microsoft.directory/userCredentialPolicies/delete | Ta bort autentiseringsprinciper för användare |
> | microsoft.directory/userCredentialPolicies/standard/read | Läsa standardegenskaper för autentiseringsprinciper för användare |
> | microsoft.directory/userCredentialPolicies/owners/read | Läsa ägare av autentiseringsprinciper för användare |
> | microsoft.directory/userCredentialPolicies/policyAppliedTo/read | Läsa policy.appliesTo navigeringslänk |
> | microsoft.directory/userCredentialPolicies/basic/update | Uppdatera grundläggande principer för användare |
> | microsoft.directory/userCredentialPolicies/owners/update | Uppdatera ägare av autentiseringsprinciper för användare |
> | microsoft.directory/userCredentialPolicies/tenantDefault/update | Uppdatera egenskapen policy.isOrganizationDefault |

## <a name="azure-ad-joined-device-local-administrator"></a>Lokal administratör för Azure AD-ansluten enhet

Den här rollen är endast tillgänglig för tilldelning som ytterligare en lokal administratör i [Enhetsinställningar.](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/) Användare med den här rollen blir lokala datoradministratörer på alla Windows 10 som är anslutna till Azure Active Directory. De har inte möjlighet att hantera enheters objekt i Azure Active Directory.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/groupSettings/standard/read | Läsa grundläggande egenskaper för gruppinställningar |
> | microsoft.directory/groupSettingTemplates/standard/read | Läsa grundläggande egenskaper för mallar för gruppinställningar |

## <a name="azure-devops-administrator"></a>Azure DevOps-administratör

Användare med den här rollen kan hantera Azure DevOps-principen för att begränsa skapandet av nya Azure DevOps-organisationer till en uppsättning konfigurerbara användare eller grupper. Användare i den här rollen kan hantera den här principen via valfri Azure DevOps-organisation som backas upp av företagets Azure AD-organisation. Den här rollen ger inga andra Azure DevOps-specifika behörigheter (till exempel projektsamlingsadministratörer) i någon av De Azure DevOps-organisationer som backas upp av företagets Azure AD-organisation.

Alla Azure DevOps-principer för företag kan hanteras av användare i den här rollen.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.azure.devOps/allEntities/allTasks | Läsa och konfigurera Azure DevOps |

## <a name="azure-information-protection-administrator"></a>Azure Information Protection administratör

Användare med den här rollen har alla behörigheter Azure Information Protection tjänsten. Med den här rollen kan du konfigurera Azure Information Protection för principen, hantera skyddsmallar och aktivera skydd. Den här rollen beviljar inte behörigheter i Identity Protection Center, Privileged Identity Management, Monitor Microsoft 365 Service Health eller Office 365 Security & Compliance Center.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.azure.informationProtection/allEntities/allTasks | Hantera alla aspekter av Azure Information Protection |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="b2c-ief-keyset-administrator"></a>B2C IEF-administratör för nyckeluppsättning

Användaren kan skapa och hantera principnycklar och hemligheter för tokenkryptering, tokensignaturer och anspråkskryptering/dekryptering. Genom att lägga till nya nycklar i befintliga nyckelcontainrar kan den här begränsade administratören återställa hemligheter efter behov utan att påverka befintliga program. Den här användaren kan se det fullständiga innehållet i dessa hemligheter och deras förfallodatum även efter att de har skapats.

> [!IMPORTANT]
> Det här är en känslig roll. Administratörsrollen för nyckeluppsättningen bör granskas noggrant och tilldelas med försiktighet under förproduktion och produktion.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/b2cTrustFrameworkKeySet/allProperties/allTasks | Läsa och uppdatera alla egenskaper för auktoriseringsprinciper |

## <a name="b2c-ief-policy-administrator"></a>B2C IEF-principadministratör

Användare i den här rollen kan skapa, läsa, uppdatera och ta bort alla anpassade principer i Azure AD B2C och har därför fullständig kontroll över Identity Experience Framework i relevant Azure AD B2C organisation. Genom att redigera principer kan den här användaren upprätta direkt federation med externa identitetsprovidrar, ändra katalogschemat, ändra allt användarriktat innehåll (HTML, CSS, JavaScript), ändra kraven för att slutföra en autentisering, skapa nya användare, skicka användardata till externa system, inklusive fullständiga migreringar och redigera all användarinformation, inklusive känsliga fält som lösenord och telefonnummer. Den här rollen kan inte ändra krypteringsnycklarna eller redigera hemligheterna som används för federation i organisationen.

> [!IMPORTANT]
> B2 IEF-principadministratören är en mycket känslig roll som bör tilldelas på en mycket begränsad grund för organisationer i produktion. Aktiviteter för dessa användare bör granskas noggrant, särskilt för organisationer i produktion.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/b2cTrustFrameworkPolicy/allProperties/allTasks | Läsa och konfigurera nyckeluppsättningar i Azure Active Directory B2C |

## <a name="billing-administrator"></a>Faktureringsadministratör

Gör inköp, hanterar prenumerationer, hanterar supportärenden och övervakar tjänstens hälsa.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/organization/basic/update | Uppdatera grundläggande egenskaper för organisationen |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.commerce.billing/allEntities/allTasks | Hantera alla aspekter av Office 365-fakturering |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="cloud-application-administrator"></a>Molnprogramadministratör

Användare i den här rollen har samma behörigheter som rollen Programadministratör, exklusive möjligheten att hantera programproxy. Den här rollen ger möjlighet att skapa och hantera alla aspekter av företagsprogram och programregistreringar. Användare som tilldelats den här rollen läggs inte till som ägare när nya programregistreringar eller företagsprogram skapas.

Den här rollen ger också möjlighet att samtycka till delegerade behörigheter och programbehörigheter, med undantag för programbehörigheter för både Microsoft Graph och Azure AD Graph.

> [!IMPORTANT]
> Det här undantaget innebär att du fortfarande kan godkänna programbehörigheter för andra _appar_ (till exempel appar som inte kommer från Microsoft eller appar som du har registrerat). Du kan fortfarande _begära_ dessa behörigheter som en del av appregistreringen, men att _bevilja_ (det vill säga samtycka till) dessa behörigheter kräver en mer privilegierad administratör, till exempel global administratör.
>
>Den här rollen ger möjlighet att hantera programautentiseringsuppgifter. Användare som har tilldelats den här rollen kan lägga till autentiseringsuppgifter i ett program och använda dessa autentiseringsuppgifter för att personifiera programmets identitet. Om programmets identitet har beviljats åtkomst till en resurs, till exempel möjligheten att skapa eller uppdatera användare eller andra objekt, kan en användare som tilldelats den här rollen utföra dessa åtgärder när den personifierar programmet. Den här möjligheten att personifiera programmets identitet kan vara en rättighetsökning över vad användaren kan göra via sina rolltilldelningar. Det är viktigt att förstå att tilldelning av en användare till rollen Programadministratör ger dem möjlighet att personifiera ett programs identitet.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/applications/create | Skapa alla typer av program |
> | microsoft.directory/applications/delete | Ta bort alla typer av program |
> | microsoft.directory/applications/appRoles/update | Uppdatera egenskapen appRoles för alla typer av program |
> | microsoft.directory/applications/audience/update | Uppdatera målgruppsegenskapen för program |
> | microsoft.directory/applications/authentication/update | Uppdatera autentisering för alla typer av program |
> | microsoft.directory/applications/basic/update | Uppdatera grundläggande egenskaper för program |
> | microsoft.directory/applications/credentials/update | Uppdatera programautentiseringsuppgifter |
> | microsoft.directory/applications/owners/update | Uppdatera programägare |
> | microsoft.directory/applications/permissions/update | Uppdatera exponerade behörigheter och nödvändiga behörigheter för alla typer av program |
> | microsoft.directory/applications/policies/update | Uppdatera principer för program |
> | microsoft.directory/applications/verification/update | Uppdatera programverifieringsegenskap |
> | microsoft.directory/applications/synchronization/standard/read | Läsa etableringsinställningar som är associerade med programobjektet |
> | microsoft.directory/applicationTemplates/instantiate | Skapa en instans av galleriprogram från programmallar |
> | microsoft.directory/auditLogs/allProperties/read | Läsa alla egenskaper i granskningsloggar, inklusive privilegierade egenskaper |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Skapa och ta bort OAuth 2.0-behörighet beviljar och läsa och uppdatera alla egenskaper |
> | microsoft.directory/applicationPolicies/create | Skapa programprinciper |
> | microsoft.directory/applicationPolicies/delete | Ta bort programprinciper |
> | microsoft.directory/applicationPolicies/standard/read | Läsa standardegenskaper för programprinciper |
> | microsoft.directory/applicationPolicies/owners/read | Läsa ägare om programprinciper |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | Läsa programprinciper som tillämpas på objektlistan |
> | microsoft.directory/applicationPolicies/basic/update | Uppdatera standardegenskaper för programprinciper |
> | microsoft.directory/applicationPolicies/owners/update | Uppdatera egenskapen ägare för programprinciper |
> | microsoft.directory/provisioningLogs/allProperties/read | Läsa alla egenskaper för etableringsloggar |
> | microsoft.directory/servicePrincipals/create | Skapa tjänsthuvudnamn |
> | microsoft.directory/servicePrincipals/delete | Ta bort tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/disable | Inaktivera tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/enable | Aktivera tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Hantera autentiseringsuppgifter för enkel inloggning med lösenord för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Hantera programetablering av hemligheter och autentiseringsuppgifter |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Starta, starta om och pausa synkroniseringsjobb för programetablering |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Skapa och hantera synkroniseringsjobb och schema för programetablering |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Läsa autentiseringsuppgifter för enkel inloggning med lösenord för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-application-admin | Bevilja medgivande för programbehörigheter och delegerade behörigheter för alla användares eller alla användares räkning, förutom programbehörigheter för Microsoft Graph och Azure AD Graph |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Uppdatera rolltilldelningar för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/audience/update | Uppdatera målgruppsegenskaper för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/authentication/update | Uppdatera autentiseringsegenskaper för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/basic/update | Uppdatera grundläggande egenskaper för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/credentials/update | Uppdatera autentiseringsuppgifterna för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/owners/update | Uppdatera ägare av tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/permissions/update | Uppdatera behörigheter för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/policies/update | Uppdatera principer för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/tag/update | Uppdatera taggegenskapen för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | Läsa etableringsinställningar som är associerade med tjänstens huvudnamn |
> | microsoft.directory/signInReports/allProperties/read | Läs alla egenskaper i inloggningsrapporter, inklusive privilegierade egenskaper |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="cloud-device-administrator"></a>Molnenhetsadministratör

Användare i den här rollen kan aktivera, inaktivera och ta bort enheter i Azure AD och läsa Windows 10 BitLocker-nycklar (om de finns) i Azure Portal. Rollen beviljar inte behörighet att hantera andra egenskaper på enheten.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Läsa alla egenskaper i granskningsloggar, inklusive privilegierade egenskaper |
> | microsoft.directory/bitlockerKeys/key/read | Läsa BitLocker-metadata och -nyckel på enheter |
> | microsoft.directory/devices/delete | Ta bort enheter från Azure AD |
> | microsoft.directory/devices/disable | Inaktivera enheter i Azure AD |
> | microsoft.directory/devices/enable | Aktivera enheter i Azure AD |
> | microsoft.directory/devices/extensionAttributes/update | Uppdatera alla värden för egenskapen devices.extensionAttributes |
> | microsoft.directory/deviceManagementPolicies/standard/read | Läsa standardegenskaper för principer för enhetshanteringsprogram |
> | microsoft.directory/deviceManagementPolicies/basic/update | Uppdatera grundläggande egenskaper för principer för enhetshanteringsprogram |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | Läsa standardegenskaper för enhetsregistreringsprinciper |
> | microsoft.directory/deviceRegistrationPolicy/basic/update | Uppdatera grundläggande egenskaper för enhetsregistreringsprinciper |
> | microsoft.directory/signInReports/allProperties/read | Läsa alla egenskaper i inloggningsrapporter, inklusive privilegierade egenskaper |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |

## <a name="compliance-administrator"></a>Efterlevnadsadministratör

Användare med den här rollen har behörighet att hantera efterlevnadsrelaterade funktioner i Microsoft 365 Efterlevnadscenter, Administrationscenter för Microsoft 365, Azure och Office 365 Security & Compliance Center. Tilldelningar kan också hantera alla funktioner i administrationscentret för Exchange och Teams & administrationscenter för Skype för företag och skapa supportärenden för Azure och Microsoft 365. Mer information finns i Om [Microsoft 365 administratörsroller.](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)

I | Kan göra
----- | ----------
[Microsoft 365 Efterlevnadscenter](https://protection.office.com) | Skydda och hantera organisationens data i Microsoft 365 tjänster<br>Hantera efterlevnadsaviseringar
[Compliance Manager (Efterlevnadshanteraren)](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Spåra, tilldela och verifiera organisationens aktiviteter för regelefterlevnad
[Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Hantera datastyrning<br>Utföra juridisk undersökning och dataundersökning<br>Hantera begäran från dataämne<br><br>Den här rollen har [](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) samma behörigheter som rollgruppen Efterlevnadsadministratör i Office 365 Security & Compliance Center rollbaserad åtkomstkontroll.
[Intune](/intune/role-based-access-control) | Visa alla Intune-granskningsdata
[Cloud App Security](/cloud-app-security/manage-admins) | Har skrivskyddade behörigheter och kan hantera aviseringar<br>Kan skapa och ändra filprinciper och tillåta filstyrningsåtgärder<br>Kan visa alla inbyggda rapporter under Datahantering

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.directory/entitlementManagement/allProperties/read | Läsa alla egenskaper i Azure AD-berättigandehantering |
> | microsoft.office365.complianceManager/allEntities/allTasks | Hantera alla aspekter av Office 365 Compliance Manager |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="compliance-data-administrator"></a>Efterlevnadsdataadministratör

Användare med den här rollen har behörighet att spåra data i Microsoft 365 Efterlevnadscenter, Administrationscenter för Microsoft 365 och Azure. Användare kan också spåra efterlevnadsdata i administrationscentret för Exchange, Compliance Manager och Administrationscenter för Teams & Skype för företag och skapa supportärenden för Azure och Microsoft 365. [Den här dokumentationen](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) innehåller information om skillnaderna mellan Efterlevnadsadministratör och Dataadministratör för efterlevnad.

I | Kan göra
----- | ----------
[Microsoft 365 Efterlevnadscenter](https://protection.office.com) | Övervaka efterlevnadsrelaterade principer i Microsoft 365 tjänster<br>Hantera efterlevnadsaviseringar
[Compliance Manager (Efterlevnadshanteraren)](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Spåra, tilldela och verifiera organisationens aktiviteter för regelefterlevnad
[Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Hantera datastyrning<br>Utföra juridisk undersökning och dataundersökning<br>Hantera begäran från dataämne<br><br>Den här rollen har samma behörigheter som rollgruppen [Efterlevnadsdataadministratör](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) i Office 365 Security & Compliance Center rollbaserad åtkomstkontroll.
[Intune](/intune/role-based-access-control) | Visa alla Intune-granskningsdata
[Cloud App Security](/cloud-app-security/manage-admins) | Har skrivskyddade behörigheter och kan hantera aviseringar<br>Kan skapa och ändra filprinciper och tillåta filstyrningsåtgärder<br>Kan visa alla inbyggda rapporter under Datahantering

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | Skapa och ta bort alla resurser och läs och uppdatera standardegenskaper i Microsoft Cloud App Security |
> | microsoft.azure.informationProtection/allEntities/allTasks | Hantera alla aspekter av Azure Information Protection |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.office365.complianceManager/allEntities/allTasks | Hantera alla aspekter av Office 365 Compliance Manager |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="conditional-access-administrator"></a>Administratör för villkorsstyrd åtkomst

Användare med den här rollen kan hantera Azure Active Directory för villkorlig åtkomst.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/conditionalAccessPolicies/create | Skapa principer för villkorsstyrd åtkomst |
> | microsoft.directory/conditionalAccessPolicies/delete | Ta bort principer för villkorlig åtkomst |
> | microsoft.directory/conditionalAccessPolicies/standard/read | Läsa villkorlig åtkomst för principer |
> | microsoft.directory/conditionalAccessPolicies/owners/read | Läs om ägarna av principer för villkorlig åtkomst |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | Läs egenskapen "tillämpad på" för principer för villkorsstyrd åtkomst |
> | microsoft.directory/conditionalAccessPolicies/basic/update | Uppdatera grundläggande egenskaper för principer för villkorlig åtkomst |
> | microsoft.directory/conditionalAccessPolicies/owners/update | Uppdatera ägare för principer för villkorlig åtkomst |
> | microsoft.directory/conditionalAccessPolicies/tenantDefault/update | Uppdatera standardklientorganisationen för principer för villkorlig åtkomst |
> | microsoft.directory/crossTenantAccessPolicies/create | Skapa åtkomstprinciper mellan klientorganisationen |
> | microsoft.directory/crossTenantAccessPolicies/delete | Ta bort åtkomstprinciper mellan klientorganisationen |
> | microsoft.directory/crossTenantAccessPolicies/standard/read | Läsa grundläggande egenskaper för åtkomstprinciper mellan klientorganisationen |
> | microsoft.directory/crossTenantAccessPolicies/owners/read | Läsägare för åtkomstprinciper mellan klientorganisation |
> | microsoft.directory/crossTenantAccessPolicies/policyAppliedTo/read | Läs egenskapen policyAppliedTo för åtkomstprinciper mellan klientorganisationen |
> | microsoft.directory/crossTenantAccessPolicies/basic/update | Uppdatera grundläggande egenskaper för åtkomstprinciper mellan klientorganisationen |
> | microsoft.directory/crossTenantAccessPolicies/owners/update | Uppdatera ägare av åtkomstprinciper mellan klientorganisation |
> | microsoft.directory/crossTenantAccessPolicies/tenantDefault/update | Uppdatera standardklientorganisationen för åtkomstprinciper mellan klientorganisationen |

## <a name="customer-lockbox-access-approver"></a>Customer LockBox-åtkomst godkännare

Hanterar Customer Lockbox [begäranden](/office365/admin/manage/customer-lockbox-requests) i din organisation. De får e-postaviseringar Customer Lockbox begäranden och kan godkänna och neka begäranden från Administrationscenter för Microsoft 365. De kan också aktivera Customer Lockbox eller inaktivera funktionen. Endast globala administratörer kan återställa lösenorden för personer som har tilldelats den här rollen.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.office365.lockbox/allEntities/allTasks | Hantera alla aspekter av Customer Lockbox |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="desktop-analytics-administrator"></a>Desktop Analytics administratör

Användare i den här rollen kan hantera Desktop Analytics tjänsten. Detta omfattar möjligheten att visa tillgångsinventering, skapa distributionsplaner och visa distributions- och hälsostatus.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.office365.desktopAnalytics/allEntities/allTasks | Hantera alla aspekter av Desktop Analytics |

## <a name="directory-readers"></a>Katalogläsare

Användare i den här rollen kan läsa grundläggande kataloginformation. Den här rollen ska användas för:

* Bevilja en specifik uppsättning gästanvändare läsbehörighet i stället för att ge den till alla gästanvändare.
* Att bevilja en specifik uppsättning icke-administratörsanvändare åtkomst till Azure Portal när "Begränsa åtkomsten till Azure AD-portalen till endast administratörer" är inställt på "Ja".
* Bevilja tjänstens huvudnamn åtkomst till katalogen där Directory.Read.All inte är ett alternativ.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/administrativeUnits/standard/read | Läsa grundläggande egenskaper på administrativa enheter |
> | microsoft.directory/administrativeUnits/members/read | Läsa medlemmar i administrativa enheter |
> | microsoft.directory/applications/standard/read | Läsa standardegenskaper för program |
> | microsoft.directory/applications/owners/read | Läsa ägare av program |
> | microsoft.directory/applications/policies/read | Läsa principer för program |
> | microsoft.directory/contacts/standard/read | Läsa grundläggande egenskaper för kontakter i Azure AD |
> | microsoft.directory/contacts/memberOf/read | Läs gruppmedlemskapet för alla kontakter i Azure AD |
> | microsoft.directory/contracts/standard/read | Läsa grundläggande egenskaper i partnerkontrakt |
> | microsoft.directory/devices/standard/read | Läsa grundläggande egenskaper på enheter |
> | microsoft.directory/devices/memberOf/read | Läsa enhetsmedlemskap |
> | microsoft.directory/devices/registeredOwners/read | Läsa registrerade ägare av enheter |
> | microsoft.directory/devices/registeredUsers/read | Läsa registrerade användare av enheter |
> | microsoft.directory/directoryRoles/standard/read | Uppdatera grundläggande egenskaper i Azure AD-roller |
> | microsoft.directory/directoryRoles/eligibleMembers/read | Läs berättigade medlemmar i Azure AD-roller |
> | microsoft.directory/directoryRoles/members/read | Läsa alla medlemmar i Azure AD-roller |
> | microsoft.directory/domains/standard/read | Läsa grundläggande egenskaper för domäner |
> | microsoft.directory/groups/standard/read | Läsa grundläggande egenskaper för grupper |
> | microsoft.directory/groups/appRoleAssignments/read | Läsa programrolltilldelningar för grupper |
> | microsoft.directory/groups/memberOf/read | Läs de grupper som en grupp är medlem i Azure AD för |
> | microsoft.directory/groups/members/read | Läsa medlemmar i grupper |
> | microsoft.directory/groups/owners/read | Läsa ägare av grupper |
> | microsoft.directory/groups/settings/read | Läsinställningar för grupper |
> | microsoft.directory/groupSettings/standard/read | Läsa grundläggande egenskaper för gruppinställningar |
> | microsoft.directory/groupSettingTemplates/standard/read | Läsa grundläggande egenskaper för mallar för gruppinställningar |
> | microsoft.directory/oAuth2PermissionGrants/standard/read | Läsa grundläggande egenskaper för OAuth 2.0-behörighetsstipendier |
> | microsoft.directory/organization/standard/read | Läsa grundläggande egenskaper i en organisation |
> | microsoft.directory/organization/trustedCAsForPasswordlessAuth/read | Läsa betrodda certifikatutfärdare för lösenordsfri autentisering |
> | microsoft.directory/applicationPolicies/standard/read | Läsa standardegenskaper för programprinciper |
> | microsoft.directory/roleAssignments/standard/read | Läsa grundläggande egenskaper för rolltilldelningar |
> | microsoft.directory/roleDefinitions/standard/read | Läsa grundläggande egenskaper för rolldefinitioner |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Läsa rolltilldelningar för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | Läsa rolltilldelningar tilldelade till tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/standard/read | Läsa grundläggande egenskaper för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/memberOf/read | Läsa gruppmedlemskap i tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | Läsa delegerade behörighetsstipendier för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/owners/read | Läsa ägare av tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/ownedObjects/read | Läsa ägda objekt för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/policies/read | Läsa principer för tjänstens huvudnamn |
> | microsoft.directory/subscribedSkus/standard/read | Läsa grundläggande egenskaper för prenumerationer |
> | microsoft.directory/users/standard/read | Läsa grundläggande egenskaper för användare |
> | microsoft.directory/users/appRoleAssignments/read | Läsa programrolltilldelningar för användare |
> | microsoft.directory/users/directReports/read | Läs direktrapporterna för användare |
> | microsoft.directory/users/manager/read | Läshanterare för användare |
> | microsoft.directory/users/memberOf/read | Läsa gruppmedlemskap för användare |
> | microsoft.directory/users/oAuth2PermissionGrants/read | Läs delegerade behörighetsstipendier för användare |
> | microsoft.directory/users/ownedDevices/read | Läsa ägda enheter för användare |
> | microsoft.directory/users/ownedObjects/read | Läsa ägda objekt för användare |
> | microsoft.directory/users/registeredDevices/read | Läsa registrerade användares enheter |

## <a name="directory-synchronization-accounts"></a>Katalogsynkroniseringskonton

Använd inte. Den här rollen tilldelas automatiskt Azure AD Connect tjänsten och är inte avsedd eller stöds för någon annan användning.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/applications/create | Skapa alla typer av program |
> | microsoft.directory/applications/delete | Ta bort alla typer av program |
> | microsoft.directory/applications/appRoles/update | Uppdatera egenskapen appRoles för alla typer av program |
> | microsoft.directory/applications/audience/update | Uppdatera målgruppsegenskapen för program |
> | microsoft.directory/applications/authentication/update | Uppdatera autentisering för alla typer av program |
> | microsoft.directory/applications/basic/update | Uppdatera grundläggande egenskaper för program |
> | microsoft.directory/applications/credentials/update | Uppdatera programautentiseringsuppgifter |
> | microsoft.directory/applications/owners/update | Uppdatera programägare |
> | microsoft.directory/applications/permissions/update | Uppdatera exponerade behörigheter och nödvändiga behörigheter för alla typer av program |
> | microsoft.directory/applications/policies/update | Uppdatera principer för program |
> | microsoft.directory/organization/dirSync/update | Uppdatera organisationskatalogsynkroniseringsegenskapen |
> | microsoft.directory/policies/create | Skapa principer i Azure AD |
> | microsoft.directory/policies/delete | Ta bort principer i Azure AD |
> | microsoft.directory/policies/standard/read | Läsa grundläggande egenskaper för principer |
> | microsoft.directory/policies/owners/read | Läsa ägare av principer |
> | microsoft.directory/policies/policyAppliedTo/read | Egenskapen Read policies.policyAppliedTo |
> | microsoft.directory/policies/basic/update | Uppdatera grundläggande egenskaper för principer |
> | microsoft.directory/policies/owners/update | Uppdatera ägare av principer |
> | microsoft.directory/policies/tenantDefault/update | Uppdatera standardorganisationsprinciper |
> | microsoft.directory/servicePrincipals/create | Skapa tjänsthuvudnamn |
> | microsoft.directory/servicePrincipals/delete | Ta bort tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/enable | Aktivera tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/disable | Inaktivera tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Hantera autentiseringsuppgifter för enkel inloggning med lösenord för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Läsa autentiseringsuppgifter för enkel inloggning med lösenord för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Läsa rolltilldelningar för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | Läsa rolltilldelningar tilldelade till tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/standard/read | Läsa grundläggande egenskaper för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/memberOf/read | Läsa gruppmedlemskap i tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | Läs delegerade behörighetsstipendier för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/owners/read | Läsa ägare av tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/ownedObjects/read | Läsa ägda objekt för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/policies/read | Läsa principer för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Uppdatera rolltilldelningar för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/audience/update | Uppdatera målgruppsegenskaper för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/authentication/update | Uppdatera autentiseringsegenskaper för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/basic/update | Uppdatera grundläggande egenskaper för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/credentials/update | Uppdatera autentiseringsuppgifterna för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/owners/update | Uppdatera ägare av tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/permissions/update | Uppdatera behörigheter för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/policies/update | Uppdatera principer för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/tag/update | Uppdatera taggegenskapen för tjänstens huvudnamn |

## <a name="directory-writers"></a>Katalogskrivare

Användare i den här rollen kan läsa och uppdatera grundläggande information om användare, grupper och tjänstens huvudnamn. Tilldela den här rollen endast till program som inte stöder [Consent Framework](../develop/quickstart-register-app.md). Den ska inte tilldelas till några användare.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/groups/assignLicense | Tilldela produktlicenser till grupper för gruppbaserad licensiering |
> | microsoft.directory/groups/create | Skapa grupper, exklusive roll-tilldelningsbara grupper |
> | microsoft.directory/groups/reprocessLicenseAssignment | Ombearbeta licenstilldelningar för gruppbaserad licensiering |
> | microsoft.directory/groups/basic/update | Uppdatera grundläggande egenskaper för grupper, exklusive roll-tilldelningsbara grupper |
> | microsoft.directory/groups/classification/update | Uppdatera klassificeringsegenskapen för grupper, exklusive roll assignable-grupper |
> | microsoft.directory/groups/dynamicMembershipRule/update | Uppdatera dynamisk medlemskapsregel för grupper, exklusive roll-tilldelningsbara grupper |
> | microsoft.directory/groups/groupType/update | Uppdatera egenskapen groupType för en grupp |
> | microsoft.directory/groups/members/update | Uppdatera medlemmar i grupper, exklusive roll-tilldelningsbara grupper |
> | microsoft.directory/groups/onPremWriteBack/update | Uppdatera Azure Active Directory grupper som ska skrivas tillbaka till den lokala platsen med Azure AD Connect |
> | microsoft.directory/groups/owners/update | Uppdatera ägare av grupper, exklusive roll-tilldelningsbara grupper |
> | microsoft.directory/groups/settings/update | Uppdatera inställningar för grupper |
> | microsoft.directory/groups/visibility/update | Uppdatera synlighetsegenskapen för grupper |
> | microsoft.directory/groupSettings/create | Skapa gruppinställningar |
> | microsoft.directory/groupSettings/delete | Ta bort gruppinställningar |
> | microsoft.directory/groupSettings/basic/update | Uppdatera grundläggande egenskaper för gruppinställningar |
> | microsoft.directory/oAuth2PermissionGrants/create | Skapa OAuth 2.0-behörighetsstipendier |
> | microsoft.directory/oAuth2PermissionGrants/basic/update | Uppdatera OAuth 2.0-behörighetsstipendier |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Hantera programetablering av hemligheter och autentiseringsuppgifter |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Starta, starta om och pausa synkroniseringsjobb för programetablering |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Skapa och hantera synkroniseringsjobb och schema för programetablering |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Bevilja tjänstens huvudnamn direkt åtkomst till en grupps data |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Uppdatera rolltilldelningar för tjänstens huvudnamn |
> | microsoft.directory/users/assignLicense | Hantera användarlicenser |
> | microsoft.directory/users/create | Lägga till användare |
> | microsoft.directory/users/disable | Inaktivera användare |
> | microsoft.directory/users/enable | Aktivera användare |
> | microsoft.directory/users/invalidateAllRefreshTokens | Framt tvinga ut inloggning genom att ogiltigförklara token för användaruppdatering |
> | microsoft.directory/users/reprocessLicenseAssignment | Ombearbeta licenstilldelningar för användare |
> | microsoft.directory/users/basic/update | Uppdatera grundläggande egenskaper för användare |
> | microsoft.directory/users/manager/update | Uppdateringshanterare för användare |
> | microsoft.directory/users/userPrincipalName/update | Uppdatera användarens huvudnamn för användare |

## <a name="domain-name-administrator"></a>Domännamnsadministratör

Användare med den här rollen kan hantera (läsa, lägga till, verifiera, uppdatera och ta bort) domännamn. De kan också läsa kataloginformation om användare, grupper och program, eftersom dessa objekt har domänberoenden. För lokala miljöer kan användare med den här rollen konfigurera domännamn för federation så att associerade användare alltid autentiseras lokalt. Dessa användare kan sedan logga in på Azure AD-baserade tjänster med sina lokala lösenord via enkel inloggning. Federationsinställningar måste synkroniseras via Azure AD Connect, så användarna har också behörighet att hantera Azure AD Connect.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/domains/allProperties/allTasks | Skapa och ta bort domäner och läs och uppdatera alla egenskaper |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |

## <a name="dynamics-365-administrator"></a>Dynamics 365-administratör

Användare med den här rollen har globala behörigheter i Microsoft Dynamics 365 Online när tjänsten finns, samt möjlighet att hantera supportärenden och övervaka tjänstens hälsa. Mer information finns i [Använda tjänstadministratörsrollen för att hantera Din Azure AD-organisation.](/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant)

> [!NOTE]
> I Microsoft Graph API och Azure AD PowerShell identifieras den här rollen som "Dynamics 365-tjänstadministratör". Det är "Dynamics 365-administratör" i [Azure Portal](https://portal.azure.com).

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.dynamics365/allEntities/allTasks | Hantera alla aspekter av Dynamics 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="exchange-administrator"></a>Exchange-administratör

Användare med den här rollen har globala behörigheter i Microsoft Exchange Online när tjänsten finns. Har också möjlighet att skapa och hantera alla Microsoft 365, hantera supportärenden och övervaka tjänstens hälsa. Mer information finns i [Om Microsoft 365 administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> I Microsoft Graph API och Azure AD PowerShell identifieras den här rollen som "Exchange-tjänstadministratör". Det är "Exchange-administratör" i [Azure Portal](https://portal.azure.com). Det är "Exchange Online-administratör" i [administrationscentret för Exchange.](https://go.microsoft.com/fwlink/p/?LinkID=529144)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/groups/hiddenMembers/read | Läsa dolda medlemmar i en grupp |
> | microsoft.directory/groups.unified/create | Skapa Microsoft 365 grupper med undantag för roll-tilldelningsbara grupper |
> | microsoft.directory/groups.unified/delete | Ta Microsoft 365 grupper med undantag för roll-tilldelningsbara grupper |
> | microsoft.directory/groups.unified/restore | Återställa Microsoft 365 grupper |
> | microsoft.directory/groups.unified/basic/update | Uppdatera grundläggande egenskaper för Microsoft 365 grupper med undantag för roll-tilldelningsbara grupper |
> | microsoft.directory/groups.unified/members/update | Uppdatera medlemmar Microsoft 365 grupper med undantag för roll-tilldelningsbara grupper |
> | microsoft.directory/groups.unified/owners/update | Uppdatera ägare Microsoft 365 grupper med undantag för roll-tilldelningsbara grupper |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.office365.exchange/allEntities/basic/allTasks | Hantera alla aspekter av Exchange Online |
> | microsoft.office365.network/performance/allProperties/read | Läsa alla egenskaper för nätverksprestanda i Administrationscenter för Microsoft 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Läsa användningsrapporter för Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="exchange-recipient-administrator"></a>Administratör för Exchange-mottagare

Användare med den här rollen har läsbehörighet till mottagare och skrivåtkomst till attributen för dessa mottagare i Exchange Online. Mer information finns på [Exchange-mottagare.](/exchange/recipients/recipients)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.office365.exchange/allRecipients/allProperties/allTasks | Skapa och ta bort alla mottagare och läs och uppdatera alla egenskaper för mottagarna i Exchange Online |
> | microsoft.office365.exchange/messageTracking/allProperties/allTasks | Hantera alla uppgifter i meddelandespårning i Exchange Online |
> | microsoft.office365.exchange/migration/allProperties/allTasks | Hantera alla uppgifter som rör migrering av mottagare i Exchange Online |

## <a name="external-id-user-flow-administrator"></a>Administratör för externt ID-användarflöde

Användare med den här rollen kan skapa och hantera användarflöden (kallas även "inbyggda" principer) i Azure Portal. Dessa användare kan anpassa HTML/CSS/JavaScript-innehåll, ändra MFA-krav, välja anspråk i token, hantera API-anslutningsappar och konfigurera sessionsinställningar för alla användarflöden i Azure AD-organisationen. Å andra sidan omfattar den här rollen inte möjligheten att granska användardata eller göra ändringar i attributen som ingår i organisationsschemat. Ändringar av Identity Experience Framework (kallas även anpassade principer) ligger också utanför den här rollens omfattning.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/b2cUserFlow/allProperties/allTasks | Läsa och konfigurera användarattribut i Azure Active Directory B2C |

## <a name="external-id-user-flow-attribute-administrator"></a>Administratör för användarflödesattribut för externt ID

Användare med den här rollen lägger till eller tar bort anpassade attribut som är tillgängliga för alla användarflöden i Azure AD-organisationen. Därför kan användare med den här rollen ändra eller lägga till nya element i slutanvändarschemat och påverka beteendet för alla användarflöden och indirekt leda till ändringar av vilka data som kan efterfrågas av slutanvändarna och slutligen skickas som anspråk till program. Den här rollen kan inte redigera användarflöden.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/b2cUserAttribute/allProperties/allTasks | Läsa och konfigurera anpassade principer i Azure Active Directory B2C |

## <a name="external-identity-provider-administrator"></a>Administratör för extern identitetsprovider

Den här administratören hanterar federation mellan Azure AD-organisationer och externa identitetsprovidrar. Med den här rollen kan användarna lägga till nya identitetsprovidrar och konfigurera alla tillgängliga inställningar (t.ex. autentiseringssökväg, tjänst-ID, tilldelade nyckelcontainrar). Den här användaren kan göra det möjligt för Azure AD-organisationen att lita på autentiseringar från externa identitetsproviders. Den resulterande effekten på slutanvändarupplevelsen beror på typen av organisation:

* Azure AD-organisationer för anställda och partner: Tillägget av en federation (t.ex. med Gmail) påverkar omedelbart alla gästinbjudningar som ännu inte har lösts in. Se [Lägga till Google som identitetsprovider för B2B-gästanvändare.](../external-identities/google-federation.md)
* Azure Active Directory B2C organisationer: Tillägg av en federation (till exempel med Facebook eller med en annan Azure AD-organisation) påverkar inte omedelbart slutanvändarflöden förrän identitetsprovidern läggs till som ett alternativ i ett användarflöde (kallas även inbyggd princip). Ett [exempel finns i Microsoft-konto en tjänst som](../../active-directory-b2c/identity-provider-microsoft-account.md) identitetsprovider. Om du vill ändra användarflöden krävs den begränsade rollen "B2C-användarflödesadministratör".

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/identityProviders/allProperties/allTasks | Läsa och konfigurera identitetsproviders i Azure Active Directory B2C |

## <a name="global-administrator"></a>Global administratör

Användare med den här rollen har åtkomst till alla administrativa funktioner i Azure Active Directory samt tjänster som använder Azure Active Directory-identiteter som Microsoft 365 Säkerhetscenter, Microsoft 365 Efterlevnadscenter, Exchange Online, SharePoint Online och Skype för företag – Online. Dessutom kan globala administratörer höja [sin behörighet för att](../../role-based-access-control/elevate-access-global-admin.md) hantera alla Azure-prenumerationer och hanteringsgrupper. Detta gör att globala administratörer kan få fullständig åtkomst till alla Azure-resurser med hjälp av respektive Azure AD-klientorganisation. Den person som registrerar sig för Azure AD-organisationen blir global administratör. Det kan finnas fler än en global administratör på ditt företag. Globala administratörer kan återställa lösenordet för alla användare och alla andra administratörer.

> [!NOTE]
> Som bästa praxis rekommenderar Microsoft att du tilldelar rollen Global administratör till färre än fem personer i din organisation. Mer information finns i [Metodtips för Azure AD-roller.](best-practices.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/accessReviews/allProperties/allTasks | Skapa och ta bort åtkomstgranskningar och läs och uppdatera alla egenskaper för åtkomstgranskningar i Azure AD |
> | microsoft.directory/administrativeUnits/allProperties/allTasks | Skapa och hantera administrativa enheter (inklusive medlemmar) |
> | microsoft.directory/applications/allProperties/allTasks | Skapa och ta bort program och läs och uppdatera alla egenskaper |
> | microsoft.directory/applications/synchronization/standard/read | Läsa etableringsinställningar som är associerade med programobjektet |
> | microsoft.directory/applicationTemplates/instantiate | Skapa en instans av galleriprogram från programmallar |
> | microsoft.directory/appRoleAssignments/allProperties/allTasks | Skapa och ta bort appRoleAssignments och läs och uppdatera alla egenskaper |
> | microsoft.directory/auditLogs/allProperties/read | Läsa alla egenskaper i granskningsloggar, inklusive privilegierade egenskaper |
> | microsoft.directory/authorizationPolicy/allProperties/allTasks | Hantera alla aspekter av auktoriseringsprinciper |
> | microsoft.directory/bitlockerKeys/key/read | Läsa bitlocker-metadata och nyckel på enheter |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | Skapa och ta bort alla resurser och läs och uppdatera standardegenskaper i Microsoft Cloud App Security |
> | microsoft.directory/connectors/create | Skapa anslutningsappar för programproxy |
> | microsoft.directory/connectors/allProperties/read | Läsa alla egenskaper för anslutningsappar för programproxy |
> | microsoft.directory/connectorGroups/create | Skapa anslutningsgrupper för programproxy |
> | microsoft.directory/connectorGroups/delete | Ta bort anslutningsgrupper för programproxy |
> | microsoft.directory/connectorGroups/allProperties/read | Läsa alla egenskaper för anslutningsgrupper för programproxy |
> | microsoft.directory/connectorGroups/allProperties/update | Uppdatera alla egenskaper för anslutningsgrupper för programproxy |
> | microsoft.directory/contacts/allProperties/allTasks | Skapa och ta bort kontakter och läs och uppdatera alla egenskaper |
> | microsoft.directory/contracts/allProperties/allTasks | Skapa och ta bort partnerkontrakt och läs och uppdatera alla egenskaper |
> | microsoft.directory/devices/allProperties/allTasks | Skapa och ta bort enheter och läs och uppdatera alla egenskaper |
> | microsoft.directory/deviceManagementPolicies/standard/read | Läsa standardegenskaper för principer för enhetshanteringsprogram |
> | microsoft.directory/deviceManagementPolicies/basic/update | Uppdatera grundläggande egenskaper för principer för enhetshanteringsprogram |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | Läsa standardegenskaper för enhetsregistreringsprinciper |
> | microsoft.directory/deviceRegistrationPolicy/basic/update | Uppdatera grundläggande egenskaper för enhetsregistreringsprinciper |
> | microsoft.directory/directoryRoles/allProperties/allTasks | Skapa och ta bort katalogroller och läs och uppdatera alla egenskaper |
> | microsoft.directory/directoryRoleTemplates/allProperties/allTasks | Skapa och ta bort Azure AD-rollmallar och läs och uppdatera alla egenskaper |
> | microsoft.directory/domains/allProperties/allTasks | Skapa och ta bort domäner och läs och uppdatera alla egenskaper |
> | microsoft.directory/entitlementManagement/allProperties/allTasks | Skapa och ta bort resurser och läs och uppdatera alla egenskaper i Azure AD-berättigandehantering |
> | microsoft.directory/groups/allProperties/allTasks | Skapa och ta bort grupper och läs och uppdatera alla egenskaper |
> | microsoft.directory/groupsAssignableToRoles/create | Skapa rolltilldelningsbara grupper |
> | microsoft.directory/groupsAssignableToRoles/delete | Ta bort rollde kan tilldelas grupper |
> | microsoft.directory/groupsAssignableToRoles/restore | Återställa roll-tilldelarbara grupper |
> | microsoft.directory/groupsAssignableToRoles/allProperties/update | Uppdatera roll-tilldelarbara grupper |
> | microsoft.directory/groupSettings/allProperties/allTasks | Skapa och ta bort gruppinställningar och läs och uppdatera alla egenskaper |
> | microsoft.directory/groupSettingTemplates/allProperties/allTasks | Skapa och ta bort mallar för gruppinställningar och läs och uppdatera alla egenskaper |
> | microsoft.directory/identityProtection/allProperties/allTasks | Skapa och ta bort alla resurser och läs och uppdatera standardegenskaper i Azure AD Identity Protection |
> | microsoft.directory/loginOrganizationBranding/allProperties/allTasks | Skapa och ta bort loginTenantBranding och läs och uppdatera alla egenskaper |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Skapa och ta bort OAuth 2.0-behörighetsstipendier och läsa och uppdatera alla egenskaper |
> | microsoft.directory/organization/allProperties/allTasks | Skapa och ta bort organisationer och läs och uppdatera alla egenskaper |
> | microsoft.directory/policies/allProperties/allTasks | Skapa och ta bort principer och läsa och uppdatera alla egenskaper |
> | microsoft.directory/conditionalAccessPolicies/allProperties/allTasks | Hantera alla egenskaper för principer för villkorlig åtkomst |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Läsa alla resurser i Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Läsa alla egenskaper för etableringsloggar |
> | microsoft.directory/roleAssignments/allProperties/allTasks | Skapa och ta bort rolltilldelningar och läs och uppdatera alla egenskaper för rolltilldelning |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | Skapa och ta bort rolldefinitioner och läs och uppdatera alla egenskaper |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | Skapa och ta bort scopedRoleMemberships och läs och uppdatera alla egenskaper |
> | microsoft.directory/serviceAction/activateService | Kan utföra åtgärden "aktivera tjänst" för en tjänst |
> | microsoft.directory/serviceAction/disableDirectoryFeature | Kan utföra tjänståtgärden "inaktivera katalogfunktion" |
> | microsoft.directory/serviceAction/enableDirectoryFeature | Kan utföra tjänståtgärden "aktivera katalogfunktion" |
> | microsoft.directory/serviceAction/getAvailableExtentionProperties | Kan utföra åtgärden getAvailableExtentionProperties-tjänsten |
> | microsoft.directory/servicePrincipals/allProperties/allTasks | Skapa och ta bort tjänstens huvudnamn och läs och uppdatera alla egenskaper |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-company-admin | Bevilja medgivande för alla behörigheter till alla program |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Bevilja tjänstens huvudnamn direkt åtkomst till en grupps data |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | Läsa etableringsinställningar som är associerade med tjänstens huvudnamn |
> | microsoft.directory/signInReports/allProperties/read | Läsa alla egenskaper i inloggningsrapporter, inklusive privilegierade egenskaper |
> | microsoft.directory/subscribedSkus/allProperties/allTasks | Köpa och hantera prenumerationer och ta bort prenumerationer |
> | microsoft.directory/users/allProperties/allTasks | Skapa och ta bort användare och läsa och uppdatera alla egenskaper |
> | microsoft.directory/permissionGrantPolicies/create | Skapa principer för behörighets beviljande |
> | microsoft.directory/permissionGrantPolicies/delete | Ta bort principer för behörighets beviljande |
> | microsoft.directory/permissionGrantPolicies/standard/read | Läsa standardegenskaper för behörighets beviljandeprinciper |
> | microsoft.directory/permissionGrantPolicies/basic/update | Uppdatera grundläggande egenskaper för behörighets beviljandeprinciper |
> | microsoft.directory/servicePrincipalCreationPolicies/create | Skapa principer för att skapa tjänstens huvudnamn |
> | microsoft.directory/servicePrincipalCreationPolicies/delete | Ta bort principer för att skapa tjänstens huvudnamn |
> | microsoft.directory/servicePrincipalCreationPolicies/standard/read | Läsa standardegenskaper för principer för att skapa tjänstens huvudnamn |
> | microsoft.directory/servicePrincipalCreationPolicies/basic/update | Uppdatera grundläggande egenskaper för principer för att skapa tjänstens huvudnamn |
> | microsoft.azure.advancedThreatProtection/allEntities/allTasks | Hantera alla aspekter av Azure Advanced Threat Protection |
> | microsoft.azure.informationProtection/allEntities/allTasks | Hantera alla aspekter av Azure Information Protection |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.commerce.billing/allEntities/allTasks | Hantera alla aspekter av Office 365-fakturering |
> | microsoft.dynamics365/allEntities/allTasks | Hantera alla aspekter av Dynamics 365 |
> | microsoft.flow/allEntities/allTasks | Hantera alla aspekter av Microsoft Power Automate |
> | microsoft.intune/allEntities/allTasks | Hantera alla aspekter av Microsoft Intune |
> | microsoft.office365.complianceManager/allEntities/allTasks | Hantera alla aspekter av Office 365 Compliance Manager |
> | microsoft.office365.desktopAnalytics/allEntities/allTasks | Hantera alla aspekter av Desktop Analytics |
> | microsoft.office365.exchange/allEntities/basic/allTasks | Hantera alla aspekter av Exchange Online |
> | microsoft.office365.lockbox/allEntities/allTasks | Hantera alla aspekter av Customer Lockbox |
> | microsoft.office365.messageCenter/messages/read | Läsa meddelanden i Meddelandecenter i Administrationscenter för Microsoft 365, exklusive säkerhetsmeddelanden |
> | microsoft.office365.messageCenter/securityMessages/read | Läsa säkerhetsmeddelanden i Meddelandecenter i Administrationscenter för Microsoft 365 |
> | microsoft.office365.network/performance/allProperties/read | Läsa alla egenskaper för nätverksprestanda i Administrationscenter för Microsoft 365 |
> | microsoft.office365.protectionCenter/allEntities/allProperties/allTasks | Hantera alla aspekter av säkerhets- och efterlevnadscenter |
> | microsoft.office365.search/content/manage | Skapa och ta bort innehåll och läs och uppdatera alla egenskaper i Microsoft Search |
> | microsoft.office365.securityComplianceCenter/allEntities/allTasks | Skapa och ta bort alla resurser och läs och uppdatera standardegenskaper i Microsoft 365 Security and Compliance Center |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.sharePoint/allEntities/allTasks | Skapa och ta bort alla resurser och läs och uppdatera standardegenskaper i SharePoint |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Hantera alla aspekter av Skype för företag – Online |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Läs användningsrapporter för Office 365 |
> | microsoft.office365.userCommunication/allEntities/allTasks | Läsa och uppdatera synligheten för nya meddelanden |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |
> | microsoft.powerApps/allEntities/allTasks | Hantera alla aspekter av Power Apps |
> | microsoft.powerApps.powerBI/allEntities/allTasks | Hantera alla aspekter av Power BI |
> | microsoft.windows.defenderAdvancedThreatProtection/allEntities/allTasks | Hantera alla aspekter av Microsoft Defender för slutpunkt |

## <a name="global-reader"></a>Global läsare

Användare i den här rollen kan läsa inställningar och administrativ information Microsoft 365 tjänster men kan inte vidta hanteringsåtgärder. Global läsare är den skrivskyddade motsvarigheten till global administratör. Tilldela global läsare i stället för global administratör för planering, granskningar eller undersökningar. Använd Global läsare i kombination med andra begränsade administratörsroller som Exchange-administratör för att göra det enklare att få jobbet gjort utan att tilldela rollen Global administratör. Global läsare arbetar med Administrationscenter för Microsoft 365, Administrationscenter för Exchange, Administrationscenter för SharePoint, Administrationscenter för Teams, Säkerhetscenter, Efterlevnadscenter, Azure AD-administrationscenter och Enhetshantering administrationscenter.

> [!NOTE]
> Rollen Global läsare har några begränsningar just nu –
>
>- [Administrationscenter för OneDrive](https://admin.onedrive.com/) – Administrationscenter för OneDrive stöder inte rollen Global läsare
>- [Administrationscenter för M365](https://admin.microsoft.com/Adminportal/Home#/homepage) – Globala läsare kan inte läsa integrerade appar. Fliken Integrerade appar visas **inte under Inställningar** i **den** vänstra rutan i administrationscentret för M365.
>- [Office Security & Compliance Center](https://sip.protection.office.com/homepage) – Global läsare kan inte läsa SCC-granskningsloggar, söka efter innehåll eller läsa Säkerhetspoäng.
>- [Teams administrationscenter –](https://admin.teams.microsoft.com) Global läsare kan inte läsa **Teams livscykel,** **Analytics & rapporter,** **IP-enhetshantering och** **appkatalog.**
>- [Privileged Access Management (PAM)](/office365/securitycompliance/privileged-access-management-overview) stöder inte rollen Global läsare.
>- [Azure Information Protection](/azure/information-protection/what-is-information-protection) – Global läsare stöds endast för [central](/azure/information-protection/reports-aip) rapportering och när din Azure AD-organisation inte finns på den enhetliga [etiketteringsplattformen](/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform).
>
> Dessa funktioner är för närvarande under utveckling.
>

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/applications/applicationProxy/read | Läsa alla egenskaper för programproxy |
> | microsoft.directory/applications/synchronization/standard/read | Läsa etableringsinställningar som är associerade med programobjektet |
> | microsoft.directory/auditLogs/allProperties/read | Läsa alla egenskaper i granskningsloggar, inklusive privilegierade egenskaper |
> | microsoft.directory/bitlockerKeys/key/read | Läsa bitlocker-metadata och nyckel på enheter |
> | microsoft.directory/connectors/allProperties/read | Läsa alla egenskaper för anslutningsappar för programproxy |
> | microsoft.directory/connectorGroups/allProperties/read | Läs alla egenskaper för anslutningsgrupper för programproxy |
> | microsoft.directory/entitlementManagement/allProperties/read | Läsa alla egenskaper i Azure AD-berättigandehantering |
> | microsoft.directory/deviceManagementPolicies/standard/read | Läsa standardegenskaper för principer för enhetshanteringsprogram |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | Läsa standardegenskaper för enhetsregistreringsprinciper |
> | microsoft.directory/groups/hiddenMembers/read | Läsa dolda medlemmar i en grupp |
> | microsoft.directory/policies/standard/read | Läsa grundläggande egenskaper för principer |
> | microsoft.directory/policies/owners/read | Läsa ägare av principer |
> | microsoft.directory/policies/policyAppliedTo/read | Egenskapen Read policies.policyAppliedTo |
> | microsoft.directory/conditionalAccessPolicies/standard/read | Läsa villkorlig åtkomst för principer |
> | microsoft.directory/conditionalAccessPolicies/owners/read | Läs om ägarna av principer för villkorlig åtkomst |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | Läs egenskapen "tillämpad på" för principer för villkorsstyrd åtkomst |
> | microsoft.directory/provisioningLogs/allProperties/read | Läsa alla egenskaper för etableringsloggar |
> | microsoft.directory/servicePrincipals/authentication/read | Läsa autentiseringsegenskaper för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | Läsa etableringsinställningar som är associerade med tjänstens huvudnamn |
> | microsoft.directory/signInReports/allProperties/read | Läsa alla egenskaper i inloggningsrapporter, inklusive privilegierade egenskaper |
> | microsoft.directory/users/strongAuthentication/read | Läs den starka autentiseringsegenskapen för användare |
> | microsoft.commerce.billing/allEntities/read | Läsa alla resurser för Office 365-fakturering |
> | microsoft.office365.exchange/allEntities/standard/read | Läsa alla resurser i Exchange Online |
> | microsoft.office365.messageCenter/messages/read | Läsa meddelanden i Meddelandecenter i Administrationscenter för Microsoft 365, exklusive säkerhetsmeddelanden |
> | microsoft.office365.messageCenter/securityMessages/read | Läsa säkerhetsmeddelanden i Meddelandecenter i Administrationscenter för Microsoft 365 |
> | microsoft.office365.network/performance/allProperties/read | Läsa alla egenskaper för nätverksprestanda i Administrationscenter för Microsoft 365 |
> | microsoft.office365.protectionCenter/allEntities/allProperties/read | Läsa alla egenskaper i säkerhets- och efterlevnadscenter |
> | microsoft.office365.securityComplianceCenter/allEntities/read | Läsa standardegenskaper i Microsoft 365 Security and Compliance Center |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Läsa användningsrapporter för Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="groups-administrator"></a>Gruppadministratör

Användare i den här rollen kan skapa/hantera grupper och dess inställningar som namngivnings- och förfalloprinciper. Det är viktigt att förstå att tilldelning av en användare till den här rollen ger dem möjlighet att hantera alla grupper i organisationen över olika arbetsbelastningar som Teams, SharePoint, Yammer utöver Outlook. Användaren kommer också att kunna hantera de olika gruppinställningarna i olika administrationsportaler som Microsoft Admin Center, Azure Portal, samt arbetsbelastningsspecifika sådana som Teams och Administrationscenter för SharePoint.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/groups/assignLicense | Tilldela produktlicenser till grupper för gruppbaserad licensiering |
> | microsoft.directory/groups/create | Skapa grupper, exklusive roll-tilldelningsbara grupper |
> | microsoft.directory/groups/delete | Ta bort grupper, exklusive roll-tilldelningsbara grupper |
> | microsoft.directory/groups/hiddenMembers/read | Läsa dolda medlemmar i en grupp |
> | microsoft.directory/groups/reprocessLicenseAssignment | Ombearbeta licenstilldelningar för gruppbaserad licensiering |
> | microsoft.directory/groups/restore | Återställa borttagna grupper |
> | microsoft.directory/groups/basic/update | Uppdatera grundläggande egenskaper för grupper, exklusive roll-tilldelningsbara grupper |
> | microsoft.directory/groups/classification/update | Uppdatera klassificeringsegenskapen för grupper, exklusive roll-tilldelningsbara grupper |
> | microsoft.directory/groups/dynamicMembershipRule/update | Uppdatera dynamisk medlemskapsregel för grupper, exklusive roll-tilldelningsbara grupper |
> | microsoft.directory/groups/groupType/update | Uppdatera egenskapen groupType för en grupp |
> | microsoft.directory/groups/members/update | Uppdatera medlemmar i grupper, exklusive roll-tilldelningsbara grupper |
> | microsoft.directory/groups/onPremWriteBack/update | Uppdatera Azure Active Directory grupper som ska skrivas tillbaka till den lokala platsen med Azure AD Connect |
> | microsoft.directory/groups/owners/update | Uppdatera ägare av grupper, exklusive roll-tilldelningsbara grupper |
> | microsoft.directory/groups/settings/update | Uppdatera inställningar för grupper |
> | microsoft.directory/groups/visibility/update | Uppdatera synlighetsegenskapen för grupper |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Bevilja tjänstens huvudnamn direkt åtkomst till en grupps data |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="guest-inviter"></a>Gäst inbjudare

Användare i den här rollen kan hantera Azure Active Directory B2B-gästanvändares inbjudningar när inställningen **Medlemmar** kan bjuda in användare är inställd på Nej. Mer information om B2B-samarbete finns [i Om Azure AD B2B-samarbete](../external-identities/what-is-b2b.md). Den innehåller inga andra behörigheter.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/users/inviteGuest | Bjud in gästanvändare |
> | microsoft.directory/users/standard/read | Läsa grundläggande egenskaper för användare |
> | microsoft.directory/users/appRoleAssignments/read | Läsa programrolltilldelningar för användare |
> | microsoft.directory/users/directReports/read | Läs direktrapporterna för användare |
> | microsoft.directory/users/manager/read | Läshanterare för användare |
> | microsoft.directory/users/memberOf/read | Läsa gruppmedlemskap för användare |
> | microsoft.directory/users/oAuth2PermissionGrants/read | Läsa delegerade behörighetsstipendier för användare |
> | microsoft.directory/users/ownedDevices/read | Läsa användares ägda enheter |
> | microsoft.directory/users/ownedObjects/read | Läsa ägda objekt för användare |
> | microsoft.directory/users/registeredDevices/read | Läsa registrerade användares enheter |

## <a name="helpdesk-administrator"></a>Supportadministratör

Användare med den här rollen kan ändra lösenord, ogiltigförklara uppdateringstoken, hantera tjänstbegäranden och övervaka tjänstens hälsa. Om en uppdateringstoken ogiltigförklaras måste användaren logga in igen. Om en supportadministratör kan återställa en användares lösenord och ogiltigförklara uppdateringstoken beror på vilken roll användaren har tilldelats. En lista över de roller som en supportadministratör kan återställa lösenord för och ogiltigförklara uppdateringstoken finns i Behörigheter [för lösenordsåterställning.](#password-reset-permissions)

> [!IMPORTANT]
> Användare med den här rollen kan ändra lösenord för personer som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i och utanför Azure Active Directory. Att ändra lösenordet för en användare kan innebära möjligheten att anta att användarens identitet och behörigheter. Exempel:
>
>- Programregistrering och ägare av företagsprogram, som kan hantera autentiseringsuppgifter för appar som de äger. Dessa appar kan ha privilegierad behörighet i Azure AD och någon annanstans inte beviljad till supportadministratörer. Via den här sökvägen kan en supportadministratör anta identiteten för en programägare och sedan ytterligare anta identiteten för ett privilegierat program genom att uppdatera autentiseringsuppgifterna för programmet.
>- Azure-prenumerationsägare, som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i Azure.
>- Säkerhetsgrupp och Microsoft 365 gruppägare, som kan hantera gruppmedlemskap. Dessa grupper kan bevilja åtkomst till känslig eller privat information eller kritisk konfiguration i Azure AD och någon annanstans.
>- Administratörer i andra tjänster utanför Azure AD som Exchange Online, Säkerhets- och efterlevnadscenter för Office och system för personalavdelningen.
>- Icke-administratörer som chefer, juridiska rådgivare och personalpersonal som kan ha åtkomst till känslig eller privat information.

Du kan delegera administrativa behörigheter över delmängder av användare och tillämpa principer på en delmängd av användare med [administrativa enheter.](administrative-units.md)

Den här rollen hette tidigare "Lösenordsadministratör" i [Azure Portal](https://portal.azure.com/). Namnet "Supportadministratör" i Azure AD matchar nu namnet i Azure AD PowerShell och Microsoft Graph API: et.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/bitlockerKeys/key/read | Läsa BitLocker-metadata och -nyckel på enheter |
> | microsoft.directory/users/invalidateAllRefreshTokens | Framt tvinga ut ut genom att ogiltigförklara token för användaruppdatering |
> | microsoft.directory/users/password/update | Återställa lösenord för alla användare |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="hybrid-identity-administrator"></a>Hybrididentitetsadministratör

Användare i den här rollen kan skapa, hantera och distribuera konfigurationskonfiguration för etablering från AD till Azure AD med hjälp av molnetablering samt hantera Azure AD Connect och federationsinställningar. Användare kan också felsöka och övervaka loggar med den här rollen.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/applications/create | Skapa alla typer av program |
> | microsoft.directory/applications/delete | Ta bort alla typer av program |
> | microsoft.directory/applications/appRoles/update | Uppdatera egenskapen appRoles för alla typer av program |
> | microsoft.directory/applications/audience/update | Uppdatera målgruppsegenskapen för program |
> | microsoft.directory/applications/authentication/update | Uppdatera autentisering för alla typer av program |
> | microsoft.directory/applications/basic/update | Uppdatera grundläggande egenskaper för program |
> | microsoft.directory/applications/credentials/update | Uppdatera programautentiseringsuppgifter |
> | microsoft.directory/applications/owners/update | Uppdatera programägare |
> | microsoft.directory/applications/permissions/update | Uppdatera exponerade behörigheter och nödvändiga behörigheter för alla typer av program |
> | microsoft.directory/applications/policies/update | Uppdatera principer för program |
> | microsoft.directory/applications/synchronization/standard/read | Läsa etableringsinställningar som är associerade med programobjektet |
> | microsoft.directory/applicationTemplates/instantiate | Instansiera galleriprogram från programmallar |
> | microsoft.directory/auditLogs/allProperties/read | Läsa alla egenskaper i granskningsloggar, inklusive privilegierade egenskaper |
> | microsoft.directory/cloudProvisioning/allProperties/allTasks | Läsa och konfigurera alla egenskaper för Azure AD Cloud Provisioning-tjänsten. |
> | microsoft.directory/domains/allProperties/read | Läsa alla egenskaper för domäner |
> | microsoft.directory/domains/federation/update | Uppdatera federationsegenskapen för domäner |
> | microsoft.directory/organization/dirSync/update | Uppdatera organisationskatalogsynkroniseringsegenskapen |
> | microsoft.directory/provisioningLogs/allProperties/read | Läsa alla egenskaper för etableringsloggar |
> | microsoft.directory/servicePrincipals/create | Skapa tjänsthuvudnamn |
> | microsoft.directory/servicePrincipals/delete | Ta bort tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/disable | Inaktivera tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/enable | Aktivera tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Hantera hemligheter och autentiseringsuppgifter för programetablering |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Starta, starta om och pausa synkroniseringsjobb för programetablering |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Skapa och hantera synkroniseringsjobb och schema för programetablering |
> | microsoft.directory/servicePrincipals/audience/update | Uppdatera målgruppsegenskaper för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/authentication/update | Uppdatera autentiseringsegenskaper för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/basic/update | Uppdatera grundläggande egenskaper för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/credentials/update | Uppdatera autentiseringsuppgifterna för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/owners/update | Uppdatera ägare av tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/permissions/update | Uppdatera behörigheter för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/policies/update | Uppdatera principer för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/tag/update | Uppdatera taggegenskapen för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | Läsa etableringsinställningar som är associerade med tjänstens huvudnamn |
> | microsoft.directory/signInReports/allProperties/read | Läsa alla egenskaper i inloggningsrapporter, inklusive privilegierade egenskaper |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.office365.messageCenter/messages/read | Läsa meddelanden i Meddelandecenter i Administrationscenter för Microsoft 365, exklusive säkerhetsmeddelanden |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="insights-administrator"></a>Insights-administratör

Användare i den här rollen kan komma åt alla administrativa funktioner i [M365 Insights-programmet](https://go.microsoft.com/fwlink/?linkid=2129521). Den här rollen har möjlighet att läsa kataloginformation, övervaka tjänstens hälsotillstånd, filsupportärenden och få åtkomst till administratörsinställningarna för Insights.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.insights/allEntities/allTasks | Hantera alla aspekter av Insights-appen |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="insights-business-leader"></a>Affärsledare för Insights

Användare i den här rollen kan komma åt en uppsättning instrumentpaneler och insikter via [M365 Insights-programmet](https://go.microsoft.com/fwlink/?linkid=2129521). Detta omfattar fullständig åtkomst till alla instrumentpaneler och funktioner för insikter och datagranskning. Användare i den här rollen har inte åtkomst till produktkonfigurationsinställningar, vilket är insights-administratörsrollens ansvar.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.insights/reports/read | Visa rapporter och instrumentpanel i Insights-appen |
> | microsoft.insights/programs/update | Distribuera och hantera program i Insights-appen |

## <a name="intune-administrator"></a>Intune-administratör

Användare med den här rollen har globala behörigheter Microsoft Intune Online när tjänsten finns. Dessutom innehåller den här rollen möjligheten att hantera användare och enheter för att associera en princip, samt skapa och hantera grupper. Mer information finns [i Rollbaserad administrationskontroll (RBAC) med Microsoft Intune](/intune/role-based-access-control).

Den här rollen kan skapa och hantera alla säkerhetsgrupper. Intune-administratören har dock inte administratörsrättigheter för Office-grupper. Det innebär att administratören inte kan uppdatera ägare eller medlemskap i alla Office-grupper i organisationen. Han eller hon kan dock hantera den Office-grupp som han skapar och som ingår i hans/hennes behörigheter för slutanvändare. Därför bör alla Office-grupper (inte säkerhetsgrupper) som han/hon skapar räknas mot hans/hennes kvot på 250.

> [!NOTE]
> I Microsoft Graph API och Azure AD PowerShell identifieras den här rollen som "Intune-tjänstadministratör". Det är "Intune-administratör" i [Azure Portal](https://portal.azure.com).

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/bitlockerKeys/key/read | Läsa BitLocker-metadata och -nyckel på enheter |
> | microsoft.directory/contacts/create | Skapa kontakter |
> | microsoft.directory/contacts/delete | Ta bort kontakter |
> | microsoft.directory/contacts/basic/update | Uppdatera grundläggande egenskaper för kontakter |
> | microsoft.directory/devices/create | Skapa enheter (registrera i Azure AD) |
> | microsoft.directory/devices/delete | Ta bort enheter från Azure AD |
> | microsoft.directory/devices/disable | Inaktivera enheter i Azure AD |
> | microsoft.directory/devices/enable | Aktivera enheter i Azure AD |
> | microsoft.directory/devices/basic/update | Uppdatera grundläggande egenskaper på enheter |
> | microsoft.directory/devices/extensionAttributes/update | Uppdatera alla värden för egenskapen devices.extensionAttributes |
> | microsoft.directory/devices/registeredOwners/update | Uppdatera registrerade ägare av enheter |
> | microsoft.directory/devices/registeredUsers/update | Uppdatera registrerade användare av enheter |
> | microsoft.directory/deviceManagementPolicies/standard/read | Läsa standardegenskaper för principer för enhetshanteringsprogram |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | Läsa standardegenskaper för enhetsregistreringsprinciper |
> | microsoft.directory/groups/hiddenMembers/read | Läsa dolda medlemmar i en grupp |
> | microsoft.directory/groups.security/create | Skapa säkerhetsgrupper med undantag för roll assignerbara grupper |
> | microsoft.directory/groups.security/delete | Ta bort säkerhetsgrupper med undantag för roll-tilldelningsbara grupper |
> | microsoft.directory/groups.security/basic/update | Uppdatera grundläggande egenskaper för säkerhetsgrupper med undantag för roll-tilldelningsbara grupper |
> | microsoft.directory/groups.security/classification/update | Uppdatera klassificeringsegenskapen för säkerhetsgrupper med undantag för rollde tilldelningsbara grupper |
> | microsoft.directory/groups.security/dynamicMembershipRule/update | Uppdatera dynamicMembershipRule-egenskapen för säkerhetsgrupperna med undantag för roll-tilldelningsbara grupper |
> | microsoft.directory/groups.security/members/update | Uppdatera medlemmar i säkerhetsgrupper med undantag för roll-tilldelningsbara grupper |
> | microsoft.directory/groups.security/owners/update | Uppdatera ägare av säkerhetsgrupper med undantag för roll-tilldelningsbara grupper |
> | microsoft.directory/groups.security/visibility/update | Uppdatera synlighetsegenskapen för säkerhetsgrupper med undantag för rolldefinerbara grupper |
> | microsoft.directory/users/basic/update | Uppdatera grundläggande egenskaper för användare |
> | microsoft.directory/users/manager/update | Uppdateringshanterare för användare |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.intune/allEntities/allTasks | Hantera alla aspekter av Microsoft Intune |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="kaizala-administrator"></a>Kaizala-administratör

Användare med den här rollen har global behörighet att hantera inställningar i Microsoft Kaizala när tjänsten finns, samt möjligheten att hantera supportärenden och övervaka tjänstens hälsa. Dessutom kan användaren komma åt rapporter som rör & användning av Kaizala av organisationsmedlemmar och affärsrapporter som genereras med hjälp av Kaizala-åtgärderna.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="knowledge-administrator"></a>Kunskapsadministratör

Användare i den här rollen har fullständig åtkomst till alla kunskaps-, inlärnings- och intelligenta funktioner i Administrationscenter för Microsoft 365. De har en allmän förståelse för produktpaketet, licensieringsinformation och har ansvar för att kontrollera åtkomsten. Kunskapsadministratören kan skapa och hantera innehåll, till exempel ämnen, förkortningar och utbildningsresurser. Dessutom kan dessa användare skapa innehållscentra, övervaka tjänstens hälsa och skapa tjänstbegäranden.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/groups.security/create | Skapa säkerhetsgrupper med undantag för roll assignerbara grupper |
> | microsoft.directory/groups.security/createAsOwner | Skapa säkerhetsgrupper med undantag för roll-tilldelningsbara grupper och skapare läggs till som den första ägaren |
> | microsoft.directory/groups.security/delete | Ta bort säkerhetsgrupper med undantag för roll-tilldelningsbara grupper |
> | microsoft.directory/groups.security/basic/update | Uppdatera grundläggande egenskaper för säkerhetsgrupper med undantag för roll-tilldelningsbara grupper |
> | microsoft.directory/groups.security/members/update | Uppdatera medlemmar i säkerhetsgrupper med undantag för roll-tilldelningsbara grupper |
> | microsoft.directory/groups.security/owners/update | Uppdatera ägare av säkerhetsgrupper med undantag för roll-tilldelningsbara grupper |
> | microsoft.office365.knowledge/contentUnderstanding/allProperties/allTasks | Läsa och uppdatera alla egenskaper för innehållsförståelse i Administrationscenter för Microsoft 365 |
> | microsoft.office365.knowledge/knowledgeNetwork/allProperties/allTasks | Läsa och uppdatera alla egenskaper för kunskapsnätverk i Administrationscenter för Microsoft 365 |
> | microsoft.office365.protectionCenter/sensitivityLabels/allProperties/read | Läs alla egenskaper för känslighetsetiketter i säkerhets- och efterlevnadscenter |
> | microsoft.office365.sharePoint/allEntities/allTasks | Skapa och ta bort alla resurser och läs och uppdatera standardegenskaper i SharePoint |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="license-administrator"></a>Licensadministratör

Användare i den här rollen kan lägga till, ta bort och uppdatera licenstilldelningar för användare, grupper (med gruppbaserad licensiering) och hantera användarnas användningsplats. Rollen ger inte möjlighet att köpa eller hantera prenumerationer, skapa eller hantera grupper eller skapa eller hantera användare utanför användningsplatsen. Den här rollen har ingen åtkomst till att visa, skapa eller hantera supportärenden.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/groups/assignLicense | Tilldela produktlicenser till grupper för gruppbaserad licensiering |
> | microsoft.directory/groups/reprocessLicenseAssignment | Ombearbeta licenstilldelningar för gruppbaserad licensiering |
> | microsoft.directory/users/assignLicense | Hantera användarlicenser |
> | microsoft.directory/users/reprocessLicenseAssignment | Ombearbeta licenstilldelningar för användare |
> | microsoft.directory/users/usageLocation/update | Uppdatera användarnas användningsplats |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="message-center-privacy-reader"></a>Meddelandecenter sekretessläsare

Användare i den här rollen kan övervaka alla meddelanden i Meddelandecenter, inklusive datasekretessmeddelanden. Meddelandecenter sekretessläsare får e-postaviseringar, inklusive sådana som rör datasekretess, och de kan avbryta prenumerationen med hjälp Meddelandecenter inställningar. Endast den globala administratören och Meddelandecenter Sekretessläsare kan läsa datasekretessmeddelanden. Dessutom innehåller den här rollen möjligheten att visa grupper, domäner och prenumerationer. Den här rollen har ingen behörighet att visa, skapa eller hantera tjänstbegäranden.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Läsa meddelanden i Meddelandecenter i Administrationscenter för Microsoft 365, exklusive säkerhetsmeddelanden |
> | microsoft.office365.messageCenter/securityMessages/read | Läsa säkerhetsmeddelanden i Meddelandecenter i Administrationscenter för Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="message-center-reader"></a>Meddelandecenter Läsare

Användare i den här rollen kan [](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) övervaka meddelanden och rådgivande hälsouppdateringar i Meddelandecenter för organisationen på konfigurerade tjänster som Exchange, Intune och Microsoft Teams. Meddelandecenter Läsare får veckovisa e-postmeddelanden med inlägg, uppdateringar och kan dela inlägg i meddelandecentret i Microsoft 365. I Azure AD har användare som tilldelats den här rollen endast skrivskyddad åtkomst till Azure AD-tjänster som användare och grupper. Den här rollen har ingen åtkomst till att visa, skapa eller hantera supportärenden.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Läsa meddelanden i Meddelandecenter i Administrationscenter för Microsoft 365, exklusive säkerhetsmeddelanden |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="modern-commerce-user"></a>Modern commerce-användare

Använd inte. Den här rollen tilldelas automatiskt från Commerce och är inte avsedd eller stöds för någon annan användning. Se informationen nedan.

Användarrollen Modern commerce ger vissa användare behörighet att komma Administrationscenter för Microsoft 365 och se de vänstra navigeringsposterna **för Start,** **Fakturering** och **Support.** Innehållet som är tillgängligt i [](../../cost-management-billing/manage/understand-mca-roles.md) dessa områden styrs av handelsspecifika roller som tilldelas användare för att hantera produkter som de har köpt för sig själva eller din organisation. Detta kan omfatta uppgifter som att betala fakturor eller för åtkomst till faktureringskonton och faktureringsprofiler.

Användare med användarrollen Modern Commerce har vanligtvis administratörsbehörighet i andra Microsoft-inköpssystem, men har inte globala administratörs- eller Faktureringsadministratör-roller som används för att få åtkomst till administrationscentret.

**När tilldelas användarrollen Modern handel?**

* **Självbetjäning i Administrationscenter för Microsoft 365** – Självbetjäning ger användarna möjlighet att prova nya produkter genom att köpa eller registrera sig för dem på egen hand. Dessa produkter hanteras i administrationscentret. Användare som köper självbetjäning tilldelas en roll i handelssystemet och användarrollen Modern handel så att de kan hantera sina inköp i administrationscentret. Administratörer kan blockera köp via självbetjäning (för Power BI, Power Apps, Power automate) via [PowerShell](/microsoft-365/commerce/subscriptions/allowselfservicepurchase-powershell). Mer information finns i [Vanliga frågor och svar om självbetjäningsköp](/microsoft-365/commerce/subscriptions/self-service-purchase-faq).
* **Köp från Microsofts** kommersiella marknadsplats – På liknande sätt som när en användare köper en produkt eller tjänst från Microsoft AppSource eller Azure Marketplace tilldelas användarrollen Modern handel om användaren inte har rollen global administratör eller faktureringsadministratör. I vissa fall kan användare blockeras från att göra dessa köp. Mer information finns i [Microsofts kommersiella marknadsplats.](../../marketplace/marketplace-faq-publisher-guide.md#what-could-block-a-customer-from-completing-a-purchase)
* **Förslag från Microsoft** – Ett förslag är ett formellt erbjudande från Microsoft om att din organisation ska köpa Microsofts produkter och tjänster. När den person som godkänner förslaget inte har rollen global administratör eller faktureringsadministratör i Azure AD tilldelas personen både en handelsspecifik roll för att slutföra förslaget och rollen Användare av modern handel för att få åtkomst till administrationscentret. När de använder administrationscentret kan de bara använda funktioner som auktoriserats av deras handelsspecifika roll.
* **Handelsspecifika roller –** Vissa användare tilldelas handelsspecifika roller. Om en användare inte är global administratör eller faktureringsadministratör får han eller hon rollen Som användare av modern handel så att de kan komma åt administrationscentret.

Om rollen Modern commerce-användare inte har tilldelats från en användare förlorar användaren åtkomst till Administrationscenter för Microsoft 365. Om de hanterade några produkter, antingen för sig själva eller för din organisation, kommer de inte att kunna hantera dem. Detta kan omfatta att tilldela licenser, ändra betalningsmetoder, betala fakturor eller andra uppgifter för att hantera prenumerationer.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.commerce.billing/partners/read | Läsa partneregenskap för Microsoft 365 fakturering |
> | microsoft.commerce.volumeLicenseServiceCenter/allEntities/allTasks | Hantera alla aspekter av Volume Licensing Service Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="network-administrator"></a>Nätverksadministratör

Användare i den här rollen kan granska rekommendationer för perimeterarkitekturen i nätverk från Microsoft som baseras på nätverkstelemetri från deras användarplatser. Nätverksprestanda för Microsoft 365 är beroende av noggrann perimeterarkitektur för företagskundnätverk, som vanligtvis är användarplatsspecifik. Med den här rollen kan du redigera identifierade användarplatser och konfigurera nätverksparametrar för dessa platser för att underlätta bättre telemetrimätningar och designrekommendationer

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.office365.network/locations/allProperties/allTasks | Hantera alla aspekter av nätverksplatser |
> | microsoft.office365.network/performance/allProperties/read | Läs alla egenskaper för nätverksprestanda i Administrationscenter för Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="office-apps-administrator"></a>Administratör för Office-appar

Användare i den här rollen kan hantera Microsoft 365-appars molninställningar. Detta omfattar hantering av molnprinciper, hämtning av självbetjäning och möjligheten att visa rapport relaterade till Office-appar. Den här rollen ger dessutom möjlighet att hantera supportärenden och övervaka tjänstens hälsa i huvudadministrationscentret. Användare som har tilldelats den här rollen kan också hantera kommunikation av nya funktioner i Office-appar.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.office365.messageCenter/messages/read | Läsa meddelanden i Meddelandecenter i Administrationscenter för Microsoft 365, exklusive säkerhetsmeddelanden |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.userCommunication/allEntities/allTasks | Läsa och uppdatera synligheten för nya meddelanden |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="partner-tier1-support"></a>Support på partnernivå 1

Använd inte. Den här rollen är inaktuell och kommer att tas bort från Azure AD i framtiden. Den här rollen är avsedd att användas av ett litet antal Microsoft-omförsäljningspartner och är inte avsedd för allmän användning.

> [!IMPORTANT]
> Den här rollen kan återställa lösenord och ogiltigförklara uppdateringstoken endast för icke-administratörer. Den här rollen bör inte användas eftersom den är inaktuell och kommer inte längre att returneras i API:et.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/applications/appRoles/update | Uppdatera egenskapen appRoles för alla typer av program |
> | microsoft.directory/applications/audience/update | Uppdatera målgruppsegenskapen för program |
> | microsoft.directory/applications/authentication/update | Uppdatera autentisering för alla typer av program |
> | microsoft.directory/applications/basic/update | Uppdatera grundläggande egenskaper för program |
> | microsoft.directory/applications/credentials/update | Uppdatera programautentiseringsuppgifter |
> | microsoft.directory/applications/owners/update | Uppdatera programägare |
> | microsoft.directory/applications/permissions/update | Uppdatera exponerade behörigheter och nödvändiga behörigheter för alla typer av program |
> | microsoft.directory/applications/policies/update | Uppdatera principer för program |
> | microsoft.directory/contacts/create | Skapa kontakter |
> | microsoft.directory/contacts/delete | Ta bort kontakter |
> | microsoft.directory/contacts/basic/update | Uppdatera grundläggande egenskaper för kontakter |
> | microsoft.directory/groups/create | Skapa grupper, exklusive roll-tilldelningsbara grupper |
> | microsoft.directory/groups/delete | Ta bort grupper, exklusive roll tilldelningsbara grupper |
> | microsoft.directory/groups/restore | Återställa borttagna grupper |
> | microsoft.directory/groups/members/update | Uppdatera medlemmar i grupper, exklusive roll-tilldelningsbara grupper |
> | microsoft.directory/groups/owners/update | Uppdatera ägare av grupper, exklusive roll-tilldelningsbara grupper |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Skapa och ta bort OAuth 2.0-behörighetsstipendier och läsa och uppdatera alla egenskaper |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Uppdatera rolltilldelningar för tjänstens huvudnamn |
> | microsoft.directory/users/assignLicense | Hantera användarlicenser |
> | microsoft.directory/users/create | Lägga till användare |
> | microsoft.directory/users/delete | Ta bort användare |
> | microsoft.directory/users/disable | Inaktivera användare |
> | microsoft.directory/users/enable | Aktivera användare |
> | microsoft.directory/users/invalidateAllRefreshTokens | Framt tvinga ut inloggning genom att ogiltigförklara token för användaruppdatering |
> | microsoft.directory/users/restore | Återställa borttagna användare |
> | microsoft.directory/users/basic/update | Uppdatera grundläggande egenskaper för användare |
> | microsoft.directory/users/manager/update | Uppdateringshanterare för användare |
> | microsoft.directory/users/password/update | Återställa lösenord för alla användare |
> | microsoft.directory/users/userPrincipalName/update | Uppdatera användarens huvudnamn för användare |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="partner-tier2-support"></a>Support för partnernivå 2

Använd inte. Den här rollen är inaktuell och kommer att tas bort från Azure AD i framtiden. Den här rollen är avsedd att användas av ett litet antal Microsoft-omförsäljningspartner och är inte avsedd för allmän användning.

> [!IMPORTANT]
> Den här rollen kan återställa lösenord och ogiltigförklara uppdateringstoken för alla icke-administratörer och administratörer (inklusive globala administratörer). Den här rollen bör inte användas eftersom den är inaktuell och kommer inte längre att returneras i API:et.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/applications/appRoles/update | Uppdatera egenskapen appRoles för alla typer av program |
> | microsoft.directory/applications/audience/update | Uppdatera målgruppsegenskapen för program |
> | microsoft.directory/applications/authentication/update | Uppdatera autentisering för alla typer av program |
> | microsoft.directory/applications/basic/update | Uppdatera grundläggande egenskaper för program |
> | microsoft.directory/applications/credentials/update | Uppdatera programautentiseringsuppgifter |
> | microsoft.directory/applications/owners/update | Uppdatera programägare |
> | microsoft.directory/applications/permissions/update | Uppdatera exponerade behörigheter och nödvändiga behörigheter för alla typer av program |
> | microsoft.directory/applications/policies/update | Uppdatera principer för program |
> | microsoft.directory/contacts/create | Skapa kontakter |
> | microsoft.directory/contacts/delete | Ta bort kontakter |
> | microsoft.directory/contacts/basic/update | Uppdatera grundläggande egenskaper för kontakter |
> | microsoft.directory/domains/allProperties/allTasks | Skapa och ta bort domäner och läs och uppdatera alla egenskaper |
> | microsoft.directory/groups/create | Skapa grupper, exklusive roll-tilldelningsbara grupper |
> | microsoft.directory/groups/delete | Ta bort grupper, exklusive roll-tilldelningsbara grupper |
> | microsoft.directory/groups/restore | Återställa borttagna grupper |
> | microsoft.directory/groups/members/update | Uppdatera medlemmar i grupper, exklusive roll-tilldelningsbara grupper |
> | microsoft.directory/groups/owners/update | Uppdatera ägare av grupper, exklusive roll-tilldelningsbara grupper |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Skapa och ta bort OAuth 2.0-behörighetsstipendier och läsa och uppdatera alla egenskaper |
> | microsoft.directory/organization/basic/update | Uppdatera grundläggande egenskaper för organisationen |
> | microsoft.directory/roleAssignments/allProperties/allTasks | Skapa och ta bort rolltilldelningar och läs och uppdatera alla egenskaper för rolltilldelning |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | Skapa och ta bort rolldefinitioner och läs och uppdatera alla egenskaper |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | Skapa och ta bort scopedRoleMemberships och läs och uppdatera alla egenskaper |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Uppdatera rolltilldelningar för tjänstens huvudnamn |
> | microsoft.directory/subscribedSkus/standard/read | Läsa grundläggande egenskaper för prenumerationer |
> | microsoft.directory/users/assignLicense | Hantera användarlicenser |
> | microsoft.directory/users/create | Lägga till användare |
> | microsoft.directory/users/delete | Ta bort användare |
> | microsoft.directory/users/disable | Inaktivera användare |
> | microsoft.directory/users/enable | Aktivera användare |
> | microsoft.directory/users/invalidateAllRefreshTokens | Framt tvinga ut inloggning genom att ogiltigförklara token för användaruppdatering |
> | microsoft.directory/users/restore | Återställa borttagna användare |
> | microsoft.directory/users/basic/update | Uppdatera grundläggande egenskaper för användare |
> | microsoft.directory/users/manager/update | Uppdateringshanterare för användare |
> | microsoft.directory/users/password/update | Återställa lösenord för alla användare |
> | microsoft.directory/users/userPrincipalName/update | Uppdatera användarens huvudnamn för användare |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="password-administrator"></a>Lösenordsadministratör

Användare med den här rollen har begränsad möjlighet att hantera lösenord. Den här rollen ger inte möjlighet att hantera tjänstbegäranden eller övervaka tjänstens hälsa. Om en lösenordsadministratör kan återställa en användares lösenord beror på vilken roll användaren har tilldelats. En lista över de roller som en lösenordsadministratör kan återställa lösenord för finns i Behörigheter [för lösenordsåterställning.](#password-reset-permissions)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/users/password/update | Återställa lösenord för alla användare |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="power-bi-administrator"></a>Power BI administratör

Användare med den här rollen har globala behörigheter inom Microsoft Power BI när tjänsten finns, samt möjlighet att hantera supportärenden och övervaka tjänstens hälsa. Mer information finns [i Förstå Power BI administratörsrollen](/power-bi/service-admin-role).

> [!NOTE]
> I Microsoft Graph API och Azure AD PowerShell identifieras den här rollen som "Power BI tjänstadministratör". Det är Power BI administratör i [Azure Portal](https://portal.azure.com).

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |
> | microsoft.powerApps.powerBI/allEntities/allTasks | Hantera alla aspekter av Power BI |

## <a name="power-platform-administrator"></a>Power Platform administratör

Användare i den här rollen kan skapa och hantera alla aspekter av miljöer, PowerApps, flöden, principer för dataförlustskydd. Dessutom kan användare med den här rollen hantera supportärenden och övervaka tjänstens hälsa.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.dynamics365/allEntities/allTasks | Hantera alla aspekter av Dynamics 365 |
> | microsoft.flow/allEntities/allTasks | Hantera alla aspekter av Microsoft Power Automate |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |
> | microsoft.powerApps/allEntities/allTasks | Hantera alla aspekter av Power Apps |

## <a name="printer-administrator"></a>Skrivaradministratör

Användare i den här rollen kan registrera skrivare och hantera alla aspekter av alla skrivarkonfigurationer i Microsoft Universell utskrift-lösningen, inklusive inställningarna för Universell utskrift Connector. De kan samtycka till alla delegerade begäranden om utskriftsbehörighet. Skrivaradministratörer har också åtkomst till utskriftsrapporter.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.azure.print/allEntities/allProperties/allTasks | Skapa och ta bort skrivare och anslutningsappar samt läsa och uppdatera alla egenskaper i Microsoft Print |

## <a name="printer-technician"></a>Skrivartekniker

Användare med den här rollen kan registrera skrivare och hantera skrivarstatus i Microsoft Universell utskrift lösningen. De kan också läsa all information om anslutningsappen. Nyckeluppgift som en skrivartekniker inte kan göra är att ange användarbehörigheter på skrivare och dela skrivare.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.azure.print/connectors/allProperties/read | Läsa alla egenskaper för anslutningsappar i Microsoft Print |
> | microsoft.azure.print/printers/allProperties/read | Läsa alla egenskaper för skrivare i Microsoft Print |
> | microsoft.azure.print/printers/register | Registrera skrivare i Microsoft Print |
> | microsoft.azure.print/printers/unregister | Avregistrera skrivare i Microsoft Print |
> | microsoft.azure.print/printers/basic/update | Uppdatera grundläggande egenskaper för skrivare i Microsoft Print |

## <a name="privileged-authentication-administrator"></a>Privilegierad autentiseringsadministratör

Användare med den här rollen kan ange eller återställa valfri autentiseringsmetod (inklusive lösenord) för alla användare, inklusive globala administratörer. Privilegierade autentiseringsadministratörer kan tvinga användare att omregistrera sig mot befintliga autentiseringsuppgifter som inte är lösenord (till exempel MFA eller FIDO) och återkalla "kom ihåg MFA på enheten" och fråga efter MFA vid nästa inloggning för alla användare.

Den [Autentiseringsadministratör](#authentication-administrator) rollen har behörighet att tvinga omregistrering och multifaktorautentisering för standardanvändare och användare med vissa administratörsroller.

[Administratörsrollen autentiseringsprincip](#authentication-policy-administrator) har behörighet att ange klientens autentiseringsmetodprincip som avgör vilka metoder varje användare kan registrera och använda.

| Roll | Hantera användarens autentiseringsmetoder | Hantera MFA per användare | Hantera MFA-inställningar | Hantera auth-metodprincip | Hantera lösenordsskyddsprincip |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Autentiseringsadministratör | Ja för vissa användare (se ovan) | Ja för vissa användare (se ovan) | Inga | Inga | Inga |
| Administratör för privilegierad autentisering| Ja för alla användare | Ja för alla användare | Inga | Inga | Inga |
| Administratör för autentiseringsprincip | Inga | Inga | Ja | Ja | Ja |

> [!IMPORTANT]
> Användare med den här rollen kan ändra autentiseringsuppgifter för personer som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i och utanför Azure Active Directory. Att ändra autentiseringsuppgifterna för en användare kan innebära möjligheten att anta användarens identitet och behörigheter. Exempel:
>
>* Programregistrering och ägare av företagsprogram, som kan hantera autentiseringsuppgifter för appar som de äger. Dessa appar kan ha privilegierad behörighet i Azure AD och någon annanstans som inte har beviljats till autentiseringsadministratörer. Via den här sökvägen kan en autentiseringsadministratör anta identiteten för en programägare och sedan ytterligare anta identiteten för ett privilegierat program genom att uppdatera autentiseringsuppgifterna för programmet.
>* Azure-prenumerationsägare, som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i Azure.
>* Säkerhetsgrupp och Microsoft 365 gruppägare som kan hantera gruppmedlemskap. Dessa grupper kan bevilja åtkomst till känslig eller privat information eller kritisk konfiguration i Azure AD och någon annanstans.
>* Administratörer i andra tjänster utanför Azure AD som Exchange Online, Säkerhets- och efterlevnadscenter för Office och system för personalavdelningen.
>* Icke-administratörer som chefer, juridiska rådgivare och personalpersonal som kan ha åtkomst till känslig eller privat information.


> [!IMPORTANT]
> Den här rollen kan för närvarande inte hantera MFA per användare i den äldre MFA-hanteringsportalen. Samma funktioner kan utföras med hjälp av Azure AD [Powershell-modulen Set-MsolUser.](/powershell/module/msonline/set-msoluser)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/users/invalidateAllRefreshTokens | Framt tvinga ut inloggning genom att ogiltigförklara token för användaruppdatering |
> | microsoft.directory/users/strongAuthentication/update | Uppdatera den starka autentiseringsegenskapen för användare |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="privileged-role-administrator"></a>Privilegierad rolladministratör

Användare med den här rollen kan hantera rolltilldelningar i Azure Active Directory, samt inom Azure AD Privileged Identity Management. De kan skapa och hantera grupper som kan tilldelas till Azure AD-roller. Dessutom tillåter den här rollen hantering av alla aspekter av Privileged Identity Management och administrativa enheter.

> [!IMPORTANT]
> Den här rollen ger dig möjlighet att hantera tilldelningar för alla Azure AD-roller, inklusive rollen Global administratör. Den här rollen omfattar inte andra privilegierade funktioner i Azure AD som att skapa eller uppdatera användare. Användare som har tilldelats den här rollen kan dock ge sig själva eller andra ytterligare privilegier genom att tilldela ytterligare roller.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/administrativeUnits/allProperties/allTasks | Skapa och hantera administrativa enheter (inklusive medlemmar) |
> | microsoft.directory/appRoleAssignments/allProperties/allTasks | Skapa och ta bort appRoleAssignments och läs och uppdatera alla egenskaper |
> | microsoft.directory/authorizationPolicy/allProperties/allTasks | Hantera alla aspekter av auktoriseringsprinciper |
> | microsoft.directory/directoryRoles/allProperties/allTasks | Skapa och ta bort katalogroller och läs och uppdatera alla egenskaper |
> | microsoft.directory/groupsAssignableToRoles/create | Skapa rolltilldelningsbara grupper |
> | microsoft.directory/groupsAssignableToRoles/delete | Ta bort roll assignerbara grupper |
> | microsoft.directory/groupsAssignableToRoles/restore | Återställa roll assignerbara grupper |
> | microsoft.directory/groupsAssignableToRoles/allProperties/update | Uppdatera roll assignable-grupper |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Skapa och ta bort OAuth 2.0-behörighet beviljar och läsa och uppdatera alla egenskaper |
> | microsoft.directory/privilegedIdentityManagement/allProperties/allTasks | Skapa och ta bort alla resurser och läs och uppdatera standardegenskaper i Privileged Identity Management |
> | microsoft.directory/roleAssignments/allProperties/allTasks | Skapa och ta bort rolltilldelningar och läs och uppdatera alla egenskaper för rolltilldelning |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | Skapa och ta bort rolldefinitioner och läs och uppdatera alla egenskaper |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | Skapa och ta bort scopedRoleMemberships och läs och uppdatera alla egenskaper |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Uppdatera rolltilldelningar för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/permissions/update | Uppdatera behörigheter för tjänstens huvudnamn |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-company-admin | Bevilja medgivande för alla behörigheter till alla program |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="reports-reader"></a>Rapportläsare

Användare med den här rollen kan visa användningsrapporter och instrumentpanelen för rapporter i Administrationscenter för Microsoft 365 och kontextpaketet för implementering i Power BI. Dessutom ger rollen åtkomst till inloggningsrapporter och -aktivitet i Azure AD och data som returneras av api:et för Microsoft Graph rapportering. En användare som har tilldelats rollen Rapportläsare kan bara komma åt relevanta användnings- och implementeringsmått. De har inte administratörsbehörighet för att konfigurera inställningar eller komma åt produktspecifika administrationscenter som Exchange. Den här rollen har ingen åtkomst till att visa, skapa eller hantera supportärenden.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Läsa alla egenskaper i granskningsloggar, inklusive privilegierade egenskaper |
> | microsoft.directory/provisioningLogs/allProperties/read | Läsa alla egenskaper för etableringsloggar |
> | microsoft.directory/signInReports/allProperties/read | Läsa alla egenskaper i inloggningsrapporter, inklusive privilegierade egenskaper |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.office365.network/performance/allProperties/read | Läsa alla egenskaper för nätverksprestanda i Administrationscenter för Microsoft 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Läsa användningsrapporter för Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="search-administrator"></a>Sökadministratör

Användare i den här rollen har fullständig åtkomst till alla Microsoft Search-hanteringsfunktioner i Administrationscenter för Microsoft 365. Dessutom kan dessa användare visa meddelandecentret, övervaka tjänstens hälsa och skapa tjänstbegäranden.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Läsa meddelanden i Meddelandecenter i Administrationscenter för Microsoft 365, exklusive säkerhetsmeddelanden |
> | microsoft.office365.search/content/manage | Skapa och ta bort innehåll och läs och uppdatera alla egenskaper i Microsoft Search |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="search-editor"></a>Sökredigeraren

Användare i den här rollen kan skapa, hantera och ta bort innehåll för Microsoft Search i Administrationscenter för Microsoft 365, inklusive bokmärken, Q&Som och platser.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Läsa meddelanden i Meddelandecenter i Administrationscenter för Microsoft 365, exklusive säkerhetsmeddelanden |
> | microsoft.office365.search/content/manage | Skapa och ta bort innehåll och läs och uppdatera alla egenskaper i Microsoft Search |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="security-administrator"></a>Säkerhetsadministratör

Användare med den här rollen har behörighet att hantera säkerhetsrelaterade funktioner i Microsoft 365 Säkerhetscenter, Azure Active Directory Identity Protection, Azure Active Directory Authentication, Azure Information Protection och Office 365 Security & Compliance Center. Mer information om Office 365-behörigheter finns i [Behörigheter i Security & Compliance Center.](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)

I | Kan göra
--- | ---
[Microsoft 365 Säkerhetscenter](https://protection.office.com) | Övervaka säkerhetsrelaterade principer i Microsoft 365 tjänster<br>Hantera säkerhetshot och aviseringar<br>Visa rapporter
Identity Protection Center | Alla behörigheter för säkerhetsläsarrollen<br>Dessutom kan du utföra alla Identity Protection Center-åtgärder förutom att återställa lösenord
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Alla behörigheter för säkerhetsläsarrollen<br>**Det går** inte att hantera rolltilldelningar eller inställningar i Azure AD
[Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Hantera säkerhetsprinciper<br>Visa, undersöka och svara på säkerhetshot<br>Visa rapporter
Azure Advanced Threat Protection | Övervaka och svara på misstänkt säkerhetsaktivitet
Windows Defender ATP och EDR | Tilldela roller<br>Hantera datorgrupper<br>Konfigurera identifiering av slutpunktshot och automatiserad reparation<br>Visa, undersöka och svara på aviseringar
[Intune](/intune/role-based-access-control) | Visar information om användare, enhet, registrering, konfiguration och program<br>Det går inte att göra ändringar i Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Lägga till administratörer, lägga till principer och inställningar, ladda upp loggar och utföra styrningsåtgärder
[Azure Security Center](../../key-vault/managed-hsm/built-in-roles.md) | Kan visa säkerhetsprinciper, visa säkerhets tillstånd, redigera säkerhetsprinciper, visa aviseringar och rekommendationer, stänga aviseringar och rekommendationer
[Microsoft 365 tjänstens hälsa](/office365/enterprise/view-service-health) | Visa hälsotillståndet för Microsoft 365 tjänster
[Smart utlåsning](../authentication/howto-password-smart-lockout.md) | Definiera tröskelvärdet och varaktigheten för utelåsningar när misslyckade inloggningshändelser inträffar.
[Lösenordsskydd](../authentication/concept-password-ban-bad.md) | Konfigurera anpassad lista över förbjudna lösenord eller lokalt lösenordsskydd.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/applications/policies/update | Uppdatera principer för program |
> | microsoft.directory/auditLogs/allProperties/read | Läsa alla egenskaper i granskningsloggar, inklusive privilegierade egenskaper |
> | microsoft.directory/bitlockerKeys/key/read | Läsa bitlocker-metadata och nyckel på enheter |
> | microsoft.directory/entitlementManagement/allProperties/read | Läsa alla egenskaper i Azure AD-berättigandehantering |
> | microsoft.directory/identityProtection/allProperties/read | Läsa alla resurser i Azure AD Identity Protection |
> | microsoft.directory/identityProtection/allProperties/update | Uppdatera alla resurser i Azure AD Identity Protection |
> | microsoft.directory/policies/create | Skapa principer i Azure AD |
> | microsoft.directory/policies/delete | Ta bort principer i Azure AD |
> | microsoft.directory/policies/basic/update | Uppdatera grundläggande egenskaper för principer |
> | microsoft.directory/policies/owners/update | Uppdatera ägare av principer |
> | microsoft.directory/policies/tenantDefault/update | Uppdatera standardorganisationsprinciper |
> | microsoft.directory/conditionalAccessPolicies/create | Skapa principer för villkorsstyrd åtkomst |
> | microsoft.directory/conditionalAccessPolicies/delete | Ta bort principer för villkorlig åtkomst |
> | microsoft.directory/conditionalAccessPolicies/standard/read | Läsa villkorlig åtkomst för principer |
> | microsoft.directory/conditionalAccessPolicies/owners/read | Läs om ägarna av principer för villkorlig åtkomst |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | Läs egenskapen "tillämpad på" för principer för villkorsstyrd åtkomst |
> | microsoft.directory/conditionalAccessPolicies/basic/update | Uppdatera grundläggande egenskaper för principer för villkorlig åtkomst |
> | microsoft.directory/conditionalAccessPolicies/owners/update | Uppdatera ägare för principer för villkorlig åtkomst |
> | microsoft.directory/conditionalAccessPolicies/tenantDefault/update | Uppdatera standardklientorganisationen för principer för villkorlig åtkomst |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Läsa alla resurser i Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Läsa alla egenskaper för etableringsloggar |
> | microsoft.directory/servicePrincipals/policies/update | Uppdatera principer för tjänstens huvudnamn |
> | microsoft.directory/signInReports/allProperties/read | Läsa alla egenskaper i inloggningsrapporter, inklusive privilegierade egenskaper |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.office365.protectionCenter/allEntities/standard/read | Läsa standardegenskaper för alla resurser i säkerhets- och efterlevnadscenter |
> | microsoft.office365.protectionCenter/allEntities/basic/update | Uppdatera grundläggande egenskaper för alla resurser i säkerhets- och efterlevnadscenter |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | Skapa och hantera attacknyttolaster i attacksimulatorn |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Läs rapporter om svar från attacksimulering och tillhörande utbildning |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/allTasks | Skapa och hantera mallar för attacksimulering i attacksimulatorn |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="security-operator"></a>Säkerhetsoperatör

Användare med den här rollen kan hantera aviseringar och ha global läsbehörighet för säkerhetsrelaterade funktioner, inklusive all information i Microsoft 365 Säkerhetscenter, Azure Active Directory, Identity Protection, Privileged Identity Management och Office 365 Security & Compliance Center. Mer information om Office 365-behörigheter finns i [Behörigheter i Security & Compliance Center.](/office365/securitycompliance/permissions-in-the-security-and-compliance-center)

I | Kan göra
--- | ---
[Microsoft 365 Säkerhetscenter](https://protection.office.com) | Alla behörigheter för rollen Säkerhetsläsare<br>Visa, undersöka och svara på aviseringar om säkerhetshot
Azure AD Identity Protection | Alla behörigheter för rollen Säkerhetsläsare<br>Dessutom kan du utföra alla Identity Protection Center-åtgärder förutom att återställa lösenord och konfigurera e-postaviseringar.
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Alla behörigheter för rollen Säkerhetsläsare
[Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Alla behörigheter för säkerhetsläsarrollen<br>Visa, undersöka och svara på säkerhetsaviseringar
Windows Defender ATP och EDR | Alla behörigheter för säkerhetsläsarrollen<br>Visa, undersöka och svara på säkerhetsaviseringar
[Intune](/intune/role-based-access-control) | Alla behörigheter för säkerhetsläsarrollen
[Cloud App Security](/cloud-app-security/manage-admins) | Alla behörigheter för säkerhetsläsarrollen
[Microsoft 365 tjänstens hälsa](/office365/enterprise/view-service-health) | Visa hälsotillståndet för Microsoft 365 tjänster

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Läsa alla egenskaper i granskningsloggar, inklusive privilegierade egenskaper |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | Skapa och ta bort alla resurser och läs och uppdatera standardegenskaper i Microsoft Cloud App Security |
> | microsoft.directory/identityProtection/allProperties/allTasks | Skapa och ta bort alla resurser och läs och uppdatera standardegenskaper i Azure AD Identity Protection |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Läsa alla resurser i Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Läsa alla egenskaper för etableringsloggar |
> | microsoft.directory/signInReports/allProperties/read | Läs alla egenskaper i inloggningsrapporter, inklusive privilegierade egenskaper |
> | microsoft.azure.advancedThreatProtection/allEntities/allTasks | Hantera alla aspekter av Azure Advanced Threat Protection |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.intune/allEntities/read | Läsa alla resurser i Microsoft Intune |
> | microsoft.office365.securityComplianceCenter/allEntities/allTasks | Skapa och ta bort alla resurser och läs och uppdatera standardegenskaper i Microsoft 365 Security and Compliance Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.windows.defenderAdvancedThreatProtection/allEntities/allTasks | Hantera alla aspekter av Microsoft Defender för slutpunkt |

## <a name="security-reader"></a>Säkerhetsläsare

Användare med den här rollen har global skrivskyddade åtkomst till säkerhetsrelaterade funktioner, inklusive all information i Microsoft 365 Säkerhetscenter, Azure Active Directory, Identity Protection, Privileged Identity Management, samt möjligheten att läsa Azure Active Directory-inloggningsrapporter och granskningsloggar samt i Office 365 Security & Compliance Center. Mer information om Office 365-behörigheter finns i [Behörigheter i Security & Compliance Center.](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)

I | Kan göra
--- | ---
[Microsoft 365 Säkerhetscenter](https://protection.office.com) | Visa säkerhetsrelaterade principer för Microsoft 365 tjänster<br>Visa säkerhetshot och aviseringar<br>Visa rapporter
Identity Protection Center | Läsa alla säkerhetsrapporter och inställningsinformation för säkerhetsfunktioner<br><ul><li>Skydd mot skräppost<li>Kryptering<li>Skydd mot dataförlust<li>Program mot skadlig kod<li>Advanced Threat Protection<li>Nätfiskeskydd<li>Regler för e-postflöde
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Har skrivskyddad åtkomst till all information som visas i Azure AD Privileged Identity Management: Principer och rapporter för Azure AD-rolltilldelningar och säkerhetsgranskningar.<br>**Det** går inte att Azure AD Privileged Identity Management eller göra några ändringar i den. I Privileged Identity Management-portalen eller via PowerShell kan någon i den här rollen aktivera ytterligare roller (till exempel Global administratör eller Privilegierad rolladministratör) om användaren är berättigad till dem.
[Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Visa säkerhetsprinciper<br>Visa och undersöka säkerhetshot<br>Visa rapporter
Windows Defender ATP och EDR | Visa och undersöka aviseringar. När du aktiverar rollbaserad åtkomstkontroll i Windows Defender ATP förlorar användare med skrivskyddade behörigheter, till exempel Azure AD Säkerhetsläsare-rollen, åtkomst tills de har tilldelats en Windows Defender ATP-roll.
[Intune](/intune/role-based-access-control) | Visar information om användare, enhet, registrering, konfiguration och programmet. Kan inte göra ändringar i Intune.
[Cloud App Security](/cloud-app-security/manage-admins) | Har skrivskyddade behörigheter och kan hantera aviseringar
[Azure Security Center](../../key-vault/managed-hsm/built-in-roles.md) | Kan visa rekommendationer och aviseringar, visa säkerhetsprinciper, visa säkerhets tillstånd, men kan inte göra ändringar
[Microsoft 365 tjänstens hälsa](/office365/enterprise/view-service-health) | Visa hälsotillståndet för Microsoft 365 tjänster

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Läsa alla egenskaper i granskningsloggar, inklusive privilegierade egenskaper |
> | microsoft.directory/bitlockerKeys/key/read | Läsa bitlocker-metadata och nyckel på enheter |
> | microsoft.directory/entitlementManagement/allProperties/read | Läsa alla egenskaper i Azure AD-berättigandehantering |
> | microsoft.directory/identityProtection/allProperties/read | Läsa alla resurser i Azure AD Identity Protection |
> | microsoft.directory/policies/standard/read | Läsa grundläggande egenskaper för principer |
> | microsoft.directory/policies/owners/read | Läsa ägare av principer |
> | microsoft.directory/policies/policyAppliedTo/read | Egenskapen Read policies.policyAppliedTo |
> | microsoft.directory/conditionalAccessPolicies/standard/read | Läsa villkorlig åtkomst för principer |
> | microsoft.directory/conditionalAccessPolicies/owners/read | Läs om ägarna av principer för villkorlig åtkomst |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | Läs egenskapen "tillämpad på" för principer för villkorsstyrd åtkomst |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Läsa alla resurser i Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Läsa alla egenskaper för etableringsloggar |
> | microsoft.directory/signInReports/allProperties/read | Läsa alla egenskaper i inloggningsrapporter, inklusive privilegierade egenskaper |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.office365.protectionCenter/allEntities/standard/read | Läsa standardegenskaper för alla resurser i säkerhets- och efterlevnadscenter |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/read | Läsa alla egenskaper för angreppsnyttolaster i attacksimulatorn |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Läsa rapporter om svar från attacksimulering och tillhörande utbildning |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/read | Läs alla egenskaper för attacksimuleringsmallar i attacksimulatorn |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="service-support-administrator"></a>Tjänstsupportadministratör

Användare med den här rollen kan öppna supportbegäranden med Microsoft för Azure och Microsoft 365-tjänster och visar instrumentpanelen för tjänsten och meddelandecentret [i Azure Portal](https://portal.azure.com) [och Administrationscenter för Microsoft 365](https://admin.microsoft.com). Mer information finns i [Om administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Tidigare kallades den här rollen "Tjänstadministratör" [i Azure Portal](https://portal.azure.com) och [Administrationscenter för Microsoft 365](https://admin.microsoft.com). Vi har bytt namn till "Tjänstsupportadministratör" så att det överensstämmer med det exsiterande namnet i Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.office365.network/performance/allProperties/read | Läs alla egenskaper för nätverksprestanda i Administrationscenter för Microsoft 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="sharepoint-administrator"></a>SharePoint-administratör

Användare med den här rollen har globala behörigheter i Microsoft SharePoint Online när tjänsten finns, samt möjligheten att skapa och hantera alla Microsoft 365-grupper, hantera supportärenden och övervaka tjänstens hälsa. Mer information finns i [Om administratörsroller.](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)

> [!NOTE]
> I Microsoft Graph API och Azure AD PowerShell identifieras den här rollen som "SharePoint-tjänstadministratör". Det är "SharePoint-administratör" i [Azure Portal](https://portal.azure.com).

> [!NOTE]
> Den här rollen ger även begränsade behörigheter till Microsoft Graph API för Microsoft Intune, vilket möjliggör hantering och konfiguration av principer som är relaterade till SharePoint- och OneDrive-resurser.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/groups.unified/create | Skapa Microsoft 365 grupper med undantag för roll-tilldelningsbara grupper |
> | microsoft.directory/groups.unified/delete | Ta Microsoft 365-grupper med undantag för roll-tilldelningsbara grupper |
> | microsoft.directory/groups.unified/restore | Återställa Microsoft 365 grupper |
> | microsoft.directory/groups.unified/basic/update | Uppdatera grundläggande egenskaper för Microsoft 365-grupper med undantag för roll-tilldelningsbara grupper |
> | microsoft.directory/groups.unified/members/update | Uppdatera medlemmar Microsoft 365-grupper med undantag för roll-tilldelningsbara grupper |
> | microsoft.directory/groups.unified/owners/update | Uppdatera ägare Microsoft 365-grupper med undantag för roll-tilldelningsbara grupper |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.office365.network/performance/allProperties/read | Läsa alla egenskaper för nätverksprestanda i Administrationscenter för Microsoft 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.sharePoint/allEntities/allTasks | Skapa och ta bort alla resurser och läs och uppdatera standardegenskaper i SharePoint |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Läs användningsrapporter för Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="skype-for-business-administrator"></a>Skype för företag-administratör

Användare med den här rollen har globala behörigheter i Microsoft Skype för företag när tjänsten finns, samt hantera Skype-specifika användarattribut i Azure Active Directory. Dessutom ger den här rollen möjlighet att hantera supportärenden och övervaka tjänstens hälsotillstånd samt få åtkomst till administrationscentret för Teams och Skype för företag. Kontot måste också vara licensierat för Teams eller så kan det inte köra Teams PowerShell-cmdlets. Mer information finns [i Om administratörsrollen för Skype](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) för företag och Licensieringsinformation för Teams i Skype för företag och Microsoft [Teams-tilläggslicensiering](/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

> [!NOTE]
> I Microsoft Graph API och Azure AD PowerShell identifieras den här rollen som "Lync-tjänstadministratör". Det är "Skype för företag-administratör" i [Azure Portal](https://portal.azure.com/).

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Hantera alla aspekter av Skype för företag – Online |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Läsa användningsrapporter för Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="teams-administrator"></a>Teams-administratör

Användare i den här rollen kan hantera alla aspekter av Microsoft Teams-arbetsbelastningen via Microsoft Teams & administrationscentret för Skype för företag och respektive PowerShell-moduler. Detta omfattar bland annat alla hanteringsverktyg som rör telefoni, meddelanden, möten och själva teamen. Den här rollen ger dessutom möjlighet att skapa och hantera alla Microsoft 365 grupper, hantera supportärenden och övervaka tjänstens hälsa.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/groups/hiddenMembers/read | Läsa dolda medlemmar i en grupp |
> | microsoft.directory/groups.unified/create | Skapa Microsoft 365 grupper med undantag för roll-tilldelningsbara grupper |
> | microsoft.directory/groups.unified/delete | Ta Microsoft 365 grupper med undantag för roll-tilldelningsbara grupper |
> | microsoft.directory/groups.unified/restore | Återställa Microsoft 365 grupper |
> | microsoft.directory/groups.unified/basic/update | Uppdatera grundläggande egenskaper för Microsoft 365 grupper med undantag för roll-tilldelningsbara grupper |
> | microsoft.directory/groups.unified/members/update | Uppdatera medlemmar Microsoft 365 grupper med undantag för roll-tilldelningsbara grupper |
> | microsoft.directory/groups.unified/owners/update | Uppdatera ägare Microsoft 365 grupper med undantag för roll-tilldelningsbara grupper |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Bevilja tjänstens huvudnamn direkt åtkomst till en grupps data |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.office365.network/performance/allProperties/read | Läs alla egenskaper för nätverksprestanda i Administrationscenter för Microsoft 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Hantera alla aspekter av Skype för företag – Online |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Läsa användningsrapporter för Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |
> | microsoft.teams/allEntities/allProperties/allTasks | Hantera alla resurser i Teams |

## <a name="teams-communications-administrator"></a>Teams-kommunikationsadministratör

Användare i den här rollen kan hantera aspekter av Microsoft Teams-arbetsbelastningen som rör & telefoni. Detta omfattar hanteringsverktygen för tilldelning av telefonnummer, röst- och mötesprinciper samt fullständig åtkomst till verktygsuppsättningen för samtalsanalys.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Hantera alla aspekter av Skype för företag – Online |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Läs användningsrapporter för Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |
> | microsoft.teams/callQuality/allProperties/read | Läsa alla data i instrumentpanelen för samtalskvalitet (CQD) |
> | microsoft.teams/meetings/allProperties/allTasks | Hantera möten, inklusive mötesprinciper, konfigurationer och konferensbryggor |
> | microsoft.teams/voice/allProperties/allTasks | Hantera röst, inklusive samtalsprinciper och inventering och tilldelning av telefonnummer |

## <a name="teams-communications-support-engineer"></a>Teams Communications Support Engineer

Användare i den här rollen kan felsöka kommunikationsproblem i Microsoft Teams & Skype för företag med hjälp av felsökningsverktygen för användarsamtal i administrationscentret för Microsoft Teams & Skype för företag. Användare i den här rollen kan visa fullständig information om samtalsposten för alla berörda deltagare. Den här rollen har ingen åtkomst till att visa, skapa eller hantera supportärenden.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Hantera alla aspekter av Skype för företag – Online |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |
> | microsoft.teams/callQuality/allProperties/read | Läsa alla data i instrumentpanelen för samtalskvalitet (CQD) |

## <a name="teams-communications-support-specialist"></a>Teams Communications Support Specialist

Användare i den här rollen kan felsöka kommunikationsproblem i Microsoft Teams & Skype för företag med hjälp av felsökningsverktygen för användarsamtal i administrationscentret för Microsoft Teams & Skype för företag. Användare i den här rollen kan bara visa användarinformation i anropet för den specifika användare som de har tittat upp. Den här rollen har ingen åtkomst till att visa, skapa eller hantera supportärenden.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Hantera alla aspekter av Skype för företag – Online |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |
> | microsoft.teams/callQuality/standard/read | Läsa grundläggande data i instrumentpanelen för samtalskvalitet (CQD) |

## <a name="teams-devices-administrator"></a>Administratör för Teams-enheter

Användare med den här rollen kan [hantera Teams-certifierade](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) enheter från Administrationscenter för Teams. Med den här rollen kan du visa alla enheter med en enda översikt, med möjlighet att söka efter och filtrera enheter. Användaren kan kontrollera information om varje enhet, inklusive det inloggade kontot, enhetens modell och modell. Användaren kan ändra inställningarna på enheten och uppdatera programvaruversionerna. Den här rollen ger inte behörighet att kontrollera Teams-aktivitet och anropa enhetens kvalitet.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |
> | microsoft.teams/devices/standard/read | Hantera alla aspekter av Teams-certifierade enheter, inklusive konfigurationsprinciper |

## <a name="usage-summary-reports-reader"></a>Läsare av användningssammanfattningsrapporter

Användare med den här rollen kan komma åt aggregerade data på klientnivå och tillhörande insikter i Microsoft 365 Administrationscenter för användnings- och produktivitetspoäng, men de kan inte komma åt information eller insikter på användarnivå. I Microsoft 365 Administrationscenter för de två rapporterna skiljer vi mellan aggregerade data på klientnivå och användarnivåinformation. Den här rollen ger ett extra skyddslager för enskilda användaridentifierade data, som begärdes av både kunder och juridiska team.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.office365.network/performance/allProperties/read | Läs alla egenskaper för nätverksprestanda i Administrationscenter för Microsoft 365 |
> | microsoft.office365.usageReports/allEntities/standard/read | Läsa aggregerade Office 365-användningsrapporter på klientnivå |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="user-administrator"></a>Användaradministratör

Användare med den här rollen kan skapa användare och hantera alla aspekter av användare med vissa begränsningar (se tabellen) och kan uppdatera förfalloprinciper för lösenord. Dessutom kan användare med den här rollen skapa och hantera alla grupper. Den här rollen omfattar även möjligheten att skapa och hantera användarvyer, hantera supportärenden och övervaka tjänstens hälsa. Användaradministratörer har inte behörighet att hantera vissa användaregenskaper för användare i de flesta administratörsroller. Användare med den här rollen har inte behörighet att hantera MFA. Rollerna som är undantag till den här begränsningen visas i följande tabell.

| Behörighet som användaradministratör | Kommentarer |
| --- | --- |
| Skapa användare och grupper<br/>Skapa och hantera användarvyer<br/>Hantera Office-supportärenden<br/>Uppdatera förfalloprinciper för lösenord |  |
| Hantera licenser<br/>Hantera alla användaregenskaper utom Användarens huvudnamn | Gäller för alla användare, inklusive alla administratörer |
| Ta bort och återställa<br/>Inaktivera och aktivera<br/>Hantera alla användaregenskaper, inklusive användarens huvudnamn<br/>Uppdatera (FIDO)-enhetsnycklar | Gäller för användare som inte är administratörer eller som har någon av följande roller:<ul><li>Supportadministratör</li><li>Användare utan roll</li><li>Användaradministratör</li></ul> |
| Ogiltigförklara uppdateringstoken<br/>Återställa lösenord | En lista över de roller som en användaradministratör kan återställa lösenord för och ogiltigförklara uppdateringstoken finns i Behörigheter [för lösenordsåterställning.](#password-reset-permissions) |

> [!IMPORTANT]
> Användare med den här rollen kan ändra lösenord för personer som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i och utanför Azure Active Directory. Att ändra lösenordet för en användare kan innebära möjligheten att anta att användarens identitet och behörigheter. Exempel:
>
>- Programregistrering och ägare av företagsprogram, som kan hantera autentiseringsuppgifter för appar som de äger. Dessa appar kan ha privilegierad behörighet i Azure AD och någon annanstans inte beviljad till användaradministratörer. Med den här sökvägen kan en användaradministratör anta identiteten för en programägare och sedan ytterligare anta identiteten för ett privilegierat program genom att uppdatera autentiseringsuppgifterna för programmet.
>- Azure-prenumerationsägare, som kan ha åtkomst till känslig eller privat information eller kritisk konfiguration i Azure.
>- Säkerhetsgrupp och Microsoft 365 gruppägare, som kan hantera gruppmedlemskap. Dessa grupper kan bevilja åtkomst till känslig eller privat information eller kritisk konfiguration i Azure AD och någon annanstans.
>- Administratörer i andra tjänster utanför Azure AD som Exchange Online, Säkerhets- och efterlevnadscenter för Office och system för personalavdelningen.
>- Icke-administratörer som chefer, juridiska rådgivare och personalpersonal som kan ha åtkomst till känslig eller privat information.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | microsoft.directory/appRoleAssignments/create | Skapa programrolltilldelningar |
> | microsoft.directory/appRoleAssignments/delete | Ta bort programrolltilldelningar |
> | microsoft.directory/appRoleAssignments/basic/update | Uppdatera grundläggande egenskaper för programrolltilldelningar |
> | microsoft.directory/contacts/create | Skapa kontakter |
> | microsoft.directory/contacts/delete | Ta bort kontakter |
> | microsoft.directory/contacts/basic/update | Uppdatera grundläggande egenskaper för kontakter |
> | microsoft.directory/entitlementManagement/allProperties/allTasks | Skapa och ta bort resurser och läs och uppdatera alla egenskaper i Azure AD-berättigandehantering |
> | microsoft.directory/groups/assignLicense | Tilldela produktlicenser till grupper för gruppbaserad licensiering |
> | microsoft.directory/groups/create | Skapa grupper, exklusive roll-tilldelningsbara grupper |
> | microsoft.directory/groups/delete | Ta bort grupper, exklusive roll tilldelningsbara grupper |
> | microsoft.directory/groups/hiddenMembers/read | Läsa dolda medlemmar i en grupp |
> | microsoft.directory/groups/reprocessLicenseAssignment | Ombearbeta licenstilldelningar för gruppbaserad licensiering |
> | microsoft.directory/groups/restore | Återställa borttagna grupper |
> | microsoft.directory/groups/basic/update | Uppdatera grundläggande egenskaper för grupper, exklusive roll-tilldelningsbara grupper |
> | microsoft.directory/groups/classification/update | Uppdatera klassificeringsegenskapen för grupper, exklusive roll assignable-grupper |
> | microsoft.directory/groups/dynamicMembershipRule/update | Uppdatera dynamisk medlemskapsregel för grupper, exklusive roll-tilldelningsbara grupper |
> | microsoft.directory/groups/groupType/update | Uppdatera egenskapen groupType för en grupp |
> | microsoft.directory/groups/members/update | Uppdatera medlemmar i grupper, exklusive roll-tilldelningsbara grupper |
> | microsoft.directory/groups/onPremWriteBack/update | Uppdatera Azure Active Directory grupper som ska skrivas tillbaka till den lokala platsen med Azure AD Connect |
> | microsoft.directory/groups/owners/update | Uppdatera ägare av grupper, exklusive roll-tilldelningsbara grupper |
> | microsoft.directory/groups/settings/update | Uppdatera inställningar för grupper |
> | microsoft.directory/groups/visibility/update | Uppdatera synlighetsegenskapen för grupper |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Skapa och ta bort OAuth 2.0-behörighet beviljar och läsa och uppdatera alla egenskaper |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Uppdatera rolltilldelningar för tjänstens huvudnamn |
> | microsoft.directory/users/assignLicense | Hantera användarlicenser |
> | microsoft.directory/users/create | Lägga till användare |
> | microsoft.directory/users/delete | Ta bort användare |
> | microsoft.directory/users/disable | Inaktivera användare |
> | microsoft.directory/users/enable | Aktivera användare |
> | microsoft.directory/users/inviteGuest | Bjud in gästanvändare |
> | microsoft.directory/users/invalidateAllRefreshTokens | Framt tvinga ut ut genom att ogiltigförklara token för användaruppdatering |
> | microsoft.directory/users/reprocessLicenseAssignment | Ombearbeta licenstilldelningar för användare |
> | microsoft.directory/users/restore | Återställa borttagna användare |
> | microsoft.directory/users/basic/update | Uppdatera grundläggande egenskaper för användare |
> | microsoft.directory/users/manager/update | Uppdateringshanterare för användare |
> | microsoft.directory/users/password/update | Återställa lösenord för alla användare |
> | microsoft.directory/users/userPrincipalName/update | Uppdatera användarens huvudnamn för användare |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure-supportärenden |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Service Health i Administrationscenter för Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Microsoft 365 tjänstbegäranden |
> | microsoft.office365.webPortal/allEntities/standard/read | Läsa grundläggande egenskaper för alla resurser i Administrationscenter för Microsoft 365 |

## <a name="how-to-understand-role-permissions"></a>Förstå rollbehörigheter

Schemat för behörigheter följer löst REST-formatet för Microsoft Graph:

`<namespace>/<entity>/<propertySet>/<action>`

Exempel:

`microsoft.directory/applications/credentials/update`

| Behörighetselement | Description |
| --- | --- |
| namnområde | Produkt eller tjänst som exponerar uppgiften och förbereds med `microsoft` . Till exempel använder alla uppgifter i Azure AD `microsoft.directory` namnområdet. |
| Enhet | Logisk funktion eller komponent som exponeras av tjänsten i Microsoft Graph. Azure AD exponerar till exempel användare och grupper, OneNote visar anteckningar och Exchange exponerar postlådor och kalendrar. Det finns ett särskilt nyckelord `allEntities` för att ange alla entiteter i ett namnområde. Detta används ofta i roller som beviljar åtkomst till en hel produkt. |
| propertySet | Specifika egenskaper eller aspekter av entiteten som åtkomst beviljas för. Till exempel ger `microsoft.directory/applications/authentication/read` möjlighet att läsa svars-URL, utloggnings-URL och implicit flödesegenskap för programobjektet i Azure AD.<ul><li>`allProperties` anger alla egenskaper för entiteten, inklusive privilegierade egenskaper.</li><li>`standard` anger gemensamma egenskaper, men exkluderar privilegierade egenskaper som är relaterade till `read` åtgärden. Omfattar till exempel möjligheten att läsa standardegenskaper som offentliga telefonnummer och e-postadress, men inte det privata sekundära telefonnumret eller e-postadressen som används för `microsoft.directory/user/standard/read` multifaktorautentisering.</li><li>`basic` anger gemensamma egenskaper, men exkluderar privilegierade egenskaper som är relaterade till `update` åtgärden. Uppsättningen egenskaper som du kan läsa kan vara annorlunda än vad du kan uppdatera. Det är därför det finns `standard` nyckelorden `basic` och som återspeglar det.</li></ul> |
| åtgärd | Åtgärden beviljas, oftast skapas, läses, uppdateras eller tas bort (CRUD). Det finns ett särskilt `allTasks` nyckelord för att ange alla ovanstående funktioner (skapa, läsa, uppdatera och ta bort). |

## <a name="deprecated-roles"></a>Inaktuella roller

Följande roller bör inte användas. De är inaktuella och kommer att tas bort från Azure AD i framtiden.

* AdHoc-licensadministratör
* Enhetskoppling
* Enhetshanterare
* Enhetsanvändare
* E-postverifierade användare skapare
* Postlådeadministratör
* Anslut till arbetsplatsenhet

## <a name="roles-not-shown-in-the-portal"></a>Roller som inte visas i portalen

Alla roller som returneras av PowerShell eller MS Graph API visas inte i Azure Portal. I följande tabell ordnas dessa skillnader.

API-namn | Azure Portal namn | Kommentarer
-------- | ------------------- | -------------
Enhetskoppling | Inaktuell | [Dokumentation om inaktuella roller](#deprecated-roles)
Enhetshanterare | Inaktuell | [Dokumentation om inaktuella roller](#deprecated-roles)
Enhetsanvändare | Inaktuell | [Dokumentation om inaktuella roller](#deprecated-roles)
Katalogsynkroniseringskonton | Visas inte eftersom den inte ska användas | [Dokumentation om katalogsynkroniseringskonton](#directory-synchronization-accounts)
Gästanvändare | Visas inte eftersom det inte kan användas | NA
Partnersupport på nivå 1 | Visas inte eftersom den inte ska användas | [Supportdokumentation för PartnerNivå 1](#partner-tier1-support)
Partnersupport på nivå 2 | Visas inte eftersom den inte ska användas | [Supportdokumentation för partnernivå 2](#partner-tier2-support)
Begränsad gästanvändare | Visas inte eftersom det inte kan användas | NA
Användare | Visas inte eftersom det inte kan användas | NA
Anslut till arbetsplatsenhet | Inaktuell | [Dokumentation om inaktuella roller](#deprecated-roles)

## <a name="password-reset-permissions"></a>Behörigheter för lösenordsåterställning

Kolumnrubriker representerar de roller som kan återställa lösenord. Tabellrader innehåller de roller som lösenordet kan återställas för.

Lösenordet kan återställas | Lösenordsadministratör | Supportadministratör | Autentiseringsadministratör | Användaradministratör | Privilegierad autentiseringsadministratör | Global administratör
------ | ------ | ------ | ------ | ------ | ------ | ------
Autentiseringsadministratör | &nbsp; | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Katalogläsare | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Global administratör | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:\*
Gruppadministratör | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Gäst inbjudare | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Supportadministratör | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Meddelandecenter Läsare | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Lösenordsadministratör | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Privilegierad autentiseringsadministratör | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Privilegierad rolladministratör | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Rapportläsare | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Användare (ingen administratörsroll) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Användaradministratör | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Läsare av användningssammanfattningsrapporter | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:

\* En global administratör kan inte ta bort sin egen tilldelning av global administratör. Detta för att förhindra en situation där en organisation har 0 globala administratörer.

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure AD-roller till grupper](groups-assign-role.md)
- [Förstå de olika rollerna](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Tilldela en användare administratörsbehörighet för en Azure-prenumeration](../../role-based-access-control/role-assignments-portal-subscription-admin.md)
