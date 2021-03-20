---
title: Kryptering i Azure Backup
description: Lär dig hur krypterings funktioner i Azure Backup hjälper dig att skydda dina säkerhets kopierings data och uppfylla verksamhetens säkerhets behov.
ms.topic: conceptual
ms.date: 08/04/2020
ms.custom: references_regions
ms.openlocfilehash: c9b1b2782a34285ae194f2998a7cd053cf3c0c70
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96325695"
---
# <a name="encryption-in-azure-backup"></a>Kryptering i Azure Backup

Alla säkerhetskopierade data krypteras automatiskt när de lagras i molnet med hjälp av Azure Storage kryptering, vilket hjälper dig att uppfylla dina åtaganden om säkerhet och efterlevnad. Den här informationen i rest krypteras med 256-bitars AES-kryptering, en av de starkaste block chiffer som är tillgängliga och är FIPS 140-2-kompatibel. Förutom kryptering i vila överförs alla dina säkerhets kopierings data i överföring via HTTPS. Det finns alltid i Azure stamnät nätverket.

## <a name="levels-of-encryption-in-azure-backup"></a>Krypterings nivåer i Azure Backup

Azure Backup innehåller kryptering på två nivåer:

- **Kryptering av data i Recovery Services valvet**
  - **Använda plattforms hanterade nycklar**: som standard krypteras alla dina data med hjälp av plattforms hanterade nycklar. Du behöver inte vidta någon uttrycklig åtgärd från din sida för att aktivera den här krypteringen. Den gäller för alla arbets belastningar som säkerhets kopie ras till Recovery Services-valvet.
  - **Använda Kundhanterade nycklar**: när du säkerhetskopierar Azure-Virtual Machines kan du välja att kryptera dina data med hjälp av krypterings nycklar som ägs och hanteras av dig. Med Azure Backup kan du använda dina RSA-nycklar som lagras i Azure Key Vault för kryptering av dina säkerhets kopior. Krypterings nyckeln som används för kryptering av säkerhets kopior kan skilja sig från den som används för källan. Data skyddas med en AES 256-baserad data krypterings nyckel (DEK), som i sin tur skyddas med hjälp av dina nycklar. Detta ger dig fullständig kontroll över data och nycklar. För att tillåta kryptering krävs det att du beviljar Recovery Services valv åtkomst till krypterings nyckeln i Azure Key Vault. Du kan inaktivera nyckeln eller återkalla åtkomst när det behövs. Du måste dock aktivera kryptering med dina nycklar innan du försöker skydda några objekt i valvet. [Mer information finns här](encryption-at-rest-with-cmk.md).
  - **Kryptering på infrastruktur nivå**: förutom att kryptera dina data i Recovery Services valvet med Kundhanterade nycklar kan du också välja att ha ett extra krypterings lager konfigurerat på lagrings infrastrukturen. Den här infrastruktur krypteringen hanteras av plattformen. Tillsammans med kryptering i vila med Kundhanterade nycklar kan du använda kryptering med två lager av dina säkerhets kopierings data. Infrastruktur kryptering kan bara konfigureras om du först väljer att använda dina egna nycklar för kryptering i vila. Infrastruktur kryptering använder plattforms hanterade nycklar för att kryptera data.
- **Kryptering som är speciell för arbets belastningen som säkerhets kopie ras**  
  - **Säkerhets kopiering av virtuella Azure-datorer**: Azure Backup stöder säkerhets kopiering av virtuella datorer med diskar som har krypterats med hjälp av [plattforms hanterade nycklar](../virtual-machines/disk-encryption.md#platform-managed-keys), samt [Kundhanterade nycklar](../virtual-machines/disk-encryption.md#customer-managed-keys) som ägs och hanteras av dig. Dessutom kan du säkerhetskopiera dina virtuella Azure-datorer som har sina operativ system eller data diskar krypterade med [Azure Disk Encryption](backup-azure-vms-encryption.md#encryption-support-using-ade). ADE använder BitLocker för virtuella Windows-datorer och DM-Crypt för virtuella Linux-datorer för att utföra kryptering i gästen.

>[!NOTE]
>Infrastruktur kryptering är för närvarande i begränsad för hands version och är tillgängligt i östra USA, USA West2, södra centrala USA, US Gov, Arizona och US GOV Virginia-regioner. Om du vill använda funktionen i någon av dessa regioner kan du fylla i [det här formuläret](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUN0VHNEpJS0ZUWklUNVdJSTEzR0hIOVRMVC4u) och skicka e-post till oss på [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering av vilande data](../storage/common/storage-service-encryption.md)
- [Azure Backup vanliga frågor och svar](backup-azure-backup-faq.md#encryption) om du behöver kryptering