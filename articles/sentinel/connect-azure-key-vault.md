---
title: Anslut Azure Key Vault diagnostikloggar till Azure Sentinel
description: Lär dig hur du använder Azure Policy för att ansluta Azure Key Vault diagnostikloggar till Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 03/07/2021
ms.author: yelevin
ms.openlocfilehash: 56587ae91de086cccb7cc85c125f935be2a56f73
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102491315"
---
# <a name="connect-azure-key-vault-diagnostics-and-auditing-logs"></a>Anslut Azure Key Vault diagnostik och gransknings loggar

Azure Key Vault är en moln tjänst för säker lagring och åtkomst till hemligheter. En hemlighet är något som du vill noggrant kontrol lera åtkomsten till, till exempel API-nycklar, lösen ord, certifikat eller krypterings nycklar.

Med den här anslutnings tjänsten kan du strömma Azure Key Vault-diagnostikloggar till Azure Sentinel, så att du kontinuerligt kan övervaka aktivitet i alla dina instanser.

Lär dig mer om att [övervaka Azure Key Vault](../azure-monitor/insights/key-vault-insights-overview.md) och om [Azure Key Vault Diagnostics-telemetri](../key-vault/general/logging.md).

## <a name="prerequisites"></a>Förutsättningar

För att mata in Azure Key Vault loggar i Azure Sentinel:

- Du måste ha läs-och Skriv behörighet på Azure Sentinel-arbetsytan.

- Om du vill använda Azure Policy för att tillämpa en princip för strömning av loggar för att Azure Key Vault resurser måste du ha ägar rollen för princip tilldelnings omfånget.

## <a name="connect-to-azure-key-vault"></a>Anslut till Azure Key Vault

Den här anslutningen använder Azure Policy för att tillämpa en enda Azure Key Vault logg strömnings konfiguration för en samling instanser, som definieras som ett omfång. Du kan se de logg typer som matats in från Azure Key Vault på den vänstra sidan av kopplings sidan under **data typer**.

1. Välj **data kopplingar** på navigerings menyn i Azure Sentinel.

1. Välj **Azure Key Vault** i data kopplings galleriet och välj sedan **Öppna kopplings sida** i förhands gransknings fönstret.

1. I **konfigurations** avsnittet på anslutnings sidan, expanderar du **aktivera diagnostikloggar på Azure Key Vault**.

1. Välj knappen **starta Azure policy tilldelnings guiden** .

    Guiden princip tilldelning öppnas och är redo att skapa en ny princip som kallas **distribuera-konfigurera diagnostikinställningar för Azure Key Vault till Log Analytics arbets yta**.

    1. På fliken **grundläggande** klickar du på knappen med de tre punkterna under **omfång** för att välja din prenumeration (och eventuellt en resurs grupp). Du kan också lägga till en beskrivning.

    1. På fliken **parametrar** väljer du din Azure Sentinel-arbetsyta i list rutan **Log Analytics arbets yta** . De återstående List fälten representerar de tillgängliga typerna av diagnos logg. Lämna markerat som "true" alla de logg typer som du vill mata in.

    1. Om du vill tillämpa principen på dina befintliga resurser väljer du fliken **reparation** och markerar kryss rutan **skapa en reparations uppgift** .

    1. På fliken **Granska + skapa** klickar du på **Skapa**. Din princip har nu tilldelats den omfattning som du har valt.

> [!NOTE]
>
> Med den här specifika data kopplingen visas anslutnings status indikatorerna (en färg remsa i galleriet för data anslutningar och anslutnings ikoner bredvid data typens namn) som *ansluten* (grön) endast om data har matats in vid någon tidpunkt under de senaste två veckorna. När två veckor har passerat utan data inmatning visas kopplingen som frånkopplad. Den tid då mer data kommer till kommer den *anslutna* statusen att returneras.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Azure Key Vault till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
