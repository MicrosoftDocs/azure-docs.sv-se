---
title: Microsoft Threat Modeling Tool version 02/11/2020 – Azure
description: Dokumenterar viktig information om versions 7.3.00206.1 för Threat Models Tool.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 9f9b162460cd2e7a624c1ad3f992011487d1b795
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94516942"
---
# <a name="threat-modeling-tool-update-release-73002061---02112020"></a>Threat Modeling Tool uppdaterings version 7.3.00206.1 – 02/11/2020

Version 7.3.00206.1 av Microsoft Threat Modeling Tool (TMT) släpptes den 11 2020 februari och innehåller följande ändringar:

- Felkorrigeringar

## <a name="notable-bug-fixes"></a>Viktiga fel korrigeringar

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Fel relaterade till prioritets värden utanför förväntade intervall

Vissa kunder hade rapporterat följande fel meddelande när de öppnade filer som skapades i "Threat Modeling Tool 2016" eller anpassade mallar:

```output
System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()

System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)
```

Det här problemet har lösts i den här versionen.

## <a name="system-requirements"></a>Systemkrav

- Operativsystem som stöds
  - [Uppdatering av Microsoft Windows 10-årsdag](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) eller senare
- .NET-version krävs
  - [.NET-4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) eller senare
- Ytterligare krav
  - En Internet anslutning krävs för att ta emot uppdateringar av verktyget och mallar.

## <a name="documentation-and-feedback"></a>Dokumentation och feedback

- Dokumentationen för Threat Modeling Tool finns på [docs.Microsoft.com](./threat-modeling-tool.md)och innehåller information [om hur du använder verktyget](./threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Nästa steg

Ladda ned den senaste versionen av [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).