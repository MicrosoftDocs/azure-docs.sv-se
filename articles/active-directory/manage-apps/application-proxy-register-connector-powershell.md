---
title: Tyst installation Azure AD App proxy Connector | Microsoft Docs
description: Beskriver hur du utför en obevakad installation av Azure AD-programproxy Connector för att ge säker fjärråtkomst till dina lokala appar.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/24/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ac442e895850de04bedf7673ffe267ac8697d26
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258158"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Skapa ett obevakat installations skript för Azure AD-programproxy-anslutningen

Det här avsnittet hjälper dig att skapa ett Windows PowerShell-skript som möjliggör obevakad installation och registrering för din Azure AD-programproxy-anslutning.

Den här funktionen är användbar när du vill:

* Installera anslutningen på Windows-servrar som inte har användar gränssnitt aktiverat eller som du inte kan komma åt med fjärr skrivbord.
* Installera och registrera många kopplingar samtidigt.
* Integrera anslutnings installationen och registreringen som en del av en annan procedur.
* Skapa en standard Server avbildning som innehåller kopplings bitarna men som inte är registrerad.

För att [Application Proxy Connector](application-proxy-connectors.md) ska fungera måste det registreras med Azure AD-katalogen med hjälp av en program administratör och ett lösen ord. Vanligt vis anges den här informationen under anslutnings installationen i en dialog ruta i ett popup-fönster, men du kan använda PowerShell för att automatisera processen i stället.

Det finns två steg för en obevakad installation. Installera först anslutningen. Sedan registrerar du anslutningen med Azure AD.

> [!IMPORTANT]
> Om du installerar anslutningen för Azure Government Cloud kontrollerar du [kraven](../hybrid/reference-connect-government-cloud.md#allow-access-to-urls) och [installations stegen](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud). Detta kräver att du aktiverar åtkomst till en annan uppsättning URL: er och ytterligare en parameter för att köra installationen.

## <a name="install-the-connector"></a>Installera anslutningen
Använd följande steg för att installera anslutningen utan att registrera den:

1. Öppna en kommandotolk.
2. Kör följande kommando, där/q innebär tyst installation. En tyst installation kräver inte att du godkänner licens avtalet för End-User.

   ```
   AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
   ```

## <a name="register-the-connector-with-azure-ad"></a>Registrera anslutningen med Azure AD
Det finns två metoder som du kan använda för att registrera anslutningen:

* Registrera anslutningen med hjälp av ett Windows PowerShell-objekt för autentiseringsuppgifter
* Registrera anslutningen med en token som skapats offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registrera anslutningen med hjälp av ett Windows PowerShell-objekt för autentiseringsuppgifter
1. Skapa ett Windows PowerShell-autentiseringsuppgifter `$cred` som innehåller ett administrativt användar namn och lösen ord för din katalog. Kör följande kommando och Ersätt *\<username\>* och *\<password\>* :

   ```powershell
   $User = "<username>"
   $PlainPassword = '<password>'
   $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
   $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
   ```
2. Gå till **C:\Program FILES\MICROSOFT AAD App proxy Connector** och kör följande skript med det `$cred` objekt som du skapade:

   ```powershell
   .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy
   ```

### <a name="register-the-connector-using-a-token-created-offline"></a>Registrera anslutningen med en token som skapats offline
1. Skapa en offline-token med AuthenticationContext-klassen med hjälp av värdena i det här kodfragmentet eller PowerShell-cmdletarna nedan:

   **Använda C#:**

   ```csharp
   using System;
   using System.Linq;
   using System.Collections.Generic;
   using Microsoft.Identity.Client;

   class Program
   {
   #region constants
   /// <summary>
   /// The AAD authentication endpoint uri
   /// </summary>
   static readonly string AadAuthenticationEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/authorize";

   /// <summary>
   /// The application ID of the connector in AAD
   /// </summary>
   static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";
 
   /// <summary>
   /// The AppIdUri of the registration service in AAD
   /// </summary>
   static readonly string RegistrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp/user_impersonation";

   #endregion

   #region private members
   private string token;
   private string tenantID;
   #endregion

   public void GetAuthenticationToken()
   {
    
   IPublicClientApplication clientApp = PublicClientApplicationBuilder
      .Create(ConnectorAppId)
      .WithDefaultRedirectUri() // will automatically use the default Uri for native app
      .WithAuthority(AadAuthenticationEndpoint)
      .Build();

      AuthenticationResult authResult = null;
            
      IAccount account = null;

      IEnumerable<string> scopes = new string[] { RegistrationServiceAppIdUri };

      try
      {
       authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
      }
       catch (MsalUiRequiredException ex)
      {
       authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();
      }


      if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
      {
       Trace.TraceError("Authentication result, token or tenant id returned are null");
       throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
      }

      token = authResult.AccessToken;
      tenantID = authResult.TenantId;
      }
      ```

   **Använda PowerShell:**

   ```powershell
   # Load MSAL (Tested with version 4.7.1) 

   Add-Type -Path "..\MSAL\Microsoft.Identity.Client.dll" 
        
   # The AAD authentication endpoint uri
        
   $authority = "https://login.microsoftonline.com/common/oauth2/v2.0/authorize"

   #The application ID of the connector in AAD

   $connectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

   #The AppIdUri of the registration service in AAD
   $registrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp/user_impersonation"

   # Define the resources and scopes you want to call 

   $scopes = New-Object System.Collections.ObjectModel.Collection["string"] 

   $scopes.Add($registrationServiceAppIdUri)

   $app = [Microsoft.Identity.Client.PublicClientApplicationBuilder]::Create($connectorAppId).WithAuthority($authority).WithDefaultRedirectUri().Build()

   [Microsoft.Identity.Client.IAccount] $account = $null

   # Acquiring the token 

   $authResult = $null

   $authResult = $app.AcquireTokenInteractive($scopes).WithAccount($account).ExecuteAsync().ConfigureAwait($false).GetAwaiter().GetResult()

   # Check AuthN result
   If (($authResult) -and ($authResult.AccessToken) -and ($authResult.TenantId)) {
        
   $token = $authResult.AccessToken
   $tenantId = $authResult.TenantId

   Write-Output "Success: Authentication result returned."
        
   }
   Else {
         
   Write-Output "Error: Authentication result, token or tenant id returned with null."
        
   } 
   ```

2. När du har token skapar du en SecureString med hjälp av token:

   ```powershell
   $SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force
   ```

3. Kör följande Windows PowerShell-kommando och Ersätt \<tenant GUID\> med ditt katalog-ID:

   ```powershell
   .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy
   ```

## <a name="next-steps"></a>Nästa steg
* [Publicera program med ditt domännamn](application-proxy-configure-custom-domain.md)
* [Aktivera enkel inloggning](application-proxy-configure-single-sign-on-with-kcd.md)
* [Felsöka problem med Application Proxy](application-proxy-troubleshoot.md)