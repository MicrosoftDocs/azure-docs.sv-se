---
title: MAU fakturerings modell för externa Azure AD-identiteter
description: Lär dig mer om Azure AD external-identiteter månads aktiva användare (MAU) fakturerings modell för gäst användar samarbete (B2B) i Azure AD. Lär dig hur du länkar din Azure AD-klient till en Azure-prenumeration.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.workload: identity
ms.collection: M365-identity-device-management
ms.openlocfilehash: 983a803245467145a0b1161a4495e8045759e7ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92442073"
---
# <a name="billing-model-for-azure-ad-external-identities"></a>Fakturerings modell för externa Azure AD-identiteter

Priser för Azure Active Directory (Azure AD) externa identiteter baseras på månatliga aktiva användare (MAU), vilket är antalet unika användare med autentiserings aktivitet inom en kalender månad. Den här fakturerings modellen gäller både B2B (Azure AD gäst användar samarbete) och [Azure AD B2C klienter](../../active-directory-b2c/billing.md). MAU-faktureringen hjälper dig att minska kostnaderna genom att erbjuda en kostnads fri nivå och flexibel, förutsägbar prissättning. I den här artikeln får du lära dig om hur du MAU fakturering och länkar dina Azure AD-klienter till en prenumeration.

> [!IMPORTANT]
> Den här artikeln innehåller ingen pris information. Den senaste informationen om fakturering och prissättning för användning finns [Azure Active Directory prissättning](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-i-need-to-do"></a>Vad behöver jag göra?

Din Azure AD-klient måste vara kopplad till en Azure-prenumeration för att kunna dra nytta av MAU-fakturering.

|Om din klient organisation är:  |Du måste:  |
|---------|---------|
| En Azure AD-klient är redan länkad till en prenumeration     | Gör ingenting. När du använder externa identiteter för att samar beta med gäst användare debiteras du automatiskt med hjälp av MAU-modellen.        |
| En Azure AD-klient har ännu inte länkats till en prenumeration     | [Länka din Azure AD-klient till en prenumeration](#link-your-azure-ad-tenant-to-a-subscription) för att aktivera Mau fakturering.        |
|  |  |

## <a name="about-monthly-active-users-mau-billing"></a>Om månatliga aktiva användare (MAU) fakturering

I din Azure AD-klient debiteras användningen av gäst användar samarbete baserat på antalet unika gäst användare med autentisering under en kalender månad. Den här modellen ersätter förhållandet mellan 1:5 och fakturerings modell, som tillåter upp till fem gäst användare för varje Azure AD Premium licens i din klient organisation. När klienten är länkad till en prenumeration och du använder externa identiteter för att samar beta med gäst användare debiteras du automatiskt med hjälp av den MAU fakturerings modellen.
  
Den pris nivå som gäller för gäst användarna baseras på den högsta pris nivå som tilldelats din Azure AD-klient. Om den högsta pris nivån i din klient organisation är Azure AD Premium P1, gäller till exempel pris nivån Premium P1 även för gäst användarna. Om den högsta prissättningen är Azure AD Free uppmanas du att uppgradera till en Premium pris nivå när du försöker använda Premium funktioner för gäst användare.

## <a name="link-your-azure-ad-tenant-to-a-subscription"></a>Länka din Azure AD-klient till en prenumeration

En Azure AD-klient måste vara länkad till en Azure-prenumeration för korrekt fakturering och åtkomst till funktioner. Om katalogen inte redan har en prenumeration som du kan länka till, har du möjlighet att lägga till en under den här processen.

1. Logga in på [Azure Portal](https://portal.azure.com/) med ett Azure-konto som har tilldelats minst [deltagar](../../role-based-access-control/built-in-roles.md) rollen i prenumerationen eller en resurs grupp i prenumerationen.

2. Välj den katalog som du vill länka: i Azure Portal verktygsfältet väljer du ikonen **katalog + prenumeration** och välj sedan katalogen.

    ![Välj ikonen katalog + prenumeration](media/external-identities-pricing/portal-mau-pick-directory.png)

3. Under **Azure-tjänster** väljer du **Azure Active Directory**.

4. På den vänstra menyn väljer du **externa identiteter**.

5. Under **prenumerationer** väljer du **länkade prenumerationer**.

6. I listan klient väljer du kryss rutan bredvid klienten och väljer sedan **länk prenumeration**.

    ![Välj klienten och länka en prenumeration](media/external-identities-pricing/linked-subscriptions.png)

7. I fönstret länka en prenumeration väljer du en **prenumeration** och en **resurs grupp**. Välj sedan **Använd**.

   > [!NOTE]
   > Om det inte finns några prenumerationer i listan kan du [associera en prenumeration till din klient organisation](../fundamentals/active-directory-how-subscriptions-associated-directory.md). Du kan också lägga till en ny prenumeration genom att välja länken **om du inte redan har en prenumeration du kan skapa en här**.

    ![Välj en prenumeration och resurs grupp](media/external-identities-pricing/link-subscription-resource.png)

När du har slutfört de här stegen faktureras din Azure-prenumeration baserat på din Azure Direct-eller Enterprise-avtal information, om tillämpligt.

## <a name="next-steps"></a>Nästa steg

Den senaste pris informationen finns i [Azure Active Directory prissättning](https://azure.microsoft.com/pricing/details/active-directory/).