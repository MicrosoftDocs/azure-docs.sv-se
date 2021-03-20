---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 0bc5977a581006dc760c0435f9d68371ced7e4cd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "83648776"
---
Azure Storage krypterar alla data i ett lagrings konto i vila. Mer information finns i [Azure Storage kryptering för vilande data](../articles/storage/common/storage-service-encryption.md).

Som standard krypteras data med Microsoft-hanterade nycklar. Om du vill ha mer kontroll över krypterings nycklar kan du ange Kundhanterade nycklar som ska användas för kryptering av BLOB-och fildata. Dessa nycklar måste finnas i Azure Key Vault för att funktioner ska kunna komma åt lagrings kontot. Mer information finns i [kryptering i vila med Kundhanterade nycklar](../articles/azure-functions/configure-encrypt-at-rest-using-cmk.md).  