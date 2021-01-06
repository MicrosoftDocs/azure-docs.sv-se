---
title: Tilldela en Azure Key Vault åtkomst princip (portal)
description: Använda Azure Portal för att tilldela en Key Vault åtkomst princip till ett huvud namn för tjänsten eller en program identitet.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 910b8dae10036cc2e396be13495fd28363dc971d
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934568"
---
# <a name="assign-a-key-vault-access-policy-using-the-azure-portal"></a>Tilldela en Key Vault åtkomst princip med hjälp av Azure Portal

En Key Vault åtkomst princip bestämmer om ett specifikt tjänstens huvud namn, nämligen ett program eller en användar grupp, kan utföra olika åtgärder på Key Vault [hemligheter](../secrets/index.yml), [nycklar](../keys/index.yml)och [certifikat](../certificates/index.yml). Du kan tilldela åtkomst principer med hjälp av Azure Portal (den här artikeln), [Azure CLI](assign-access-policy-cli.md)eller [Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Mer information om hur du skapar grupper i Azure Active Directory via Azure Portal finns i [skapa en grundläggande grupp och lägga till medlemmar](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

## <a name="assign-an-access-policy"></a>Tilldela en åtkomstprincip

1.  I [Azure Portal](https://portal.azure.com)navigerar du till Key Vault resursen. 

1.  Välj **åtkomst principer** under **Inställningar** och välj sedan **Lägg till åtkomst princip**:

    ![Välj åtkomst principer, Välj Lägg till roll tilldelning](../media/authentication/assign-policy-portal-01.png)

1.  Välj de behörigheter som du vill använda under **certifikat behörigheter**, **nyckel behörigheter** och **hemliga behörigheter**. Du kan också välja en mall som innehåller gemensamma behörighets kombinationer:

    ![Ange åtkomst princip behörigheter](../media/authentication/assign-policy-portal-02.png)

1. Under **Välj huvud konto** väljer du länken **ingen som marker ATS** för att öppna fönstret **huvud** val. Ange namnet på appen eller tjänstens huvud namn i Sök fältet, Välj lämpligt resultat och välj sedan **Välj**.

    ![Välja tjänstens huvud namn för åtkomst principen](../media/authentication/assign-policy-portal-03.png)

    Om du använder en hanterad identitet för appen söker du efter och väljer namnet på själva appen. (Mer information om hanterade identitets-och tjänst huvud namn finns i [Key Vault autentisering-app identitet och tjänstens huvud namn](authentication.md#app-identity-and-security-principals).)
 
1.  Tillbaka i fönstret **Lägg till åtkomst princip** väljer du **Lägg till** för att spara åtkomst principen.

    ![Lägger till åtkomst principen med den tilldelade tjänstens huvud namn](../media/authentication/assign-policy-portal-04.png)

1. Gå tillbaka till sidan **åtkomst principer** , kontrol lera att åtkomst principen visas under **aktuella åtkomst principer** och välj sedan **Spara**. Åtkomst principerna tillämpas inte förrän du sparar dem.

    ![Spara ändringar i åtkomst principen](../media/authentication/assign-policy-portal-05.png)


## <a name="next-steps"></a>Nästa steg

- [Azure Key Vault säkerhet: identitets-och åtkomst hantering](security-overview.md#identity-management)
- [Skydda nyckel valvet](secure-your-key-vault.md).
- [Guide för Azure Key Vault utvecklare](developers-guide.md)