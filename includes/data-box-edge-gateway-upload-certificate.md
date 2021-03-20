---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "67448639"
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

