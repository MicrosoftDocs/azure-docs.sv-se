---
title: Hantera användare i Azure FarmBeats
description: Den här artikeln beskriver hur du hanterar användare i Azure-FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-ummehabiba
ms.openlocfilehash: de9a11cac9856417422bd72579cbca44233ab422
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179909"
---
# <a name="manage-users"></a>Hantera användare

Azure FarmBeats innehåller användar hantering för personer som är en del av din Azure Active Directory-instans (Azure AD). Du kan lägga till användare till din Azure FarmBeats-instans för att få åtkomst till API: erna, Visa de genererade kartorna och få åtkomst till sensor telemetri från Server gruppen.

## <a name="prerequisites"></a>Förutsättningar

- Installation av Azure-FarmBeats krävs. Mer information finns i [Installera Azure-FarmBeats](install-azure-farmbeats.md).
- E-ID för de användare som du vill lägga till eller ta bort från din Azure FarmBeats-instans.

## <a name="manage-azure-farmbeats-users"></a>Hantera Azure FarmBeats-användare

Azure FarmBeats använder Azure AD för autentisering, åtkomst kontroll och roller. Du kan lägga till användare i Azure AD-klienten som användare i Azure FarmBeats.

> [!NOTE]
> Om en användare inte är en Azure AD-innehavaradministratör följer du anvisningarna i avsnittet **Lägg till Azure AD-användare** för att slutföra installationen.

Azure FarmBeats stöder två typer av användar roller:

 - **Admin**: fullständig åtkomst till Azure FarmBeats Datahub-API: er. Användare med den här rollen kan fråga alla Azure FarmBeats Datahub-objekt och utföra alla åtgärder från FarmBeats-acceleratorn.
 - **Skrivskyddad**: skrivskyddad åtkomst till FarmBeats Datahub-API: er. Användare kan visa Datahub-API: er, Accelerator-instrumentpaneler och kartor. Användare med Läs behörighet kan inte utföra åtgärder som att generera kartor, associera enheter eller skapa Server grupper.

## <a name="add-users-to-azure-farmbeats"></a>Lägga till användare i Azure FarmBeats

Så här lägger du till användare i Azure FarmBeats:

1. Logga in på Accelerator och välj sedan ikonen **Inställningar** .
2. Välj **Access Control**.

    ![Fönstret inställningar för Server grupper](./media/create-farms-in-azure-farmbeats/settings-users-1.png)

3. Ange e-postadressen för den användare som du vill bevilja åtkomst till.
4. Välj den önskade rollen, **administratören** eller **Skriv skyddet**.
5. Välj **Lägg till roll**.

Den tillagda användaren kan nu komma åt Azure-FarmBeats (både Datahub och Accelerator).

## <a name="delete-users-from-azure-farmbeats"></a>Ta bort användare från Azure FarmBeats

Ta bort användare från Azure FarmBeats-systemet:

1. Logga in på Accelerator och välj sedan ikonen **Inställningar** .
2. Välj **Access Control**.
3. Välj **Ta bort**.

   Användaren tas bort från systemet. Du får följande bekräftelse meddelande:

   ![Bekräftelse meddelande för Azure-FarmBeats](./media/create-farms-in-azure-farmbeats/manage-users-2.png)

## <a name="add-azure-ad-users"></a>Lägg till Azure AD-användare

> [!NOTE]
> Azure FarmBeats-användare måste finnas i Azure AD-klienten innan du tilldelar dem till program och roller. Om en användare inte finns i Azure AD-klienten följer du anvisningarna i det här avsnittet. Hoppa över instruktionerna om det redan finns en användare i Azure AD-klienten.

Följ stegen för att lägga till användare i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Längst upp till höger väljer du ditt konto och växlar sedan till Azure AD-klienten som är associerad med FarmBeats.
3. Välj **Azure Active Directory**  >  **användare**.

    En lista med Azure AD-användare visas.

4. Om du vill lägga till en användare i katalogen väljer du **ny användare**. Om du vill lägga till en extern användare väljer du **ny gäst användare**.

    ![Fönstret alla användare](./media/create-farms-in-azure-farmbeats/manage-users-3.png)

5. Välj den nya användarens namn och fyll sedan i de obligatoriska fälten för den användaren.
6. Välj **Skapa**.

Information om hur du hanterar Azure AD-användare finns i [lägga till eller ta bort användare i Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="next-steps"></a>Nästa steg

Du har lagt till användare till din Azure FarmBeats-instans. Nu kan du lära dig att [skapa och hantera Server grupper](manage-farms-in-azure-farmbeats.md#create-farms).