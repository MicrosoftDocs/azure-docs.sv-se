---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: b602cbfde22cc87b42a32b007c19b626814d1660
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554257"
---
## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/dotnet/).

## <a name="create-azure-communication-resource"></a>Skapa Azure Communication-resurs

Om du vill skapa en Azure Communication Services-resurs loggar du först in på [Azure Portal](https://portal.azure.com). I det övre vänstra hörnet på sidan väljer du **+ skapa en resurs**. 

:::image type="content" source="../media/create-a-communication-resource/create-resource-plus-sign.png" alt-text="Skärm bild som markerar knappen Skapa en resurs i Azure Portal.":::

Ange **kommunikation** i ingångs läget för **Marketplace** eller Sök fältet överst i portalen.

:::image type="content" source="../media/create-a-communication-resource/searchbar-communication-portal.png" alt-text="Skärm bild som visar en sökning efter kommunikations tjänster i Sök fältet.":::

Välj **kommunikations tjänster** i resultatet och välj sedan **skapa**.

:::image type="content" source="../media/create-a-communication-resource/create-communication-portal.png" alt-text="Skärm bild som visar panelen kommunikations tjänster och markerar knappen Skapa.":::

Nu kan du konfigurera resursen för kommunikations tjänster. På den första sidan i skapa-processen uppmanas du att ange:

* Prenumerationen
* Resurs gruppen (du kan skapa en ny eller välja en befintlig resurs grupp)
* Namnet på kommunikations tjänst resursen
* Den geografi som resursen ska associeras med

I nästa steg kan du tilldela-taggar till resursen. Taggar kan användas för att organisera dina Azure-resurser. Mer information om taggar finns i [dokumentationen för resurs taggning](../../../azure-resource-manager/management/tag-resources.md) .

Slutligen kan du granska konfigurationen och **skapa** resursen. Observera att distributionen tar några minuter att slutföra.

## <a name="manage-your-communication-services-resource"></a>Hantera kommunikations tjänst resursen

Om du vill hantera din kommunikations tjänst resurs går du till [Azure Portal](https://portal.azure.com)och söker efter och väljer **Azure Communication Services**.

På sidan **kommunikations tjänster** väljer du namnet på din resurs.

**Översikts** sidan för din resurs innehåller alternativ för grundläggande hantering som att bläddra, stoppa, starta, starta om och ta bort. Du hittar fler konfigurations alternativ på den vänstra menyn på resurs sidan.
