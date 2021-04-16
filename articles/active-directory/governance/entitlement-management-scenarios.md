---
title: Vanliga scenarier för berättigandehantering – Azure AD
description: Lär dig de avancerade stegen som du bör följa för vanliga scenarier i Azure Active Directory berättigandehantering.
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
ms.openlocfilehash: 28c16e4d73fc2379806e1a2bce2fa5dbb3247fed
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531958"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Vanliga scenarier i Azure AD-berättigandehantering

Det finns flera sätt att konfigurera berättigandehantering för din organisation. Men om du precis har börjat är det bra att förstå vanliga scenarier för administratörer, katalogägare, åtkomstpakethanterare, godkännare och beställare.

## <a name="delegate"></a>Delegera

### <a name="administrator-delegate-management-of-resources"></a>Administratör: Delegera hantering av resurser

1. [Titta på video: Delegering från IT till avdelningschef](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [Delegera användare till rollen katalogskapare](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>Katalogskapare: Delegera hantering av resurser

- [Skapa en ny katalog](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>Katalogägare: Delegera hantering av resurser

1. [Lägga till medägare i katalogen](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [Lägga till resurser i katalogen](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>Katalogägare: Delegera hantering av åtkomstpaket

1. [Titta på video: Delegering från katalogägare för att få åtkomst till pakethanteraren](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [Delegera användare att få åtkomst till pakethanteringsrollen](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>Styra åtkomst för användare i din organisation

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>Åtkomstpakethanterare: Tillåt anställda i din organisation att begära åtkomst till resurser

1. [Skapa ett nytt åtkomstpaket](entitlement-management-access-package-create.md#start-new-access-package)
1. [Lägga till grupper, Teams, program eller SharePoint-webbplatser för att få åtkomst till paketet](entitlement-management-access-package-create.md#resource-roles)
1. [Lägg till en begärandeprincip för att tillåta användare i din katalog att begära åtkomst](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [Ange förfalloinställningar](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>Beställare: Begär åtkomst till resurser

1. [Logga in på Min åtkomst portalen](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Hitta åtkomstpaket
1. [Begär åtkomst](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Godkännare: Godkänna begäranden till resurser

1. [Öppna begäran i Min åtkomst portalen](entitlement-management-request-approve.md#open-request)
1. [Godkänna eller neka åtkomstbegäran](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>Beställare: Visa de resurser som du redan har åtkomst till

1. [Logga in på Min åtkomst portalen](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Visa aktiva åtkomstpaket

## <a name="govern-access-for-users-outside-your-organization"></a>Styra åtkomst för användare utanför organisationen

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>Administratör: Samarbeta med en extern partnerorganisation

1. [Läs om hur åtkomst fungerar för externa användare](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [Granska inställningar för externa användare](entitlement-management-external-users.md#settings-for-external-users)
1. [Lägga till en anslutning till den externa organisationen](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>Åtkomstpakethanterare: Samarbeta med en extern partnerorganisation

1. [Skapa ett nytt åtkomstpaket](entitlement-management-access-package-create.md#start-new-access-package)
1. [Lägga till grupper, Teams, program eller SharePoint-webbplatser för att få åtkomst till paketet](entitlement-management-access-package-resources.md#add-resource-roles)
1. [Lägg till en begärandeprincip för att tillåta användare som inte finns i din katalog att begära åtkomst](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [Ange förfalloinställningar](entitlement-management-access-package-create.md#lifecycle)
1. [Kopiera länken för att begära åtkomstpaketet](entitlement-management-access-package-settings.md)
1. Skicka länken till din externa partnerkontaktpartner och dela den med användarna

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>Beställare: Begär åtkomst till resurser som en extern användare

1. Hitta länken för åtkomstpaketet som du fick från din kontakt
1. [Logga in på Min åtkomst portalen](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [Begär åtkomst](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Godkännare: Godkänna begäranden till resurser

1. [Öppna begäran i Min åtkomst portalen](entitlement-management-request-approve.md#open-request)
1. [Godkänna eller neka åtkomstbegäran](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>Beställare: Visa de resurser som du redan har åtkomst till

1. [Logga in på Min åtkomst portalen](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Visa aktiva åtkomstpaket

## <a name="day-to-day-management"></a>Den dagliga hanteringen

### <a name="access-package-manager-update-the-resources-for-a-project"></a>Åtkomstpakethanterare: Uppdatera resurserna för ett projekt

1. [Titta på video: Den dagliga hanteringen: Saker har ändrats](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Öppna åtkomstpaketet
1. [Lägga till eller ta bort grupper, Teams, program eller SharePoint-webbplatser](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>Åtkomstpakethanterare: Uppdatera varaktigheten för ett projekt

1. [Titta på video: Den dagliga hanteringen: Saker har ändrats](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Öppna åtkomstpaketet
1. [Öppna livscykelinställningarna](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [Uppdatera förfalloinställningarna](entitlement-management-access-package-lifecycle-policy.md#lifecycle) 

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>Åtkomstpakethanterare: Uppdatera hur åtkomst godkänns för ett projekt

1. [Titta på video: Den dagliga hanteringen: Saker har ändrats](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Öppna en befintlig princip för begärandeinställningar](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Uppdatera godkännandeinställningarna](entitlement-management-access-package-approval-policy.md#change-approval-settings-of-an-existing-access-package)

### <a name="access-package-manager-update-the-people-for-a-project"></a>Åtkomstpakethanterare: Uppdatera personerna för ett projekt

1. [Titta på video: Den dagliga hanteringen: Saker har ändrats](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Ta bort användare som inte längre behöver åtkomst](entitlement-management-access-package-assignments.md)
1. [Öppna en befintlig princip för begärandeinställningar](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Lägga till användare som behöver åtkomst](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>Åtkomstpakethanterare: Tilldela specifika användare direkt till ett åtkomstpaket

1. [Om användarna behöver olika livscykelinställningar lägger du till en ny princip i åtkomstpaketet](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Tilldela specifika användare direkt till åtkomstpaketet](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>Tilldelningar och rapporter

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>Administratör: Visa vem som har tilldelningar till ett åtkomstpaket

1. Öppna ett åtkomstpaket
1. [Visa tilldelningar](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [Arkivera rapporter och loggar](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>Administratör: Visa resurser som tilldelats till användare

1. [Visa åtkomstpaket för en användare](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [Visa resurstilldelningar för en användare](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>Programmatisk administration

Du kan också hantera åtkomstpaket, kataloger, principer, begäranden och tilldelningar med hjälp av Microsoft Graph.  En användare i en lämplig roll med ett program som har den delegerade `EntitlementManagement.ReadWrite.All` behörigheten kan anropa [API:et för rättighetshantering.](/graph/tutorial-access-package-api)

## <a name="next-steps"></a>Nästa steg

- [Delegering och roller](entitlement-management-delegate.md)
- [Begärandeprocess och e-postaviseringar](entitlement-management-process.md)