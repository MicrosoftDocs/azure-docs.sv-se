---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/31/2021
ms.author: glenga
ms.openlocfilehash: 9a5c143927f549124cb6e69a4c8eb4829709a9e9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99493493"
---
+ En [resurs grupp](../articles/azure-resource-manager/management/overview.md), som är en logisk behållare för relaterade resurser.
+ Ett standard [Azure Storage-konto](../articles/storage/common/storage-account-create.md)som upprätthåller tillstånd och annan information om dina projekt.
+ En förbruknings plan som definierar den underliggande värden för din server lös Function-app. 
+ En Function-app som tillhandahåller miljön för att köra funktions koden. Med en Function-app kan du gruppera funktioner som en logisk enhet för enklare hantering, distribution och delning av resurser inom samma värd plan.
+ En Application Insights instans som är ansluten till Function-appen, som spårar användningen av din server lös funktion.