---
title: Vanliga scenarier i hantering av rättigheter – Azure AD
description: Lär dig mer om de övergripande steg som du bör följa för vanliga scenarier i Azure Active Directory rättighets hantering.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0f45a1481661aa350815560d795ab7411f99545
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92317833"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Vanliga scenarier i hantering av Azure AD-berättigande

Det finns flera sätt som du kan konfigurera rättighets hantering för din organisation. Men om du precis är igång är det bra att förstå vanliga scenarier för administratörer, katalog ägare, åtkomst till paket hanterare, god kännare och beställare.

## <a name="delegate"></a>Delegera

### <a name="administrator-delegate-management-of-resources"></a>Administratör: delegera hantering av resurser

1. [Titta på video: delegering från IT till avdelnings chef](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [Delegera användare till katalog skapare roll](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>Katalog skapare: delegera hantering av resurser

- [Skapa en ny katalog](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>Katalog ägare: delegera hantering av resurser

1. [Lägg till medägare i katalogen](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [Lägga till resurser i katalogen](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>Katalog ägare: delegera hantering av åtkomst paket

1. [Titta på video: delegering från katalog ägare för att komma åt paket hanteraren](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [Delegera användare åtkomst till Package Manager-rollen](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>Styr åtkomsten för användare i din organisation

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>Åtkomst till paket hanteraren: Tillåt anställda i din organisation att begära åtkomst till resurser

1. [Skapa ett nytt åtkomstpaket](entitlement-management-access-package-create.md#start-new-access-package)
1. [Lägg till grupper, team, program eller SharePoint-platser för att få åtkomst till paketet](entitlement-management-access-package-create.md#resource-roles)
1. [Lägg till en princip för begäran för att tillåta användare i katalogen att begära åtkomst](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [Ange inställningar för förfallo datum](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>Beställare: begär åtkomst till resurser

1. [Logga in på portalen för åtkomst](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Hitta Access-paket
1. [Begär åtkomst](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>God kännare: Godkänn begär anden till resurser

1. [Öppna begäran i min åtkomst Portal](entitlement-management-request-approve.md#open-request)
1. [Godkänn eller neka åtkomst förfrågan](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>Begär ande: Visa de resurser du redan har åtkomst till

1. [Logga in på portalen för åtkomst](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Visa aktiva åtkomst paket

## <a name="govern-access-for-users-outside-your-organization"></a>Styra åtkomsten för användare utanför organisationen

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>Administratör: samar beta med en extern partner organisation

1. [Läs hur åtkomst fungerar för externa användare](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [Granska inställningar för externa användare](entitlement-management-external-users.md#settings-for-external-users)
1. [Lägg till en anslutning till den externa organisationen](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>Access Package Manager: samar beta med en extern partner organisation

1. [Skapa ett nytt åtkomstpaket](entitlement-management-access-package-create.md#start-new-access-package)
1. [Lägg till grupper, team, program eller SharePoint-platser för att få åtkomst till paketet](entitlement-management-access-package-resources.md#add-resource-roles)
1. [Lägg till en princip för begäran för att tillåta användare som inte i din katalog att begära åtkomst](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [Ange inställningar för förfallo datum](entitlement-management-access-package-create.md#lifecycle)
1. [Kopiera länken för att begära åtkomst paketet](entitlement-management-access-package-settings.md)
1. Skicka länken till din externa partner kontakt partner för att dela med sina användare

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>Beställare: begär åtkomst till resurser som en extern användare

1. Hitta länken till det Access-paket som du fick från din kontakt
1. [Logga in på portalen för åtkomst](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [Begär åtkomst](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>God kännare: Godkänn begär anden till resurser

1. [Öppna begäran i min åtkomst Portal](entitlement-management-request-approve.md#open-request)
1. [Godkänn eller neka åtkomst förfrågan](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>Begär ande: Visa de resurser som du redan har åtkomst till

1. [Logga in på portalen för åtkomst](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Visa aktiva åtkomst paket

## <a name="day-to-day-management"></a>Daglig hantering

### <a name="access-package-manager-update-the-resources-for-a-project"></a>Access Package Manager: uppdatera resurserna för ett projekt

1. [Titta på video: daglig hantering: saker har ändrats](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Öppna Access-paketet
1. [Lägga till eller ta bort grupper, team, program eller SharePoint-webbplatser](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>Access Package Manager: uppdatera varaktigheten för ett projekt

1. [Titta på video: daglig hantering: saker har ändrats](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Öppna Access-paketet
1. [Öppna livs cykel inställningarna](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [Uppdatera inställningarna för förfallo datum](entitlement-management-access-package-lifecycle-policy.md#lifecycle) 

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>Access Package Manager: uppdatera hur åtkomst godkänns för ett projekt

1. [Titta på video: daglig hantering: saker har ändrats](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Öppna en befintlig princip för förfrågnings inställningar](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Uppdatera godkännande inställningarna](entitlement-management-access-package-approval-policy.md#change-approval-settings-of-an-existing-access-package)

### <a name="access-package-manager-update-the-people-for-a-project"></a>Access Package Manager: uppdatera personerna för ett projekt

1. [Titta på video: daglig hantering: saker har ändrats](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Ta bort användare som inte längre behöver åtkomst](entitlement-management-access-package-assignments.md)
1. [Öppna en befintlig princip för förfrågnings inställningar](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Lägg till användare som behöver åtkomst](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>Access Package Manager: tilldela vissa användare direkt till ett Access-paket

1. [Om användarna behöver olika livs cykel inställningar lägger du till en ny princip i Access-paketet](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Tilldela åtkomst paketet vissa användare direkt](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>Tilldelningar och rapporter

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>Administratör: Visa vem som har tilldelningar till ett Access-paket

1. Öppna ett Access-paket
1. [Visa tilldelningar](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [Arkivera rapporter och loggar](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>Administratör: Visa resurser som är tilldelade till användare

1. [Visa åtkomst paket för en användare](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [Visa resurstilldelningar för en användare](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>Programmatisk administration

Du kan också hantera åtkomst paket, kataloger, principer, förfrågningar och tilldelningar med hjälp av Microsoft Graph.  En användare i en lämplig roll med ett program som har den delegerade `EntitlementManagement.ReadWrite.All` behörigheten kan anropa [API: et för rättighets hantering](/graph/tutorial-access-package-api?view=graph-rest-beta).

## <a name="next-steps"></a>Nästa steg

- [Delegering och roller](entitlement-management-delegate.md)
- [Begär process och e-postmeddelanden](entitlement-management-process.md)