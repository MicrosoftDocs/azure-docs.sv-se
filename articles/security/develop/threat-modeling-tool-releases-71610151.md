---
title: Microsoft Threat Modeling Tool version 10/16/2019 – Azure
description: Dokumenterar viktig information om versions 7.1.61015.1 för Threat Models Tool.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 10/16/2019
ms.openlocfilehash: 761ad45496e799a555b60480ff575b9d8f30e984
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94516993"
---
# <a name="threat-modeling-tool-update-release-71610151---10162019"></a>Threat Modeling Tool, uppdaterad version 7.1.61015.1 – 2019-10-16

Version 7.1.61015.1 av Microsoft Threat Modeling Tool (TMT) släpptes den 16 2019 oktober och innehåller följande ändringar:

- Förbättrad användbarhet
- Felkorrigeringar
- Nya stenciler för Azure Logic Apps och Azure Datautforskaren

## <a name="notable-bug-fixes"></a>Viktiga fel korrigeringar

### <a name="improved-backward-compatibility-with-files-created-in-threat-modeling-tool-2016"></a>Bättre bakåtkompatibilitet med filer som skapats i "Threat Modeling Tool 2016"

Flera buggar som rör öppningen eller visningen av hot modell filer som skapats i "Threat Modeling Tool 2016" har åtgärd ATS.

## <a name="feature-enhancements"></a>Funktions förbättringar

### <a name="new-stencils-for-azure-logic-apps-and-azure-data-explorer"></a>Nya stenciler för Azure Logic Apps och Azure Datautforskaren

Nya stenciler för Azure Logic Apps och Azure Datautforskaren har lagts till i Azure-stencilen tillsammans med tillhör ande hot och begränsningar.

![Stenciler för Azure Logic Apps och Azure Datautforskaren](./media/threat-modeling-tool-releases-71610151/tmt-logic-apps.png)

## <a name="known-issues"></a>Kända problem

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Fel relaterade till prioritets värden utanför förväntade intervall

Vissa kunder har rapporterat att följande fel meddelande visas när filer som skapats i "Threat Modeling Tool 2016" eller anpassade mallar öppnas:

```output
System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()

System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)
```

Det här problemet är under undersökning

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