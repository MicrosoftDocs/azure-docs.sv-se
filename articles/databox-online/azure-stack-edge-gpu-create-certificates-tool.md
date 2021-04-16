---
title: Skapa certifikat med Microsoft Azure stackhubbens verktyg för beredskapskontroll | Microsoft Docs
description: Beskriver hur du skapar certifikatbegäranden och sedan hämtar och installerar certifikat på din GPU Azure Stack Edge Pro enhet med hjälp av Azure Stack Hub för beredskapskontroll.
services: Azure Stack Edge Pro
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 8316dd0abfa437d4bf88e8268dfe034344c6614c
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389340"
---
# <a name="create-certificates-for-your-azure-stack-edge-pro-using-azure-stack-hub-readiness-checker-tool"></a>Skapa certifikat för din Azure Stack Edge Pro med Azure Stack Hub för beredskapskontroll 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

I den här artikeln beskrivs hur du skapar certifikat för Azure Stack Edge Pro med hjälp Azure Stack Hub för beredskapskontroll. 

## <a name="using-azure-stack-hub-readiness-checker-tool"></a>Använda Azure Stack Hub för beredskapskontroll

Använd verktyget Azure Stack Hub för beredskapskontroll för att skapa begäranden om certifikatsignering (CSR) för en Azure Stack Edge Pro-enhetsdistribution. Du kan skapa dessa begäranden när du har skapat en beställning Azure Stack Edge Pro enheten och vänta tills enheten har anlänt.

> [!NOTE]
> Använd bara det här verktyget i test- eller utvecklingssyfte och inte för produktionsenheter. 

Du kan använda Azure Stack Hub för beredskapskontroll (AzsReadinessChecker) för att begära följande certifikat:

- Azure Resource Manager certifikat
- Certifikat för lokalt användargränssnitt
- Nodcertifikat
- Blobcertifikat
- VPN-certifikat


## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa csr Azure Stack Edge Pro för distribution av Azure Stack Edge Pro ser du till att: 

- Du har en klient som kör Windows 10 eller Windows Server 2016 eller senare. 
- Du har laddat ned Microsoft Azure stackhubbens beredskapskontrollverktyg [från PowerShell-galleriet](https://aka.ms/AzsReadinessChecker) här systemet.
- Du har följande information för certifikaten:
  - Enhetsnamn
  - Nodserienummer
  - Externt fullständigt kvalificerat domännamn (FQDN)

## <a name="generate-certificate-signing-requests"></a>Generera certifikatsigneringsbegäranden

Använd de här stegen för att förbereda Azure Stack Edge Pro enhetscertifikat:

1. Kör PowerShell som administratör (5.1 eller senare).
2. Installera Azure Stack Hub för beredskapskontroll. I PowerShell-prompten skriver du: 

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
    
4. Ange följande information för att skapa en certifikatbegäran. Om du genererar ett VPN-certifikat gäller inte vissa av dessa indata.
    
    |Indata |Beskrivning  |
    |---------|---------|
    |`OutputRequestPath`|Filsökvägen på den lokala klienten där du vill att certifikatbegäranden ska skapas.        |
    |`DeviceName`|Namnet på enheten på sidan **Enheter i** enhetens lokala webbgränssnitt. <br> Det här fältet krävs inte för ett VPN-certifikat.         |
    |`NodeSerialNumber`|Serienumret för enhetsnoden på **sidan Nätverk** i enhetens lokala webbgränssnitt. <br> Det här fältet krävs inte för ett VPN-certifikat.       |
    |`ExternalFQDN`|DNSDomain-värdet på **sidan Enheter** i enhetens lokala webbgränssnitt.         |
    |`RequestType`|Begärandetypen kan vara `MultipleCSR` för – olika certifikat för de olika slutpunkterna, eller – ett enda certifikat för alla `SingleCSR` slutpunkter. <br> Det här fältet krävs inte för ett VPN-certifikat.     |

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

    
5. Du hittar filerna för certifikatbegäran i katalogen som du angav i parametern OutputRequestPath ovan. När du `MultipleCSR` använder parametern visas följande fyra filer med `.req` tillägget :

    
    |Filnamn  |Typ av certifikatbegäran  |
    |---------|---------|
    |Börja med din `DeviceName`     |Certifikatbegäran för lokalt webbgränssnitt      |
    |Börja med din `NodeSerialNumber`     |Nodcertifikatbegäran         |
    |Börja med `login`     |Azure Resource Manager slutpunktscertifikatbegäran       |
    |Börja med `wildcard`     |Begäran om Blob Storage-certifikat. Det innehåller ett jokertecken eftersom det omfattar alla lagringskonton som du kan skapa på enheten.          |
    |Börja med `AzureStackEdgeVPNCertificate`     |Begäran om VPN-klientcertifikat.         |

    Du ser även en INF-mapp. Detta innehåller en management.<edge-devicename>-informationsfil i klartext som förklarar certifikatinformationen.  


6. Skicka filerna till certifikatutfärdaren (antingen intern eller offentlig). Se till att certifikatutfärdaren genererar certifikat med hjälp av din [](azure-stack-edge-gpu-manage-certificates.md#node-certificates)genererade begäran som uppfyller Azure Stack Edge Pro certifikatkraven för nodcertifikat, slutpunktscertifikat [och](azure-stack-edge-gpu-manage-certificates.md#endpoint-certificates) [lokala UI-certifikat.](azure-stack-edge-gpu-manage-certificates.md#local-ui-certificates)

## <a name="prepare-certificates-for-deployment"></a>Förbereda certifikat för distribution

Certifikatfilerna som du får från din certifikatutfärdare (CA) måste importeras och exporteras med egenskaper som matchar certifikatkraven för den Azure Stack Edge Pro enheten. Utför följande steg i samma system där du genererade certifikatsigneringsbegäranden.

- Om du vill importera certifikaten följer du stegen i [Importera certifikat på klienter som har åtkomst Azure Stack Edge Pro enhet](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).

- Om du vill exportera certifikaten följer du stegen i [Exportera certifikat från klienten som har åtkomst Azure Stack Edge Pro enheten](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).


## <a name="validate-certificates"></a>Verifiera certifikat

Först genererar du en korrekt mappstruktur och placerar certifikaten i motsvarande mappar. Först då verifierar du certifikaten med hjälp av verktyget.

1. Kör PowerShell som administratör.

2. Generera lämplig mappstruktur genom att skriva följande i prompten:

    `New-AzsCertificateFolder -CertificateType AzureStackEdgeDevice -OutputPath "$ENV:USERPROFILE\Documents\AzureStackCSR"`

3. Konvertera PFX-lösenordet till en säker sträng. Ange:       

    `$pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString` 

4. Verifiera sedan certifikaten. Ange:

    `Invoke-AzsCertificateValidation -CertificateType AzureStackEdgeDevice -DeviceName mytea1 -NodeSerialNumber VM1500-00025 -externalFQDN azurestackedge.contoso.com -CertificatePath $ENV:USERPROFILE\Documents\AzureStackCSR\AzureStackEdge -pfxPassword $pfxPassword`

## <a name="next-steps"></a>Nästa steg

[Distribuera din Azure Stack Edge Pro enhet](azure-stack-edge-gpu-deploy-prep.md)
