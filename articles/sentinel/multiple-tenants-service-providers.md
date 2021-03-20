---
title: Hantera flera klienter i Azure Sentinel som en hanterad säkerhets tjänst leverantör | Microsoft Docs
description: Publicera och hantera flera klienter i Azure Sentinel som en hanterad säkerhets tjänst leverantör (MSSP) med hjälp av Azure Lighthouse.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2020
ms.author: yelevin
ms.openlocfilehash: 011ddb883c028a954a8b0683c220bf6341eddb66
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91578162"
---
# <a name="manage-multiple-tenants-in-azure-sentinel-as-an-mssp"></a>Hantera flera klienter i Azure Sentinel som en MSSP

Om du är en hanterad säkerhets tjänst leverantör (MSSP) och använder [Azure Lighthouse](../lighthouse/overview.md) för att erbjuda dina kunder Security Operations Center-tjänster (SOC) kan du hantera dina kunders Azure Sentinel-resurser direkt från din egen Azure-klient, utan att behöva ansluta till kundens klient. 

## <a name="prerequisites"></a>Förutsättningar

- [Publicera Azure-Lighthouse](../lighthouse/how-to/onboard-customer.md)

- För att detta ska fungera korrekt måste klienten (MSSP-klienten) ha Azure Sentinel-resursposterna registrerade på minst en prenumeration. Dessutom måste var och en av dina kunders klienter ha de registrerade resurs leverantörerna. Om du har registrerat Azure Sentinel i din klient, och dina kunder i deras, är du redo att komma igång. Utför följande steg för att verifiera registreringen:

    1. Välj **prenumerationer** från Azure Portal och välj sedan en relevant prenumeration på menyn.

    1. I navigerings menyn på prenumerations skärmen under **Inställningar** väljer du **resurs leverantörer**.

    1. Från ***prenumerations namnet* | Fönstret resurs leverantörer** , Sök efter och välj *Microsoft. OperationalInsights* och *Microsoft. SecurityInsights* och kontrol lera kolumnen **status** . Om leverantörens status är *NotRegistered* väljer du **Registrera**.
    
        :::image type="content" source="media/multiple-tenants-service-providers/check-resource-provider.png" alt-text="Kontrol lera resurs leverantörer":::

## <a name="how-to-access-azure-sentinel-in-managed-tenants"></a>Få åtkomst till Azure Sentinel i hanterade klienter

1. Under **katalog + prenumeration** väljer du de delegerade katalogerna (Directory = Tenant) och de prenumerationer där kundens Azure Sentinel-arbetsytor finns.

    :::image type="content" source="media/multiple-tenants-service-providers/directory-subscription.png" alt-text="Välj innehavare och prenumerationer":::

1. Öppna Azure Sentinel. Du ser alla arbets ytor i de valda prenumerationerna och du kommer att kunna arbeta med dem sömlöst, som alla arbets ytor i din egen klient organisation.

> [!NOTE]
> Du kommer inte att kunna distribuera kopplingar i Azure Sentinel inifrån en hanterad arbets yta. Om du vill distribuera en anslutning måste du direkt logga in på den klient som du vill distribuera en anslutning till och autentisera där med de behörigheter som krävs.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du hanterar flera Azure Sentinel-klienter sömlöst. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).

