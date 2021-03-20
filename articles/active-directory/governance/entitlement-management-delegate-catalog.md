---
title: Delegera åtkomst styrning till katalog skapare i Azure AD-hantering av rättigheter – Azure Active Directory
description: Lär dig hur du delegerar åtkomst styrning från IT-administratörer till katalog skapare och projektledare så att de kan hantera åtkomst själva.
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
ms.openlocfilehash: e41f372cd0105896765d5a267456b6bda767dd4d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87798434"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Delegera åtkomst styrning till katalog skapare i hantering av Azure AD-berättigande

En katalog är en behållare för resurser och åtkomst paket. Du skapar en katalog när du vill gruppera relaterade resurser och åtkomst paket. Som standard kan en global administratör eller en användar administratör [skapa en katalog](entitlement-management-catalog-create.md), och kan lägga till fler användare som katalog ägare.

Om du vill delegera till användare som inte är administratörer, så att de kan skapa sina egna kataloger, kan du lägga till dessa användare i rollen Azure AD-rättighets hantering-definierad katalog skapare. Du kan lägga till enskilda användare eller lägga till en grupp, vars medlemmar sedan kan skapa kataloger.  När du har skapat en katalog kan de därefter lägga till resurser som de äger i sin katalog.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>Delegera till en katalog skapare som IT-administratör

Följ dessa steg om du vill tilldela en användare rollen som katalog skapare.

**Nödvändig roll:** Global administratör eller användar administratör

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. På den vänstra menyn i avsnittet **rättighets hantering** klickar du på **Inställningar**.

1. Klicka på **Redigera**.

    ![Inställningar för att lägga till katalog skapare](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. I avsnittet **delegera rättighets hantering** klickar du på **Lägg till katalog skapare** för att välja de användare eller grupper som du vill delegera den här rättighets hanterings rollen till.

1. Klicka på **Välj**.

1. Klicka på **Spara**.

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>Tillåt delegerade roller att komma åt Azure Portal

Om du vill tillåta delegerade roller, t. ex. katalog skapare och åtkomst paket hanterare, för att komma åt Azure Portal att hantera åtkomst paket, bör du kontrol lera inställningen administrations Portal.

**Nödvändig roll:** Global administratör eller användar administratör

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **användare**.

1. På den vänstra menyn klickar du på **användar inställningar**.

1. Se till att **begränsa åtkomsten till Azure AD administrations Portal** är inställd på **Nej**.

    ![Användar inställningar för Azure AD – administrations portalen](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>Nästa steg

- [Skapa och hantera en katalog med resurser](entitlement-management-catalog-create.md)
- [Delegera åtkomst styrning för att komma åt paket hanterare](entitlement-management-delegate-managers.md)

