---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: ffd053b98a54d3e23eec62427f5c3d82df58954d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800345"
---
<!-- List and set subscriptions -->

1. Hämta en lista över dina prenumerationer med kommandot [az account list](/cli/azure/account#az_account_list):

    ```
    az account list --output table
    ```

2. Använd `az account set` med det prenumerations-ID eller namn som du vill växla till.

    ```
    az account set --subscription "My Demos"
    ```
