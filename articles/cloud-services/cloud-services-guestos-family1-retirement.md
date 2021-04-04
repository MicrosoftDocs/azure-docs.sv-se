---
title: Påminnelse om gäst operativ system familj 1 | Microsoft Docs
description: Innehåller information om när indragningen av Azure gäst operativ system familj 1 inträffade och hur du avgör om du påverkas
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
ms.service: cloud-services
ms.topic: article
ms.date: 5/21/2017
ms.author: raiye
ms.openlocfilehash: 7f6d3feee95d4cce654b2cc1547b8bd7f4eb45d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743023"
---
# <a name="guest-os-family-1-retirement-notice"></a>Påminnelse om gäst operativ system familj 1

Indragningen av OS-familjen 1 lanserades första den 1 juni 2013.

**Sept 2, 2014** Azure gäst operativ systemet (gäst operativ system) Family 1. x, som baseras på operativ systemet Windows Server 2008, drogs tillbaka officiellt. Alla försök att distribuera nya tjänster eller uppgradera befintliga tjänster som använder Family 1 kommer att Miss lyckas med ett fel meddelande om att gäst operativ system familj 1 har dragits tillbaka.

**3 November 2014** Utökat stöd för gäst operativ system familj 1 har avslut ATS och är helt tillbakadraget. Alla tjänster som fortfarande körs på familj 1 påverkas. Vi kan när som helst stoppa dessa tjänster. Det finns ingen garanti för att dina tjänster ska fortsätta att köras om du inte manuellt uppgraderar dem själv.

Om du har fler frågor kan du besöka [sidan Microsoft Q&en fråga för Cloud Services](/answers/topics/azure-cloud-services.html) eller [kontakta Azure-supporten](https://azure.microsoft.com/support/options/).

## <a name="are-you-affected"></a>Påverkas du?
Cloud Services påverkas om något av följande gäller:

1. Du har värdet "osFamily =" 1 "som anges explicit i filen ServiceConfiguration. cscfg för din moln tjänst.
2. Du har inget värde för osFamily som anges explicit i filen ServiceConfiguration. cscfg för din moln tjänst. För närvarande använder systemet standardvärdet "1" i det här fallet.
3. I Azure Portal listas ditt gäst operativ system familje värde som "Windows Server 2008".

Om du vill ta reda på vilka av dina moln tjänster som kör operativ system familjen kan du köra följande skript i Azure PowerShell, men du måste [konfigurera Azure PowerShell](/powershell/azure/) först. Mer information om skriptet finns i [Azure gäst operativ system familj 1 uttjänta: 2014 juni](/archive/blogs/ryberry/azure-guest-os-family-1-end-of-life-june-2014).

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Dina moln tjänster påverkas av OS-familj 1 om osFamily-kolumnen i skriptets utdata är tom eller innehåller en "1".

## <a name="recommendations-if-you-are-affected"></a>Rekommendationer om du påverkas
Vi rekommenderar att du migrerar dina moln tjänst roller till en av de gäst operativ system familjer som stöds:

**Gäst operativ system familj 4. x** -Windows Server 2012 R2 *(rekommenderas)*

1. Se till att ditt program använder SDK 2,1 eller senare med .NET Framework 4,0, 4,5 eller 4.5.1.
2. Ange osFamily-attributet till "4" i filen ServiceConfiguration. cscfg och distribuera om moln tjänsten.

**Gäst operativ system familj 3. x** -Windows Server 2012

1. Se till att ditt program använder SDK 1,8 eller senare med .NET Framework 4,0 eller 4,5.
2. Ange osFamily-attributet till "3" i filen ServiceConfiguration. cscfg och distribuera om din moln tjänst.

**Gäst operativ system familj 2. x** -Windows Server 2008 R2

1. Se till att ditt program använder SDK 1,3 och senare med .NET Framework 3,5 eller 4,0.
2. Ange osFamily-attributet till "2" i filen ServiceConfiguration. cscfg och distribuera om din moln tjänst.

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Utökat stöd för gäst operativ system familj 1 avslutad den 3 november 2014
Moln tjänster på gäst operativ system familj 1 stöds inte längre. Migrera från familj 1 så snart som möjligt för att undvika avbrott i tjänsten.  

## <a name="next-steps"></a>Nästa steg
Granska de senaste [versionerna av gäst operativ systemet](cloud-services-guestos-update-matrix.md).