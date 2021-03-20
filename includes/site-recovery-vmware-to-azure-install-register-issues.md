---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 5f794420eebb052538de0903727951de39f44a48
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001581"
---
### <a name="installation-failures"></a>Installationsfel
| **Exempel på fel meddelande** | **Rekommenderad åtgärd** |
|--------------------------|------------------------|
|ERROR   Failed to load Accounts. Fel: System.IO.IOException: Det går inte att läsa data från transportanslutningen när du installerar och registrerar CS-servern.| Kontrollera att TLS 1.0 är aktiverad på datorn. |

### <a name="registration-failures"></a>Registreringsfel
Du kan felsöka registreringsfel genom att granska loggarna i mappen **%ProgramData%\ASRLogs**.

| **Exempel på fel meddelande** | **Rekommenderad åtgärd** |
|--------------------------|------------------------|
|**09:20:06**:InnerException.Type: SrsRestApiClientLib.AcsException,InnerException.<br>Meddelande: ACS50008: SAML-token är ogiltig.<br>Spårnings-ID: 1921ea5b-4723-4be7-8087-a75d3f9e1072<br>Korrelations-ID: 62fea7e6-2197-4be4-a2c0-71ceb7aa2d97><br>Tidsstämpel: **2016-12-12 14:50:08Z<br>** | Kontrollera att tiden på din systemklocka inte skiljer sig mer än 15 minuter jämfört med din lokala tid. Kör installationsprogrammet igen för att slutföra registreringen.|
|**09:35:27** :DRRegistrationException när alla haveriberedskapsvalv för det valda certifikatet skulle hämtas: : Utlöste Exception.Type:Microsoft.DisasterRecovery.Registration.DRRegistrationException, Exception.Message: ACS50008: SAML-token är ogiltig.<br>Spårnings-ID: e5ad1af1-2d39-4970-8eef-096e325c9950<br>Korrelations-ID: abe9deb8-3e64-464d-8375-36db9816427a<br>Tidsstämpel: **2016-05-19 01:35:39Z**<br> | Kontrollera att tiden på din systemklocka inte skiljer sig mer än 15 minuter jämfört med din lokala tid. Kör installationsprogrammet igen för att slutföra registreringen.|
|06:28:45: Det gick inte att skapa certifikatet<br>06:28:45: Installationen måste avbrytas. Det går inte att skapa ett certifikat som krävs för att autentisera till Site Recovery. Kör installationen igen | Kontrol lera att du kör installations programmet som lokal administratör. |
