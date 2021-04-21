---
title: Tilldela en Azure Key Vault åtkomstprincip (portal)
description: Hur du använder Azure Portal för att tilldela en Key Vault åtkomstprincip till ett säkerhetsobjekt eller en programidentitet.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 443b269e7155fc206ee50e7907a7acded2c22f53
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751498"
---
# <a name="assign-a-key-vault-access-policy-using-the-azure-portal"></a>Tilldela en Key Vault åtkomstprincip med hjälp av Azure Portal

En Key Vault-åtkomstprincip avgör om ett visst säkerhetsobjekt, nämligen en användare, ett program eller en användargrupp, kan utföra olika åtgärder på Key Vault [hemligheter,](../secrets/index.yml) [nycklar](../keys/index.yml)och [certifikat.](../certificates/index.yml) Du kan tilldela åtkomstprinciper med hjälp av Azure Portal (den här artikeln), [Azure CLI](assign-access-policy-cli.md) [eller Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Mer information om hur du skapar grupper i Azure Active Directory via Azure Portal finns i [Skapa en grundläggande grupp och lägga till medlemmar](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

## <a name="assign-an-access-policy"></a>Tilldela en åtkomstprincip

1.  I [Azure Portal](https://portal.azure.com)navigerar du till Key Vault resurs. 

1.  Under **Inställningar** väljer du **Åtkomstprinciper** och sedan Lägg **till åtkomstprincip:**

    ![Välj Åtkomstprinciper och sedan Lägg till rolltilldelning](../media/authentication/assign-policy-portal-01.png)

1.  Välj de behörigheter som du vill använda under **Certifikatbehörigheter,** **Nyckelbehörigheter** och **Hemliga behörigheter.** Du kan också välja en mall som innehåller vanliga behörighetskombinationer:

    ![Ange behörigheter för åtkomstprincip](../media/authentication/assign-policy-portal-02.png)

1. Under **Välj huvudnamn** väljer du **länken Ingen vald** för att öppna **markeringsfönstret** Huvudnamn. Ange namnet på användaren, appen eller tjänstens huvudnamn i sökfältet, välj lämpligt resultat och välj sedan **Välj**.

    ![Välja säkerhetsobjekt för åtkomstprincipen](../media/authentication/assign-policy-portal-03.png)

    Om du använder en hanterad identitet för appen söker du efter och väljer namnet på själva appen. (Mer information om hanterade identiteter och tjänstens huvudnamn finns [i Key Vault autentisering – appidentitet och tjänstens huvudnamn](authentication.md#app-identity-and-security-principals).)
 
1.  I fönstret Lägg **till åtkomstprincip väljer** du Lägg **till för** att spara åtkomstprincipen.

    ![Lägga till åtkomstprincipen med det tilldelade säkerhetsobjekt](../media/authentication/assign-policy-portal-04.png)

1. På sidan **Åtkomstprinciper** kontrollerar du att din åtkomstprincip visas under **Aktuella åtkomstprinciper** och väljer sedan **Spara.** Åtkomstprinciper tillämpas inte förrän du sparar dem.

    ![Spara ändringar i åtkomstprincipen](../media/authentication/assign-policy-portal-05.png)


## <a name="next-steps"></a>Nästa steg

- [Azure Key Vault: Identitets- och åtkomsthantering](security-overview.md#identity-management)
- [Skydda ditt nyckelvalv.](security-overview.md)
- [Azure Key Vault utvecklarguide](developers-guide.md)