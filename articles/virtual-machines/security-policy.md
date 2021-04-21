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
ms.openlocfilehash: 840045da33938d4c1cd725fd5a99bf1b8014f6b1
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748474"
---
# <a name="secure-and-use-policies-on-virtual-machines-in-azure"></a>Skydda och använda principer på virtuella datorer i Azure

Det är viktigt att skydda den virtuella datorn (VM) för de program som du kör. Att skydda dina virtuella datorer kan omfatta en eller flera Azure-tjänster och -funktioner som omfattar säker åtkomst till dina virtuella datorer och säker lagring av dina data. Den här artikeln innehåller information som hjälper dig att skydda dina virtuella datorer och program.

## <a name="antimalware"></a>Programvara mot skadlig kod

Det moderna hotlandskapet för molnmiljöer är dynamiskt, vilket ökar trycket att upprätthålla ett effektivt skydd för att uppfylla efterlevnads- och säkerhetskraven. [Microsoft Antimalware för Azure](../security/fundamentals/antimalware.md) är en kostnadsfri realtidsskyddsfunktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig programvara. Aviseringar kan konfigureras för att meddela dig när känd skadlig eller oönskad programvara försöker installera sig själv eller köras på den virtuella datorn. Det stöds inte på virtuella datorer som kör Linux eller Windows Server 2008.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../security-center/security-center-introduction.md) hjälper dig att förhindra, identifiera och svara på hot mot dina virtuella datorer. Security Center ger integrerad säkerhetsövervakning och principhantering i dina Azure-prenumerationer, hjälper till att identifiera hot som annars kan gå obemärkt förbi och fungerar med ett brett ekosystem med säkerhetslösningar.

Security Center just-in-time-åtkomst kan tillämpas i hela distributionen av den virtuella datorn för att låsa inkommande trafik till dina virtuella Azure-datorer, vilket minskar exponeringen för attacker samtidigt som du enkelt kan ansluta till virtuella datorer när det behövs. När just-in-time är aktiverat och en användare begär åtkomst till en virtuell Security Center kontrollerar vilka behörigheter användaren har för den virtuella datorn. Om de har rätt behörigheter godkänns begäran och Security Center konfigurerar automatiskt nätverkssäkerhetsgrupperna (NSG:er) för att tillåta inkommande trafik till de valda portarna under en begränsad tid. När tiden har gått ut Security Center NSG:erna till sina tidigare tillstånd. 

## <a name="encryption"></a>Kryptering

Det finns två krypteringsmetoder för hanterade diskar. Kryptering på OS-nivå, som Azure Disk Encryption, och kryptering på plattformsnivå, vilket är kryptering på serversidan.

### <a name="server-side-encryption"></a>Kryptering på serversidan

Hanterade Azure-diskar krypterar automatiskt dina data som standard när de sparas i molnet. Kryptering på serversidan skyddar dina data och hjälper dig att uppfylla organisationens säkerhets- och efterlevnadsåtaganden. Data i Azure-hanterade diskar krypteras transparent med 256-bitars [AES-kryptering,](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)ett av de starkaste blockchiffreringarna som är tillgängliga och är FIPS 140-2-kompatibel.

Kryptering påverkar inte prestanda för hanterade diskar. Krypteringen kostar inget extra.

Du kan förlita dig på plattformshanterade nycklar för kryptering av din hanterade disk eller hantera kryptering med dina egna nycklar. Om du väljer att hantera kryptering med dina  egna nycklar kan du ange en kundhanterad nyckel som ska användas för kryptering och dekryptering av alla data i hanterade diskar. 

Mer information om kryptering på serversidan finns i artiklarna för [Windows](./disk-encryption.md) eller [Linux.](./disk-encryption.md)

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Virtuella [diskar i](windows/disk-encryption-overview.md) Azure kan krypteras för förbättrad säkerhet och efterlevnad för virtuella Windows-datorer och [Virtuella Linux-datorer.](linux/disk-encryption-overview.md) Virtuella diskar på virtuella Windows-datorer krypteras i vila med hjälp av BitLocker. Virtuella diskar på virtuella Linux-datorer krypteras i vila med dm-crypt. 

Det finns ingen avgift för kryptering av virtuella diskar i Azure. Kryptografiska nycklar lagras i Azure Key Vault med hjälp av programvaruskydd, eller så kan du importera eller generera dina nycklar i maskinvarusäkerhetsmoduler (HSM)-certifierade enligt FIPS 140-2 level 2-standarder. Dessa kryptografiska nycklar används för att kryptera och dekryptera virtuella diskar som är anslutna till den virtuella datorn. Du behåller kontrollen över dessa kryptografiska nycklar och kan granska deras användning. Ett Azure Active Directory tjänsthuvudnamn ger en säker mekanism för att utfärda dessa kryptografiska nycklar när virtuella datorer är påslagna och avstängda.

## <a name="key-vault-and-ssh-keys"></a>Key Vault- och SSH-nycklar

Hemligheter och certifikat kan modelleras som resurser och tillhandahålls av [Key Vault](../key-vault/general/basic-concepts.md). Du kan använda Azure PowerShell för att skapa nyckelvalv för virtuella [Windows-datorer](windows/key-vault-setup.md) och Azure CLI för [virtuella Linux-datorer.](linux/key-vault-setup.md) Du kan också skapa nycklar för kryptering.

Åtkomstprinciper för nyckelvalv beviljar behörigheter till nycklar, hemligheter och certifikat separat. Du kan till exempel ge en användare åtkomst till bara nycklar, men inte hemligheter. Behörighet att komma åt nycklar eller hemligheter eller certifikat är dock valvnivå. Åtkomstprincipen för [nyckelvalvet stöder med](../key-vault/general/security-overview.md) andra ord inte behörigheter på objektnivå.

När du ansluter till virtuella datorer bör du använda kryptografi med offentliga nycklar för att ge ett säkrare sätt att logga in på dem. Den här processen omfattar ett offentligt och privat nyckelutbyte med hjälp av SSH-kommandot (Secure Shell) för att autentisera dig själv i stället för ett användarnamn och lösenord. Lösenord är sårbara för råstyrkattacker, särskilt på Internetuppriktade virtuella datorer, till exempel webbservrar. Med ett SSH-nyckelpar (Secure Shell) kan du skapa en virtuell [Linux-dator](linux/mac-create-ssh-keys.md) som använder SSH-nycklar för autentisering, vilket eliminerar behovet av lösenord för att logga in. Du kan också använda SSH-nycklar för att ansluta från en virtuell [Windows-dator](linux/ssh-from-windows.md) till en virtuell Linux-dator.

## <a name="managed-identities-for-azure-resources"></a>Hanterade identiteter för Azure-resurser

En vanlig utmaning vid utvecklingen av molnprogram är hur man ska hantera autentiseringsuppgifterna i koden som krävs för autentisering mot molntjänsterna. Det är viktigt att dessa autentiseringsuppgifter skyddas. Helst bör autentiseringsuppgifterna aldrig visas på utvecklarnas arbetsstationer eller checkas in i källkontrollen. Azure Key Vault är ett sätt att lagra autentiseringsuppgifter, hemligheter och andra nycklar på ett säkert sätt, men din kod måste autentisera mot Key Vault för att kunna hämta dem. 

Funktionen Hanterade identiteter för Azure-resurser i Azure Active Directory (Azure AD) löser det här problemet. Funktionen förser Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Du kan använda identiteten för att autentisera mot alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan att du behöver lägga in några autentiseringsuppgifter i din kod.  Din kod som körs på en virtuell dator kan begära en token från två slutpunkter som endast är tillgängliga från den virtuella datorn. Mer detaljerad information om den här tjänsten finns på översiktssidan [för hanterade identiteter för](../active-directory/managed-identities-azure-resources/overview.md) Azure-resurser.   

## <a name="policies"></a>Principer

[Azure-principer](../governance/policy/overview.md) kan användas för att definiera önskat beteende för din organisations virtuella [Windows-datorer och](./windows/policy.md) [virtuella Linux-datorer.](./linux/policy.md) Med hjälp av principer kan en organisation framtvinga olika konventioner och regler i hela företaget. Framtvingande av det önskade beteendet kan minska risken samtidigt som det bidrar till att organisationen lyckas.

## <a name="azure-role-based-access-control"></a>Rollbaserad Azure-åtkomstkontroll

Med hjälp av rollbaserad åtkomstkontroll [i Azure (Azure RBAC)](../role-based-access-control/overview.md)kan du åtseger uppgifter i ditt team och endast bevilja den mängd åtkomst till användare på den virtuella datorn som de behöver för att utföra sitt arbete. I stället för att ge alla obegränsad behörighet på den virtuella datorn kan du bara tillåta vissa åtgärder. Du kan konfigurera åtkomstkontroll för den virtuella datorn [i Azure Portal](../role-based-access-control/role-assignments-portal.md), med hjälp av [Azure CLI](/cli/azure/role)eller[Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).


## <a name="next-steps"></a>Nästa steg
- Gå igenom stegen för att övervaka säkerheten för virtuella datorer med hjälp av Azure Security Center [för Linux](../security/fundamentals/overview.md) eller [Windows](/previous-versions/azure/virtual-machines/tutorial-azure-security).