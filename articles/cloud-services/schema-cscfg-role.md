---
title: Roll schema för Azure Cloud Services (klassisk) | Microsoft Docs
description: Roll elementet i en tjänst konfigurations fil anger hur många roll instanser som ska distribueras för varje roll, konfigurations värden och certifikat tumavtrycken.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2dc8e14a4e4d8855abb615632bb7d43b9034d360
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743448"
---
# <a name="azure-cloud-services-classic-config-role-schema"></a>Schema för konfigurations roll för Azure Cloud Services (klassisk)

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

`Role`Elementet i konfigurations filen anger antalet roll instanser som ska distribueras för varje roll i tjänsten, värdena för alla konfigurations inställningar och tumavtrycken för alla certifikat som är associerade med en roll.

Mer information om konfigurations schema för Azure-tjänsten finns i [konfigurations schema för moln tjänst (klassisk)](schema-cscfg-file.md). Mer information om definitions schema för Azure-tjänsten finns i [definitions schema för moln tjänst (klassisk)](schema-csdef-file.md).

##  <a name="role-element"></a><a name="Role"></a> Roll element
I följande exempel visas `Role` elementet och dess underordnade element.

```xml 
<ServiceConfiguration>
  <Role name="<role-name>" vmName="<vm-name>">
    <Instances count="<number-of-instances>"/>
    <ConfigurationSettings>
      <Setting name="<setting-name>" value="<setting-value>" />
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="<certificate-name>" thumbprint="<certificate-thumbprint>" thumbprintAlgorithm="<algorithm>"/>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

I följande tabell beskrivs attributen för `Role` elementet.

| Attribut | Beskrivning |
| --------- | ----------- |
| name   | Krävs. Anger namnet på rollen. Namnet måste matcha det angivna namnet för rollen i tjänst definitions filen.|
| vmName | Valfritt. Anger DNS-namnet för en virtuell dator. Namnet får innehålla högst 10 tecken.|

I följande tabell beskrivs `Role` elementets underordnade element.

| Element | Beskrivning |
| ------- | ----------- |
| Instanser | Krävs. Anger antalet instanser som ska distribueras för rollen. Antalet instanser definieras av ett heltal för `count` attributet.|
| Inställning   | Valfritt. Anger ett inställnings namn och-värde i en samling inställningar för en roll. Inställnings namnet definieras av en sträng för `name` attributet och värdet Setting definieras av en sträng för `value` attributet.|
| Certifikat | Valfritt. Anger namn, tumavtryck och algoritm för ett tjänst certifikat som ska associeras med rollen. Certifikat namnet definieras av en sträng för `name` attributet. Tumavtryck för certifikatet definieras av en sträng med hexadecimala tal som inte innehåller några blank steg för `thumbprint` attributet. De hexadecimala talen måste representeras med siffror och versaler i alfabetet. Certifikatets algoritm definieras av en sträng för `thumbprintAlgorithm` attributet.|

## <a name="see-also"></a>Se även
[Konfigurations schema för moln tjänst (klassisk)](schema-cscfg-file.md)