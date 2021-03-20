---
title: Roll schema för Azure Cloud Services (utökad support) | Microsoft Docs
description: Information som rör roll schemat för Cloud Services (utökad support)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2567f5bb817a34f6274d5e265a266d67a9c81413
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98744704"
---
# <a name="azure-cloud-services-extended-support-config-role-schema"></a>Azure Cloud Services (utökad support) konfiguration av roll schema

`Role`Elementet i konfigurations filen anger antalet roll instanser som ska distribueras för varje roll i tjänsten, värdena för alla konfigurations inställningar och tumavtrycken för alla certifikat som är associerade med en roll.

Mer information om konfigurations schema för Azure-tjänsten finns i [konfigurations schema för moln tjänst (utökad support)](schema-cscfg-file.md). Mer information om definitions schema för Azure-tjänsten finns i [definitions schema för moln tjänst (utökad support)](schema-csdef-file.md).

##  <a name="role-element"></a><a name="Role"></a> roll element
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
[Konfigurations schema för moln tjänst (utökad support)](schema-cscfg-file.md).