---
title: Ansluta diagnostikloggar för Azure Kubernetes service (AKS) till Azure Sentinel
description: Lär dig hur du använder Azure Policy för att ansluta Azure Kubernetes service-diagnostikloggar till Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 03/07/2021
ms.author: yelevin
ms.openlocfilehash: c3a4593aa92acededf9784974b2a1e2dd3cfb319
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102507185"
---
# <a name="connect-azure-kubernetes-service-diagnostics-logs"></a>Anslut Azure Kubernetes service-diagnostikloggar

Azure Kubernetes service (AKS) är en fullständigt hanterad behållar Dirigerings tjänst med öppen källkod som gör att du kan distribuera, skala och hantera Docker-behållare och containerbaserade program i en kluster miljö.

Med den här anslutningen kan du strömma dina Azure Kubernetes service (AKS)-diagnostikloggar till Azure Sentinel, så att du kontinuerligt kan övervaka aktivitet i alla dina instanser. 

Lär dig mer om att [övervaka Azure Kubernetes-tjänsten](../azure-monitor/containers/container-insights-overview.md) och om [AKS-diagnostik](../aks/view-control-plane-logs.md).

## <a name="prerequisites"></a>Förutsättningar

För att mata in AKS-loggar i Azure Sentinel:

- Du måste ha läs-och Skriv behörighet på Azure Sentinel-arbetsytan.

- Om du vill använda Azure Policy för att tillämpa en princip för strömning av loggar till AKS-resurser måste du ha ägar rollen för princip tilldelnings omfånget.

## <a name="connect-to-azure-kubernetes-service"></a>Ansluta till Azure Kubernetes-tjänsten

Den här anslutningen använder Azure Policy för att tillämpa en enda konfiguration av en Azure Kubernetes service log-direktuppspelning till en samling instanser, definierade som en omfattning. Du kan se de logg typer som matats in från Azure Kubernetes-tjänsten på den vänstra sidan av anslutnings sidan under **data typer**.

1. Välj **data kopplingar** på navigerings menyn i Azure Sentinel.

1. Välj **Azure Kubernetes service (AKS)** i data kopplings galleriet och välj sedan **Öppna kopplings sida** i förhands gransknings fönstret.

1. I **konfigurations** avsnittet på anslutnings sidan, expanderar du **Aktivera diagnostikloggar på Azure KUBERNETES service (AKS)**.

1. Välj knappen **starta Azure policy tilldelnings guiden** .

    Guiden princip tilldelning öppnas och är redo att skapa en ny princip som kallas **distribuera-konfigurera diagnostikinställningar för Azure Kubernetes service till Log Analytics-arbetsyta**.

    1. På fliken **grundläggande** klickar du på knappen med de tre punkterna under **omfång** för att välja din prenumeration (och eventuellt en resurs grupp). Du kan också lägga till en beskrivning.

    1. På fliken **parametrar** väljer du din Azure Sentinel-arbetsyta i list rutan **Log Analytics arbets yta** . De återstående List fälten representerar de tillgängliga typerna av diagnos logg. Lämna markerat som "true" alla de logg typer som du vill mata in.

    1. Om du vill tillämpa principen på dina befintliga resurser väljer du fliken **reparation** och markerar kryss rutan **skapa en reparations uppgift** .

    1. På fliken **Granska + skapa** klickar du på **Skapa**. Din princip har nu tilldelats den omfattning som du har valt.

> [!NOTE]
>
> Med den här specifika data kopplingen visas anslutnings status indikatorerna (en färg remsa i galleriet för data anslutningar och anslutnings ikoner bredvid data typens namn) som *ansluten* (grön) endast om data har matats in vid någon tidpunkt under de senaste två veckorna. När två veckor har passerat utan data inmatning visas kopplingen som frånkopplad. Den tid då mer data kommer till kommer den *anslutna* statusen att returneras.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Azure Kubernetes-tjänsten till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
