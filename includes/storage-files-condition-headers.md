---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4604616cd4f2d6c75c272586df1331fc405061cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "70737499"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Fel ConditionHeadersNotSupported från ett webb program med Azure Files från webbläsare

ConditionHeadersNotSupported-felet uppstår vid åtkomst till innehåll som finns i Azure Files via ett program som använder villkorliga rubriker, till exempel en webbläsare, åtkomsten Miss lyckas. Felet anger att villkors rubriker inte stöds.

![Azure Files villkorliga huvud fel](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Orsak

Villkorliga rubriker stöds inte ännu. Program som implementerar dem måste begära den fullständiga filen varje gång filen öppnas.

### <a name="workaround"></a>Lösning

När en ny fil laddas upp är egenskapen Cache-Control som standard "no-cache". För att tvinga programmet att begära filen varje gång måste filens Cache-Control-egenskap uppdateras från "no-cache" till "no-cache, No-Store," måste revalidate ". Detta kan uppnås med hjälp av [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

![Ändring av cache för innehålls cache i Storage Explorer för Azure Files villkorliga huvuden](media/storage-files-condition-headers/storage-explorer-cache.png)