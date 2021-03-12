---
title: Konfigurera export inställningar i Azure API för FHIR
description: Den här artikeln beskriver hur du konfigurerar export inställningar i Azure API för FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/5/2020
ms.author: matjazl
ms.openlocfilehash: bb728b81d4dad5d880c27d1ebe8f85f1508f8231
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019759"
---
# <a name="configure-export-setting-and-set-up-the-storage-account"></a>Konfigurera export inställning och konfigurera lagrings kontot

Azure API för FHIR stöder $export kommandot som gör att du kan exportera data från Azure API för FHIR-konto till ett lagrings konto.

Det finns tre steg när du konfigurerar export i Azure API för FHIR:

1. Aktivera hanterad identitet på Azure API för FHIR-tjänsten
2. Skapa ett Azure Storage-konto (om det inte gjorts tidigare) och tilldela behörighet till Azure API för FHIR till lagrings kontot
3. Välja lagrings kontot i Azure API för FHIR som export Storage-konto

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Aktivera hanterad identitet på Azure API för FHIR

Det första steget i att konfigurera Azure API för FHIR för export är att aktivera systemomfattande hanterad identitet för tjänsten. Du kan läsa mer om hanterade identiteter i Azure [här](../../active-directory/managed-identities-azure-resources/overview.md).

Det gör du genom att gå till Azure API för FHIR-tjänsten och välja identitets blad. Genom att ändra status till på aktive ras hanterad identitet i Azure API för FHIR-tjänsten.

![Aktivera hanterad identitet](media/export-data/fhir-mi-enabled.png)

Nu kan vi gå vidare till nästa steg och skapa ett lagrings konto och tilldela behörighet till vår tjänst.

## <a name="adding-permission-to-storage-account"></a>Lägger till behörighet till lagrings kontot

Nästa steg i exporten är att tilldela behörighet för Azure API för FHIR-tjänsten att skriva till lagrings kontot.

När vi har skapat ett lagrings konto navigerar du till bladet Access Control (IAM) i lagrings kontot och väljer Lägg till roll tilldelningar

![Exportera roll tilldelning](media/export-data/fhir-export-role-assignment.png)

Här lägger vi till roll lagrings data deltagare i vårt tjänst namn.

![Lägg till roll](media/export-data/fhir-export-role-add.png)

Nu är vi redo för nästa steg där vi kan välja lagrings kontot i Azure API för FHIR som ett standard lagrings konto för $export.

## <a name="selecting-the-storage-account-for-export"></a>Välja lagrings konto för $export

Sista steget är att tilldela det Azure Storage-konto som Azure API för FHIR ska använda för att exportera data till. Det gör du genom att gå till integrations bladet i Azure API for FHIR service i Azure Portal och välja lagrings kontot

![FHIR export Storage](media/export-data/fhir-export-storage.png)

När vi är redo att exportera data med hjälp av kommandot $export.

>[!div class="nextstepaction"]
>[Ytterligare inställningar](azure-api-for-fhir-additional-settings.md)
