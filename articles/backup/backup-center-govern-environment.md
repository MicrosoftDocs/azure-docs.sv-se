---
title: Hantera ditt innehav av säkerhetskopior med Backup Center
description: Lär dig hur du styr din Azure-miljö för att se till att alla resurser är kompatibla från ett säkerhets kopierings perspektiv med Backup Center.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 283c99c4b17683850f71b25fb2006784e43f3b8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102506217"
---
# <a name="govern-your-backup-estate-using-backup-center"></a>Hantera ditt innehav av säkerhetskopior med Backup Center

Backup Center hjälper dig att styra din Azure-miljö och se till att alla resurser är kompatibla från ett säkerhets kopierings perspektiv. Nedan visas några av styrnings funktionerna i säkerhets kopierings Center:

* Visa och tilldela Azure-principer för säkerhets kopiering

* Visa kompatibilitet för dina resurser på alla inbyggda Azure-principer för säkerhets kopiering.

* Visa alla data källor som inte har kon figurer ATS för säkerhets kopiering.

## <a name="supported-scenarios"></a>Scenarier som stöds

* Se [support mat ris](backup-center-support-matrix.md) för en detaljerad lista över scenarier som stöds och som inte stöds.

## <a name="azure-policies-for-backup"></a>Azure-principer för säkerhets kopiering

Om du vill visa alla [Azure-principer](../governance/policy/overview.md) som är tillgängliga för säkerhets kopiering väljer du meny alternativet Azure- **principer för säkerhets kopiering** . Då visas alla inbyggda och anpassade [definitioner av Azure-principer för säkerhets kopiering](policy-reference.md) som är tillgängliga för tilldelning till dina prenumerationer och resurs grupper.

Genom att välja någon av definitionerna kan du [tilldela principen](../governance/policy/tutorials/create-and-manage.md#assign-a-policy) till ett definitions område.

![Välj Azure Policy definitioner](./media/backup-center-govern-environment/azure-policy-definitions.png)

## <a name="backup-compliance"></a>Kompatibilitet för säkerhets kopiering

Genom att klicka på meny alternativet kompatibilitet för säkerhets kopiering kan du se [kompatibiliteten](../governance/policy/how-to/get-compliance-data.md) för dina resurser enligt de olika inbyggda principerna som du har tilldelat din Azure-miljö. Du kan visa procent andelen resurser som är kompatibla med alla principer, samt de principer som har en eller flera icke-kompatibla resurser.

![Visa kompatibilitet för säkerhets kopiering](./media/backup-center-govern-environment/azure-policy-compliance.png)

## <a name="protectable-datasources"></a>Skydds bara data källor

Om du väljer meny alternativet **skydds bara data källor** kan du Visa alla dina data källor som inte har kon figurer ATS för säkerhets kopiering. Du kan filtrera listan efter DataSource-prenumeration, resurs grupp, plats, typ och taggar. När du har identifierat en data källa som behöver säkerhets kopie ras kan du högerklicka på motsvarande rutnäts objekt och välja **säkerhets kopiering** för att konfigurera säkerhets kopiering för resursen.

![Menyn skydds bara data källor](./media/backup-center-govern-environment/protectable-datasources.png)

> [!NOTE]
> Om du väljer **SQL i Azure VM** som typ av data källa visar vyn **skydds bara data källor** listan över alla virtuella Galleri datorer som inte har några SQL-databaser som har kon figurer ATS för säkerhets kopiering.
> Om du väljer **Azure Storage (Azure Files)** som typ av data källa visar vyn **skydds bara data källor** listan över alla lagrings konton (som stöder fil resurser) som inte har några fil resurser som har kon figurer ATS för säkerhets kopiering.


## <a name="next-steps"></a>Nästa steg

* [Övervaka och hantera säkerhets kopior](backup-center-monitor-operate.md)
* [Utföra åtgärder med Backup Center](backup-center-actions.md)
* [Få insikter om dina säkerhets kopior](backup-center-obtain-insights.md)