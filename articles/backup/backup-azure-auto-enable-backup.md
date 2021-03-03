---
title: Aktivera automatisk säkerhetskopiering av skapande av virtuell dator med Azure Policy
description: En artikel som beskriver hur du använder Azure Policy för att automatiskt aktivera säkerhets kopiering för alla virtuella datorer som skapats inom ett angivet omfång
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: dfa4364eeaa9f5b60af3f5d6a19aaeb188d4f65e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101707310"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Aktivera automatisk säkerhetskopiering av skapande av virtuell dator med Azure Policy

En av de viktigaste ansvars områdena för en säkerhets kopierings-eller efterlevnadsprincip i en organisation är att se till att alla affärs kritiska datorer säkerhets kopie ras med lämplig kvarhållning.

I dag är Azure Backup en rad inbyggda principer (med [Azure policy](../governance/policy/overview.md)) som hjälper dig att automatiskt se till att dina virtuella Azure-datorer har kon figurer ATS för säkerhets kopiering. Beroende på hur dina säkerhets kopierings team och resurser organiseras, kan du använda någon av följande principer:

## <a name="policy-1---configure-backup-on-vms-without-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>Princip 1 – Konfigurera säkerhets kopiering på virtuella datorer utan en tilldelad tagg till ett befintligt Recovery Services-valv på samma plats

Om din organisation har en central säkerhets kopierings grupp som hanterar säkerhets kopieringar över program team kan du använda den här principen för att konfigurera säkerhets kopiering till ett befintligt centralt Recovery Services valv i samma prenumeration och plats som de virtuella datorer som regleras. Du kan välja att **undanta** virtuella datorer som innehåller en viss tagg, från den här principens omfattning.

## <a name="policy-2---preview-configure-backup-on-vms-with-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>Princip 2-[Preview] Konfigurera säkerhets kopiering på virtuella datorer med en specifik tagg till ett befintligt Recovery Services-valv på samma plats
Den här principen fungerar på samma sätt som princip 1 ovan, men den enda skillnaden är att du kan använda den här principen för att **ta** med virtuella datorer som innehåller en viss tagg i omfånget för den här principen. 

## <a name="policy-3---preview-configure-backup-on-vms-without-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>Princip 3-[för hands version] Konfigurera säkerhets kopiering på virtuella datorer utan en särskild tagg till ett nytt Recovery Services-valv med en standard princip
Om du ordnar program i dedikerade resurs grupper och vill att de ska säkerhets kopie ras av samma valv, kan du använda den här principen för att automatiskt hantera den här åtgärden. Du kan välja att **undanta** virtuella datorer som innehåller en viss tagg, från den här principens omfattning.

## <a name="policy-4---preview-configure-backup-on-vms-with-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>Princip 4-[för hands version] Konfigurera säkerhets kopiering på virtuella datorer med en specifik tagg till ett nytt Recovery Services-valv med en standard princip
Den här principen fungerar på samma sätt som princip 3 ovan, men den enda skillnaden är att du kan använda den här principen för att **ta** med virtuella datorer som innehåller en viss tagg i omfånget för den här principen. 

Förutom ovan visas Azure Backup även en princip för [endast granskning](../governance/policy/concepts/effects.md#audit) – **Azure Backup ska vara aktive rad för Virtual Machines**. Den här principen identifierar vilka virtuella datorer som inte har säkerhets kopior aktiverade, men som inte automatiskt konfigurerar säkerhets kopior för dessa virtuella datorer. Detta är användbart när du bara vill utvärdera den övergripande kompatibiliteten för de virtuella datorerna, men inte att du behöver vidta några åtgärder direkt.

## <a name="supported-scenarios"></a>Scenarier som stöds

* Den inbyggda principen stöds för närvarande endast för virtuella Azure-datorer. Användarna måste noga se till att den bevarande princip som anges under tilldelningen är en bevarande princip för virtuella datorer. Se [det här](./backup-azure-policy-supported-skus.md) dokumentet för att se alla VM-SKU: er som stöds av den här principen.

* Princip 1 och 2 kan tilldelas till en enda plats och en prenumeration i taget. Om du vill aktivera säkerhets kopiering för virtuella datorer över platser och prenumerationer måste flera instanser av princip tilldelningen skapas, en för varje kombination av plats och prenumeration.

* Hanterings gruppens omfattning stöds för närvarande inte för principer 1 och 2.

* För princip 1 och 2 kan det angivna valvet och de virtuella datorer som kon figurer ATS för säkerhets kopiering vara under olika resurs grupper.

* Principer 1, 2, 3 och 4 är för närvarande inte tillgängliga i nationella moln.

* Principer 3 och 4 kan tilldelas till en enda prenumeration i taget (eller en resurs grupp inom en prenumeration).

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="using-the-built-in-policies"></a>Använda de inbyggda principerna

Stegen nedan beskriver processen från slut punkt till slut punkt för att tilldela princip 1: **Konfigurera säkerhets kopiering på virtuella datorer utan en särskild tagg till ett befintligt Recovery Services-valv på samma plats** i ett visst omfång. Liknande instruktioner gäller för de andra principerna. När den har tilldelats konfigureras alla nya virtuella datorer som skapats i omfånget automatiskt för säkerhets kopiering.

1. Logga in på Azure Portal och gå till instrument panelen för **principer** .
2. Välj **definitioner** på den vänstra menyn för att få en lista över alla inbyggda principer i Azure-resurser.
3. Filtrera listan för **kategori = säkerhets kopiering** och välj principen med namnet "Konfigurera säkerhets kopiering på virtuella datorer för en plats till ett befintligt centralt valv på samma plats".
![Princip instrument panel](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. Välj namnet på principen. Du omdirigeras till den detaljerade definitionen för den här principen.
![Fönstret princip definition](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. Välj knappen **tilldela** högst upp i fönstret. Detta omdirigerar dig till fönstret **tilldela princip** .
6. Under **grunderna** väljer du de tre punkterna bredvid fältet **omfång** . Det här öppnar ett höger kontext fönster där du kan välja prenumerationen för den princip som ska tillämpas på. Du kan också välja en resurs grupp, så att principen endast tillämpas för virtuella datorer i en viss resurs grupp.
![Grundläggande princip tilldelning](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. På fliken **parametrar** väljer du en plats i list rutan och väljer det valv och den säkerhets kopierings princip som de virtuella datorerna i omfånget måste vara associerade till. Du kan också välja att ange ett taggnamn och en matris med märkes värden. En virtuell dator som innehåller något av de angivna värdena för den givna taggen kommer att uteslutas från omfånget för princip tilldelningen.
![Parametrar för princip tilldelning](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. Se till att **inställningen är inställd** på deployIfNotExists.
9. Gå till **Granska + skapa** och välj **skapa**.

> [!NOTE]
>
> Azure Policy kan också användas på befintliga virtuella datorer, med hjälp av [reparation](../governance/policy/how-to/remediate-resources.md).

> [!NOTE]
>
> Vi rekommenderar att den här principen inte tilldelas till fler än 200 virtuella datorer i taget. Om principen är tilldelad till fler än 200 virtuella datorer kan det leda till att säkerhets kopieringen utlöses några timmar senare än vad som anges i schemat.

## <a name="next-steps"></a>Nästa steg

[Läs mer om Azure Policy](../governance/policy/overview.md)