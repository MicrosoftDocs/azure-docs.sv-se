---
title: Börja använda PIM – Azure Active Directory | Microsoft Docs
description: Lär dig hur du aktiverar och kommer igång med Azure AD Privileged Identity Management (PIM) i Azure Portal.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: how-to
ms.workload: identity
ms.date: 09/15/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bcfb21ab15355653780355f1b5e459bc806ec8c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90600729"
---
# <a name="start-using-privileged-identity-management"></a>Börja använda Privileged Identity Management

I den här artikeln beskrivs hur du aktiverar Privileged Identity Management (PIM) och kommer igång med det.

Använd Privileged Identity Management (PIM) för att hantera, kontrol lera och övervaka åtkomst i din Azure Active Directory (Azure AD)-organisation. Med PIM kan du tillhandahålla nödvändiga och just-in-Time-åtkomst till Azure-resurser, Azure AD-resurser och andra Microsoft-onlinetjänster som Microsoft 365 eller Microsoft Intune.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda Privileged Identity Management måste du ha någon av följande licenser:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Mer information finns i [licens krav för att använda Privileged Identity Management](subscription-requirements.md).

> [!Note]
> När en användare som är aktiv i en privilegie rad roll i en Azure AD-organisation med Premium P2-licens går till **roller och administratörer** i Azure AD och väljer en roll (eller till och med bara Privileged Identity Management):
>
> - Vi aktiverar automatiskt PIM för organisationen
> - Deras upplevelse är nu att de antingen kan tilldela en "vanlig" roll tilldelning eller en berättigad roll tilldelning
>
> När PIM är aktiverat har det inte någon annan inverkan på din organisation som du behöver oroa dig för. Du får ytterligare tilldelnings alternativ, till exempel aktiva kontra berättigade med start-och slut tid. Med PIM kan du också definiera omfång för roll tilldelningar med hjälp av administrativa enheter och anpassade roller. Om du är global administratör eller administratör för privilegierade roller kan du börja få ytterligare e-postmeddelanden som PIM veckovis sammandrag. Du kan också se MS-PIM-tjänstens huvud namn i gransknings loggen som är relaterad till roll tilldelningen. Detta är en förväntad ändring som inte ska påverka arbets flödet.

## <a name="prepare-pim-for-azure-ad-roles"></a>Förbereda PIM för Azure AD-roller

Här är de uppgifter som vi rekommenderar för att förbereda Privileged Identity Management för att hantera Azure AD-roller:

1. [Konfigurera inställningar för Azure AD-roller](pim-how-to-change-default-settings.md).
1. [Ge berättigade tilldelningar](pim-how-to-add-role-to-user.md).
1. [Tillåt att berättigade användare aktiverar sin Azure AD-roll just-in-Time](pim-how-to-activate-role.md).

## <a name="prepare-pim-for-azure-roles"></a>Förbereda PIM för Azure-roller

Här är de uppgifter vi rekommenderar att du förbereder Privileged Identity Management för att hantera Azure-roller för en prenumeration:

1. [Identifiera Azure-resurser](pim-resource-roles-discover-resources.md)
1. [Konfigurera inställningar för Azure-roller](pim-resource-roles-configure-role-settings.md).
1. [Ge berättigade tilldelningar](pim-resource-roles-assign-roles.md).
1. [Tillåt att berättigade användare aktiverar sina Azure-roller just-in-Time](pim-resource-roles-activate-your-roles.md).

## <a name="navigate-to-your-tasks"></a>Gå till dina uppgifter

När Privileged Identity Management har kon figurer ATS kan du lära dig hur du gör.

![Navigerings fönster i Privileged Identity Management visar uppgifter och hantera alternativ](./media/pim-getting-started/pim-quickstart-tasks.png)

| Aktivitet + hantera | Beskrivning |
| --- | --- |
| **Mina roller**  | Visar en lista över giltiga och aktiva roller som har tilldelats dig. Här kan du aktivera tilldelade berättigade roller. |
| **Mina förfrågningar** | Visar väntande begär Anden om att aktivera kvalificerade roll tilldelningar. |
| **Godkänn förfrågningar** | Visar en lista med begär Anden om att aktivera berättigade roller av användare i din katalog som du är behörig att godkänna. |
| **Granska åtkomst** | Visar en lista över aktiva åtkomst granskningar som du har tilldelats, oavsett om du granskar åtkomst åt dig själv eller någon annan. |
| **Azure AD-roller** | Visar en instrument panel och inställningar för privilegierade roll administratörer för att hantera roll tilldelningar för Azure AD. Instrumentpanelen är inaktiverad för alla som inte är en privilegierad rolladministratör. De här användarna har åtkomst till en särskild instrumentpanel som heter My view (Min vy). På instrument panelen för min vy visas endast information om användaren som har åtkomst till instrument panelen, inte hela organisationen. |
| **Azure-resurser** | Visar en instrument panel och inställningar för privilegierade roll administratörer för hantering av roll tilldelningar för Azure-resurser. Instrumentpanelen är inaktiverad för alla som inte är en privilegierad rolladministratör. De här användarna har åtkomst till en särskild instrumentpanel som heter My view (Min vy). På instrument panelen för min vy visas endast information om användaren som har åtkomst till instrument panelen, inte hela organisationen. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Lägg till en PIM-panel på instrument panelen

För att göra det lättare att öppna Privileged Identity Management lägger du till en PIM-panel i Azure Portal instrument panelen.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj **alla tjänster** och Sök efter tjänsten **Azure AD Privileged Identity Management** .

    ![Azure AD Privileged Identity Management i alla tjänster](./media/pim-getting-started/pim-all-services-find.png)

1. Välj **snabb start** för Privileged Identity Management.

1. Välj **Fäst bladet på instrument panelen** för att fästa Privileged Identity Management **snabb start** sidan på instrument panelen.

    ![Ikon för kartnål för att fästa Privileged Identity Management sida på instrument panelen](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    På Azure-instrumentpanelen ser du en panel så här:

    ![Privileged Identity Management snabb starts panel på instrument panelen](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure AD-roller i Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Hantera åtkomst till Azure-resurser i Privileged Identity Management](pim-resource-roles-discover-resources.md)
