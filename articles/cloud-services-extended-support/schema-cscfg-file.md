---
title: Definitions schema för Azure Cloud Services (utökad support) (. cscfg-fil) | Microsoft Docs
description: Information som rör definitions schema för Cloud Services (utökad support)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 3aac5ca8d146f0cd74f483846d22fdf0052f86f2
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744696"
---
# <a name="azure-cloud-services-extended-support-config-schema-cscfg-file"></a>Azure Cloud Services (utökad support) konfigurations schema (cscfg-fil)

Tjänst konfigurations filen anger antalet roll instanser som ska distribueras för varje roll i tjänsten, värdena för alla konfigurations inställningar och tumavtrycken för alla certifikat som är associerade med en roll. Om tjänsten är en del av en Virtual Network måste konfigurations information för nätverket anges i tjänst konfigurations filen, samt i konfigurations filen för det virtuella nätverket. Standard tillägget för tjänst konfigurations filen är cscfg.

Tjänst modellen beskrivs i [definitions schemat för moln tjänsten (utökad support)](schema-csdef-file.md).

Som standard installeras den Azure-diagnostik konfigurations schema filen i `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` katalogen. Ersätt `<version>` med den installerade versionen av [Azure SDK](https://azure.microsoft.com/downloads/).

Mer information om hur du konfigurerar roller i en tjänst finns i [Vad är moln tjänst modellen](../cloud-services/cloud-services-model-and-package.md).

## <a name="basic-service-configuration-schema"></a>Grundläggande tjänst konfigurations schema
Det grundläggande formatet för tjänst konfigurations filen är följande.

```xml
<ServiceConfiguration serviceName="<service-name>" osFamily="<osfamily-number>" osVersion="<os-version>" schemaVersion="<schema-version>">

  <Role …>
    …
  </Role>

  <NetworkConfiguration>
    …
  </NetworkConfiguration>

</ServiceConfiguration>
```

## <a name="schema-definitions"></a>Schema definitioner
I följande avsnitt beskrivs schemat för `ServiceConfiguration` elementet:

- [Role-schema](schema-cscfg-role.md)
- [NetworkConfiguration-schema](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Namn område för tjänst konfiguration
XML-namnområdet för tjänst konfigurations filen är: `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration` .

##  <a name="serviceconfiguration-element"></a><a name="ServiceConfiguration"></a> ServiceConfiguration-element
`ServiceConfiguration`Elementet är det översta elementet i tjänst konfigurations filen.

I följande tabell beskrivs attributen för- `ServiceConfiguration` elementet. Alla attributvärden är sträng typer.

| Attribut | Beskrivning |
| --------- | ----------- |
|serviceName|Krävs. Namnet på moln tjänsten. Namnet som anges här måste matcha det namn som anges i tjänst definitions filen.|
|osFamily|Valfritt. Anger det gäst operativ system som ska köras på roll instanser i moln tjänsten. Information om vilka gäst operativ system som stöds finns i [Azure gäst operativ system utgåvor och SDK-kompatibilitet](../cloud-services/cloud-services-guestos-update-matrix.md).<br /><br /> Om du inte inkluderar ett `osFamily` värde och du inte har angett `osVersion` attributet till en angiven gäst operativ system version, används standardvärdet 1.|
|osVersion|Valfritt. Anger den version av gäst operativ systemet som ska köras på roll instanser i moln tjänsten. Mer information om gäst operativ system versioner finns i [Azure gäst operativ system versioner och SDK-kompatibilitet matris](../cloud-services/cloud-services-guestos-update-matrix.md).<br /><br /> Du kan ange att gäst operativ systemet ska uppgraderas automatiskt till den senaste versionen. Det gör du genom att ange värdet för `osVersion` attributet till `*` . När det är inställt på `*` , distribueras roll instanserna med den senaste versionen av gäst operativ systemet för den angivna OS-familjen och uppgraderas automatiskt när nya versioner av gäst operativ systemet släpps.<br /><br /> Om du vill ange en version manuellt, använder du `Configuration String` tabellen i avsnittet **framtida, aktuella och över gångs gäst operativ system versioner** i [Azure gäst operativ system versioner och SDK-kompatibel matris](../cloud-services/cloud-services-guestos-update-matrix.md).<br /><br /> Standardvärdet för `osVersion` attributet är `*` .|
|Schema|Valfritt. Anger versionen för tjänst konfigurations schema. Med schema versionen kan Visual Studio välja rätt SDK-verktyg som ska användas för schema validering om fler än en version av SDK: n installeras sida vid sida. Mer information om kompatibilitet för scheman och versioner finns i [Azure gäst operativ system utgåvor och SDK-kompatibilitet mat ris](../cloud-services/cloud-services-guestos-update-matrix.md)|

Tjänst konfigurations filen måste innehålla ett- `ServiceConfiguration` element. `ServiceConfiguration`Elementet kan innehålla valfritt antal `Role` element och noll eller 1 `NetworkConfiguration` element.

## <a name="see-also"></a>Se även

[Definitions schema för Azure Cloud Services (utökad support) (csdef-fil)](schema-csdef-file.md)