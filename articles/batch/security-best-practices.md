---
title: Metod tips för batch-säkerhet och efterlevnad
description: Lär dig metod tips och användbara tips för att öka säkerheten med dina Azure Batch-lösningar.
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: 6ec4a1d89ebaa9318986fc0d51e832652ba51683
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98723820"
---
# <a name="batch-security-and-compliance-best-practices"></a>Metod tips för batch-säkerhet och efterlevnad

Den här artikeln innehåller rikt linjer och metod tips för att förbättra säkerheten när du använder Azure Batch.

Azure Batch-konton har som standard en offentlig slut punkt och är allmänt tillgängliga. När en Azure Batch-pool skapas, tillhandahålls poolen i ett angivet undernät i ett virtuellt Azure-nätverk. Virtuella datorer i batch-poolen nås via offentliga IP-adresser som skapas av batch. Compute-noder i en pool kan kommunicera med varandra vid behov, t. ex. för att köra aktiviteter med flera instanser, men noder i en pool kan inte kommunicera med virtuella datorer utanför poolen.

:::image type="content" source="media/security-best-practices/typical-environment.png" alt-text="Diagram som visar en typisk batch-miljö.":::

Det finns många funktioner som hjälper dig att skapa en säkrare Azure Batch-distribution. Du kan begränsa åtkomsten till noder och minska identifieringen av noder från Internet genom [att tillhandahålla poolen utan offentliga IP-adresser](batch-pool-no-public-ip-address.md). Compute-noderna kan kommunicera på ett säkert sätt med andra virtuella datorer eller med ett lokalt nätverk genom [att tillhandahålla poolen i ett undernät i ett virtuellt Azure-nätverk](batch-virtual-network.md). Och du kan aktivera [privat åtkomst från virtuella nätverk](private-connectivity.md) från en tjänst som drivs av en privat Azure-länk.

:::image type="content" source="media/security-best-practices/secure-environment.png" alt-text="Diagram som visar en säkrare batch-miljö.":::

## <a name="general-security-related-best-practices"></a>Allmänna säkerhets-relaterade metod tips

### <a name="pool-configuration"></a>Konfiguration av pool

Många säkerhetsfunktioner är bara tillgängliga för pooler som kon figurer ATS med [konfiguration av virtuell dator](nodes-and-pools.md#configurations)och inte för pooler med Cloud Services konfiguration. Vi rekommenderar att du använder konfigurationer för virtuella datorer, som använder [skalnings uppsättningar för virtuella datorer](../virtual-machine-scale-sets/overview.md), närhelst det är möjligt.

### <a name="batch-account-authentication"></a>Autentisering med batch-konto

Åtkomst till batch-kontot har stöd för två metoder för autentisering: delad nyckel och [Azure Active Directory (Azure AD)](batch-aad-auth.md).

Vi rekommenderar starkt att du använder Azure AD för autentisering med batch-konto. Vissa batch-funktioner kräver den här autentiseringsmetoden, inklusive många av säkerhetsrelaterade funktioner som diskuteras här.

### <a name="batch-account-pool-allocation-mode"></a>Allokeringsfel för batch-konto

När du skapar ett batch-konto kan du välja mellan två [pool tilldelnings lägen](accounts.md#batch-accounts):

- **Batch-tjänst**: standard alternativet där de underliggande moln tjänsterna eller skalnings uppsättnings resurserna för virtuella datorer som används för att allokera och hantera pooler skapas i interna prenumerationer och visas inte direkt i Azure Portal. Endast batch-pooler och noder visas. 
- **Användar prenumeration**: den underliggande moln tjänsten eller den virtuella datorns skalnings uppsättnings resurser skapas i samma prenumeration som batch-kontot. Dessa resurser visas därför i prenumerationen, förutom motsvarande batch-resurser.

Med användar prenumerations läge skapas virtuella batch-datorer och andra resurser direkt i prenumerationen när en pool skapas. Läge för användar prenumeration krävs om du vill skapa batch-pooler med Azure Reserved VM Instances använder du Azure Policy på resurser för skalnings uppsättning för virtuella datorer och/eller hanterar kärn kvoten för prenumerationen (delas mellan alla batch-konton i prenumerationen). Du måste registrera din prenumeration med Azure Batch och koppla kontot till ett Azure Key Vault för att kunna skapa ett Batch-konto i läget Användarprenumeration.

## <a name="restrict-network-endpoint-access"></a>Begränsa åtkomst till nätverks slut punkt

### <a name="batch-network-endpoints"></a>Slut punkter för batch-nätverk

Tänk på att som standard används slut punkter med offentliga IP-adresser för att kommunicera med batch-konton, batch-pooler och pooler-noder.

### <a name="batch-account-api"></a>Batch-konto-API

 När ett batch-konto skapas skapas en offentlig slut punkt som används för att anropa de flesta åtgärder för kontot med hjälp av en [REST API](/rest/api/batchservice/). Konto slut punkten har en bas-URL med hjälp av formatet `https://{account-name}.{region-id}.batch.azure.com` . Åtkomst till batch-kontot skyddas, med kommunikation till konto slut punkten som krypteras med HTTPS, och varje begäran autentiseras med hjälp av antingen delad nyckel eller Azure Active Directory (Azure AD)-autentisering.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Förutom åtgärder som är särskilt för ett batch-konto gäller [hanterings åtgärder](/rest/api/batchmanagement/) för enskilda och flera batch-konton. Dessa hanterings åtgärder nås via Azure Resource Manager.

Batch Management-åtgärder via Azure Resource Manager krypteras med HTTPS, och varje begäran autentiseras med Azure AD-autentisering.

### <a name="batch-pool-nodes"></a>Noder i batch-pool

Batch-tjänsten kommunicerar med en batch Node-agent som körs på varje nod i poolen. Till exempel instruerar tjänsten Node agent att köra en uppgift, stoppa en uppgift eller hämta filer för en aktivitet. Kommunikation med Node-agenten aktive ras av en eller flera belastningsutjämnare, vilket beror på antalet noder i en pool. Belastningsutjämnaren vidarebefordrar kommunikationen till önskad nod, där varje nod åtgärdas av ett unikt port nummer. Som standard har belastningsutjämnare offentliga IP-adresser. Du kan också fjärrans luta till pooler via RDP eller SSH (denna åtkomst är aktive rad som standard, med kommunikation via belastningsutjämnare).

### <a name="restricting-access-to-batch-endpoints"></a>Begränsa åtkomsten till batch-slutpunkter 

Det finns flera funktioner som kan användas för att begränsa åtkomsten till de olika batch-slutpunkterna, särskilt när lösningen använder ett virtuellt nätverk. 

#### <a name="use-private-endpoints"></a>Använda privata slutpunkter

[Azures privata länk](../private-link/private-link-overview.md) ger åtkomst till Azure PaaS Services och Azure-värdbaserade/partner tjänster över en privat slut punkt i det virtuella nätverket. Du kan använda en privat länk för att begränsa åtkomsten till ett batch-konto inifrån det virtuella nätverket eller från ett peer-kopplat virtuellt nätverk. Resurser som är mappade till privat länk är också tillgängliga lokalt via privat peering via VPN eller Azure ExpressRoute.

Om du vill använda privata slut punkter måste ett batch-konto konfigureras på lämpligt sätt när det skapas. konfiguration av offentlig nätverks åtkomst måste inaktive ras. När de har skapats kan privata slut punkter skapas och associeras med batch-kontot. Mer information finns i [använda privata slut punkter med Azure Batch-konton](private-connectivity.md).

#### <a name="create-pools-in-virtual-networks"></a>Skapa pooler i virtuella nätverk

Compute-noder i en batch-pool kan kommunicera med varandra, till exempel för att köra aktiviteter med flera instanser, utan att ett virtuellt nätverk (VNET) krävs. Noder i en pool kan dock inte kommunicera med virtuella datorer som ligger utanför poolen i ett virtuellt nätverk och har privata IP-adresser, till exempel licens servrar eller fil servrar.

Om du vill tillåta att Compute-noder kommunicerar säkert med andra virtuella datorer eller med ett lokalt nätverk kan du konfigurera en pool så att den är i ett undernät för ett Azure VNET.

När pooler har offentliga IP-slutpunkter måste under nätet tillåta inkommande kommunikation från batch-tjänsten att kunna schemalägga aktiviteter och utföra andra åtgärder på datornoderna, och utgående kommunikation för att kommunicera med Azure Storage eller andra resurser efter behov av arbets belastningen. För pooler i den virtuella dator konfigurationen lägger batch till nätverks säkerhets grupper (NSG: er) på nätverks gränssnitts nivån som är kopplad till datornoderna. Dessa NSG: er har regler för att aktivera:

- Inkommande TCP-trafik från batch-tjänstens IP-adresser
- Inkommande TCP-trafik för fjärråtkomst
- Utgående trafik på alla portar till det virtuella nätverket (kan ändras per NSG-regler för under näts nivå)
- Utgående trafik på alla portar till Internet (kan ändras per NSG-regler för under näts nivå)

Du behöver inte ange NSG: er på under näts nivån för det virtuella nätverket eftersom batch konfigurerar sin egen NSG: er. Om du har en NSG som är associerad med under nätet där batch Compute-noderna har distribuerats, eller om du vill tillämpa anpassade NSG-regler för att åsidosätta standardvärdena, måste du konfigurera den här NSG med minst inkommande och utgående säkerhets regler för att tillåta kommunikation med batch-tjänsten till poolens noder och kommunikation i pooler till Azure Storage.

Mer information finns i [skapa en Azure Batch pool i ett virtuellt nätverk](batch-virtual-network.md).

#### <a name="create-pools-with-static-public-ip-addresses"></a>Skapa pooler med statiska offentliga IP-adresser

Som standard är de offentliga IP-adresser som är kopplade till pooler dynamiska. de skapas när en pool skapas och IP-adresser kan läggas till eller tas bort när en pool storleks änd ras. När de aktivitets program som körs på pooler behöver åtkomst till externa tjänster kan åtkomsten till dessa tjänster behöva begränsas till vissa IP-adresser.  I det här fallet går det inte att hantera dynamiska IP-adresser.

Du kan skapa statiska offentliga IP-adressresurser i samma prenumeration som batch-kontot innan du skapar en pool. Du kan sedan ange dessa adresser när du skapar poolen.

Mer information finns i [skapa en Azure Batch pool med angivna offentliga IP-adresser](create-pool-public-ip.md).

#### <a name="create-pools-without-public-ip-addresses"></a>Skapa pooler utan offentliga IP-adresser

Som standard tilldelas alla datornoder i en Azure Batch pool för konfiguration av virtuella datorer en eller flera offentliga IP-adresser. Dessa slut punkter används av batch-tjänsten för att schemalägga aktiviteter och för kommunikation med Compute-noder, inklusive utgående åtkomst till Internet.

Om du vill begränsa åtkomsten till dessa noder och minska identifieringen av dessa noder från Internet kan du etablera poolen utan offentliga IP-adresser.

Mer information finns i [skapa en pool utan offentliga IP-adresser](batch-pool-no-public-ip-address.md).

#### <a name="limit-remote-access-to-pool-nodes"></a>Begränsa fjärråtkomst till pool-noder

Som standard tillåter batch att en nods användare med nätverks anslutning ansluter externt till en Compute-nod i en batch-pool med hjälp av RDP eller SSH.

Använd någon av följande metoder för att begränsa fjärråtkomst till noder:

- Konfigurera [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) för att neka åtkomst. Lämplig nätverks säkerhets grupp (NSG) kommer att kopplas till poolen.
- Skapa poolen [utan offentliga IP-adresser](batch-pool-no-public-ip-address.md). Dessa pooler kan som standard inte nås utanför det virtuella nätverket.
- Koppla en NSG till VNet för att neka åtkomst till RDP-eller SSH-portarna.
- Skapa inte några användare på noden. Det går inte att komma åt fjärråtkomst utan någon Node-användare.

## <a name="encrypt-data"></a>Kryptera data

### <a name="encrypt-data-in-transit"></a>Kryptera data under överföring

All kommunikation till batch-kontots slut punkt (eller via Azure Resource Manager) måste använda HTTPS. Du måste använda `https://` i batch-kontots URL: er som anges i API: er vid anslutning till batch-tjänsten.

Klienter som kommunicerar med batch-tjänsten ska konfigureras att använda Transport Layer Security (TLS) 1,2.

### <a name="encrypt-batch-data-at-rest"></a>Kryptera batch-data i vila

En del av den information som anges i batch-API: er, till exempel konto certifikat, metadata för jobb och aktiviteter och aktivitets kommando rader, krypteras automatiskt när den lagras av batch-tjänsten. Som standard krypteras dessa data med Azure Batch plattforms hanterade nycklar som är unika för varje batch-konto.

Du kan också kryptera dessa data med [Kundhanterade nycklar](batch-customer-managed-key.md). [Azure Key Vault](../key-vault/general/overview.md) används för att generera och lagra nyckeln, med nyckel identifieraren registrerad med ditt batch-konto.

### <a name="encrypt-compute-node-disks"></a>Kryptera Compute Node-diskar

Batch Compute-noder har två diskar som standard: en OS-disk och den lokala tillfälliga SSD. [Filer och kataloger](files-and-directories.md) som hanteras av batch finns på den tillfälliga SSD, som är standard platsen för filer, till exempel filer för Uppgiftsutdata. Program för batch-aktiviteter kan använda standard platsen på SSD-eller OS-disken.

För extra säkerhet kan du kryptera diskarna med någon av följande funktioner för Azure Disk Encryption:

- [Hanterad disk kryptering i vila med plattforms hanterade nycklar](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [Kryptering på värden med en plattforms-hanterad nyckel](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure Disk Encryption](disk-encryption.md)

## <a name="securely-access-services-from-compute-nodes"></a>Säkert åtkomst till tjänster från Compute-noder

Batch-noder kan [komma åt autentiseringsuppgifter och hemligheter](credential-access-key-vault.md) som lagras i [Azure Key Vault](../key-vault/general/overview.md)på ett säkert sätt, vilket kan användas av uppgifts program för att få åtkomst till andra tjänster. Ett certifikat används för att ge pool-noderna åtkomst till Key Vault.

## <a name="governance-and-compliance"></a>Styrning och efterlevnad

### <a name="compliance"></a>Efterlevnad

För att hjälpa kunder att uppfylla sina egna krav på efterlevnad i reglerade branscher och marknader över hela världen, har Azure en [stor portfölj med erbjudanden för regelefterlevnad](https://azure.microsoft.com/overview/trusted-cloud/compliance). 

Dessa erbjudanden baseras på olika typer av garantier, inklusive formella certifieringar, attesteringar, verifieringar, godkännanden och utvärderingar som producerats av oberoende gransknings byråer från tredje part, samt avtals ändringar, själv bedömningar och kund väglednings dokument som skapats av Microsoft. Läs igenom den [omfattande översikten över Compliance-erbjudanden](https://aka.ms/AzureCompliance) för att avgöra vilka som kan vara relevanta för dina batch-lösningar.

### <a name="azure-policy"></a>Azure Policy

[Azure policy](../governance/policy/overview.md) hjälper till att upprätthålla organisations standarder och utvärdera kompatibiliteten i stor skala. Vanliga användnings fall för Azure Policy inkluderar implementering av styrning för resurs konsekvens, regelefterlevnad, säkerhet, kostnad och hantering.

Beroende på poolens tilldelnings läge och de resurser som en princip gäller för, använder du Azure Policy med batch på något av följande sätt:

- Direkt, med hjälp av Microsoft.BatCH/batchAccounts-resursen. En delmängd av egenskaperna för ett batch-konto kan användas. Din princip kan till exempel innehålla giltiga batch-konto regioner, tilldelnings läge för tillåten pool och om ett offentligt nätverk är aktiverat för-konton.
- Indirekt med hjälp av Microsoft. Compute/virtualMachineScaleSets-resursen. Batch-konton med poolen för användar prenumerations tilldelning kan ha en princip som har angetts på de resurser för skalnings uppsättningen för virtuella datorer som skapas i batch-kontots prenumeration. Till exempel tillåtna VM-storlekar och se till att vissa tillägg körs på varje nod i poolen.

## <a name="next-steps"></a>Nästa steg

- Granska [Azures säkerhets bas linje för batch](security-baseline.md).
- Läs mer [metod tips för Azure Batch](best-practices.md).