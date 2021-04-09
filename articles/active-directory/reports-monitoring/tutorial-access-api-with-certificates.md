---
title: Självstudie för AD repor ting API med certifikat | Microsoft Docs
description: I den här självstudien beskrivs hur du använder Azure AD repor ting-API med autentiseringsuppgifter för certifikat för att hämta data från kataloger utan åtgärder från användaren.
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
ms.openlocfilehash: c3443cb73e85fc69349e7293597a5f4a723959d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93130059"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Självstudie: Hämta data med hjälp av Azure Active Directory rapporterings-API med certifikat

[Azure Active Directory reporting API: er](concept-reporting-api.md) ger programmässig åtkomst till data via en uppsättning REST-baserade API: er. Du kan anropa API: erna från en mängd olika programmeringsspråk och verktyg. Om du vill få åtkomst till Azure AD repor ting-API utan åtgärder från användaren måste du konfigurera åtkomsten till att använda certifikat.

I den här självstudien får du lära dig hur du använder ett test certifikat för att komma åt MS-Graph API för rapportering. Vi rekommenderar inte att du använder test certifikat i en produktions miljö. 

## <a name="prerequisites"></a>Förutsättningar

1. Kontrol lera att du har en Azure Active Directory-klient med en Premium-licens (P1/P2) för att få åtkomst till inloggnings data. Se [Kom igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) för att uppgradera din Azure Active Directory-version. Observera att om du inte har några aktivitetsdata före uppgraderingen tar det ett par dagar innan data visas i rapporterna när du har uppgraderat till en premiumlicens. 

2. Skapa eller växla till ett användar konto i rollen **Global administratör**, **säkerhets administratör**, **säkerhets läsare** eller **rapport läsare** för klienten. 

3. Slutför [kraven för att få åtkomst till Azure Active Directory rapporterings-API: et](howto-configure-prerequisites-for-reporting-api.md). 

4. Hämta och installera [Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md).

5. Installera [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Den här modulen tillhandahåller flera verktygs-cmdlets, däribland:
    - ADAL-bibliotek som krävs för autentisering
    - Åtkomsttoken från användare, programnycklar och certifikat med ADAL
    - Växlingsbara resultat för Graph API-hantering

6. Om det är första gången du använder modulen kör **install-MSCloudIdUtilsModule**, annars importerar du den med hjälp av PowerShell **-kommandot Import-Module** . Din session bör se ut ungefär som den här skärmen: ![ Windows PowerShell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. Använd PowerShell **-kommandot New-SelfSignedCertificate** för att skapa ett test certifikat.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. Använd **export-Certificate** kommandot för att exportera den till en certifikat fil.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Hämta data med hjälp av Azure Active Directory Reporting-API:et med certifikat

1. Gå till [Azure Portal](https://portal.azure.com), Välj **Azure Active Directory** och välj sedan **Appregistreringar** och välj ditt program i listan. 

2. Välj **certifikat & hemligheter** under **Hantera** avsnitt på program registrerings bladet och välj **Ladda upp certifikat**.

3. Välj certifikat filen i föregående steg och välj **Lägg till**. 

4. Anteckna program-ID och tumavtrycket för det certifikat som du precis har registrerat i ditt program. Om du vill hitta tumavtrycket från din program sida i portalen går du till **certifikat & hemligheter** under avsnittet **Hantera** . Tumavtrycket kommer att finnas i listan **certifikat** .

5. Öppna applikations manifestet i den infogade manifest redigeraren och kontrol lera att egenskapen *autentiseringsuppgifter* har uppdaterats med den nya certifikat informationen som visas nedan – 

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
6. Nu kan du hämta en åtkomsttoken för MS Graph API att använda det här certifikatet. Använd cmdleten **Get-MSCloudIdMSGraphAccessTokenFromCert** från MSCloudIdUtils PowerShell-modulen för att skicka in program-ID och det tumavtryck som du fick från föregående steg. 

   ![Skärm bild som visar ett PowerShell-fönster med ett kommando som skapar en åtkomsttoken.](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

7. Använd åtkomsttoken i PowerShell-skriptet för att fråga Graph API. Använd cmdleten **Invoke-MSCloudIdMSGraphQuery** från MSCloudIDUtils för att räkna upp inloggningar och directoryAudits-slutpunkten. Denna cmdlet hanterar flera växlade resultat och skickar dessa resultat till PowerShell-pipeline.

8. Fråga directoryAudits-slutpunkten för att hämta gransknings loggarna. 

   ![Skärm bild som visar ett PowerShell-fönster med ett kommando för att fråga directoryAudits-slutpunkten med hjälp av åtkomsttoken från tidigare i den här proceduren.](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

9. Fråga inloggningar-slutpunkten för att hämta inloggnings loggarna.

    ![Skärm bild som visar ett PowerShell-fönster med ett kommando för att fråga inloggningar-slutpunkten med hjälp av åtkomsttoken från tidigare i den här proceduren.](./media/tutorial-access-api-with-certificates/query-signins.png)

10. Du kan nu välja att exportera dessa data till en CSV-fil och spara dem i ett SIEM-system. Du kan också ta med skriptet i en schemalagd aktivitet för att regelbundet hämta Azure AD-data från din klientorganisation utan att behöva lagra programnycklar i källkoden. 

## <a name="next-steps"></a>Nästa steg

* [Få ett första intryck av rapport-API:er](concept-reporting-api.md)
* [Granska API-referens](/graph/api/resources/directoryaudit?view=graph-rest-beta) 
* [Rapport-API-referens för inloggnings aktivitet](/graph/api/resources/signin?view=graph-rest-beta)