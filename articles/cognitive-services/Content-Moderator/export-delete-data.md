---
title: Exportera eller ta bort användar data – Content Moderator
titleSuffix: Azure Cognitive Services
description: Du har fullständig kontroll över dina data. Lär dig hur du visar, exporterar eller tar bort data i Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 9f74fdc9cd30e1dfbd4df6c94842a9dccb435ef4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "92913661"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Exportera eller ta bort användar data i Content Moderator

Content Moderator samlar in användar data för att kunna använda tjänsten, men kunder har fullständig kontroll för att visa, exportera och ta bort sina data med hjälp av [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com/) och API: erna för styrning [och granskning](./api-reference.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Mer information om hur du exporterar och tar bort användar data i Content Moderator finns i följande tabell.

| Data | Exportera åtgärd | Ta bort åtgärd |
| ---- | ---------------- | ---------------- |
| Konto information (prenumerations nycklar) | Ej tillämpligt | Ta bort med Azure Portal (Azure-prenumerationer). Eller Använd knappen **ta bort team** på sidan [Granska inställningar för användar gränssnitts](https://contentmoderator.cognitive.microsoft.com/) team. |
| Bilder för anpassad matchning | Anropa [API för Hämta avbildnings-ID](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). Avbildningar lagras i ett enkelriktat, patentskyddat hash-format och det finns inget sätt att extrahera de faktiska bilderna. | Anropa API: et för att [ta bort alla avbildningar](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). Eller ta bort Content Moderator resursen med hjälp av Azure Portal. |
| Villkor för anpassad matchning | Cal [Hämta alla villkors-API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | Anropa [API: et ta bort alla villkor](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). Eller ta bort Content Moderator resursen med hjälp av Azure Portal. |
| Taggar | Ej tillämpligt | Använd ikonen **ta bort** som är tillgänglig för varje tagg på sidan Granska inställningar för användar gränssnitts tag. Eller Använd knappen **ta bort team** på sidan [Granska inställningar för användar gränssnitts](https://contentmoderator.cognitive.microsoft.com/) team. |
| Omdömen | Anropa [API för Get-granskning](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Använd knappen **ta bort team** på sidan [Granska inställningar för användar gränssnitts](https://contentmoderator.cognitive.microsoft.com/) team.
| Användare | Ej tillämpligt | Använd ikonen **ta bort** som är tillgänglig för varje användare på sidan Granska inställningar för [användar gränssnitts](https://contentmoderator.cognitive.microsoft.com/) team. Eller Använd knappen **ta bort team** på sidan [Granska inställningar för användar gränssnitts](https://contentmoderator.cognitive.microsoft.com/) team. |