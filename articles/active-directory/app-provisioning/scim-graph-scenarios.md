---
title: Använd SCIM, Microsoft Graph och Azure AD för att etablera användare och utöka appar med data
description: Använda SCIM och Microsoft Graph tillsammans för att tillhandahålla användare och förbättra ditt program med de data som krävs.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 04/26/2020
ms.author: kenwith
ms.reviewer: arvinh, celested
ms.openlocfilehash: 0a5d84585f28f6d13cbceb1fec41d6cdabf6d08c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99255635"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>Använda SCIM och Microsoft Graph tillsammans för att etablera användare och förbättra ditt program med de data som krävs

**Mål grupp:** Den här artikeln är riktad mot utvecklare som skapar program som ska integreras med Azure Active Directory (Azure AD). Om du vill använda program som redan är integrerade med Azure AD, till exempel zoom, ServiceNow och DropBox, kan du hoppa över den här artikeln och granska programspecifika [själv studie kurser](../saas-apps/tutorial-list.md) eller granska [hur etablerings tjänsten fungerar](./how-provisioning-works.md).

**Vanliga scenarier**

Azure AD tillhandahåller en välkomst tjänst för etablering och en utöknings bar plattform för att bygga dina program på. Besluts trädet beskriver hur en utvecklare ska använda [scim](https://aka.ms/scimoverview) och [Microsoft Graph](/graph/overview) för att automatisera etableringen. 

> [!div class="checklist"]
> * Skapa användare automatiskt i mitt program
> * Ta bort användare från mitt program automatiskt när de inte längre har åtkomst
> * Integrera mitt program med flera identitets leverantörer för etablering
> * Utöka programmet med data från Microsoft-tjänster, till exempel team, Outlook och Office.
> * Skapa, uppdatera och ta bort användare och grupper automatiskt i Azure AD och Active Directory

![Besluts träd för SCIM Graph](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>Scenario 1: skapa automatiskt användare i min app
I dag kan IT-administratörer etablera användare genom att manuellt skapa användar konton eller regelbundet ladda upp CSV-filer till mitt program. Processen är tids krävande för kunder och gör att programmet kan bli långsammare. Allt jag behöver är grundläggande användar information, till exempel namn, e-post och userPrincipalName, för att skapa en användare. 

**Rekommendation**: 
* Om dina kunder använder olika IDP: er och du inte vill underhålla en Sync-motor för att integrera med var och en, stöder en SCIM-kompatibel [/Users](https://aka.ms/scimreferencecode) -slutpunkt. Kunderna kan enkelt använda slut punkten för att integrera med Azure AD Provisioning-tjänsten och automatiskt skapa användar konton när de behöver åtkomst. Du kan bygga slut punkten en gång och den är kompatibel med alla IDP: er. Kolla in exempel förfrågan nedan för hur en användare skulle skapas med SCIM.
* Om du behöver användar data som finns på användarobjektet i Azure AD och andra data från Microsoft, kan du skapa en SCIM-slutpunkt för användar etablering och anropa den Microsoft Graph för att hämta resten av data. 

```json
POST /Users
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "BillG",
    "active": true,
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "Bill Gates",
        "familyName": "Gates",
        "givenName": "Bill"
    },
    "roles": []
}
```

## <a name="scenario-2-automatically-remove-users-from-my-app"></a>Scenario 2: ta bort användare från min app automatiskt
Kunderna som använder mitt program är säkerhet fokuserad och har styrnings krav för att ta bort konton när de anställda inte längre behöver dem. Hur kan jag automatisera avetableringen från mitt program?

**Rekommendation:** Stöd för en SCIM-kompatibel/Users-slutpunkt. Azure AD Provisioning-tjänsten skickar begär Anden om att inaktivera och ta bort när användaren inte längre har åtkomst. Vi rekommenderar att du stöder både inaktive ring och borttagning av användare. Se exemplen nedan för hur en begäran om att inaktivera och ta bort ska se ut. 

Inaktivera användare
```json
PATCH /Users/5171a35d82074e068ce2 HTTP/1.1
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```
Ta bort användare
```json
DELETE /Users/5171a35d82074e068ce2 HTTP/1.1
```

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>Scenario 3: Automatisera hantering av grupp medlemskap i min app
Mitt program är beroende av grupper för åtkomst till olika resurser och kunder vill återanvända de grupper som de har i Azure AD. Hur kan jag importera grupper från Azure AD och hålla dem uppdaterade när medlemskapet ändras?  

**Rekommendation:** Stöd för en SCIM-kompatibel/Groups- [slutpunkt](https://aka.ms/scimreferencecode). Azure AD Provisioning-tjänsten tar hand om att skapa grupper och hantera medlemskaps uppdateringar i ditt program. 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>Scenario 4: utöka min app med data från Microsoft-tjänster som Teams, Outlook och OneDrive
Mitt program är inbyggt i Microsoft Teams och förlitar sig på meddelande data. Dessutom lagrar vi filer för användare i OneDrive. Hur kan jag förbättra mitt program med data från de här tjänsterna och i Microsoft?

**Rekommendation:** [Microsoft Graph](/graph/) är din start punkt för att komma åt Microsoft-data. Varje arbets belastning exponerar API: er med de data som du behöver. Microsoft Graph kan användas tillsammans med scim- [etablering](./use-scim-to-provision-users-and-groups.md) för scenarierna ovan. Du kan använda SCIM för att etablera grundläggande användarattribut i programmet samtidigt som du anropar Graph för att hämta andra data som du behöver. 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>Scenario 5: spåra ändringar i Microsoft-tjänster som Teams, Outlook och Azure AD
Jag behöver kunna spåra ändringar i team och Outlook-meddelanden och reagera på dem i real tid. Hur kan jag få dessa ändringar att skickas till mitt program?

**Rekommendation:** Microsoft Graph tillhandahåller [ändrings meddelanden](/graph/webhooks) och [ändrings spårning](/graph/delta-query-overview) för olika resurser. Observera följande begränsningar i ändrings meddelanden:
- Om en händelse mottagare bekräftar en händelse, men inte agerar på den av någon anledning, kan händelsen gå förlorad.
- Ordningen i vilken ändringar tas emot är inte garanterat kronologisk.
- Ändrings meddelanden innehåller inte alltid [resurs data](/graph/webhooks-with-resource-data) för orsakerna ovan. utvecklare använder ofta ändrings meddelanden tillsammans med ändrings spårning för synkroniseringskonflikter. 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>Scenario 6: etablera användare och grupper i Azure AD
Mitt program skapar information om en användare som kunder behöver i Azure AD. Detta kan vara ett HR-program än hanterar anställning, en kommunikations app som skapar telefonnummer för användare eller någon annan app som genererar data som är värdefulla i Azure AD. Hur gör jag för att du fylla i användar posten i Azure AD med dessa data? 

**Rekommendation** Microsoft Graph visar/Users-och/Groups-slutpunkter som du kan integrera med idag för att etablera användare i Azure AD. Observera att Azure Active Directory inte har stöd för att skriva tillbaka de här användarna till Active Directory. 

> [!NOTE]
> Microsoft har en etablerings tjänst som hämtar data från HR-program som Workday och SuccessFactors. Dessa integreringar skapas och hanteras av Microsoft. För att registrera ett nytt HR-program i vår tjänst kan du begära det på [UserVoice](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests). 

## <a name="related-articles"></a>Relaterade artiklar

- [Läs dokumentationen om synkronisering Microsoft Graph](/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Integrera en anpassad SCIM-app med Azure AD](use-scim-to-provision-users-and-groups.md)