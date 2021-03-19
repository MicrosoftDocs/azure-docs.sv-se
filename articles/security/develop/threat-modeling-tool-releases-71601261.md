---
title: Microsoft Threat Modeling Tool version 1/29/2019
titleSuffix: Azure
description: Läs viktig information om de Microsoft Threat Modeling Tool som lanserades den 1/29/2019. Anteckningarna innehåller funktions ändringar och kända problem.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 01/25/2019
ms.openlocfilehash: 17147d412bd888cdd3cd270829ad6d6103867b34
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87539058"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>Threat Modeling Tool, uppdaterad version 7.1.60126.1 – 2019-01-29

Version 7.1.60126.1 av Microsoft Threat Modeling Tool lanserades den 29 2019 januari och innehåller följande ändringar:

- Den tidigaste versionen av .NET som krävs har ökat till [.NET-4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262).
- Den tidigaste version av Windows som krävs har ökat till [uppdatering för Windows 10-årsdagen](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) på grund av .net-beroendet.
- En växlings funktion för modell validering har lagts till i verktygets alternativ meny.
- Flera länkar i egenskaperna för hotet har uppdaterats.
- Mindre UX-ändringar av verktygets Start skärm.
- Threat Modeling Tool ärver nu TLS-inställningarna för värd operativ systemet och stöds i miljöer som kräver TLS 1,2 eller senare.

## <a name="feature-changes"></a>Funktions ändringar

### <a name="model-validation-option"></a>Modell validerings alternativ

Baserat på kundfeedback har ett alternativ lagts till i verktyget för att aktivera eller inaktivera modell valideringen. Tidigare, om din mall använde ett enda enkelriktat data flöde mellan två objekt, kan det hända att du har fått ett fel meddelande i meddelande fönstret som säger: ObjectsName kräver minst ett ' any '. Om du inaktiverar modell valideringen så förhindras dessa varningar från att visas i vyn.

Alternativet att aktivera och inaktivera modell validering finns i menyn fil >inställningar->alternativ. Standardvärdet för den här inställningen är inaktiverat.

![Modell validerings alternativ](./media/threat-modeling-tool-releases-71601261/tmt_model_validation_option.png)

## <a name="system-requirements"></a>Systemkrav

- Operativsystem som stöds
  - [Uppdatering av Microsoft Windows 10-årsdag](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) eller senare
- .NET-version krävs
  - [.NET-4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) eller senare
- Ytterligare krav
  - En Internet anslutning krävs för att ta emot uppdateringar av verktyget och mallar.

## <a name="known-issues"></a>Kända problem

### <a name="unsupported-systems"></a>System som inte stöds

#### <a name="issue"></a>Problem

Användare av Windows 10-system som inte kan installera .NET 4.7.1 eller senare, t. ex. Windows 10 Enterprise LTSB (version 1507), kommer inte att kunna öppna verktyget efter uppgraderingen. Dessa äldre versioner av Windows stöds inte längre för Threat Modeling Tool och bör inte installera den senaste uppdateringen.

#### <a name="workaround"></a>Lösning

Användare av Windows 10 Enterprise LTSB (version 1507) som har installerat den senaste uppdateringen kan återgå till den tidigare versionen av Threat Modeling Tool via dialog rutan avinstallera i appar &-funktioner.

## <a name="documentation-and-feedback"></a>Dokumentation och feedback

- Dokumentationen för Threat Modeling Tool finns på [docs.Microsoft.com](threat-modeling-tool.md)och innehåller information [om hur du använder verktyget](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Nästa steg

Ladda ned den senaste versionen av [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
