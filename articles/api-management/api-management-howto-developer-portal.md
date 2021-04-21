---
title: Översikt över utvecklarportalen i Azure API Management
titleSuffix: Azure API Management
description: Lär dig mer om utvecklarportalen i API Management – en anpassningsbar webbplats där API-konsumenter kan utforska dina API:er.
services: api-management
documentationcenter: API Management
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3f5da5ed6c828278eb25a9fa4fa5cfb90e16f8f4
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815967"
---
# <a name="overview-of-the-developer-portal"></a>Översikt över utvecklarportalen

Utvecklarportalen är en automatiskt genererad, helt anpassningsbar webbplats med dokumentationen för dina API:er. Det är där API-konsumenter kan identifiera dina API:er, lära sig hur de används, begära åtkomst och testa dem.

Som det beskrivs i den här artikeln kan du anpassa och utöka utvecklarportalen för dina specifika scenarier. 

![API Management utvecklarportalen](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="migration-from-the-legacy-portal"></a>Migrering från den äldre portalen

> [!IMPORTANT]
> Den äldre utvecklarportalen är nu inaktuell och får endast säkerhetsuppdateringar. Du kan fortsätta att använda den, som vanligt, tills den tas ur bruk i oktober 2023, när den tas bort från alla API Management tjänster.

Migrering till den nya utvecklarportalen beskrivs i den [dedikerade dokumentationsartikeln](developer-portal-deprecated-migration.md).

## <a name="customization-and-styling"></a>Anpassning och formatering

Utvecklarportalen kan anpassas och formateras via den inbyggda dra och släpp-redigeraren för visuella objekt. Mer [information finns i](api-management-howto-developer-portal-customize.md) den här självstudien.

## <a name="extensibility"></a><a name="managed-vs-self-hosted"></a> Utökningsbarhet

Din API Management-tjänst innehåller en inbyggd, alltid uppdaterad hanterad **utvecklarportal.** Du kan komma åt den från Azure Portal-gränssnittet.

Om du behöver utöka den med anpassad logik, som inte stöds in-of-the-box, kan du ändra dess kodbas. Portalens kodbas är tillgänglig på [en GitHub-lagringsplats.](https://github.com/Azure/api-management-developer-portal) Du kan till exempel implementera en ny widget som integreras med ett supportsystem från tredje part. När du implementerar nya funktioner kan du välja något av följande alternativ:

- **Värd för den** resulterande portalen utanför din API Management tjänst. När du har en egen värd för portalen blir du dess underhållare och du ansvarar för dess uppgraderingar. Azure Support är begränsad till den grundläggande [konfigurationen av lokala portaler.](developer-portal-self-host.md)
- Öppna en pull-begäran för API Management att sammanslå nya funktioner till **den hanterade** portalens kodbas.

Information och instruktioner om utökningsbarhet finns i [GitHub-lagringsplatsen](https://github.com/Azure/api-management-developer-portal) och i självstudien för [att implementera en widget](developer-portal-implement-widgets.md). Självstudien [för att anpassa den](api-management-howto-developer-portal-customize.md) hanterade portalen vägs igenom  portalens administrativa panel, som är gemensam för hanterade **och lokala** versioner.


## <a name="next-steps"></a>Nästa steg

Läs mer om den nya utvecklarportalen:

- [Få åtkomst till och anpassa den hanterade utvecklarportalen](api-management-howto-developer-portal-customize.md)
- [Konfigurera en egen version av portalen](developer-portal-self-host.md)
- [Implementera din egen widget](developer-portal-implement-widgets.md)

Bläddra bland andra resurser:

- [GitHub-lagringsplats med källkoden](https://github.com/Azure/api-management-developer-portal)
- [Vanliga frågor och svar om utvecklarportalen](developer-portal-faq.md)
