---
title: Red Hat-arbetsbelastningar på Azure-översikt | Microsoft Docs
description: Lär dig mer om de Red Hat-produktsortiment som är tillgängliga i Azure.
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: overview
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: bd8f2e5b825f88d1b0e04ed231b85296ac47e998
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676059"
---
# <a name="red-hat-workloads-on-azure"></a>Red Hat-arbetsbelastningar på Azure

Red Hat-arbetsbelastningar stöds via en mängd olika erbjudanden i Azure. Red Hat Enterprise Linux-avbildningar (RHEL) finns i kärnan av RHEL-arbetsbelastningar, precis som i Red Hat Update-infrastrukturen (RHUI).

## <a name="red-hat-enterprise-linux-images"></a>Red Hat Enterprise Linux bilder

Azure erbjuder ett brett utbud av RHEL-avbildningar på Azure. De här avbildningarna görs tillgängliga via två olika licensierings modeller: betala per användning och hämta din egen prenumeration (BYOS). Nya RHEL-avbildningar på Azure publiceras när nya RHEL-versioner släpps och uppdateras under hela livs cykeln, om det behövs.

### <a name="pay-as-you-go-images"></a>Betala per användning-avbildningar

Azure erbjuder en rad RHEL-avbildningar som du betalar per användning. De här avbildningarna kommer att ha rätt till RHEL och är kopplade till en källa med uppdateringar (Red Hat-uppdaterings infrastruktur). De här avbildningarna debiterar en Premium avgift för RHEL-rättigheterna och uppdateringar. RHEL för betala per användning är:

* Standard RHEL.
* RHEL för SAP.
* RHEL för SAP med hög tillgänglighet och uppdaterings tjänster.

Du kanske vill använda avbildningarna enligt principen betala per användning om du inte vill oroa dig för att betala separat för lämpligt antal prenumerationer.

### <a name="red-hat-gold-images"></a>Red Hat guld-bilder

Azure erbjuder även Red Hat Gold-bilder ( `rhel-byos` ). De här avbildningarna kan vara användbara för kunder som har befintliga Red Hat-prenumerationer och vill använda dem i Azure. Du måste aktivera dina befintliga Red Hat-prenumerationer för Red Hat Cloud Access innan du kan använda dem i Azure. Åtkomst till dessa avbildningar beviljas automatiskt när dina Red Hat-prenumerationer är aktiverade för moln åtkomst och uppfyller kraven för behörighet. Med hjälp av dessa avbildningar kan en kund undvika dubbel fakturering som kan uppstå när du använder avbildningarna enligt principen betala per användning.
* Lär dig hur du [aktiverar Red Hat-prenumerationer för moln åtkomst med Azure](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/enabling-and-maintaining-subs_cloud-access).
* Lär dig hur du [hittar de röda hat Gold-bilderna i Azure Portal, Azure CLI eller PowerShell-cmdleten](./byos.md).

> [!NOTE]
> Dubbel fakturering uppstår när en användare betalar två gånger för RHEL-prenumerationer. Det här scenariot inträffar vanligt vis när en kund använder Red Hat-Subscription-Manager för att koppla en rättighet till en virtuell dator med RHEL betala per användning. Till exempel är en kund som använder Subscription-Manager för att bifoga en rättighet för SAP-paket på en RHEL-avbildning enligt principen betala per användning indirekt dubbelt, eftersom de betalar två gånger för RHEL. De betalar en gång via Premium avgiften betala per användning och en gång via SAP-prenumerationen. Det här scenariot sker inte för att BYOS avbildnings användare.

### <a name="generation-2-images"></a>Generation 2-avbildningar

Virtuella datorer i generation 2 (VM) tillhandahåller vissa nya funktioner jämfört med virtuella datorer i generation 1. Mer information finns i [generation 2-dokumentationen](../../generation-2.md). Den viktigaste skillnaden från ett RHEL bild perspektiv är att virtuella datorer i generation 2 använder en UEFI i stället för den inbyggda program varans BIOS. De använder också GPT (GUID Partition Table) i stället för en Master Boot Record (MBR) vid start. Användningen av GPT ger bland annat OS-disk storlekar som är större än 2 TB. Dessutom körs [virtuella datorer i Mv2-serien](../../mv2-series.md) bara på generation 2-avbildningar.

RHEL generation 2-avbildningar finns på Azure Marketplace. Sök efter "Gen2" i avbildnings-SKU: n i listan över alla avbildningar som visas när du använder Azure CLI. Gå till fliken **Avancerat** i distributions processen för virtuella datorer för att distribuera en virtuell dator i generation 2.

## <a name="red-hat-update-infrastructure"></a>Red Hat Update Infrastructure

Azure tillhandahåller endast en uppdaterings infrastruktur för Red Hat för virtuella datorer med "betala per användning"-RHEL. RHUI är en spegling av Red Hat-CDN, men är bara tillgänglig för de virtuella Azure-RHEL-datorerna. Du har åtkomst till lämpliga paket beroende på vilken RHEL-avbildning som du har distribuerat. Till exempel har en RHEL för SAP-avbildning åtkomst till SAP-paketen förutom Base RHEL-paket.

### <a name="rhui-update-behavior"></a>RHUI uppdaterings beteende

RHEL-avbildningar som är anslutna till RHUI-uppdateringen som standard till den senaste lägre versionen av RHEL när en `yum update` körs. Det innebär att en virtuell RHEL 7,4-dator kan uppgraderas till RHEL 7,7 om en `yum update` åtgärd körs på den. Det här beteendet är avsiktligt för RHUI. Du kan minimera det här uppgraderings beteendet genom att växla från vanliga RHEL-lagringsplatser till [utökade uppdaterings support databaser](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [RHEL-avbildningar på Azure](./redhat-images.md).
* Läs mer om [Red Hats uppdaterings infrastruktur](./redhat-rhui.md).
* Lär dig mer om [erbjudandet för Red Hat Gold Image ( `rhel-byos` )](./byos.md).