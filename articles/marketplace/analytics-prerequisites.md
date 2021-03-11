---
title: Förutsättningar för att få åtkomst till Analytics-data via programmering
description: Lär dig de krav som du behöver uppfylla innan du kan komma åt data från kommersiella Marketplace-analyser.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 3b109048be4a94990c26e31aa5bc2ad36fdd0211
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584183"
---
# <a name="prerequisites-to-programmatically-access-analytics-data"></a>Förutsättningar för att få åtkomst till Analytics-data via programmering

Innan du kan komma åt data på kommersiella Marketplace-analyser måste du uppfylla följande krav.

## <a name="commercial-marketplace-enrollment"></a>Registrering av handels-Marketplace

För att få åtkomst till kommersiella Marketplace Analytics-data program mässigt måste du vara registrerad i det kommersiella Marketplace-programmet och ha ett partner Center-konto. Mer information finns i [skapa ett kommersiellt marknads plats konto i Partner Center](./partner-center-portal/create-account.md).

## <a name="create-azure-active-directory-application"></a>Skapa Azure Active Directory program

Vanliga autentiseringsuppgifter för användare kan inte användas för programmerings åtkomst av kommersiella data för Marketplace-analys. Ett Azure Active Directory-program (Azure AD) måste skapas tillsammans med en hemlighet för att komma åt analys-API: erna. Information om hur du skapar ett Azure AD-program och en hemlighet finns i [snabb start: registrera ett program med Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

## <a name="associate-the-azure-ad-application-to-the-partner-center-tenant"></a>Koppla Azure AD-programmet till Partner Center-klienten

Azure AD-programmet som du skapade i Azure Portal måste kopplas till ditt partner Center-konto. Stegen är följande:

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard).
1. Välj kugg hjuls ikonen i det övre högra hörnet och välj sedan **konto inställningar**.
1. I menyn **konto inställningar** väljer du **användar hantering**.
1. Välj **Azure AD-program** och välj sedan **+ Skapa Azure AD-program**.
1. Välj det Azure AD-program som du skapade på Azure Portal och välj sedan **Nästa**.
1. Markera kryss rutan **chef (Windows)** och välj sedan **Lägg till**.

    :::image type="content" source="./media/analytics-programmatic-access/azure-ad-roles.png" alt-text="Visar sidan Skapa Azure AD-program med kryss rutorna för att välja roller.":::

## <a name="generate-an-azure-ad-token"></a>Generera en Azure AD-token

Du måste skapa en Azure AD-token med hjälp av program-ID: t (klient). Detta ID hjälper till att unikt identifiera ditt klient program i Microsoft Identity Platform och klient hemligheten från föregående steg. Anvisningar om hur du skapar en Azure AD-token finns i [tjänst-till-tjänst-anrop med klientautentiseringsuppgifterna (delad hemlighet eller certifikat)](https://docs.microsoft.com/azure/active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow).

> [!NOTE]
> Token är giltig i en timme.

## <a name="next-steps"></a>Nästa steg

- [Paradigm för program mässig åtkomst](analytics-programmatic-access.md)
