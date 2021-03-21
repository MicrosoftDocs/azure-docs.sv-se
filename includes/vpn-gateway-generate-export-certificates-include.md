---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 946ff043828034340ae3273fc0629e32de755540
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96027238"
---
## <a name="create-a-self-signed-root-certificate"></a><a name="rootcert"></a>Skapa ett självsignerat rot certifikat

Använd New-SelfSignedCertificate-cmdlet för att skapa ett självsignerat rot certifikat. Mer parameter information finns i [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate).

1. Från en dator som kör Windows 10 eller Windows Server 2016 öppnar du en Windows PowerShell-konsol med utökade privilegier. Dessa exempel fungerar inte i Azure Cloud Shell "testa". Du måste köra dessa exempel lokalt.
1. Använd följande exempel för att skapa det självsignerade rot certifikatet. I följande exempel skapas ett självsignerat rot certifikat med namnet ' P2SRootCert ' som installeras automatiskt i ' certificates-Current User\Personal\Certificates '. Du kan visa certifikatet genom att öppna *certmgr. msc* eller *hantera användar certifikat*.

   Logga in med `Connect-AzAccount` cmdleten. Kör sedan följande exempel med eventuella nödvändiga ändringar.

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```

1. Låt PowerShell-konsolen vara öppen och fortsätt med nästa steg för att generera ett klient certifikat.

## <a name="generate-a-client-certificate"></a><a name="clientcert"></a>Generera ett klientcertifikat

Varje klientdator som ansluter till ett virtuellt nätverk med punkt-till-plats måste ha ett klientcertifikat installerat. Du genererar ett klient certifikat från det självsignerade rot certifikatet och exporterar och installerar sedan klient certifikatet. Autentiseringen Miss lyckas om klient certifikatet inte är installerat. 

Följande steg beskriver hur du skapar ett klient certifikat från ett självsignerat rot certifikat. Du kan generera flera klient certifikat från samma rot certifikat. När du genererar klient certifikat med stegen nedan installeras klient certifikatet automatiskt på den dator som du använde för att generera certifikatet. Om du vill installera ett klient certifikat på en annan klient dator kan du exportera certifikatet.

Exemplen använder New-SelfSignedCertificate-cmdleten för att generera ett klient certifikat som upphör att gälla om ett år. För ytterligare parameter information, till exempel att ange ett annat förfallo värde för klient certifikatet, se [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate).

### <a name="example-1---powershell-console-session-still-open"></a>Exempel 1 – PowerShell-konsolsessionen är fortfarande öppen

Använd det här exemplet om du inte har stängt PowerShell-konsolen efter att du har skapat det självsignerade rot certifikatet. Det här exemplet fortsätter från föregående avsnitt och använder den deklarerade $cert variabeln. Om du stängde PowerShell-konsolen efter att du har skapat det självsignerade rot certifikatet, eller om du skapar ytterligare klient certifikat i en ny PowerShell-konsolsession, använder du stegen i [exempel 2](#ex2).

Ändra och kör exemplet för att generera ett klient certifikat. Om du kör följande exempel utan att ändra det, är resultatet ett klient certifikat med namnet ' P2SChildCert '.  Om du vill namnge det underordnade certifikatet något annat ändrar du värdet för CN. Ändra inte TextExtension när du kör det här exemplet. Det klient certifikat som du skapar installeras automatiskt i "certificates-Current User\Personal\Certificates" på din dator.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="example-2---new-powershell-console-session"></a><a name="ex2"></a>Exempel 2 – ny PowerShell-konsolsession

Använd följande steg om du skapar ytterligare klient certifikat eller inte använder samma PowerShell-session som du använde för att skapa ett självsignerat rot certifikat:

1. Identifiera det självsignerade rot certifikatet som är installerat på datorn. Den här cmdleten returnerar en lista över certifikat som är installerade på datorn.

   ```powershell
   Get-ChildItem -Path "Cert:\CurrentUser\My"
   ```

1. Leta upp ämnes namnet i den returnerade listan och kopiera sedan det tumavtryck som finns bredvid den till en textfil. I följande exempel finns det två certifikat. CN-namnet är namnet på det självsignerade rot certifikat som du vill skapa ett underordnat certifikat från. I det här fallet "P2SRootCert".

   ```
   Thumbprint                                Subject
  
   AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
   7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
   ```

1. Deklarera en variabel för rot certifikatet med tumavtrycket från föregående steg. Ersätt TUMAVTRYCKET med tumavtrycket för det rot certifikat som du vill skapa ett underordnat certifikat från.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
   ```

   Om du till exempel använder tumavtrycket för P2SRootCert i föregående steg ser variabeln ut så här:

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
   ```

1. Ändra och kör exemplet för att generera ett klient certifikat. Om du kör följande exempel utan att ändra det, är resultatet ett klient certifikat med namnet ' P2SChildCert '. Om du vill namnge det underordnade certifikatet något annat ändrar du värdet för CN. Ändra inte TextExtension när du kör det här exemplet. Det klient certifikat som du skapar installeras automatiskt i "certificates-Current User\Personal\Certificates" på din dator.

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="export-the-root-certificate-public-key-cer"></a><a name="cer"></a>Exportera den offentliga nyckeln för rot certifikatet (. cer)

[!INCLUDE [Export public key](vpn-gateway-certificates-export-public-key-include.md)]

### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Exportera det självsignerade rot certifikatet och den privata nyckeln för att lagra det (valfritt)

Du kanske vill exportera det självsignerade rot certifikatet och lagra det säkert som säkerhets kopiering. Om det behövs kan du senare installera det på en annan dator och generera fler klient certifikat. Om du vill exportera det självsignerade rot certifikatet som en. pfx väljer du rot certifikatet och använder samma steg som beskrivs i [Exportera ett klient certifikat](#clientexport).

## <a name="export-the-client-certificate"></a><a name="clientexport"></a>Exportera klientcertifikat

[!INCLUDE [Export client certificate](vpn-gateway-certificates-export-client-cert-include.md)]