---
title: Microsoft Threat Modeling Tool version 03/22/2020 – Azure
description: Dokumenterar viktig information om versions 7.3.00316.1 för Threat Models Tool.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: e182d40d20529b5743fa9105c68108a8ae55d943
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94516908"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>Threat Modeling Tool uppdaterings version 7.3.00316.1 – 03/22/2020

Version 7.3.00316.1 av Microsoft Threat Modeling Tool (TMT) gavs ut den 22 2020 mars och innehåller följande ändringar:

- Förbättrad användbarhet
- Felkorrigeringar
- Ny DiagramReader-funktion

## <a name="notable-bug-fixes"></a>Viktiga fel korrigeringar

### <a name="exporting-the-threat-list-to-csv"></a>Exportera hot listan till CSV

Funktionen Exportera till CSV var inkonsekvent med att välja vilka fält från hot listan som ska exporteras. Nu kommer alla fält från hot listan att exporteras till CSV-filen. 

### <a name="ux-bugs"></a>UX-buggar

- Hjälp menyerna i det primära arbets flödet (skapa/öppna/analysera) och redigeraren för mallar har nu konsekventa meny alternativ.
- Sök fältet i stencil rutan har nu en standard markör och lämpliga etiketter har lagts till.

## <a name="new-features"></a>Nya funktioner

### <a name="diagramreader-feature-has-been-added"></a>DiagramReader-funktionen har lagts till

En ny DiagramReader-funktion har lagts till på huvud menyn när en modell är öppen. Med den här funktionen konverteras den grafiska åter givningen av modellen till en textbaserad bild. 

## <a name="system-requirements"></a>Systemkrav

- Operativsystem som stöds:
  - [Uppdatering av Microsoft Windows 10-årsdag](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) eller senare
- .NET-version krävs:
  - [.NET-4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) eller senare
- Ytterligare krav:
  - En Internet anslutning för att ta emot uppdateringar till verktyget samt mallar

## <a name="documentation-and-feedback"></a>Dokumentation och feedback

- Dokumentationen för Threat Modeling Tool finns på [docs.Microsoft.com](./threat-modeling-tool.md)och innehåller information [om hur du använder verktyget](./threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Nästa steg

Ladda ned den senaste versionen av [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).