---
title: Metodtips för att använda Key Vault – Azure Key Vault | Microsoft Docs
description: Läs om metodtips för Azure Key Vault, inklusive att kontrollera åtkomst, när du ska använda separata nyckelvalv, rekommendationer, loggning och återställningsalternativ.
services: key-vault
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: mbaldwin
ms.openlocfilehash: 83fc2639ccfeccb7de974739562e1a212bac78a4
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749914"
---
# <a name="best-practices-to-use-key-vault"></a>Metodtips för att använda Key Vault

## <a name="use-separate-key-vaults"></a>Använda separata nyckelvalv

Vår rekommendation är att använda ett valv per program per miljö (utveckling, förproduktion och produktion). Detta hjälper dig att inte dela hemligheter mellan miljöer och minskar även hotet vid ett intrång.

## <a name="control-access-to-your-vault"></a>Kontrollera åtkomsten till ditt valv

Azure Key Vault är en molntjänst som skyddar krypteringsnycklar och hemligheter som certifikat, anslutningssträngar och lösenord. Eftersom dessa data är känsliga och affärskritiska måste du skydda åtkomsten till dina nyckelvalv genom att endast tillåta auktoriserade program och användare. Den [här](security-overview.md) artikeln innehåller en översikt över Key Vault åtkomstmodellen. Den förklarar autentisering och auktorisering och beskriver hur du skyddar åtkomsten till dina nyckelvalv.

Här är några förslag när du kontrollerar åtkomsten till valvet:
1. Lås åtkomst till din prenumeration, resursgrupp och Nyckelvalv (Azure RBAC)
2. Skapa åtkomstprinciper för varje valv
3. Använd åtkomstobjekt med minsta behörighet för att bevilja åtkomst
4. Aktivera brandvägg och [VNET-tjänstslutpunkter](overview-vnet-service-endpoints.md)

## <a name="backup"></a>Backup

Se till att du tar regelbundna valv back ups när du uppdaterar/tar bort/skapar objekt i ett valv.

### <a name="azure-powershell-backup-commands"></a>Azure PowerShell för säkerhetskopiering

* [Säkerhetskopieringscertifikat](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate)
* [Säkerhetskopieringsnyckel](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey)
* [Säkerhetskopieringshemlighet](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)

### <a name="azure-cli-backup-commands"></a>Azure CLI-säkerhetskopieringskommandon

* [Säkerhetskopieringscertifikat](/cli/azure/keyvault/certificate#az-keyvault-certificate-backup)
* [Säkerhetskopieringsnyckel](/cli/azure/keyvault/key#az-keyvault-key-backup)
* [Säkerhetskopieringshemlighet](/cli/azure/keyvault/secret#az-keyvault-secret-backup)


## <a name="turn-on-logging"></a>Aktivera loggning

[Aktivera loggning](logging.md) för valvet. Konfigurera även aviseringar.

## <a name="turn-on-recovery-options"></a>Aktivera återställningsalternativ

1. Aktivera Mjuk [borttagning.](soft-delete-overview.md)
2. Aktivera rensningsskydd om du vill skydda mot tvingad borttagning av hemligheten/valvet även när mjuk borttagning har aktiverats.