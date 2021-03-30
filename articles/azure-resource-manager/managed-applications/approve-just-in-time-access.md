---
title: Godkänna just-in-time-åtkomst
description: Beskriver hur användare av Azure Managed Applications godkänner begär Anden för just-in-Time-åtkomst till ett hanterat program.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 4a3604a3256fb2d3f4253891bbc28c7685748cd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75651025"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Konfigurera och godkänn just-in-Time-åtkomst för Azure Managed Applications

Som konsument för ett hanterat program kanske du inte är bekant med att ge Publisher permanent åtkomst till den hanterade resurs gruppen. För att ge dig större kontroll över beviljandet av åtkomst till hanterade resurser Azure Managed Applications tillhandahåller en funktion som kallas JIT-åtkomst (just-in-Time), som för närvarande är en för hands version. Du kan godkänna när och hur länge utgivaren har åtkomst till resurs gruppen. Utgivaren kan göra nödvändiga uppdateringar under den tiden, men när tiden är över går utgivarens åtkomst ut.

Arbets flödet för att bevilja åtkomst är:

1. Utgivaren lägger till ett hanterat program på Marketplace och anger att JIT-åtkomst är tillgänglig.

1. Under distributionen aktiverar du JIT-åtkomst för din instans av det hanterade programmet.

1. Efter distributionen kan du ändra inställningarna för JIT-åtkomst.

1. Utgivaren skickar en begäran om åtkomst.

1. Du godkänner begäran.

Den här artikeln fokuserar på de åtgärder som kunderna vidtar för att aktivera JIT-åtkomst och godkänna begär Anden. Information om hur du publicerar ett hanterat program med JIT-åtkomst finns i [begär åtkomst till just-in-Time i Azure Managed Applications](request-just-in-time-access.md).

> [!NOTE]
> Om du vill använda just-in-Time-åtkomst måste du ha en [Azure Active Directory P2-licens](../../active-directory/privileged-identity-management/subscription-requirements.md).

## <a name="enable-during-deployment"></a>Aktivera under distribution

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Hitta en Marketplace-post för ett hanterat program med JIT aktiverat. Välj **Skapa**.

1. När du anger värden för det nya hanterade programmet kan du med **JIT-konfigurationen** aktivera eller inaktivera JIT-åtkomst för det hanterade programmet. Välj **Ja** om du vill **Aktivera JIT-åtkomst**. Det här alternativet är valt som standard för hanterade program som har definierats med JIT aktiverat i Marketplace.

   ![Konfigurera åtkomst](./media/approve-just-in-time-access/configure-jit-access.png)

   Du kan bara aktivera JIT-åtkomst under distributionen. Om du väljer **Nej** får utgivaren permanent åtkomst till den hanterade resurs gruppen. Du kan inte aktivera JIT-åtkomst senare.

1. Om du vill ändra standardinställningarna för godkännande väljer du **Anpassa JIT-konfiguration**.

   ![Anpassa åtkomst](./media/approve-just-in-time-access/customize-jit-access.png)

   Som standard har ett hanterat program med JIT aktiverat följande inställningar:

   * Godkännande läge – automatiskt
   * Maximal åtkomst varaktighet – 8 timmar
   * God kännare – ingen

   När godkännande läget är inställt på **automatiskt** får god kännare ett meddelande för varje begäran, men begäran godkänns automatiskt. När god kännare är inställd på **manuell** får de ett meddelande för varje begäran och en av dem måste godkänna det.

   Maximal varaktighet för aktivering anger den maximala tid som en utgivare kan begära åtkomst till den hanterade resurs gruppen.

   Listan god kännare är Azure Active Directory användare som kan godkänna JIT-begäranden. Om du vill lägga till en god kännare väljer du **Lägg till god kännare** och Sök efter användaren.

   När du har uppdaterat inställningen väljer du **Spara**.

## <a name="update-after-deployment"></a>Uppdatera efter distribution

Du kan ändra värdena för hur förfrågningarna ska godkännas. Men om du inte har aktiverat JIT-åtkomst under distributionen kan du inte aktivera det senare.

Ändra inställningarna för ett distribuerat hanterat program:

1. Välj alternativet hantera program i portalen.

1. Välj **JIT-konfiguration** och ändra inställningarna efter behov.

   ![Ändra åtkomst inställningar](./media/approve-just-in-time-access/change-settings.png)

1. Välj **Spara** när du är klar.

## <a name="approve-requests"></a>Godkänn förfrågningar

När utgivaren begär åtkomst får du ett meddelande om begäran. Du kan godkänna JIT-åtkomst begär Anden antingen direkt via det hanterade programmet eller i alla hanterade program via Azure AD Privileged Identity Management tjänsten. Om du vill använda just-in-Time-åtkomst måste du ha en [Azure Active Directory P2-licens](../../active-directory/privileged-identity-management/subscription-requirements.md).

För att godkänna begär Anden via det hanterade programmet:

1. Välj **JIT-åtkomst** för det hanterade programmet och välj **Godkänn begär Anden**.

   ![Godkänn förfrågningar](./media/approve-just-in-time-access/approve-requests.png)
 
1. Välj begäran att godkänna.

   ![Välj begäran](./media/approve-just-in-time-access/select-request.png)

1. Ange orsaken till godkännandet i formuläret och välj **Godkänn**.

För att godkänna begär Anden via Azure AD Privileged Identity Management:

1. Välj **alla tjänster** och börja söka efter **Azure AD Privileged Identity Management**. Välj den från de tillgängliga alternativen.

   ![Sök efter tjänst](./media/approve-just-in-time-access/search.png)

1. Välj **Godkänn begär Anden**.

   ![Välj Godkänn begär Anden](./media/approve-just-in-time-access/select-approve-requests.png)

1. Välj **Azure-hanterade program** och välj begäran att godkänna.

   ![Välj begär Anden](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>Nästa steg

Information om hur du publicerar ett hanterat program med JIT-åtkomst finns i [begär åtkomst till just-in-Time i Azure Managed Applications](request-just-in-time-access.md).
