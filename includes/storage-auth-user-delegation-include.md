---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/15/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0a4a10797109cb3363027e2445259d06d9aa071
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "76118093"
---
## <a name="about-the-user-delegation-sas"></a>Om användar Delegerings-SAS

En SAS-token för åtkomst till en behållare eller BLOB kan skyddas med hjälp av antingen Azure AD-autentiseringsuppgifter eller en konto nyckel. En SAS som skyddas med Azure AD-autentiseringsuppgifter kallas för en användar Delegerings-SAS, eftersom OAuth 2,0-token som används för att signera SAS begärs för användarens räkning.

Microsoft rekommenderar att du använder Azure AD-autentiseringsuppgifter när det är möjligt som en säkerhets åtgärd, i stället för att använda konto nyckeln, vilket kan vara enklare att avslöja. När program designen kräver signaturer för delad åtkomst använder du autentiseringsuppgifter för Azure AD för att skapa en användar Delegerings-SAS för överlägsen säkerhet. Mer information om sa för användar delegering finns i [skapa en användar Delegerings-SAS](/rest/api/storageservices/create-user-delegation-sas).

> [!CAUTION]
> Alla klienter som har en giltig SAS kan komma åt data i ditt lagrings konto som tillåts av denna SAS. Det är viktigt att skydda en SAS från skadlig eller oavsiktlig användning. Använd vad som helst när du distribuerar en SAS och har en plan för att återkalla en komprometterad SAS.

Mer information om signaturer för delad åtkomst finns i [bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av signaturer för delad åtkomst (SAS)](../articles/storage/common/storage-sas-overview.md).
