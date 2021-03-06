---
title: Konfigurera Azure Defender för Storage
titleSuffix: Azure Storage
description: Konfigurera Azure Defender för lagring för att identifiera avvikelser i konto aktivitet och få meddelanden om potentiellt skadliga försök att komma åt ditt konto.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: e2f044ab267365885260b031638572846184bc83
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063193"
---
# <a name="configure-azure-defender-for-storage"></a>Konfigurera Azure Defender för Storage

Azure Defender för Storage ger ytterligare ett lager med säkerhet information som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagringskonton. Med det här skyddslagret kan du hantera hot utan att behöva vara säkerhetsexpert eller hantera säkerhetsövervakningssystem.

Säkerhetsaviseringar utlöses när avvikelser i en aktivitet inträffar. Dessa säkerhets aviseringar är integrerade med [Azure Security Center](https://azure.microsoft.com/services/security-center/)och skickas också via e-post till prenumerations administratörer med information om misstänkt aktivitet och rekommendationer om hur du undersöker och åtgärdar hot.

Tjänsten matar in resurs loggar med Läs-, skriv-och borttagnings begär anden till Blob Storage och Azure Files för hot identifiering. Om du vill undersöka aviseringar från Azure Defender kan du Visa relaterad lagrings aktivitet med hjälp av Lagringsanalys loggning. Mer information finns i **Konfigurera loggning** i [övervaka ett lagrings konto i Azure Portal](./manage-storage-analytics-logs.md#configure-logging).

## <a name="availability"></a>Tillgänglighet

Azure Defender för Storage är för närvarande tillgängligt för Blob Storage, Azure Files och Azure Data Lake Storage Gen2. Kontotyper som stöder Azure Defender inkluderar konton för general-purpose v2, blockblob och Blob Storage. Azure Defender för Storage är tillgängligt i alla offentliga moln och moln för amerikanska myndigheter, men inte i andra nationella molnregioner eller Azure Government-molnregioner.

Konton med hierarkiska namnrymder aktiverade för Data Lake Storage stöder transaktioner med både Azure Blob Storage-API:er och Data Lake Storage-API:er. Azure-filresurser stöder transaktioner via SMB.

För pris information, inklusive en kostnads fri 30-dagars utvärderings version, se [sidan Azure Security Center prissättning](https://azure.microsoft.com/pricing/details/security-center/).

I följande lista sammanfattas tillgängligheten för Azure Defender för lagring:

- Versions tillstånd:
  - [Blob Storage](https://azure.microsoft.com/services/storage/blobs/) (allmän tillgänglighet)
  - [Azure Files](../files/storage-files-introduction.md) (allmän tillgänglighet)
  - Azure Data Lake Storage Gen2 (allmän tillgänglighet)
- Moln<br>
    ✔ Kommersiella moln<br>
    ✔ US Gov<br>
    ✘ Kina gov, andra gov

## <a name="set-up-azure-defender"></a>Konfigurera Azure Defender

Du kan konfigurera Azure Defender för lagring på flera olika sätt, som beskrivs i följande avsnitt.

### <a name="azure-security-center"></a>[Azure Security Center](#tab/azure-security-center)

Azure Defender är inbyggt i Azure Security Center. När du aktiverar Azure Defender på din prenumeration aktive ras Azure Defender för Azure Storage automatiskt för alla dina lagrings konton. Du kan aktivera eller inaktivera Azure Defender för dina lagrings konton under en speciell prenumeration på följande sätt:

1. Starta **Azure Security Center** i [Azure Portal](https://portal.azure.com).
1. Välj **pris & inställningar** under **hantering** i huvud menyn.
1. Välj den prenumeration som du vill aktivera eller inaktivera Azure Defender.
1. Välj **Azure Defender på** för att aktivera Azure Defender för prenumerationen.
1. Leta upp **lagrings** raden under **Välj Azure Defender-plan efter resurs typ** och välj **aktive rad** i kolumnen **plan** .
1. Spara ändringarna.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-security-center.png" alt-text="Skärm bild som visar hur du aktiverar Azure Defender för lagring i Security Center":::

Azure Defender har nu Aktiver ATS för alla lagrings konton i den här prenumerationen.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Starta [Azure Portal](https://portal.azure.com/).
1. Navigera till ditt lagringskonto. Under **Inställningar** väljer du **Avancerad säkerhet**.
1. Välj **Aktivera Azure Defender för Storage**.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-portal.png" alt-text="Skärm bild som visar hur du aktiverar Azure Defender för ett Azure Storage konto":::

Azure Defender har nu Aktiver ATS för det här lagrings kontot.

### <a name="template"></a>[Mall](#tab/template)

Använd en Azure Resource Manager mall för att distribuera ett Azure Storage konto med Azure Defender aktiverat. Mer information finns i [lagrings konto med avancerat skydd](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Använd en Azure Policy för att aktivera Azure Defender över lagrings konton under en speciell prenumeration eller resurs grupp.

1. Starta sidan Azure **policy-definitions** .
1. Sök efter principen **Distribuera Azure Defender på lagrings konton** .

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy-definitions.png" alt-text="Använd princip för att aktivera Azure Defender för lagrings konton":::

1. Välj en Azure-prenumeration eller resurs grupp.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy2.png" alt-text="Välj en prenumeration eller resurs grupp för principens omfattning ":::

1. Tilldela principen.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy1.png" alt-text="Tilldela en princip för att aktivera Azure Defender för lagring":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill aktivera Azure Defender för ett lagrings konto med PowerShell kontrollerar du först att du har installerat modulen [AZ. Security](https://www.powershellgallery.com/packages/Az.Security) . Anropa sedan kommandot [Enable-AzSecurityAdvancedThreatProtection](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection) . Kom ihåg att ersätta värden inom vinkelparenteser med dina egna värden:

```azurepowershell
Enable-AzSecurityAdvancedThreatProtection -ResourceId "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/"
```

Om du vill kontrol lera Azure Defender-inställningen för ett lagrings konto med PowerShell anropar du kommandot [Get-AzSecurityAdvancedThreatProtection](/powershell/module/az.security/get-azsecurityadvancedthreatprotection) . Kom ihåg att ersätta värden inom vinkelparenteser med dina egna värden:

```azurepowershell
Get-AzSecurityAdvancedThreatProtection -ResourceId "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/"
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill aktivera Azure Defender för ett lagrings konto med Azure CLI anropar du kommandot [AZ Security ATP Storage Update](/cli/azure/security/atp/storage#az_security_atp_storage_update) . Kom ihåg att ersätta värden inom vinkelparenteser med dina egna värden:

```azurecli
az security atp storage update \
    --resource-group <resource-group> \
    --storage-account <storage-account> \
    --is-enabled true
```

Om du vill kontrol lera Azure Defender-inställningen för ett lagrings konto med Azure CLI anropar du kommandot [AZ Security ATP Storage show](/cli/azure/security/atp/storage#az_security_atp_storage_show) . Kom ihåg att ersätta värden inom vinkelparenteser med dina egna värden:

```azurecli
az security atp storage show \
    --resource-group <resource-group> \
    --storage-account <storage-account>
```

---

## <a name="explore-security-anomalies"></a>Utforska säkerhetsavvikelser

När avvikelser i lagringsaktiviteter inträffar får du en e-postavisering med information om den misstänkta säkerhetshändelsen. Information om händelsen omfattar:

- Avvikelsens karaktär
- Lagringskontots namn
- Händelsetiden
- Lagringstypen
- Möjliga orsaker
- Undersökningssteg
- Reparationssteg

E-postmeddelandet innehåller även information om möjliga orsaker och rekommenderade åtgärder för att undersöka och åtgärda det potentiella hotet.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert-email.png" alt-text="Azure Defender för lagrings aviserings-e-post":::

Du kan granska och hantera dina aktuella säkerhets aviseringar från Azure Security Center [rutan säkerhets aviseringar](../../security-center/security-center-managing-and-responding-alerts.md). Om du klickar på en specifik avisering visas information och åtgärder för att undersöka det aktuella hotet och åtgärda framtida hot.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert.png" alt-text="Azure Defender för lagrings avisering":::

## <a name="security-alerts"></a>Säkerhetsaviseringar

Aviseringar genereras av ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagrings konton. En lista över aviseringar för Azure Storage finns i [varningar för Azure Storage](../../security-center/alerts-reference.md#alerts-azurestorage).

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Defender för Storage](../../security-center/defender-for-storage-introduction.md)
- [Azure Security Center](../../security-center/security-center-introduction.md)
- [Loggar i Azure Storage konton](/rest/api/storageservices/About-Storage-Analytics-Logging)
