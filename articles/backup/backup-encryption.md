---
title: Kryptering i Azure Backup
description: Lär dig hur krypteringsfunktioner i Azure Backup hjälper dig att skydda dina säkerhetskopierade data och uppfylla företagets säkerhetsbehov.
ms.topic: conceptual
ms.date: 08/04/2020
ms.custom: references_regions
ms.openlocfilehash: 28d165ccc8a966091a96fc433660899d8eef1595
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518481"
---
# <a name="encryption-in-azure-backup"></a>Kryptering i Azure Backup

Alla säkerhetskopierade data krypteras automatiskt när de lagras i molnet med Azure Storage kryptering, vilket hjälper dig att uppfylla dina säkerhets- och efterlevnadsåtaganden. Dessa vilodata krypteras med 256-bitars AES-kryptering, ett av de starkaste blockchiffreringarna som är tillgängliga och är FIPS 140-2-kompatibel. Förutom kryptering i vila överförs alla dina säkerhetskopierade data under överföring via HTTPS. Den finns alltid kvar i Azure-stamnätverket.

## <a name="levels-of-encryption-in-azure-backup"></a>Krypteringsnivåer i Azure Backup

Azure Backup kryptering på två nivåer:

- **Kryptering av data i Recovery Services-valvet**
  - **Med plattformsbaserade nycklar:** Som standard krypteras alla dina data med plattformsbaserade nycklar. Du behöver inte vidta några explicita åtgärder från din slutpunkt för att aktivera den här krypteringen. Det gäller för alla arbetsbelastningar som säkerhetskopieras till Recovery Services-valvet.
  - **Använda kund hanterade nycklar:** När du Virtual Machines Azure-tjänsten kan du välja att kryptera dina data med hjälp av krypteringsnycklar som ägs och hanteras av dig. Azure Backup kan du använda dina RSA-nycklar som lagras i Azure Key Vault för att kryptera dina säkerhetskopior. Krypteringsnyckeln som används för att kryptera säkerhetskopior kan vara en annan än den som används för källan. Data skyddas med en AES 256-baserad datakrypteringsnyckel (DEK), som i sin tur skyddas med hjälp av dina nycklar. Detta ger dig fullständig kontroll över data och nycklar. För att tillåta kryptering måste du ge Recovery Services-valvet åtkomst till krypteringsnyckeln i Azure Key Vault. Du kan inaktivera nyckeln eller återkalla åtkomsten när det behövs. Du måste dock aktivera kryptering med hjälp av dina nycklar innan du försöker skydda objekt i valvet. [Mer information finns här](encryption-at-rest-with-cmk.md).
  - **Kryptering på infrastrukturnivå:** Förutom att kryptera dina data i Recovery Services-valvet med kund hanterade nycklar kan du också välja att ha ytterligare ett krypteringslager konfigurerat i lagringsinfrastrukturen. Den här infrastrukturkrypteringen hanteras av plattformen. Tillsammans med kryptering i vila med kund hanterade nycklar möjliggör det tvålagerskryptering av dina säkerhetskopierade data. Infrastrukturkryptering kan bara konfigureras om du först väljer att använda dina egna nycklar för kryptering i vila. Infrastrukturkryptering använder plattform hanterade nycklar för kryptering av data.
- **Kryptering som är specifik för den arbetsbelastning som säkerhetskopieras**  
  - **Säkerhetskopiering av virtuella** [Azure-datorer:](../virtual-machines/disk-encryption.md#platform-managed-keys)Azure Backup stöder säkerhetskopiering av virtuella datorer med diskar [](../virtual-machines/disk-encryption.md#customer-managed-keys) krypterade med plattform hanterade nycklar, samt kund hanterade nycklar som ägs och hanteras av dig. Dessutom kan du bladet dina virtuella Azure-datorer som har sina operativsystem eller datadiskar krypterade med hjälp av [Azure Disk Encryption](backup-azure-vms-encryption.md#encryption-support-using-ade). ADE använder BitLocker för virtuella Windows-datorer och DM-Crypt virtuella Linux-datorer för att utföra kryptering på gäst.

>[!NOTE]
>Infrastrukturkryptering är för närvarande i begränsad förhandsversion och är endast tillgängligt i regionerna USA, östra, USA, västra 2, USA, södra centrala, US Gov, Arizona och US GOV, Virginia. Om du vill använda funktionen i någon av dessa regioner fyller du i det här formuläret och skickar [ett e-postmeddelande](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUN0VHNEpJS0ZUWklUNVdJSTEzR0hIOVRMVC4u) till oss på [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering av vilande data](../storage/common/storage-service-encryption.md)
- [Azure Backup vanliga frågor](/backup-azure-backup-faq.yml#encryption) och svar om du har frågor om kryptering