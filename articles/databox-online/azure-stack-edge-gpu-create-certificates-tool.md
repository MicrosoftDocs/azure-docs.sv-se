---
title: Skapa certifikat med Microsoft Azure Stack Hub readiness Checker-verktyget | Microsoft Docs
description: Beskriver hur du skapar certifikat begär Anden och sedan hämtar och installerar certifikat på din Azure Stack Edge Pro GPU-enhet med hjälp av verktyget Azure Stack Hub readiness Checker.
services: Azure Stack Edge Pro
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: a04243093b89b6a2498efc48f80cbd7a47d57337
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102437731"
---
# <a name="create-certificates-for-your-azure-stack-edge-pro-using-azure-stack-hub-readiness-checker-tool"></a>Skapa certifikat för din Azure Stack Edge Pro med Azure Stack Hub readiness Checker-verktyget 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Den här artikeln beskriver hur du skapar certifikat för Azure Stack Edge Pro med hjälp av Azure Stack Hub readiness Checker-verktyget. 

## <a name="using-azure-stack-hub-readiness-checker-tool"></a>Använda Azure Stack Hub readiness Checker-verktyget

Använd Azure Stack Hub readiness Checker-verktyget för att skapa certifikat signerings förfrågningar (kund tjänst representant) för en Azure Stack distribution av Edge Pro-enheter. Du kan skapa dessa förfrågningar när du har placerat en order för Azure Stack Edge Pro-enheten och väntar tills enheten har anlänt.

> [!NOTE]
> Använd endast det här verktyget i test-eller utvecklings syfte och inte för produktions enheter. 

Du kan använda verktyget Azure Stack Hub readiness Checker (AzsReadinessChecker) för att begära följande certifikat:

- Azure Resource Manager certifikat
- Lokalt UI-certifikat
- Node-certifikat
- BLOB-certifikat
- VPN-certifikat


## <a name="prerequisites"></a>Förutsättningar

Kontrol lera följande om du vill skapa kund service representanter för Azure Stack Edge Pro-enhets distribution: 

- Du har en klient som kör Windows 10 eller Windows Server 2016 eller senare. 
- Du har laddat ned verktyget Microsoft Azure Stack Hub readiness Checker [från PowerShell-galleriet](https://aka.ms/AzsReadinessChecker) på det här systemet.
- Du har följande information för certifikaten:
  - Enhetsnamn
  - Nodens serie nummer
  - Externt fullständigt kvalificerat domän namn (FQDN)

## <a name="generate-certificate-signing-requests"></a>Generera begär Anden om certifikat signering

Använd de här stegen för att förbereda Azure Stack Edge Pro-enhetens certifikat:

1. Kör PowerShell som administratör (5,1 eller senare).
2. Installera Azure Stack Hub readiness Checker-verktyget. I PowerShell-prompten skriver du: 

    ```azurepowershell
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

    Hämta den installerade versionen genom att skriva:  

    ```azurepowershell
    Get-InstalledModule -Name Microsoft.AzureStack.ReadinessChecker  | ft Name, Version 
    ```

3. Skapa en katalog för alla certifikat om du inte redan har en. Ange: 
    
    ```azurepowershell
    New-Item "C:\certrequest" -ItemType Directory
    ``` 
    
4. Ange följande information för att skapa en certifikat förfrågan. Om du genererar ett VPN-certifikat gäller inte några av dessa indata.
    
    |Indata |Description  |
    |---------|---------|
    |`OutputRequestPath`|Fil Sök vägen på den lokala klienten där du vill att certifikat begär Anden ska skapas.        |
    |`DeviceName`|Namnet på enheten på sidan **enheter** i det lokala webb gränssnittet på enheten. <br> Det här fältet krävs inte för ett VPN-certifikat.         |
    |`NodeSerialNumber`|Serie numret för enhetens nod på sidan **nätverk** i det lokala webb gränssnittet på enheten. <br> Det här fältet krävs inte för ett VPN-certifikat.       |
    |`ExternalFQDN`|DNSDomain-värdet på sidan **enheter** i enhetens lokala webb gränssnitt.         |
    |`RequestType`|Typen av begäran kan vara för `MultipleCSR` olika certifikat för olika slut punkter, eller `SingleCSR` ett enskilt certifikat för alla slut punkter. <br> Det här fältet krävs inte för ett VPN-certifikat.     |

    För alla certifikat utom VPN-certifikatet skriver du: 
    
    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeDEVICE'
        DeviceName = 'myTEA1'
        NodeSerialNumber = 'VM1500-00025'
        externalFQDN = 'azurestackedge.contoso.com'
        requestType = 'MultipleCSR'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    Om du skapar ett VPN-certifikat skriver du: 

    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeVPN'
        externalFQDN = 'azurestackedge.contoso.com'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    
5. Du hittar filerna för certifikatbegäran i den katalog som du angav i parametern OutputRequestPath ovan. När du använder `MultipleCSR` -parametern visas följande fyra filer med `.req` tillägget:

    
    |Filnamn  |Typ av certifikat förfrågan  |
    |---------|---------|
    |Börja med din `DeviceName`     |Begäran om lokalt webb GRÄNSSNITTs certifikat      |
    |Börja med din `NodeSerialNumber`     |Begäran om nod-certifikat         |
    |Från och med `login`     |Begäran om Azure Resource Manager slut punkts certifikat       |
    |Från och med `wildcard`     |Begäran om Blob Storage-certifikat. Det innehåller ett jokertecken eftersom det täcker alla lagrings konton som du kan skapa på enheten.          |
    |Från och med `AzureStackEdgeVPNCertificate`     |VPN-klientens certifikat förfrågan.         |

    Du ser också en INF-mapp. Detta innehåller en hantering. <Edge-enhets namn> informations filen i klartext som förklarar certifikat informationen.  


6. Skicka filerna till din certifikat utfärdare (antingen intern eller offentlig). Se till att certifikat utfärdaren genererar certifikat med hjälp av den genererade begäran som uppfyller Azure Stack gräns för gräns-och slutanvändare för- [nod](azure-stack-edge-gpu-manage-certificates.md#node-certificates)-certifikat, [slut punkts certifikat](azure-stack-edge-gpu-manage-certificates.md#endpoint-certificates)och [lokala UI-certifikat](azure-stack-edge-gpu-manage-certificates.md#local-ui-certificates).

## <a name="prepare-certificates-for-deployment"></a>Förbereda certifikat för distribution

De certifikatfiler som du får från certifikat utfärdaren (CA) måste importeras och exporteras med egenskaper som matchar certifikat kraven för den Azure Stack Edge Pro-enheten. Utför följande steg på samma system som du genererade begär Anden om certifikat signering.

- Om du vill importera certifikaten följer du stegen i [Importera certifikat på klienterna som har åtkomst till din Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).

- Exportera certifikaten genom att följa stegen i [Exportera certifikat från klienten med åtkomst till Azure Stack Edge Pro-enheten](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).


## <a name="validate-certificates"></a>Verifiera certifikat

Först ska du skapa en lämplig mappstruktur och placera certifikaten i motsvarande mappar. Du kommer bara att validera certifikaten med hjälp av verktyget.

1. Kör PowerShell som administratör.

2. Du genererar lämplig mappstruktur genom att skriva följande i prompten:

    `New-AzsCertificateFolder -CertificateType AzureStackEdge -OutputPath "$ENV:USERPROFILE\Documents\AzureStackCSR"`

3. Konvertera PFX-lösenordet till en säker sträng. Ange:       

    `$pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString` 

4. Verifiera sedan certifikaten. Ange:

    `Invoke-AzsCertificateValidation -CertificateType AzureStackEdge -DeviceName mytea1 -NodeSerialNumber VM1500-00025 -externalFQDN azurestackedge.contoso.com -CertificatePath $ENV:USERPROFILE\Documents\AzureStackCSR\AzureStackEdge -pfxPassword $pfxPassword`

## <a name="next-steps"></a>Nästa steg

[Distribuera din Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-deploy-prep.md)
