---
title: ta med fil
description: ta med fil
services: batch
documentationcenter: ''
author: JnHs
manager: evansma
editor: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.date: 02/16/2021
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: baf146bdd89d45c5d7e1ed359822a35d383b7b6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103561928"
---
### <a name="general-requirements"></a>Allmänna krav

* Det virtuella nätverket måste vara i samma prenumeration och region som det Batch-konto som du använder för att skapa din pool.

* Det undernät som anges för poolen måste ha tillräckliga otilldelade IP-adresser för det antal virtuella datorer som är mål för poolen. Summan av egenskaperna `targetDedicatedNodes` och `targetLowPriorityNodes` för poolen. Om undernätet inte har tillräckligt med lediga IP-adresser, allokerar poolen datornoderna partiellt och ett storleksändringsfel inträffar.

* Azure Storage-slutpunkt behöver matchas av eventuella anpassade DNS-servrar som servar ditt virtuella nätverk. Mer specifikt, ska URL:er av formen `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` och `<account>.blob.core.windows.net` vara matchningsbara.

* Flera pooler kan skapas i samma VNet eller i samma undernät (så länge det finns tillräckligt med adress utrymme). En enskild pool får inte finnas i flera virtuella nätverk eller undernät.

Ytterligare krav för virtuella nätverk varierar beroende på huruvida Batch-poolen finns i den virtuella datorkonfigurationen eller Cloud Services-konfigurationen. För nya pooldistributioner till ett virtuellt nätverk rekommenderas konfigurationen för virtuell dator.

### <a name="pools-in-the-virtual-machine-configuration"></a>Pooler i konfigurationen för virtuell dator

**Virtuella nätverk som stöds** – endast Azure Resource Manager-baserade virtuella nätverk

**Undernät-ID** – när du anger undernätet med hjälp av Batch API:er använder du *resursidentifieraren* för undernätet. Undernät-ID har formatet:

`/subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}`

**Behörigheter** – kontrollera huruvida dina säkerhetsprinciper eller lås på det virtuella nätverkets prenumeration eller resursgruppen begränsar en användares behörigheter att hantera det virtuella nätverket.

**Ytterligare nätverks resurser** – batch skapar automatiskt ytterligare nätverks resurser i resurs gruppen som innehåller VNet.

> [!IMPORTANT]
> För varje 100-dedikerad eller låg prioritets nod skapar batch: en nätverks säkerhets grupp (NSG), en offentlig IP-adress och en belastningsutjämnare. Dessa resurser begränsas av prenumerationens [resurskvoter](../articles/azure-resource-manager/management/azure-subscription-service-limits.md). För stora pooler kan du behöva begära en kvot ökning för en eller flera av dessa resurser.

#### <a name="network-security-groups-batch-default"></a>Nätverks säkerhets grupper: standard för batch

Under nätet måste tillåta inkommande kommunikation från batch-tjänsten för att kunna schemalägga aktiviteter på datornoderna och utgående kommunikation för att kommunicera med Azure Storage eller andra resurser efter behov av din arbets belastning. För pooler i konfigurationen för den virtuella datorn lägger batchen till NSG: er på nätverkskorts nivån som är kopplad till datornoderna. Dessa NSG: er konfigureras med följande ytterligare regler:

* Inkommande TCP-trafik på portarna 29876 och 29877 från batch-tjänstens IP-adresser som motsvarar `BatchNodeManagement` tjänst tag gen.
* Inkommande TCP-trafik på port 22 (Linux-noder) eller port 3389 (Windows-noder) för att tillåta fjärråtkomst. För vissa typer av aktiviteter med flera instanser i Linux (till exempel MPI) måste du även tillåta SSH port 22-trafik för IP-adresser i under nätet som innehåller batch-datornoder. Detta kan blockeras per NSG-regler för under näts nivå (se nedan).
* Utgående trafik på vilken port som helst till det virtuella nätverket. Detta kan ändras per NSG-regler för under näts nivå (se nedan).
* Utgående trafik på alla portar till Internet. Detta kan ändras per NSG-regler för under näts nivå (se nedan).

> [!IMPORTANT]
> Använd försiktighet om du ändrar eller lägger till regler för inkommande eller utgående trafik i batch-konfigurerade NSG: er. Om kommunikationen till Compute-noderna i det angivna under nätet nekas av en NSG, kommer batch-tjänsten att ange status för beräknings noderna till **oanvändbar**. Dessutom bör inga resurs lås användas för någon resurs som skapats av batch, eftersom detta kan förhindra att resurser rensas till följd av användar initierade åtgärder som att ta bort en pool.

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>Nätverks säkerhets grupper: Ange regler för under näts nivå

Du behöver inte ange NSG: er på under näts nivån för det virtuella nätverket eftersom batch konfigurerar sin egen NSG: er (se ovan). Om du har en NSG som är associerad med under nätet där batch Compute-noderna distribueras, eller om du vill tillämpa anpassade NSG-regler för att åsidosätta de standardvärden som tillämpas, måste du konfigurera den här NSG med minst de inkommande och utgående säkerhets reglerna som visas i följande tabeller.

Konfigurera inkommande trafik på port 3389 (Windows) eller 22 (Linux) endast om du behöver tillåta fjärråtkomst till Compute-noderna från externa källor. Du kan behöva aktivera port 22-regler på Linux om du behöver stöd för aktiviteter med flera instanser med vissa MPI-körningar. Att tillåta trafik på dessa portar är inte absolut nödvändigt för att datornoder ska kunna användas.

> [!WARNING]
> IP-adresser för batch-tjänsten kan ändras med tiden. Därför rekommenderar vi starkt att du använder `BatchNodeManagement` tjänst tag gen (eller en regional variant) för de NSG-regler som anges i följande tabeller. Undvik att fylla i NSG-regler med en speciell IP-adress för batch-tjänsten.

**Säkerhetsregler för inkommande trafik**

| Käll-IP-adresser | Käll tjänst tag gen | Källportar | Mål | Målportar | Protokoll | Action |
| --- | --- | --- | --- | --- | --- | --- |
| Ej tillämpligt | `BatchNodeManagement`[service tag](../articles/virtual-network/network-security-groups-overview.md#service-tags) (om du använder en regional variant, i samma region som batch-kontot) | * | Valfri | 29876–29877 | TCP | Tillåt |
| Användar Källans IP-adresser för fjärråtkomst fjärråtkomst till Compute-noder och/eller Compute Node-undernät för Linux-aktiviteter med flera instanser, om det behövs. | Ej tillämpligt | * | Valfri | 3389 (Windows), 22 (Linux) | TCP | Tillåt |

**Säkerhetsregler för utgående trafik**

| Källa | Källportar | Mål | Måltjänsttagg | Målportar | Protokoll | Action |
| --- | --- | --- | --- | --- | --- | --- |
| Valfri | * | [Tjänsttagg](../articles/virtual-network/network-security-groups-overview.md#service-tags) | `Storage` (om du använder regional variant, i samma region som batch-kontot) | 443 | TCP | Tillåt |
| Valfri | * | [Tjänsttagg](../articles/virtual-network/network-security-groups-overview.md#service-tags) | `BatchNodeManagement` (om du använder regional variant, i samma region som batch-kontot) | 443 | TCP | Tillåt |

Utgående till `BatchNodeManagement` krävs för att kontakta batch-tjänsten från Compute-noderna, till exempel för Job Manager-uppgifter.

### <a name="pools-in-the-cloud-services-configuration"></a>Pooler i Cloud Services-konfigurationen

> [!WARNING]
> Konfigurations pooler för moln tjänster är inaktuella. Använd konfigurations pooler för virtuella datorer i stället.

**Virtuella nätverk som stöds** – endast klassiska virtuella nätverk

**Undernät-ID** – när du anger undernätet med hjälp av Batch API:er använder du *resursidentifieraren* för undernätet. Undernät-ID har formatet:

`/subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicNetwork /virtualNetworks/{network}/subnets/{subnet}`

**Behörigheter** - `Microsoft Azure Batch` tjänstens huvud namn måste ha `Classic Virtual Machine Contributor` Azure-rollen för angivet VNet.

#### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

Undernätet måste tillåta inkommande kommunikation från Batch-tjänsten för att kunna schemalägga uppgifter på beräkningsnoderna samt utgående kommunikation för att kommunicera med Azure Storage eller andra resurser.

Du behöver inte ange en NSG eftersom Batch endast konfigurerar inkommande kommunikation från Batch-IP-adresser till poolnoderna. Om det angivna undernätet dock har associerade NSG:er (NSG:er) och/eller en brandvägg konfigurerar du säkerhetsreglerna för inkommande och utgående trafik enligt det som visas i följande tabeller. Om kommunikationen till Compute-noderna i det angivna under nätet nekas av en NSG, anger batch-tjänsten status för beräknings noderna till **oanvändbar**.

Konfigurera inkommande trafik på port 3389 för Windows om du behöver tillåta RDP-åtkomst till noderna i poolen. Detta krävs inte för att pool-noderna ska kunna användas.

**Säkerhetsregler för inkommande trafik**

| Käll-IP-adresser | Källportar | Mål | Målportar | Protokoll | Action |
| --- | --- | --- | --- | --- | --- |
Valfri <br /><br />Även om detta i princip kräver ”tillåt alla” så tillämpar Batch-tjänsten en ACL-regel på nivån för varje nod som filtrerar ut alla IP-adresser som inte gäller för Batch-tjänsten. | * | Valfri | 10100, 20100, 30100 | TCP | Tillåt |
| Valfritt, för att tillåta RDP-åtkomst till Compute-noder. | * | Valfri | 3389 | TCP | Tillåt |

**Säkerhetsregler för utgående trafik**

| Källa | Källportar | Mål | Målportar | Protokoll | Action |
| --- | --- | --- | --- | --- | --- |
| Valfri | * | Valfri | 443  | Valfri | Tillåt |
