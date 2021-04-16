---
title: Använd SCIM, Microsoft Graph och Azure AD för att etablera användare och utöka appar med data
description: Använda SCIM och Microsoft Graph för att etablera användare och utöka programmet med de data som behövs.
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
ms.openlocfilehash: 87df7efcbab89c87a42e611f5fc1219239de6873
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530524"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>Använda SCIM och Microsoft Graph för att etablera användare och utöka ditt program med de data som behövs

**Målgrupp:** Den här artikeln riktar sig till utvecklare som skapar program som ska integreras Azure Active Directory (Azure AD). Om du vill använda program som redan är integrerade med Azure AD, till exempel Zoom, ServiceNow [](../saas-apps/tutorial-list.md) och DropBox, kan du hoppa över den här artikeln och granska de programspecifika självstudierna eller granska hur [etableringstjänsten fungerar.](./how-provisioning-works.md)

**Vanliga scenarier**

Azure AD tillhandahåller en out-of-box-tjänst för etablering och en utökningsbar plattform för att bygga dina program på. Beslutsträdet beskriver hur en utvecklare använder [SCIM och](https://aka.ms/scimoverview) [Microsoft Graph](/graph/overview) för att automatisera etableringen. 

> [!div class="checklist"]
> * Skapa användare automatiskt i mitt program
> * Ta automatiskt bort användare från mitt program när de inte längre ska ha åtkomst
> * Integrera mitt program med flera identitetsproviders för etablering
> * Utöka mitt program med data från Microsoft-tjänster till exempel Teams, Outlook och Office.
> * Skapa, uppdatera och ta bort användare och grupper automatiskt i Azure AD och Active Directory

![BESLUTsträd för SCIM Graph](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>Scenario 1: Skapa användare automatiskt i min app
I dag etablerar IT-administratörer användare genom att manuellt skapa användarkonton eller regelbundet ladda upp CSV-filer till mitt program. Processen är tidskrävande för kunder och gör att mitt program blir långsammare. Allt jag behöver är grundläggande användarinformation som namn, e-post och userPrincipalName för att skapa en användare. 

**Rekommendation**: 
* Om dina kunder använder olika IdP:er och du inte vill underhålla en synkroniseringsmotor för integrering med var och en, stöder du en SCIM-kompatibel [/Users-slutpunkt.](https://aka.ms/scimreferencecode) Dina kunder kan enkelt använda den här slutpunkten för att integrera med Azure AD-etableringstjänsten och automatiskt skapa användarkonton när de behöver åtkomst. Du kan skapa slutpunkten en gång så är den kompatibel med alla IP:er. Kolla in exempelbegäran nedan för hur en användare skulle skapas med SCIM.
* Om du behöver användardata som finns på användarobjektet i Azure AD och andra data från Microsoft kan du överväga att skapa en SCIM-slutpunkt för användareablering och anrop till Microsoft Graph för att hämta resten av data. 

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

## <a name="scenario-2-automatically-remove-users-from-my-app"></a>Scenario 2: Ta automatiskt bort användare från min app
Kunder som använder mitt program är säkerhetsfokuserade och har styrningskrav för att ta bort konton när anställda inte längre behöver dem. Hur automatiserar jag avetablering från mitt program?

**Rekommendation:** Stöd för en SCIM-kompatibel /Users-slutpunkt. Azure AD-etableringstjänsten skickar begäranden om att inaktivera och ta bort när användaren inte längre ska ha åtkomst. Vi rekommenderar att du stöder både inaktivering och borttagning av användare. Se exemplen nedan för hur en inaktiverings- och borttagningsbegäran ser ut. 

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

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>Scenario 3: Automatisera hanteringen av gruppmedlemskap i min app
Mitt program förlitar sig på grupper för åtkomst till olika resurser och kunder vill återanvända de grupper som de har i Azure AD. Hur kan jag importera grupper från Azure AD och hålla dem uppdaterade när medlemskapen ändras?  

**Rekommendation:** Stöd för en SCIM-kompatibel [/Groups-slutpunkt.](https://aka.ms/scimreferencecode) Azure AD-etableringstjänsten tar hand om att skapa grupper och hantera medlemskapsuppdateringar i ditt program. 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>Scenario 4: Utöka min app med data från Microsoft-tjänster till exempel Teams, Outlook och OneDrive
Mitt program är inbyggt i Microsoft Teams och förlitar sig på meddelandedata. Dessutom lagrar vi filer för användare i OneDrive. Hur kan jag utöka mitt program med data från dessa tjänster och från Microsoft?

**Rekommendation:** Den [Microsoft Graph är](/graph/) din startpunkt för åtkomst till Microsoft-data. Varje arbetsbelastning exponerar API:er med de data som du behöver. Microsoft Graph kan användas tillsammans med [SCIM-etablering för](./use-scim-to-provision-users-and-groups.md) scenarierna ovan. Du kan använda SCIM för att etablera grundläggande användarattribut i ditt program samtidigt som du anropar graph för att hämta andra data som du behöver. 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>Scenario 5: Spåra ändringar i Microsoft-tjänster till exempel Teams, Outlook och Azure AD
Jag måste kunna spåra ändringar i Teams- och Outlook-meddelanden och reagera på dem i realtid. Hur kan jag få de här ändringarna push-över till mitt program?

**Rekommendation:** I Microsoft Graph finns [ändringsmeddelanden](/graph/webhooks) och [ändringsspårning](/graph/delta-query-overview) för olika resurser. Observera följande begränsningar för ändringsmeddelanden:
- Om en händelsemottagare bekräftar en händelse, men inte agerar på den av någon anledning, kan händelsen gå förlorad.
- Den ordning i vilken ändringar tas emot är inte garanterat kronologisk.
- Ändringsmeddelanden innehåller inte alltid [resursdata](/graph/webhooks-with-resource-data) Av ovanstående skäl använder utvecklare ofta ändringsmeddelanden tillsammans med ändringsspårning för synkroniseringsscenarier. 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>Scenario 6: Etablera användare och grupper i Azure AD
Mitt program skapar information om en användare som kunder behöver i Azure AD. Det kan vara ett HR-program än att hantera anställning, en kommunikationsapp som skapar telefonnummer för användare eller någon annan app som genererar data som kan vara värdefulla i Azure AD. Hur gör jag för att fylla i användarposten i Azure AD med dessa data? 

**Rekommendation** Microsoft-diagrammet visar slutpunkter för /Users och /Groups som du kan integrera med idag för att etablera användare i Azure AD. Observera att Azure Active Directory inte stöder skrivning av dessa användare tillbaka till Active Directory. 

> [!NOTE]
> Microsoft har en etableringstjänst som hämtar data från HR-program som Workday och SuccessFactors. Dessa integreringar byggs och hanteras av Microsoft. Om du vill registrera ett nytt HR-program till vår tjänst kan du begära det på [UserVoice.](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests) 

## <a name="related-articles"></a>Relaterade artiklar

- [Läs dokumentationen om Microsoft Graph synkroniserings-](/graph/api/resources/synchronization-overview)
- [Integrera en anpassad SCIM-app med Azure AD](use-scim-to-provision-users-and-groups.md)