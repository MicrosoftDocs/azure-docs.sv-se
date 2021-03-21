---
title: Skydda och använda principer
description: Lär dig mer om säkerhet och principer för virtuella datorer i Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.date: 11/27/2018
ms.author: cynthn
ms.topic: conceptual
ms.openlocfilehash: 291b9a210bf5f8cc18ccf8a523e282d3fc85aa28
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101673343"
---
# <a name="secure-and-use-policies-on-virtual-machines-in-azure"></a>Skydda och använda principer på virtuella datorer i Azure

Det är viktigt att du håller den virtuella datorn säker för de program som du kör. Att skydda dina virtuella datorer kan omfatta en eller flera Azure-tjänster och-funktioner som omfattar säker åtkomst till dina virtuella datorer och säker lagring av dina data. Den här artikeln innehåller information som gör att du kan skydda dina virtuella datorer och program.

## <a name="antimalware"></a>Programvara mot skadlig kod

Modern Threat landskap för moln miljöer är dynamisk, vilket ökar trycket för att upprätthålla det effektiva skyddet för att uppfylla efterlevnads-och säkerhets kraven. [Microsoft Antimalware för Azure](../security/fundamentals/antimalware.md) är en kostnads fri real tids skydds funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig program vara. Aviseringar kan konfigureras för att meddela dig när kända skadlig eller oönskad program vara försöker installera sig själv eller köra på den virtuella datorn. Det finns inte stöd för virtuella datorer som kör Linux eller Windows Server 2008.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../security-center/security-center-introduction.md) hjälper dig att förhindra, identifiera och svara på hot mot dina virtuella datorer. Security Center tillhandahåller integrerad säkerhetsövervakning och princip hantering i dina Azure-prenumerationer, hjälper till att identifiera hot som annars kan gå vidare och fungerar med ett brett eko system med säkerhetslösningar.

Security Centers just-in-Time-åtkomst kan användas över din VM-distribution för att låsa inkommande trafik till dina virtuella Azure-datorer, vilket minskar exponeringen för attacker och ger enkel åtkomst till att ansluta till virtuella datorer när det behövs. När just-in-Time är aktiverat och en användare begär åtkomst till en virtuell dator, kontrollerar Security Center vilka behörigheter användaren har för den virtuella datorn. Om de har rätt behörigheter godkänns begäran och Security Center automatiskt konfigurerar nätverks säkerhets grupper (NSG: er) för att tillåta inkommande trafik till de valda portarna under en begränsad tid. När tiden har gått ut Security Center återställer NSG: er till sina tidigare tillstånd. 

## <a name="encryption"></a>Kryptering

Två krypterings metoder erbjuds för hanterade diskar. Kryptering på operativ system nivå, som är Azure Disk Encryption och kryptering på plattforms nivå, vilket är kryptering på Server sidan.

### <a name="server-side-encryption"></a>Kryptering på serversidan

Azure Managed disks krypterar automatiskt dina data som standard när de sparas i molnet. Kryptering på Server sidan skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden. Data i Azure Managed disks krypteras transparent med 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), en av de starkaste block chiffer som är tillgängliga och är FIPS 140-2-kompatibel.

Kryptering påverkar inte prestandan hos hanterade diskar. Krypteringen kostar inget extra.

Du kan förlita dig på plattforms hanterade nycklar för kryptering av din hanterade disk, eller så kan du hantera kryptering med hjälp av dina egna nycklar. Om du väljer att hantera kryptering med dina egna nycklar kan du ange en *kundhanterad nyckel* som ska användas för att kryptera och dekryptera alla data i Managed disks. 

Mer information om kryptering på Server sidan finns i artiklarna för [Windows](./disk-encryption.md) eller [Linux](./disk-encryption.md).

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Virtuella diskar i Azure kan krypteras för förbättrad [VM](linux/disk-encryption-overview.md) -säkerhet och efterlevnad för virtuella [Windows-datorer](windows/disk-encryption-overview.md) . Virtuella diskar på virtuella Windows-datorer krypteras i vila med BitLocker. Virtuella diskar på virtuella Linux-datorer krypteras i vila med hjälp av dm-crypt. 

Det kostar inget att kryptera virtuella diskar i Azure. Krypterings nycklar lagras i Azure Key Vault med hjälp av program varu skydd, eller så kan du importera eller generera nycklar i HSM: er (Hardware Security modules) som är certifierade enligt standarden FIPS 140-2 nivå 2. Dessa kryptografiska nycklar används för att kryptera och dekryptera virtuella diskar som är anslutna till den virtuella datorn. Du behåller kontrollen över dessa kryptografiska nycklar och kan granska deras användning. Ett Azure Active Directory tjänstens huvud namn är en säker mekanism för att utfärda kryptografiska nycklar när virtuella datorer är påslagna.

## <a name="key-vault-and-ssh-keys"></a>Key Vault och SSH-nycklar

Hemligheter och certifikat kan modelleras som resurser och tillhandahålls av [Key Vault](../key-vault/general/basic-concepts.md). Du kan använda Azure PowerShell för att skapa nyckel valv för virtuella [Windows-datorer](windows/key-vault-setup.md) och Azure CLI för [virtuella Linux-datorer](linux/key-vault-setup.md). Du kan också skapa nycklar för kryptering.

Åtkomst principer för nyckel valv beviljar behörigheter till nycklar, hemligheter och certifikat separat. Du kan till exempel ge en användare åtkomst till bara nycklar, men inte hemligheter. Behörighet att komma åt nycklar eller hemligheter eller certifikat är dock valvnivå. [Åtkomst principen för nyckel valvet](../key-vault/general/secure-your-key-vault.md) stöder med andra ord inte behörigheter på objekt nivå.

När du ansluter till virtuella datorer bör du använda kryptering med offentliga nycklar för att skapa ett säkrare sätt att logga in på dem. Den här processen omfattar ett offentligt och privat nyckel utbyte med hjälp av SSH-kommandot (Secure Shell) för att autentisera sig själv i stället för ett användar namn och lösen ord. Lösen ord är utsatta för angrepp som är utsatta för intrång, särskilt på Internetbaserade virtuella datorer, till exempel webb servrar. Med ett SSH-nyckelpar (Secure Shell) kan du skapa en [virtuell Linux-dator](linux/mac-create-ssh-keys.md) som använder SSH-nycklar för autentisering, vilket eliminerar behovet av lösen ord för inloggning. Du kan också använda SSH-nycklar för att ansluta från en [virtuell Windows-dator](linux/ssh-from-windows.md) till en virtuell Linux-dator.

## <a name="managed-identities-for-azure-resources"></a>Hanterade identiteter för Azure-resurser

En vanlig utmaning vid utvecklingen av molnprogram är hur man ska hantera autentiseringsuppgifterna i koden som krävs för autentisering mot molntjänsterna. Det är viktigt att dessa autentiseringsuppgifter skyddas. Helst bör autentiseringsuppgifterna aldrig visas på utvecklarnas arbetsstationer eller checkas in i källkontrollen. Azure Key Vault är ett sätt att lagra autentiseringsuppgifter, hemligheter och andra nycklar på ett säkert sätt, men din kod måste autentisera mot Key Vault för att kunna hämta dem. 

Funktionen Hanterade identiteter för Azure-resurser i Azure Active Directory (Azure AD) löser det här problemet. Funktionen förser Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Du kan använda identiteten för att autentisera mot alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan att du behöver lägga in några autentiseringsuppgifter i din kod.  Din kod som körs på en virtuell dator kan begära en token från två slut punkter som endast är tillgängliga från den virtuella datorn. Mer detaljerad information om den här tjänsten finns på översikts sidan [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) .   

## <a name="policies"></a>Principer

[Azure-principer](../governance/policy/overview.md) kan användas för att definiera det önskade beteendet för din organisations virtuella [Windows-datorer](./windows/policy.md) och [virtuella Linux-datorer](./linux/policy.md). Med hjälp av principer kan en organisation tillämpa olika konventioner och regler i hela företaget. Verk ställandet av det önskade beteendet kan hjälpa till att minska risken och bidra till organisationens framgång.

## <a name="azure-role-based-access-control"></a>Rollbaserad Azure-åtkomstkontroll

Med hjälp av [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md)kan du åtskilja uppgifter i teamet och bara ge åtkomst till användare på den virtuella datorn som de behöver för att utföra sina jobb. I stället för att ge alla obegränsade behörigheter på den virtuella datorn kan du bara tillåta vissa åtgärder. Du kan konfigurera åtkomst kontroll för den virtuella datorn i [Azure Portal](../role-based-access-control/role-assignments-portal.md), med hjälp av [Azure CLI](/cli/azure/role)eller[Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).


## <a name="next-steps"></a>Nästa steg
- Gå igenom stegen för att övervaka säkerheten för virtuella datorer med hjälp av Azure Security Center för [Linux](../security/fundamentals/overview.md) eller [Windows](/previous-versions/azure/virtual-machines/tutorial-azure-security).