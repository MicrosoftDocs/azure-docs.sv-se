---
title: Inaktivera den hanterade Azure Automation-identiteten för ditt Azure Automation konto
description: Den här artikeln beskriver hur du inaktiverar och tar bort en hanterad identitet för ett Azure Automation konto.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2021
ms.topic: conceptual
ms.openlocfilehash: 74d029db48f64b38eb323150068e173746d379b7
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107495098"
---
# <a name="disable-your-azure-automation-account-managed-identity"></a>Inaktivera den hanterade Azure Automation-identiteten för ditt Azure Automation konto

Det finns två sätt att inaktivera en system tilldelad identitet i Azure Automation. Du kan slutföra den här uppgiften från Azure Portal eller med hjälp av en arm Azure Resource Manager mall (arm).

## <a name="disable-managed-identity-in-the-azure-portal"></a>Inaktivera hanterad identitet i Azure Portal

Du kan inaktivera den hanterade identiteten från Azure Portal oavsett hur den hanterade identiteten ursprungligen konfigurerades.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Gå till ditt Automation-konto och välj **Identitet** under **Kontoinställningar.**

1. Ange alternativet **System tilldelad** till **Av och** tryck på **Spara.** När du uppmanas att bekräfta trycker du på **Ja.**

Den hanterade identiteten tas bort och har inte längre åtkomst till målresursen.

## <a name="disable-using-azure-resource-manager-template"></a>Inaktivera med Azure Resource Manager mall

Om du har skapat den hanterade identiteten för ditt Automation-konto med en Azure Resource Manager-mall kan du inaktivera den hanterade identiteten genom att återanvända mallen och ändra dess inställningar. Ange typen för identitetsobjektets underordnade egenskap till **Ingen** som visas i följande exempel och kör sedan mallen på samma sätt.

```json
"identity": { 
   "type": "None" 
} 
```

Om du tar bort en system tilldelad identitet med den här metoden tas den även bort från Azure AD. System tilldelade identiteter tas också bort automatiskt från Azure AD när appresursen som de är tilldelade till tas bort.

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du aktiverar hanterad identitet i Azure Automation finns i [Aktivera och använda hanterad identitet för Automation.](enable-managed-identity-for-automation.md)

- En översikt över Automation-kontosäkerhet finns i Översikt [över Automation-kontoautentisering.](automation-security-overview.md)
