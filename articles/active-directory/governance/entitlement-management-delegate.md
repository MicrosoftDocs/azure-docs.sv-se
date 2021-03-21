---
title: Delegering och roller i hantering av rättigheter – Azure AD
description: Lär dig hur du delegerar åtkomst styrning från IT-administratörer till avdelnings chefer och projekt chefer så att de kan hantera åtkomst själva.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 204d83b96e3cbe26759d678126d8826d0b2e492e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100577844"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Delegering och roller i hantering av Azure AD-rättigheter

Globala administratörer och användar administratörer kan som standard skapa och hantera alla aspekter av hantering av Azure AD-rättigheter. Användare i dessa roller kan dock inte känna till alla situationer där åtkomst paket krävs. Vanligt vis är det användare inom respektive avdelning, team eller projekt som vet vilka de samarbetar med, med hjälp av vilka resurser och hur länge. I stället för att ge användarna obegränsad behörighet till icke-administratörer kan du ge användarna de minst behörigheter de behöver för att utföra sitt jobb och undvika att skapa motstridiga eller olämpliga åtkomst rättigheter.

Den här videon ger en översikt över hur du delegerar åtkomst styrning från IT-administratören till användare som inte är administratörer.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>Ombuds exempel

För att förstå hur du kan delegera åtkomst styrning i hantering av rättigheter, hjälper det till att överväga ett exempel. Anta att din organisation har följande administratör och chefer.

![Delegera från IT-administratören till chefer](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

Som IT-administratör har Hana kontakter på varje avdelning – Mamta i marknadsföring, mark i ekonomi och Joe i juridisk person som ansvarar för deras avdelnings resurser och affärs kritiskt innehåll.

Med rättighets hantering kan du delegera åtkomst styrning till dessa icke-administratörer eftersom de är de som vet vilka användare som behöver åtkomst, för hur lång tid och vilka resurser. Att delegera till icke-administratörer garanterar att rätt personer hanterar åtkomst för sina avdelningar.

Här är ett sätt som Hana kan delegera åtkomst styrning till marknadsförings-, ekonomi-och juridiska avdelningar.

1. Hana skapar en ny Azure AD-säkerhetsgrupp och lägger till Mamta, markera och Johan som medlemmar i gruppen.

1. Hana lägger till den gruppen i katalogens skapare roll.

    Mamta, mark och Johan kan nu skapa kataloger för sina avdelningar, lägga till resurser som deras avdelningar behöver och göra ytterligare delegering i katalogen. De kan inte se var and s kataloger.

1. Mamta skapar en **marknadsförings** katalog som är en behållare med resurser.

1. Mamta lägger till de resurser som marknadsförings avdelningen äger till den här katalogen.

1. Mamta kan lägga till andra personer från hennes avdelning som katalog ägare för den här katalogen, som hjälper till att dela katalog hanterings ansvars områden.

1. Mamta kan ytterligare delegera skapande och hantering av åtkomst paket i marknadsförings katalogen till projekt chefer på marknadsförings avdelningen. Hon kan göra detta genom att tilldela dem till Access Package Manager-rollen. En Access Package Manager kan skapa och hantera åtkomst paket. 

I följande diagram visas kataloger med resurser för marknadsförings-, ekonomi-och juridisk avdelningen. Med hjälp av dessa kataloger kan projektledarna skapa åtkomst paket för sina team eller projekt.

![Ombuds exempel för rättighets hantering](./media/entitlement-management-delegate/elm-delegate.png)

Efter delegeringen kan marknadsförings avdelningen ha roller som liknar följande tabell.

| User | Jobb roll | Azure AD-roll | Rättighets hanterings roll |
| --- | --- | --- | --- |
| Hana | IT-administratör | Global administratör eller användar administratör |  |
| Mamta | Marknadsförings chef | User | Katalog skapare och katalog ägare |
| Bob | Marknads ledande kund | User | Katalog ägare |
| Jessica | Marketing Project Manager | User | Åtkomst till paket hanteraren |

## <a name="entitlement-management-roles"></a>Rättighets hanterings roller

Rättighets hantering har följande roller som är speciella för rättighets hantering.

| Rättighets hanterings roll | Beskrivning |
| --- | --- |
| Katalog skapare | Skapa och hantera kataloger. Vanligt vis en IT-administratör som inte är en global administratör eller resurs ägare för en samling resurser. Personen som skapar en katalog blir automatiskt katalogens första katalog ägare och kan lägga till fler katalog ägare. En katalog skapare kan inte hantera eller se kataloger som de inte äger och kan inte lägga till resurser som de inte äger i en katalog. Om katalog skaparen behöver hantera en annan katalog eller lägga till resurser som de inte äger, kan de begära att bli medägare till katalogen eller resursen. |
| Katalog ägare | Redigera och hantera befintliga kataloger. Vanligt vis en IT-administratör eller resurs ägare, eller en användare som katalog ägaren har valt. |
| Åtkomst till paket hanteraren | Redigera och hantera alla befintliga åtkomst paket i en katalog. |
| Åtkomst till paket tilldelnings hanteraren | Redigera och hantera alla befintliga åtkomst paket tilldelningar. |

Dessutom har den valda god kännaren och en beställare av ett Access-paket behörighet, även om de inte är roller.

| Höger | Beskrivning |
| --- | --- |
| Godkännare | Auktoriserad av en princip för att godkänna eller neka begär Anden om åtkomst till paket, men de kan inte ändra definitioner för åtkomst paket. |
| Requestor | Auktoriserad av en princip för ett Access-paket för att begära det Access-paketet. |

I följande tabell visas de aktiviteter som rättighets hanterings rollerna kan utföra.

| Uppgift | Administratör | Katalog skapare | Katalog ägare | Åtkomst till paket hanteraren | Åtkomst till paket tilldelnings hanteraren |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Delegera till en katalog skapare](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |  |
| [Lägga till en ansluten organisation](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |  |
| [Skapa en ny katalog](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |  |
| [Lägga till en resurs i en katalog](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Lägg till en katalog ägare](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Redigera en katalog](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Ta bort en katalog](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Delegera till en Access Package Manager](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Ta bort en Access Package Manager](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Skapa ett nytt Access-paket i en katalog](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [Ändra resurs roller i ett Access-paket](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Skapa och redigera principer](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Tilldela en användare direkt till ett Access-paket](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [Ta bort en användare direkt från ett Access-paket](entitlement-management-access-package-assignments.md#remove-an-assignment) | :heavy_check_mark:  |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Visa vem som har en tilldelning till ett Access-paket](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [Visa ett Access-pakets begär Anden](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:  |
| [Visa leverans fel för en begäran](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [Ombearbeta en begäran](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:  |
| [Avbryt en väntande begäran](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Dölj ett Access-paket](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Ta bort ett Access-paket](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Nödvändiga roller för att lägga till resurser i en katalog

En global administratör kan lägga till eller ta bort alla grupper (molnbaserade säkerhets grupper eller molnbaserade Microsoft 365 grupper), program eller SharePoint Online-platser i en katalog. En användar administratör kan lägga till eller ta bort alla grupper eller program i en katalog, förutom en grupp som kon figurer ATS som tilldelnings bara till en katalog roll. Observera att en användar administratör kan hantera åtkomst paket i en katalog som innehåller grupper som kon figurer ATS som tilldelnings bara till en katalog roll.  Om du vill ha mer information om roll tilldelnings bara grupper [skapar du en roll tilldelnings grupp i Azure Active Directory](../roles/groups-create-eligible.md).

För en användare som inte är en global administratör eller en användar administratör, för att lägga till grupper, program eller SharePoint Online-webbplatser till en katalog, måste användaren ha *både* den nödvändiga Azure AD-katalog rollen och katalog ägaren rättighets hanterings roll. I följande tabell visas de roll kombinationer som krävs för att lägga till resurser i en katalog. Om du vill ta bort resurser från en katalog måste du ha samma roller.

| Azure AD-katalog roll | Rättighets hanterings roll | Kan lägga till säkerhets grupp | Kan lägga till Microsoft 365 grupp | Kan lägga till app | Kan lägga till SharePoint Online-webbplats |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Global administratör](../roles/permissions-reference.md) | saknas |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Användaradministratör](../roles/permissions-reference.md) | saknas |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Intune-administratör](../roles/permissions-reference.md) | Katalog ägare | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange-administratör](../roles/permissions-reference.md) | Katalog ägare |  | :heavy_check_mark: |  |  |
| [Team tjänst administratör](../roles/permissions-reference.md) | Katalog ägare |  | :heavy_check_mark: |  |  |
| [SharePoint-administratör](../roles/permissions-reference.md) | Katalog ägare |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Program administratör](../roles/permissions-reference.md) | Katalog ägare |  |  | :heavy_check_mark: |  |
| [Moln program administratör](../roles/permissions-reference.md) | Katalog ägare |  |  | :heavy_check_mark: |  |
| User | Katalog ägare | Endast om grupp ägare | Endast om grupp ägare | Endast om app-ägare |  |

Om du vill fastställa den lägsta privilegierade rollen för en aktivitet kan du också referera till [Administratörs roller efter administratörs uppgift i Azure Active Directory](../roles/delegate-by-task.md#entitlement-management).

## <a name="next-steps"></a>Nästa steg

- [Delegera åtkomst styrning till katalog skapare](entitlement-management-delegate-catalog.md)
- [Skapa och hantera en katalog med resurser](entitlement-management-catalog-create.md)
