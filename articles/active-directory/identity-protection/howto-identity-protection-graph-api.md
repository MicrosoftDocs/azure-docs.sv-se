---
title: Microsoft Graph PowerShell SDK och Azure Active Directory Identity Protection
description: Lär dig hur du frågar Microsoft Graph risk identifieringar och tillhör ande information från Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 01/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2db8cfe652c0fca4b68b00d846e345c1b60cd05d
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880244"
---
# <a name="azure-active-directory-identity-protection-and-the-microsoft-graph-powershell-sdk"></a>Azure Active Directory Identity Protection och Microsoft Graph PowerShell SDK

Microsoft Graph är Microsoft Unified API-slutpunkten och start för [Azure Active Directory Identity Protection](./overview-identity-protection.md) -API: er. Den här artikeln visar hur du använder [Microsoft Graph POWERSHELL SDK](/graph/powershell/get-started) för att få riskfyllda användar uppgifter med hjälp av PowerShell. Organisationer som vill fråga Microsoft Graph-API: er direkt kan använda artikeln, [Självstudier: identifiera och åtgärda risker med hjälp av Microsoft Graph-API: er](/graph/tutorial-riskdetection-api) för att påbörja den resan.


## <a name="connect-to-microsoft-graph"></a>Anslut till Microsoft Graph

Det finns fyra steg för att komma åt identitets skydds data via Microsoft Graph:

- [Skapa ett certifikat](#create-a-certificate)
- [Skapa en ny app-registrering](#create-a-new-app-registration)
- [Konfigurera API-behörigheter](#configure-api-permissions)
- [Konfigurera en giltig autentiseringsuppgift](#configure-a-valid-credential)

### <a name="create-a-certificate"></a>Skapa ett certifikat

I en produktions miljö använder du ett certifikat från certifikat utfärdaren för produktion, men i det här exemplet ska vi använda ett självsignerat certifikat. Skapa och exportera certifikatet med följande PowerShell-kommandon.

```powershell
$cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"
```

### <a name="create-a-new-app-registration"></a>Skapa en ny app-registrering

1. I Azure Portal bläddrar du till **Azure Active Directory**  >  **Appregistreringar**.
1. Välj **ny registrering**.
1. Utför följande steg på sidan **skapa** :
   1. I text rutan **namn** anger du ett namn för ditt program (t. ex. Azure AD-API för risk identifiering).
   1. Under **typer av konto typer som stöds** väljer du den typ av konton som ska använda API: erna.
   1. Välj **Register** (Registrera).
1. Anteckna **program-ID** och katalog (klient) **-ID: t** eftersom du behöver dessa objekt senare.

### <a name="configure-api-permissions"></a>Konfigurera API-behörigheter

I det här exemplet konfigurerar vi program behörigheter som tillåter att det här exemplet används obevakat. Om du beviljar behörigheter till en användare som ska vara inloggad väljer du delegerade behörigheter i stället. Mer information om olika behörighets typer finns i artikeln, [behörigheter och medgivande i Microsoft Identity Platform](../develop/v2-permissions-and-consent.md#permission-types).

1. Välj **API-behörigheter** från det **program** som du har skapat.
1. På sidan **konfigurerade behörigheter** , i verktygsfältet högst upp, klickar du på **Lägg till en behörighet**.
1. På sidan **Lägg till API-åtkomst** klickar du på **Välj ett API**.
1. På sidan **Välj ett API** väljer du **Microsoft Graph** och klickar sedan på **Välj**.
1. På sidan **begär API-behörigheter** : 
   1. Välj **Programbehörigheter**.
   1. Markera kryss rutorna bredvid `IdentityRiskEvent.Read.All` och `IdentityRiskyUser.Read.All` .
   1. Välj **Lägg till behörigheter**.
1. Välj **bevilja administrativt godkännande för domänen** 

### <a name="configure-a-valid-credential"></a>Konfigurera en giltig autentiseringsuppgift

1. Välj **certifikat & hemligheter** från det **program** som du har skapat.
1. Under **certifikat** väljer du **Ladda upp certifikat**.
   1. Välj det tidigare exporterade certifikatet från fönstret som öppnas.
   1. Välj **Lägg till**.
1. Anteckna **tumavtrycket** för certifikatet eftersom du kommer att behöva den här informationen i nästa steg.

## <a name="list-risky-users-using-powershell"></a>Lista riskfyllda användare med PowerShell

Om du vill aktivera möjligheten att fråga Microsoft Graph måste vi installera `Microsoft.Graph` modulen i PowerShell-fönstret med `Install-Module Microsoft.Graph` kommandot.

Ändra följande variabler för att inkludera den information som genererades i föregående steg och kör sedan den som helhet för att få riskfylld användar information med hjälp av PowerShell.

```powershell
$ClientID       = "<your client ID here>"        # Application (client) ID gathered when creating the app registration
$tenantdomain   = "<your tenant domain here>"    # Directory (tenant) ID gathered when creating the app registration
$Thumbprint     = "<your client secret here>"    # Certificate thumbprint gathered when configuring your credential

Select-MgProfile -Name "beta"
  
Connect-MgGraph -ClientId $ClientID -TenantId $tenantdomain -CertificateThumbprint $Thumbprint

Get-MgRiskyUser -All
```

## <a name="next-steps"></a>Nästa steg

- [Kom igång med Microsoft Graph PowerShell SDK](/graph/powershell/get-started)
- [Självstudie: identifiera och åtgärda risker med Microsoft Graph API: er](/graph/tutorial-riskdetection-api)
- [Översikt över Microsoft Graph](https://developer.microsoft.com/graph/docs)
- [Få åtkomst utan en användare](/graph/auth-v2-service)
- [Azure AD Identity Protection tjänst roten](/graph/api/resources/identityprotectionroot)
- [Azure Active Directory Identity Protection](./overview-identity-protection.md)
