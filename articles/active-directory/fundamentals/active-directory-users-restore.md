---
title: Återställ eller ta bort nyligen borttagen användare – Azure AD
description: Visa återställas-användare, återställa en borttagen användare eller ta bort en användare permanent med Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/23/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd4d42e1cafa58aaf6a47188c9e86c24ac175047
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92514397"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Återställa eller ta bort en nyligen borttagen användare med hjälp av Azure Active Directory
När du har tagit bort en användare förblir kontot i ett pausat tillstånd i 30 dagar. Under den 30-dagars perioden kan användar kontot återställas, tillsammans med alla dess egenskaper. När 30-dagars perioden har passerat startas den permanenta borttagnings processen automatiskt.

Du kan visa dina återställas-användare, återställa en borttagen användare eller permanent ta bort en användare med hjälp av Azure Active Directory (Azure AD) i Azure Portal.

>[!Important]
>Varken du eller Microsofts kund support kan återställa en permanent borttagen användare.

## <a name="required-permissions"></a>Behörigheter som krävs
Du måste ha någon av följande roller för att kunna återställa och ta bort användare permanent.

- Global administratör

- Support för partner 1

- Support för partner – nivå 2

- Användaradministratör

## <a name="view-your-restorable-users"></a>Visa dina återställas-användare
Du kan se alla användare som har tagits bort under 30 dagar sedan. Dessa användare kan återställas.

### <a name="to-view-your-restorable-users"></a>Visa dina återställas-användare
1. Logga in på [Azure Portal](https://portal.azure.com/) med ett globalt administratörs konto för organisationen.

2. Välj **Azure Active Directory**, Välj **användare** och välj sedan **borttagna användare**.

    Granska listan över användare som är tillgängliga för återställning.

    ![Sidan användare – borttagna användare med användare som fortfarande kan återställas](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Återställa en nyligen borttagen användare

När ett användar konto tas bort från organisationen är kontot i ett inaktiverat tillstånd och all relaterad organisations information bevaras. När du återställer en användare återställs även den här organisations informationen.

> [!Note]
> När en användare återställs återställs även licenser som tilldelats användaren vid tidpunkten för borttagning även om det inte finns några platser tillgängliga för dessa licenser. Om du sedan förbrukar fler licenser mer än vad du har köpt kan din organisation tillfälligt vara inkompatibel med licens användningen.

### <a name="to-restore-a-user"></a>Återställa en användare
1. På sidan **användare – borttagna användare** söker du efter och väljer en av de tillgängliga användarna. Till exempel _Mary parker_.

2. Välj **Återställ användare**.

    ![Sidan användare – borttagna användare med alternativet Återställ användare markerat](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>Ta bort en användare permanent
Du kan ta bort en användare från din organisation Permanent utan att vänta 30 dagar för automatisk borttagning. En permanent borttagen användare kan inte återställas av dig, en annan administratör eller av Microsofts kund support.

>[!Note]
>Om du tar bort en användare permanent av misstag måste du skapa en ny användare och ange all föregående information manuellt. Mer information om hur du skapar en ny användare finns i [Lägg till eller ta bort användare](add-users-azure-active-directory.md).

### <a name="to-permanently-delete-a-user"></a>Ta bort en användare permanent

1. På sidan **användare – borttagna användare** söker du efter och väljer en av de tillgängliga användarna. Till exempel _Rae Huff_.

2. Välj **ta bort permanent**.

    ![Sidan användare – borttagna användare med alternativet ta bort användare markerat](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>Nästa steg
När du har återställt eller tagit bort dina användare kan du:

- [Lägga till eller ta bort användare](add-users-azure-active-directory.md)

- [Tilldela roller till användare](active-directory-users-assign-role-azure-portal.md)

- [Lägga till eller ändra profil information](active-directory-users-profile-azure-portal.md)

- [Lägga till gäst användare från en annan organisation](../external-identities/what-is-b2b.md)

Mer information om andra tillgängliga användar hanterings uppgifter, [dokumentation om användar hantering i Azure AD](../enterprise-users/index.yml).
