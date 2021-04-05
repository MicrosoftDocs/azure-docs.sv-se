---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 27a4d775b8d88e02be69655e5adfc6155f867ef6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582746"
---
Ett korrekt SSL-certifikat garanterar att du skickar krypterad information till rätt server. Förutom kryptering kan certifikatet också användas för autentisering. Du kan ladda upp ditt eget betrodda SSL-certifikat via enhetens PowerShell-gränssnitt.

1. [Anslut till PowerShell-gränssnittet](#connect-to-the-powershell-interface).
2. Använd `Set-HcsCertificate` cmdleten för att ladda upp certifikatet. Ange följande parametrar när du uppmanas till det:

   - `CertificateFilePath` – Sökvägen till resursen som innehåller certifikat filen i *PFX* -format.
   - `CertificatePassword` – Ett lösen ord som används för att skydda certifikatet.
   - `Credentials` -Användar namn för att få åtkomst till den resurs som innehåller certifikatet. Ange lösenordet till nätverksresursen när du uppmanas till det.

     I följande exempel visas användningen av denna cmdlet:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```
