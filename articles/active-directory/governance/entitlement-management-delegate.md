---
title: Delegering och roller i hantering av rättigheter – Azure AD
description: Lär dig hur du delegerar åtkomst styrning från IT-administratörer till avdelnings chefer och projekt chefer så att de kan hantera åtkomst själva.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: a01f945496d2f0bc81a108c5e58c89587c1c4e38
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505486"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Delegering och roller i hantering av Azure AD-rättigheter

Globala administratörer och användar administratörer kan som standard skapa och hantera alla aspekter av hantering av Azure AD-rättigheter. Användare i dessa roller kan dock inte känna till alla situationer där åtkomst paket krävs. Vanligt vis är det användare inom respektive avdelningar, team eller projekt som vet vilka de samarbetar med, med hjälp av vilka resurser och hur länge. I stället för att ge användarna obegränsad behörighet till icke-administratörer, kan du ge användarna minst de behörigheter de behöver för att utföra sitt jobb och undvika att skapa motstridiga eller olämpliga åtkomst rättigheter.

Den här videon ger en översikt över hur du delegerar åtkomst styrning från IT-administratören till användare som inte är administratörer.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>Ombuds exempel

För att förstå hur du kan delegera åtkomst styrning i hantering av rättigheter, hjälper det till att överväga ett exempel. Anta att din organisation har följande administratör och chefer.

![Delegera från IT-administratören till chefer](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

Som IT-administratör har Hana kontakter på varje avdelning – Mamta i marknadsföring, mark i ekonomi och Joe i juridisk person som ansvarar för deras avdelnings resurser och affärs kritiskt innehåll.

Med rättighets hantering kan du delegera åtkomst styrning till dessa icke-administratörer eftersom de är de som vet vilka användare som behöver åtkomst, för hur lång tid och vilka resurser. Detta säkerställer att rätt personer hanterar åtkomst för sina avdelningar.

Här är ett sätt som Hana kan delegera åtkomst styrning till marknadsförings-, ekonomi-och juridiska avdelningar.

1. Hana skapar en ny Azure AD-säkerhetsgrupp och lägger till Mamta, markera och Johan som medlemmar i gruppen.

1. Hana lägger till den gruppen i katalogens skapare roll.

    Mamta, mark och Johan kan nu skapa kataloger för sina avdelningar, lägga till resurser som deras avdelningar behöver och göra ytterligare delegering i katalogen.

    Observera att Mamta, mark och Johan inte kan se var and Johan kataloger.

1. Mamta skapar en **marknadsförings** katalog som är en behållare med resurser.

1. Mamta lägger till de resurser som marknadsförings avdelningen äger till den här katalogen.

1. Mamta kan lägga till fler personer från hennes avdelning som katalog ägare för den här katalogen. Detta hjälper dig att dela ansvars områden för katalog hantering.

1. Mamta kan ytterligare delegera skapande och hantering av åtkomst paket i marknadsförings katalogen till projekt chefer på marknadsförings avdelningen. Hon kan göra detta genom att tilldela dem till Access Package Manager-rollen. En Access Package Manager kan skapa och hantera åtkomst paket. 

I följande diagram visas kataloger med resurser för marknadsförings-, ekonomi-och juridisk avdelningen. Med hjälp av dessa kataloger kan projektledarna skapa åtkomst paket för sina team eller projekt.

![Ombuds exempel för rättighets hantering](./media/entitlement-management-delegate/elm-delegate.png)

Efter delegeringen kan marknadsförings avdelningen ha roller som liknar följande tabell.

| Användare | Jobb roll | Azure AD-roll | Rättighets hanterings roll |
| --- | --- | --- | --- |
| Hana | IT-administratör | Global administratör eller användar administratör |  |
| Mamta | Marknadsförings chef | Användare | Katalog skapare och katalog ägare |
| Bob | Marknads ledande kund | Användare | Katalog ägare |
| Jessica | Marketing Project Manager | Användare | Åtkomst till paket hanteraren |

## <a name="entitlement-management-roles"></a>Rättighets hanterings roller

Rättighets hantering har följande roller som är speciella för rättighets hantering.

| Rättighets hanterings roll | Beskrivning |
| --- | --- |
| Katalog skapare | Skapa och hantera kataloger. Vanligt vis en IT-administratör som inte är en global administratör eller en resurs ägare för en samling resurser. Personen som skapar en katalog blir automatiskt katalogens första katalog ägare och kan lägga till ytterligare katalog ägare. En katalog skapare kan inte hantera eller se kataloger som de inte äger och kan inte lägga till resurser som de inte äger i en katalog. Om katalog skaparen behöver hantera en annan katalog eller lägga till resurser som de inte äger, kan de begära att bli medägare till katalogen eller resursen. |
| Katalog ägare | Redigera och hantera befintliga kataloger. Vanligt vis en IT-administratör eller resurs ägare, eller en användare som har tilldelats katalog ägaren. |
| Åtkomst till paket hanteraren | Redigera och hantera alla befintliga åtkomst paket i en katalog. |

Dessutom har en utsedd god kännare och en beställare av ett Access-paket även rättigheter, även om de inte är roller.

| Höger | Beskrivning |
| --- | --- |
| God kännare | Auktoriserad av en princip för att godkänna eller neka begär Anden om åtkomst till paket, även om de inte kan ändra definitioner för åtkomst paket. |
| Requestor | Auktoriserad av en princip för ett Access-paket för att begära det Access-paketet. |

I följande tabell visas de aktiviteter som rättighets hanterings rollerna kan utföra.

| Aktivitet | Administratör | Katalog skapare | Katalog ägare | Åtkomst till paket hanteraren |
| --- | :---: | :---: | :---: | :---: |
| [Delegera till en katalog skapare](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |
| [Lägga till en ansluten organisation](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |
| [Skapa en ny katalog](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Lägga till en resurs i en katalog](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Lägg till en katalog ägare](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Redigera en katalog](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Ta bort en katalog](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Delegera till en Access Package Manager](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Ta bort en Access Package Manager](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Skapa ett nytt Access-paket i en katalog](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |
| [Ändra resurs roller i ett Access-paket](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Skapa och redigera principer](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Tilldela en användare direkt till ett Access-paket](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Visa vem som har en tilldelning till ett Access-paket](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Visa ett Access-pakets begär Anden](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Visa leverans fel för en begäran](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Ombearbeta en begäran](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Avbryt en väntande begäran](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Dölj ett Access-paket](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Ta bort ett Access-paket](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Nödvändiga roller för att lägga till resurser i en katalog

En global administratör kan lägga till eller ta bort alla grupper (molnbaserade säkerhets grupper eller molnbaserade Office 365-grupper), program eller SharePoint Online-platser i en katalog. En användar administratör kan lägga till eller ta bort alla grupper eller program i en katalog, förutom en grupp som kon figurer ATS som tilldelnings bara till en katalog roll.

För en användare som inte är en global administratör eller en användar administratör, för att lägga till grupper, program eller SharePoint Online-webbplatser till en katalog, måste användaren ha *både* den nödvändiga Azure AD-katalog rollen och katalog ägaren rättighets hanterings roll. I följande tabell visas de roll kombinationer som krävs för att lägga till resurser i en katalog. Om du vill ta bort resurser från en katalog måste du ha samma roller.

| Azure AD-katalog roll | Rättighets hanterings roll | Kan lägga till säkerhets grupp | Kan lägga till Office 365-gruppen | Kan lägga till app | Kan lägga till SharePoint Online-webbplats |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Global administratör](../users-groups-roles/directory-assign-admin-roles.md) | saknas |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Användaradministratör](../users-groups-roles/directory-assign-admin-roles.md) | saknas |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Intune-administratör](../users-groups-roles/directory-assign-admin-roles.md) | Katalog ägare | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange-administratör](../users-groups-roles/directory-assign-admin-roles.md) | Katalog ägare |  | :heavy_check_mark: |  |  |
| [Team tjänst administratör](../users-groups-roles/directory-assign-admin-roles.md) | Katalog ägare |  | :heavy_check_mark: |  |  |
| [SharePoint-administratör](../users-groups-roles/directory-assign-admin-roles.md) | Katalog ägare |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Program administratör](../users-groups-roles/directory-assign-admin-roles.md) | Katalog ägare |  |  | :heavy_check_mark: |  |
| [Moln program administratör](../users-groups-roles/directory-assign-admin-roles.md) | Katalog ägare |  |  | :heavy_check_mark: |  |
| Användare | Katalog ägare | Endast om grupp ägare | Endast om grupp ägare | Endast om app-ägare |  |

> [!NOTE]
> Om en användare lägger till en säkerhets grupp eller en Office 365-grupp kan gruppen inte vara roll tilldelnings bara. Om användaren lägger till en grupp som är roll tilldelnings bara när de skapar åtkomst paketet måste de också vara ägare till den roll tilldelnings bara gruppen. Om du vill ha mer information [skapar du en roll tilldelnings grupp i Azure Active Directory](../users-groups-roles/roles-groups-create-eligible.md).

Om du vill fastställa den lägsta privilegierade rollen för en aktivitet kan du också referera till [Administratörs roller efter administratörs uppgift i Azure Active Directory](../users-groups-roles/roles-delegate-by-task.md#entitlement-management).

## <a name="next-steps"></a>Nästa steg

- [Delegera åtkomst styrning till katalog skapare](entitlement-management-delegate-catalog.md)
- [Skapa och hantera en katalog med resurser](entitlement-management-catalog-create.md)
