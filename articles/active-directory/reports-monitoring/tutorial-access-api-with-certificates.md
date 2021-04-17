---
title: Självstudie för AD Reporting API med certifikat | Microsoft Docs
description: I den här självstudien beskrivs hur du använder Azure AD Reporting API med certifikatautentiseringsuppgifter för att hämta data från kataloger utan att användaren behöver göra något.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: c4f4ac6ab3825c82ac0cb8ef5c31f9396ef7b41f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533737"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Självstudie: Hämta data med hjälp Azure Active Directory API för rapportering med certifikat

[Azure Active Directory reporting API: er](concept-reporting-api.md) ger programmässig åtkomst till data via en uppsättning REST-baserade API: er. Du kan anropa API: erna från en mängd olika programmeringsspråk och verktyg. Om du vill komma åt Azure AD Reporting API utan inblandning av användaren måste du konfigurera din åtkomst för att använda certifikat.

I den här självstudien lär du dig att använda ett testcertifikat för att få åtkomst till MS Graph API för rapportering. Vi rekommenderar inte att du använder testcertifikat i en produktionsmiljö. 

## <a name="prerequisites"></a>Förutsättningar

1. Om du vill komma åt inloggningsdata kontrollerar du att du har Azure Active Directory klientorganisation med en Premium-licens (P1/P2). Se [Kom igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) för att uppgradera din Azure Active Directory-version. Observera att om du inte har några aktivitetsdata före uppgraderingen tar det ett par dagar innan data visas i rapporterna när du har uppgraderat till en premiumlicens. 

2. Skapa eller växla till ett användarkonto i rollen **global administratör,** **säkerhetsadministratör,** **säkerhetsläsare** eller **rapportläsare** för klientorganisationen. 

3. Slutför [förutsättningarna för åtkomst till Azure Active Directory rapporterings-API.](howto-configure-prerequisites-for-reporting-api.md) 

4. Ladda ned och [installera Azure AD PowerShell V2.](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md)

5. Installera [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Den här modulen tillhandahåller flera verktygs-cmdlets, däribland:
    - De ADAL-bibliotek som behövs för autentisering
    - Åtkomsttoken från användare, programnycklar och certifikat med ADAL
    - Växlingsbara resultat för Graph API-hantering

6. Om det är första gången du använder modulen kör **du Install-MSCloudIdUtilsModule.** Annars importerar du den med hjälp av **PowerShell-kommandot Import-Module.** Sessionen bör se ut ungefär så här: ![ Windows PowerShell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. Använd **PowerShell-kommandot New-SelfSignedCertificate** för att skapa ett testcertifikat.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. Använd kommandot **Export-Certificate** för att exportera den till en certifikatfil.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Hämta data med hjälp av Azure Active Directory Reporting-API:et med certifikat

1. Gå till [Azure Portal](https://portal.azure.com), **välj Azure Active Directory** och välj **sedan Appregistreringar** och välj ditt program i listan. 

2. Välj **Certifikat & hemligheter** under avsnittet **Hantera** på bladet Programregistrering och välj Ladda **upp certifikat.**

3. Välj certifikatfilen från föregående steg och välj Lägg **till**. 

4. Anteckna program-ID:t och tumavtrycket för det certifikat som du precis registrerade i ditt program. Du hittar tumavtrycket på programsidan i portalen genom att gå till **Certifikat & under** **avsnittet** Hantera. Tumavtrycket finns under **certifikatlistan.**

5. Öppna programmanifestet i det infogade manifestredigeraren och kontrollera att *egenskapen keyCredentials* har uppdaterats med din nya certifikatinformation enligt nedan – 

   ```
   "keyCredentials": [
        {
            "customKeyIdentifier": "$base64Thumbprint", //base64 encoding of the certificate hash
            "keyId": "$keyid", //GUID to identify the key in the manifest
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "value":  "$base64Value" //base64 encoding of the certificate raw data
        }
    ]
   ``` 
6. Nu kan du hämta en åtkomsttoken för MS-Graph API med det här certifikatet. Använd **cmdleten Get-MSCloudIdMSGraphAccessTokenFromCert** från PowerShell-modulen MSCloudIdUtils och skicka program-ID:t och tumavtrycket som du fick från föregående steg. 

   ![Skärmbild som visar ett PowerShell-fönster med ett kommando som skapar en åtkomsttoken.](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

7. Använd åtkomsttoken i PowerShell-skriptet för att fråga Graph API. Använd cmdleten **Invoke-MSCloudIdMSGraphQuery** från MSCloudIDUtils för att räkna upp signins- och directoryAudits-slutpunkten. Den här cmdleten hanterar resultat med flera sidor och skickar dessa resultat till PowerShell-pipelinen.

8. Fråga katalogenGranskningsslutpunkt för att hämta granskningsloggarna. 

   ![Skärmbild som visar ett PowerShell-fönster med ett kommando för att fråga katalogenGranskningsslutpunkt med hjälp av åtkomsttoken från tidigare i den här proceduren.](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

9. Fråga inloggningsslutpunkten för att hämta inloggningsloggarna.

    ![Skärmbild som visar ett PowerShell-fönster med ett kommando för att fråga inloggningsslutpunkten med hjälp av åtkomsttoken från tidigare i den här proceduren.](./media/tutorial-access-api-with-certificates/query-signins.png)

10. Nu kan du välja att exportera dessa data till en CSV-fil och spara dem i ett SIEM-system. Du kan också ta med skriptet i en schemalagd aktivitet för att regelbundet hämta Azure AD-data från din klientorganisation utan att behöva lagra programnycklar i källkoden. 

## <a name="next-steps"></a>Nästa steg

* [Få ett första intryck av rapport-API:er](concept-reporting-api.md)
* [Granska API-referens](/graph/api/resources/directoryaudit) 
* [API-referens för inloggningsaktivitetsrapport](/graph/api/resources/signin)