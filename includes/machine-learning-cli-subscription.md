---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 493c674fa161bf33436e560fdcbb9196410db931
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102209482"
---
> [!TIP]
> När du har loggat in visas en lista över prenumerationer som är associerade med ditt Azure-konto. Prenumerations informationen med `isDefault: true` är den aktuella aktiverade prenumerationen för Azure CLI-kommandon. Den här prenumerationen måste vara samma som innehåller din Azure Machine Learning-arbetsyta. Du hittar prenumerations-ID: t från [Azure Portal](https://portal.azure.com) genom att gå till översikts sidan för din arbets yta. Du kan också använda SDK: n för att hämta prenumerations-ID: t från arbets ytans objekt. Till exempel `Workspace.from_config().subscription_id`.
> 
> Om du vill välja en annan prenumeration använder du `az account set -s <subscription name or ID>` kommandot och anger det prenumerations namn eller-ID som du vill växla till. Mer information om val av prenumeration finns i [använda flera Azure-prenumerationer](/cli/azure/manage-azure-subscriptions-azure-cli).