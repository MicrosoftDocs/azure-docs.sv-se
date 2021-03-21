---
title: Så här hittar du din Azure-prenumeration
description: Hitta din Azure-prenumeration så att du kan konfigurera din miljö.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: cli,portal, devx-track-azurecli
ms.openlocfilehash: f16cc83257dbf6419fc794f34e4cd6df033d3f8b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102213971"
---
# <a name="find-your-azure-subscription"></a>Hitta din Azure-prenumeration

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="portal"></a>[Portal](#tab/portal/)

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Under rubriken Azure-tjänster väljer du prenumerationer. (Om inga prenumerationer visas kan du behöva byta Azure AD-klienter.) Dina prenumerations-ID: n visas i den andra kolumnen.
1. Kopiera prenumerations-ID och klistra in det i ett text dokument som du väljer att använda senare.

## <a name="cli"></a>[CLI](#tab/cli/)

## <a name="use-the-azure-cli"></a>Använda Azure CLI

<!-- NOTE: The following are in the includes file and are reused in other How To articles. All task based content should be in the includes folder with the "task-" prepended to the file name. -->

### <a name="list-your-azure-subscriptions-with-cli"></a>Lista dina Azure-prenumerationer med CLI

[!INCLUDE [List your Azure subscriptions with CLI](./includes/task-list-set-subscription-cli.md)]

### <a name="see-also"></a>Se även

* [Azure CLI](/cli/azure/ams)

---

## <a name="next-steps"></a>Nästa steg

[Strömma en fil](stream-files-dotnet-quickstart.md)
