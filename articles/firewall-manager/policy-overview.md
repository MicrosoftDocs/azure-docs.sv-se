---
title: Översikt över Azure Firewall Manager-principer
description: Lär dig mer om Azure Firewall Manager-principer
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: victorh
ms.openlocfilehash: 73a07af0fa98adf66d6104f1ab545d31a0cfd6d7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95802031"
---
# <a name="azure-firewall-manager-policy-overview"></a>Översikt över Azure Firewall Manager-principer

Brand Väggs principen är en Azure-resurs som innehåller samlingar för NAT, nätverk och program regler och hot information-inställningar. Det är en global resurs som kan användas i flera Azure Firewall-instanser i säkra virtuella hubbar och virtuella hubb nätverk. Principer fungerar mellan regioner och prenumerationer.

![Azure Firewall Manager-princip](media/policy-overview/policy-overview.png)

## <a name="policy-creation-and-association"></a>Skapa och associera princip

En princip kan skapas och hanteras på flera sätt, inklusive Azure Portal, REST API, mallar, Azure PowerShell och CLI.

Du kan också migrera befintliga regler från Azure-brandväggen med hjälp av portalen eller Azure PowerShell för att skapa principer. Mer information finns i [så här migrerar du Azure Firewall-konfigurationer till Azure brand Väggs princip](migrate-to-policy.md). 

Principer kan associeras med en eller flera virtuella nav eller virtuella nätverk. Brand väggen kan finnas i alla prenumerationer som är kopplade till ditt konto och i vilken region som helst.

## <a name="hierarchical-policies"></a>Hierarkiska principer

Nya principer kan skapas från grunden eller ärvas från befintliga principer. Arv gör att DevOps kan skapa lokala brand Väggs principer överst i organisationen som har tilldelats bas principen.

Principer som skapats med icke-tomma överordnade principer ärver alla regel samlingar från den överordnade principen. Nätverks regel samlingarna som ärvs från en överordnad princip prioriteras alltid ovanför nätverks regel samlingar som definierats som en del av en ny princip. Samma logik gäller även för program regel samlingar. Nätverks regel samlingar bearbetas dock alltid innan program regel samlingar, oavsett arv.

Hot informations läget ärvs också från den överordnade principen. Du kan ställa in ditt hot informations läge på ett annat värde om du vill åsidosätta det här beteendet, men du kan inte inaktivera det. Det går bara att åsidosätta med ett striktare värde. Om din överordnade princip till exempel är inställd på **endast avisering**, kan du konfigurera den här lokala principen för att **Varna och neka**.

Som hot informations läge ärvs listan över hot information från den överordnade principen. Den underordnade principen kan lägga till ytterligare IP-adresser i listan över tillåtna.

NAT-regel samlingar ärvs inte eftersom de är specifikt för en specifik brand vägg.

Med arv tillämpas eventuella ändringar i den överordnade principen automatiskt ned till associerade underordnade brand Väggs principer.

## <a name="traditional-rules-and-policies"></a>Traditionella regler och principer

Azure-brandväggen stöder både traditionella regler och principer. I följande tabell jämförs principer och regler:


| Ämne | Policy  | Regler |
| ------- | ------- | ----- |
|Innehåller     |NAT, nätverk, program regler, anpassade inställningar för DNS-och DNS-proxy, IP-grupper och hot information-inställningar (inklusive listan över tillåtna)|NAT-, nätverks-och program regler, anpassade inställningar för DNS-och DNS-proxy, IP-grupper och hot informations inställningar (inklusive listan över tillåtna)|
|Skyddar     |Virtuella hubbar och virtuella nätverk|Endast virtuella nätverk|
|Portalmiljö     |Central hantering med hjälp av brand Väggs hanteraren|Fristående brand Väggs upplevelse|
|Stöd för flera brand väggar     |Brand Väggs principen är en separat resurs som kan användas i olika brand väggar|Exportera och importera regler manuellt eller med hjälp av hanterings lösningar från tredje part |
|Priser     |Faktureras baserat på brand Väggs Association. Se [prissättning](#pricing).|Kostnadsfri|
|Distributions metoder som stöds     |Portal, REST API, mallar, Azure PowerShell och CLI|Portal, REST API, mallar, PowerShell och CLI. |

## <a name="pricing"></a>Priser

Principer faktureras baserat på brand Väggs associationer. En princip med noll eller en brand Väggs koppling är kostnads fri. En princip med flera brand Väggs associationer debiteras enligt ett fast pris. Mer information finns i [priser för Azure Firewall Manager](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="next-steps"></a>Nästa steg

Information om hur du distribuerar en Azure-brandvägg finns i [Självstudier: skydda ditt moln nätverk med Azure Firewall Manager med hjälp av Azure Portal](secure-cloud-network.md).
