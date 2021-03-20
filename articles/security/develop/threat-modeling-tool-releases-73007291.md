---
title: Microsoft Threat Modeling Tool version 07/29/2020 – Azure
description: Dokumenterar viktig information om versions 7.3.00729.1 för Threat Models Tool.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: 3e6fcd52ad9cb6c127c14bac2f33223fb921519e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94516381"
---
# <a name="threat-modeling-tool-update-release-73007291---07292020"></a>Threat Modeling Tool uppdaterings version 7.3.00729.1 – 07/29/2020

Version 7.3.00729.1 av Microsoft Threat Modeling Tool (TMT) lanserades den 29 2020 juli och innehåller följande ändringar:

- Felkorrigeringar
 
## <a name="known-issues"></a>Kända problem

### <a name="errors-related-to-tmt7application-file-deserialization"></a>Fel som rör avserialisering av TMT7. program fil

#### <a name="issue"></a>Problem

Vissa kunder har rapporterat att följande fel meddelande visas vid hämtning av Threat Modeling Tool:

```
The threat model file '$PATH\TMT7.application' could not be deserialized. File is not an actual threat model or the threat model may be corrupted.
```

Det här felet beror på att vissa webbläsare inte har inbyggt stöd för ClickOnce-installation. I sådana fall hämtas program filen ClickOnce till användarens hård disk.

#### <a name="workaround"></a>Lösning

Det här felet fortsätter att visas om Threat Modeling Tool startas genom att dubbelklicka på filen TMT7. Application. Men när felet har hoppats över fungerar verktyget normalt. I stället för att starta Threat Modeling Tool genom att dubbelklicka på filen TMT7. Application ska användarna använda genvägar som skapats på Windows-menyn under installationen för att starta Threat Modeling Tool.

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