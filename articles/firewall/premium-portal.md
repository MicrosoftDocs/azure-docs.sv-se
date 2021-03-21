---
title: För hands versionen av Azure Firewall Premium i Azure Portal
description: Läs mer om för hands versionen av Azure Firewall Premium i Azure Portal.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 3d56fc73faeb0d48ba7e5b21449c61d6213afa40
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100549834"
---
# <a name="azure-firewall-premium-preview-in-the-azure-portal"></a>För hands versionen av Azure Firewall Premium i Azure Portal

> [!IMPORTANT]
> Azure Firewall Premium är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Azure Firewall Premium Preview är en nästa generations brand vägg med funktioner som krävs för mycket känsliga och reglerade miljöer. Det innehåller följande funktioner:

- **TLS-inspektion** – dekrypterar utgående trafik, bearbetar data och krypterar sedan data och skickar dem till målet.
- **IDP: er** – ett system för identifiering och skydd av nätverks intrång (IDP: er) gör att du kan övervaka nätverks aktiviteter för skadlig aktivitet, Logga information om den här aktiviteten, rapportera den och eventuellt försöka blockera den.
- **URL-filtrering** – utökar Azure-brandväggens funktioner för FQDN-filtrering för att överväga en hel URL. Till exempel i `www.contoso.com/a/c` stället för `www.contoso.com` .
- **Webb kategorier** – administratörer kan tillåta eller neka användar åtkomst till webbplats kategorier som spel webbplatser, sociala media webbplatser och andra.

Mer information finns i [Azure Firewall Premium-funktioner](premium-features.md).

## <a name="deploy-the-firewall"></a>Distribuera brandväggen

Att distribuera en Azure Firewall Premium-förhands granskning liknar att distribuera en standard-Azure-brandvägg:

:::image type="content" source="media/premium-portal/premium-portal.png" alt-text="Portal distribution":::

För **brand Väggs nivå** väljer du **Premium (för hands version)** och för **brand Väggs princip** väljer du en befintlig Premium policy eller skapar en ny.

## <a name="configure-the-premium-policy"></a>Konfigurera Premium principen

Att konfigurera en Premium brand Väggs princip liknar att konfigurera en standard brand Väggs princip. Med en Premium policy kan du konfigurera Premium funktionerna:

:::image type="content" source="media/premium-portal/premium-policy.png" alt-text="Distribution av Premium policy":::

### <a name="rule-configuration"></a>Regel konfiguration

När du konfigurerar program regler i en Premium-princip kan du konfigurera ytterligare Premium-funktioner:

:::image type="content" source="media/premium-portal/premium-application-rule.png" alt-text="Premium regel":::

## <a name="next-steps"></a>Nästa steg

Information om för hands versions funktionerna i Azure Firewall Premium finns i [distribuera och konfigurera Azure Firewall Premium Preview](premium-deploy.md).