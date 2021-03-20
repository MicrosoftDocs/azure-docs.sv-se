---
title: Identifiera Azure-resurser som ska hanteras i PIM – Azure AD | Microsoft Docs
description: Lär dig hur du identifierar Azure-resurser som ska hanteras i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 09/29/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42d38990289169412f06b0c7e4bcbdf67f688da7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99539017"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>Identifiera Azure-resurser som ska hanteras i Privileged Identity Management

Med hjälp av Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kan du förbättra skyddet av dina Azure-resurser. Detta är användbart för att:

- Organisationer som redan använder Privileged Identity Management för att skydda Azure AD-roller
- Hanterings grupp och prenumerations ägare som försöker skydda produktions resurser

När du först konfigurerar Privileged Identity Management för Azure-resurser måste du identifiera och välja de resurser som du vill skydda med Privileged Identity Management. Det finns ingen gräns för antalet resurser som du kan hantera med Privileged Identity Management. Vi rekommenderar dock att du börjar med de mest kritiska produktions resurserna.

## <a name="discover-resources"></a>Identifiera resurser

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure-resurser**.

    Om det här är första gången du använder Privileged Identity Management för Azure-resurser visas sidan **identifiera resurser** .

    ![Fönstret identifiera resurser utan resurser som visas för första gången](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Om en annan administratör i din organisation redan hanterar Azure-resurser i Privileged Identity Management visas en lista över de resurser som hanteras för närvarande.

    ![Fönstret identifiera resurser som visar resurser som för närvarande hanteras](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Välj **identifiera resurser** för att starta identifierings upplevelsen.

    ![I identifierings fönstret visas resurser som kan hanteras, till exempel prenumerationer och hanterings grupper](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. På sidan **identifiering** använder du **filtret resurs tillstånd** och **väljer resurs typ** för att filtrera hanterings grupper eller prenumerationer som du har Skriv behörighet till. Det är förmodligen enklast att börja med **alla** inlednings vis.

   Du kan söka efter och välja hanterings grupp eller prenumerations resurser som du vill hantera i Privileged Identity Management. När du hanterar en hanterings grupp eller en prenumeration i Privileged Identity Management kan du också hantera dess underordnade resurser.

   > [!Note]
   > När du lägger till en ny underordnad Azure-resurs i en PIM-hanterad hanterings grupp kan du ta den underordnade resursen under hantering genom att söka efter den i PIM.

1. Välj eventuella ohanterade resurser som du vill hantera.

1. Välj **Hantera resurs** för att börja hantera de valda resurserna.

    > [!NOTE]
    > När en hanterings grupp eller prenumeration hanteras kan den inte hanteras. Detta förhindrar att en annan resurs administratör tar bort Privileged Identity Management inställningar.

    ![Identifierings fönstret med en resurs vald och alternativet hantera resurs är markerat](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Om du ser ett meddelande om att bekräfta onboarding av den valda resursen för hantering väljer du **Ja**. PIM konfigureras sedan för att hantera alla nya och befintliga underordnade objekt under resurs (er).

    ![Meddelande som bekräftar att de valda resurserna har publicerats för hantering](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera inställningar för Azure-resurs roll i Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Tilldela Azure-resurs roller i Privileged Identity Management](pim-resource-roles-assign-roles.md)
