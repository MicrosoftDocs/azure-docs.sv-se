---
title: Inaktivera din Azure Automation-kontoidentitet (förhandsversion)
description: Den här artikeln beskriver hur du inaktiverar och tar bort en hanterad identitet för Azure Automation konto.
services: automation
ms.subservice: process-automation
ms.date: 04/14/2021
ms.topic: conceptual
ms.openlocfilehash: e17e1afda50d9a0263067a77bf26435f53b4f237
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519280"
---
# <a name="disable-your-azure-automation-account-managed-identity-preview"></a>Inaktivera din Azure Automation-kontoidentitet (förhandsversion)

Det finns två sätt att inaktivera en system tilldelad identitet i Azure Automation. Du kan slutföra den här uppgiften från Azure Portal eller med hjälp av en Azure Resource Manager (ARM)-mall.

## <a name="disable-managed-identity-in-the-azure-portal"></a>Inaktivera hanterad identitet i Azure Portal

Du kan inaktivera den hanterade identiteten från Azure Portal oavsett hur den hanterade identiteten ursprungligen konfigurerades.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Gå till ditt Automation-konto och välj **Identitet** under **Kontoinställningar.**

1. Ange alternativet **System tilldelad** till **Av och** tryck på **Spara.** När du uppmanas att bekräfta trycker du på **Ja.**

Den hanterade identiteten tas bort och har inte längre åtkomst till målresursen.

## <a name="disable-using-azure-resource-manager-template"></a>Inaktivera med Azure Resource Manager mall

Om du har skapat den hanterade identiteten för ditt Automation-konto med hjälp av en Azure Resource Manager-mall kan du inaktivera den hanterade identiteten genom att återanvända mallen och ändra dess inställningar. Ange typen för identitetsobjektets underordnade egenskap till **Ingen** som visas i följande exempel och kör sedan mallen på ny plats.

```json
"identity": { 
   "type": "None" 
} 
```

Om du tar bort en system tilldelad identitet med den här metoden tas den även bort från Azure AD. System tilldelade identiteter tas också automatiskt bort från Azure AD när appresursen som de är tilldelade till tas bort.

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du aktiverar hanterade identiteter i Azure Automation finns [i Aktivera och använda hanterad identitet för Automation (förhandsversion)](enable-managed-identity-for-automation.md).

- En översikt över säkerheten för Automation-konton finns i [Översikt över Automation-kontoautentisering.](automation-security-overview.md)
