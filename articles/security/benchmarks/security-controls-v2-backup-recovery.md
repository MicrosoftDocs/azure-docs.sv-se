---
title: Azures säkerhets benchmark v2 – säkerhets kopiering och återställning
description: Säkerhets kopiering och återställning av Azure-säkerhetsprestandatest v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 39466ad621eff1a7d3490c936c90fbff6f63e0fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051556"
---
# <a name="security-control-v2-backup-and-recovery"></a>Säkerhets kontroll v2: säkerhets kopiering och återställning

Säkerhets kopiering och återställning täcker kontroller för att säkerställa att säkerhets kopiering av data och konfigurationer på olika tjänst nivåer utförs, verifieras och skyddas.

Om du vill se vilka inbyggda Azure Policy som finns kan du läsa mer i avsnittet [om Azures säkerhetsmekanism för regelefterlevnad, inbyggt initiativ: säkerhets kopiering och återställning](../../governance/policy/samples/azure-security-benchmark.md#backup-and-recovery)

## <a name="br-1-ensure-regular-automated-backups"></a>BR-1: Säkerställ vanliga automatiserade säkerhets kopieringar

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| BR-1 | 10.1 | CP-2, CP4, CP-6, CP-9 |

Se till att du säkerhetskopierar system och data för att bibehålla affärs kontinuiteten efter en oväntad händelse. Detta bör definieras av alla mål för återställnings punkt mål (återställnings punkt mål) och återställnings tids mål (RTO).

Aktivera Azure Backup och konfigurera säkerhets kopierings källan (till exempel virtuella Azure-datorer, SQL Server, HANA-databaser eller fil resurser) samt önskad frekvens och kvarhållningsperiod.

För en högre skydds nivå kan du aktivera alternativet Geo-redundant lagring för att replikera säkerhetskopierade data till en sekundär region och återställa med återställning mellan regioner.

- [Affärskontinuitet och haveriberedskap i företagsskala](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

- [Så här aktiverar du Azure Backup](../../backup/index.yml)

- [Aktivera återställning mellan regioner](../../backup/backup-azure-arm-restore-vms.md#cross-region-restore)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Principer och standarder](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [Säkerhetsarkitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Incidentberedskap](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-2-encrypt-backup-data"></a>BR-2: kryptera säkerhets kopierings data

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| BR-2 | 10,2 | CP-9 |

Se till att säkerhets kopiorna skyddas mot attacker. Detta bör innefatta kryptering av säkerhets kopiorna för att skydda mot förlust av konfidentialitet.

För lokala säkerhets kopieringar med Azure Backup anges kryptering vid vila med hjälp av den lösen fras som du anger. För vanliga säkerhets kopieringar av Azure-tjänster krypteras säkerhetskopierade data automatiskt med hjälp av Azure Platform-hanterade nycklar. Du kan välja att kryptera säkerhets kopiorna med hjälp av kund Managed Key. I det här fallet ser du till att den här Kundhanterade nyckeln i nyckel valvet också finns i säkerhets kopierings omfånget.

Använd rollbaserad åtkomst kontroll i Azure i Azure Backup, Azure Key Vault eller andra resurser för att skydda säkerhets kopior och Kundhanterade nycklar. Dessutom kan du aktivera avancerade säkerhetsfunktioner så att du kan kräva MFA innan du kan ändra eller ta bort säkerhets kopior.

- [Översikt över säkerhetsfunktioner i Azure Backup](../../backup/security-overview.md)

- [Kryptering av säkerhetskopieringsdata med kundhanterade nycklar](../../backup/encryption-at-rest-with-cmk.md) 

- [Säkerhetskopiera Key Vault nycklar i Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Säkerhetsfunktioner som hjälper till att skydda hybrid säkerhets kopieringar från attacker](../../backup/backup-azure-security-feature.md#prevent-attacks)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhetsarkitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Incidentberedskap](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Validera all säkerhetskopiering med kundhanterade nycklar

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| BR-3 | 10,3 | CP-4, CP-9 |

Regelbundet utföra Data återställning av säkerhets kopian. Se till att du kan återställa säkerhetskopierade nycklar som hanteras av kunden.

- [Återställa filer från säkerhets kopiering av virtuella Azure-datorer](../../backup/backup-azure-restore-files-from-vm.md)

- [Så här återställer du Key Vault nycklar i Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Incidentberedskap](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Hantering av säkerhetskompatibilitet](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: Minska risken för förlorade nycklar

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| BR-4 | 10,4 | CP-9 |

Se till att du har mått på plats för att förhindra och återställa förlust av nycklar. Aktivera mjuk borttagning och rensningsskydd i Azure Key Vault som skydd mot oavsiktlig eller skadlig borttagning.

- [Aktivera mjuk borttagning och rensningsskydd i Key Vault](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhetsarkitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Incidentberedskap](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Data säkerhet](/azure/cloud-adoption-framework/organize/cloud-security-data-security)