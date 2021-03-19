---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 24d146da7946176c92902698d0f52ae01baf79ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93061663"
---
Om du kör PowerShell lokalt öppnar du PowerShell-konsolen med utökade privilegier och ansluter till ditt Azure-konto. Med cmdleten *Connect-AzAccount* uppmanas du att ange autentiseringsuppgifter. Efter autentiseringen hämtas dina konto inställningar så att de är tillgängliga för Azure PowerShell.

Om du använder Azure Cloud Shell i stället för att köra PowerShell lokalt, kommer du att märka att du inte behöver köra *Connect-AzAccount*. Azure Cloud Shell ansluter till ditt Azure-konto automatiskt när du har valt **prova**.

1. Om du kör PowerShell lokalt loggar du in.

   ```azurepowershell
   Connect-AzAccount
   ```

1. Om du har mer än en prenumeration hämtar du en lista över dina Azure-prenumerationer.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. Ange den prenumeration som du vill använda.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
