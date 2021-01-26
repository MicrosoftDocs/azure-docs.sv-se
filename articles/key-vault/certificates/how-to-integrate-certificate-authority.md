---
title: Integrera nyckelvalv med DigiCert-certifikatutfärdare
description: Så här integrerar du Key Vault med certifikat utfärdare för DigiCert
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 06/02/2020
ms.author: sebansal
ms.openlocfilehash: c36353448c140450044f352062c3349939e3f7b5
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98789018"
---
# <a name="integrating-key-vault-with-digicert-certificate-authority"></a>Integrera nyckelvalv med DigiCert-certifikatutfärdare

Med Azure Key Vault kan du enkelt etablera, hantera och distribuera digitala certifikat för nätverket och aktivera säker kommunikation för program. Ett digitalt certifikat är en elektronisk autentiseringsuppgift för att upprätta bevis på identitet i en elektronisk transaktion. 

Azure Key Vault-användare kan generera DigiCert-certifikat direkt från sina Key Vault. Key Vault skulle garantera hantering av livs cykeln för certifikat från slut punkt till slut punkt för de certifikat som utfärdats av DigiCert via Key Vault betrodda partnerskap med DigiCert certifikat utfärdare.

Mer allmän information om certifikat finns i [Azure Key Vault certifikat](./about-certificates.md).

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här guiden måste du ha följande resurser.
* Ett nyckel valv. Du kan använda ett befintligt nyckel valv eller skapa ett nytt genom att följa stegen i någon av följande snabb starter:
   - [Skapa ett nyckel valv med Azure CLI](../general/quick-create-cli.md)
   - [Skapa ett nyckel valv med Azure PowerShell](../general/quick-create-powershell.md)
   - [Skapa ett nyckel valv med Azure Portal](../general/quick-create-portal.md).
*   Du måste aktivera DigiCert CertCentral-kontot. [Registrera dig](https://www.digicert.com/account/signup/) för ditt CertCentral-konto.
*   Behörigheter på administratörs nivå i dina konton.


### <a name="before-you-begin"></a>Innan du börjar

Se till att du har följande information som är praktisk från ditt DigiCert CertCentral-konto:
-   Konto-ID för CertCentral
-   Organisations-id
-   API-nyckel

## <a name="adding-certificate-authority-in-key-vault"></a>Lägger till certifikat utfärdare i Key Vault 
När du har samlat in information från DigiCert CertCentral-kontot kan du nu lägga till DigiCert i certifikat utfärdarens lista i nyckel valvet.

### <a name="azure-portal"></a>Azure Portal

1.  Om du vill lägga till DigiCert-certifikatutfärdare navigerar du till det nyckel valv som du vill lägga till DigiCert. 
2.  Välj **certifikat** på sidan Key Vault egenskaper.
3.  Välj fliken **certifikat utfärdare** . ![ Välj certifikat utfärdare](../media/certificates/how-to-integrate-certificate-authority/select-certificate-authorities.png)
4.  Välj **Lägg till** alternativ.
 ![Lägg till certifikat utfärdare](../media/certificates/how-to-integrate-certificate-authority/add-certificate-authority.png)
5.  På skärmen **skapa en certifikat utfärdare** väljer du följande värden:
    -   **Namn**: Lägg till ett identifierbart Issuer-namn. Exempel DigicertCA
    -   **Provider**: Välj DigiCert på menyn.
    -   **Konto-ID**: Ange ditt DigiCert CertCentral-konto-ID
    -   **Konto lösen ord**: Ange den API-nyckel som du genererade i ditt DigiCert CertCentral-konto
    -   **Organisations-ID**: Ange OrgID som samlats in från DigiCert CertCentral-kontot 
    -   Klicka på **Skapa**.
   
6.  Du kommer att se att DigicertCA nu har lagts till i listan certifikat utfärdare.


### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell används för att skapa och hantera Azure-resurser med hjälp av kommandon eller skript. Azure-värdar Azure Cloud Shell, en interaktiv gränssnitts miljö som du kan använda via din Azure Portal i själva webbläsaren.

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modul version 1.0.0 eller senare. Skriv `$PSVersionTable.PSVersion` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzAccount` för att skapa en anslutning till Azure.

```azurepowershell-interactive
Login-AzAccount
```

1.  Skapa en **resurs grupp**

Skapa en Azure-resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

2. Skapa en **Key Vault**

Du måste använda ett unikt namn för nyckel valvet. Här "contoso-Vaultname" är namnet på Key Vault i den här hand boken.

- **Valv namn** Contoso-Vaultname.
- **Resurs grupps namn** ContosoResourceGroup.
- **Plats** Platsen eastus.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vaultname' -ResourceGroupName 'ContosoResourceGroup' -Location 'EastUS'
```

3. Definiera variabler för information som samlas in från DigiCert CertCentral-kontot.

- Definiera variabel för **konto-ID**
- Definiera **org ID-** variabel
- Definiera **API-nyckel** variabel

```azurepowershell-interactive
$accountId = "myDigiCertCertCentralAccountID"
$org = New-AzKeyVaultCertificateOrganizationDetail -Id OrganizationIDfromDigiCertAccount
$secureApiKey = ConvertTo-SecureString DigiCertCertCentralAPIKey -AsPlainText –Force
```

4. Ange **utfärdare**. Detta lägger till DigiCert som en certifikat utfärdare i nyckel valvet. Läs mer om parametrarna [här](/powershell/module/az.keyvault/Set-AzKeyVaultCertificateIssuer)
```azurepowershell-interactive
Set-AzKeyVaultCertificateIssuer -VaultName "Contoso-Vaultname" -Name "TestIssuer01" -IssuerProvider DigiCert -AccountId $accountId -ApiKey $secureApiKey -OrganizationDetails $org -PassThru
```

5. **Ställer in principen för certifikatet och utfärdar certifikat** från DigiCert direkt i Key Vault.

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "TestIssuer01" -ValidityInMonths 12 -RenewAtNumberOfDaysBeforeExpiry 60
Add-AzKeyVaultCertificate -VaultName "Contoso-Vaultname" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

Certifikatet har nu utfärdats av DigiCert CA i angivet Key Vault via den här integrationen.

## <a name="troubleshoot"></a>Felsöka

Om certifikatet som utfärdats är inaktiverat i Azure Portal kan du fortsätta att visa **certifikat åtgärden** för att granska DigiCert-felmeddelandet för det certifikatet.

 ![Certifikat åtgärd](../media/certificates/how-to-integrate-certificate-authority/certificate-operation-select.png)

Fel meddelande: utför en sammanslagning för att slutföra den här certifikatbegäran.
Du måste slå samman CSR-filen som signerats av certifikat utfärdaren för att slutföra den här begäran. Läs mer [här](./create-certificate-signing-request.md)

Mer information finns i [certifikat åtgärderna i Key Vault REST API referens](/rest/api/keyvault). Information om hur du etablerar behörigheter finns i [valv – skapa eller uppdatera](/rest/api/keyvault/vaults/createorupdate) och [valv – uppdatera åtkomst princip](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

- Kan jag generera ett DigiCert-certifikat med jokertecken i ett nyckel valv? 
   Ja. Det beror på hur du har konfigurerat ditt DigiCert-konto.
- Hur skapar jag **OV-SSL eller ev-SSL-** certifikat med DigiCert? 
   Key Vault stöder skapande av OV-och EV SSL-certifikat. När du skapar ett certifikat klickar du på avancerad princip konfiguration och anger sedan certifikat typen. De värden som stöds är: OV-SSL, EV-SSL
   
   Du kan skapa den här typen av certifikat i Key Vault om ditt DigiCert-konto tillåter. För den här typen av certifikat utförs verifieringen av DigiCert och support teamet skulle kunna hjälpa dig med lösningen, om valideringen Miss lyckas. Du kan lägga till ytterligare information när du skapar ett certifikat genom att definiera dem i subjectName.

Exempel
    ```SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
    ```
   
- Finns det en tids fördröjning i att skapa DigiCert-certifikat via integrering eller förvärva certifikat via DigiCert direkt?
   Nej. När du skapar ett certifikat är det verifierings processen som kan ta tid och verifieringen är beroende av process DigiCert nedan.


## <a name="next-steps"></a>Nästa steg

- [Autentisering, begär Anden och svar](../general/authentication-requests-and-responses.md)
- [Utvecklarguide för Key Vault](../general/developers-guide.md)