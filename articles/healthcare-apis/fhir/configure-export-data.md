---
title: Konfigurera export inställningar i Azure API för FHIR
description: Den här artikeln beskriver hur du konfigurerar export inställningar i Azure API för FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/18/2021
ms.author: matjazl
ms.openlocfilehash: ee110420c697afb6ecad857ba823c61d03c6be6c
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046999"
---
# <a name="configure-export-setting-and-set-up-the-storage-account"></a>Konfigurera export inställning och konfigurera lagrings kontot

Azure API för FHIR stöder $export kommandot som gör att du kan exportera data från Azure API för FHIR-konto till ett lagrings konto.

Det finns tre steg när du konfigurerar export i Azure API för FHIR:

1. Aktivera hanterad identitet på Azure API för FHIR-tjänsten.
2. Skapa ett Azure Storage-konto (om det inte gjorts före) och tilldela behörighet till Azure API för FHIR till lagrings kontot.
3. Välja lagrings kontot i Azure API för FHIR som export Storage-konto.

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Aktivera hanterad identitet på Azure API för FHIR

Det första steget i att konfigurera Azure API för FHIR för export är att aktivera systemomfattande hanterad identitet för tjänsten. Mer information om hanterade identiteter i Azure finns i [om hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

Det gör du genom att gå till Azure API för FHIR-tjänsten och välja **identitet**. Genom att ändra status till **på** aktive ras hanterad identitet i Azure API för FHIR-tjänsten.

![Aktivera hanterad identitet](media/export-data/fhir-mi-enabled.png)

Nu kan du gå vidare till nästa steg genom att skapa ett lagrings konto och tilldela behörighet till vår tjänst.

## <a name="adding-permission-to-storage-account"></a>Lägger till behörighet till lagrings kontot

Nästa steg i exporten är att tilldela behörighet för Azure API för FHIR-tjänsten att skriva till lagrings kontot.

När du har skapat ett lagrings konto går du till **Access Control (IAM)** i lagrings kontot och väljer **Lägg till roll tilldelning**.

![Exportera roll tilldelning](media/export-data/fhir-export-role-assignment.png)

Det är här du lägger till en roll **lagrings-BLOB data deltagare** i vårt tjänst namn och väljer sedan **Spara**.

![Lägg till roll](media/export-data/fhir-export-role-add.png)

Nu är du redo att välja lagrings kontot i Azure API för FHIR som ett standard lagrings konto för $export.

## <a name="selecting-the-storage-account-for-export"></a>Välja lagrings konto för $export

Det sista steget är att tilldela det Azure Storage-konto som Azure API för FHIR ska använda för att exportera data till. Det gör du genom att gå till **integrering** i Azure API för FHIR-tjänsten och välja lagrings kontot.

![FHIR export Storage](media/export-data/fhir-export-storage.png)

När du har slutfört det sista steget är du nu redo att exportera data med hjälp av kommandot $export.

> [!Note]
> Endast lagrings konton i samma prenumeration som för Azure API för FHIR kan registreras som mål för $export åtgärder.

Mer information om hur du konfigurerar databas inställningar, åtkomst kontroll, aktiverar diagnostikloggning och använder anpassade rubriker för att lägga till data i gransknings loggar finns i:

>[!div class="nextstepaction"]
>[Ytterligare inställningar](azure-api-for-fhir-additional-settings.md)
