---
author: tanmaygore
ms.author: tagore
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.openlocfilehash: 5deba9d8968f71ef1b21517e74e6af4c39aa9271
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "76279287"
---
> [!WARNING]
> När du aktiverar diagnostik för en befintlig roll inaktive ras alla tillägg som du redan har angett när paketet distribueras. Dessa omfattar:
>
> * Diagnostik för Microsoft Monitoring Agent
> * Microsoft Azure säkerhets övervakning
> * Microsoft-programvara mot skadlig kod                 
> * Microsoft Monitoring Agent
> * Microsoft Service Profiler-agent      
> * Windows Azure-domännätverk        
> * Windows Azure-diagnostik-tillägg   
> * Windows Azure fjärr skrivbords tillägg
> * Windows Azure logg insamlare
>
> Du kan återställa dina tillägg via Azure Portal eller PowerShell när du har distribuerat den uppdaterade rollen.
>
