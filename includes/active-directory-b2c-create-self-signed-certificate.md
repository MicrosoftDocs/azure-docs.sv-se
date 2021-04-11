---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: e26dd7ea9f45af6f725f4deaefd9b5bd79a37e1c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106073545"
---
Om du inte redan har ett certifikat kan du använda ett självsignerat certifikat. Ett självsignerat certifikat är ett säkerhetscertifikat som inte är signerat av en certifikat utfärdare (CA) och som inte tillhandahåller säkerhets garantier för ett certifikat som har signerats av en certifikat utfärdare. 

# <a name="windows"></a>[Windows](#tab/windows)

I Windows använder du PowerShell: s [New-SelfSignedCertificate-](/powershell/module/pkiclient/new-selfsignedcertificate) cmdlet för att skapa ett certifikat.

1. Kör PowerShell-kommandot för att generera ett självsignerat certifikat. Ändra `-Subject` argumentet efter behov för ditt program och Azure AD B2C klient namn. Du kan också justera `-NotAfter` datumet för att ange ett annat förfallo datum för certifikatet.

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. Öppna **hantera användar certifikat**  >  **aktuella användare**  >  **personliga**  >  **certifikat**  >  *yourappname.yourtenant.onmicrosoft.com*.
1. Välj certifikatet och välj sedan **åtgärden**  >  **alla aktiviteter**  >  **Exportera**.
1. Välj **Ja**  >  **Nästa**  >  **Ja, exportera den privata nyckeln**  >  **Nästa**.
1. Acceptera standardinställningarna för **export fil format**.
1. Ange ett lösen ord för certifikatet.

För att Azure AD B2C ska acceptera. pfx-filens lösen ord måste lösen ordet vara krypterat med alternativet TripleDES-SHA1 i export verktyget för Windows Certificate Store i stället för AES256-SHA256.

# <a name="macos"></a>[macOS](#tab/macos)

I macOS använder du [certifikat assistenten](https://support.apple.com/guide/keychain-access/aside/glosa3ed0609/11.0/mac/11.0) i nyckel ringar för att skapa ett certifikat.

1. Följ anvisningarna för hur du [skapar självsignerade certifikat i nyckel rings åtkomst på Mac](https://support.apple.com/guide/keychain-access/kyca8916/mac).
1. I Access-appen för nyckel ringar på din Mac väljer du det certifikat som du skapade.
1. Välj **fil**  >  **export objekt**.
1. Välj ett fil namn för att spara certifikatet. Till exempel **självsignerat certifikat. p12**.
1. För **fil formatet** väljer du **personal information Exchange (. P12)**.
1. Välj **Spara**.
1. Ange ett **lösen ord** och **Bekräfta** lösen ordet.
1. Ersätt fil tillägget till `.pfx` . Till exempel **Self-Signed-Certificate. pfx**.

---