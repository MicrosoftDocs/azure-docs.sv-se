---
title: Integrera nyckelvalv med DigiCert-certifikatutfärdare
description: Så här integrerar Key Vault med DigiCert-certifikatutfärdare
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 06/02/2020
ms.author: sebansal
ms.openlocfilehash: 4351526c77961856b118bdeae07cecf48340f5fe
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749320"
---
# <a name="integrating-key-vault-with-digicert-certificate-authority"></a>Integrera nyckelvalv med DigiCert-certifikatutfärdare

Azure Key Vault kan du enkelt etablera, hantera och distribuera digitala certifikat för ditt nätverk och aktivera säker kommunikation för program. Ett digitalt certifikat är elektroniska autentiseringsuppgifter för att fastställa identitetsbevis i en elektronisk transaktion. 

Azure Key Vault-användare kan generera DigiCert-certifikat direkt från sina Key Vault. Key Vault säkerställer livscykelhanteringen för certifikat från slutet till slut för de certifikat som utfärdats av DigiCert via Key Vault:s betrodda partnerskap med DigiCert-certifikatutfärdaren.

Mer allmän information om certifikat finns i [Azure Key Vault certifikat](./about-certificates.md).

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här guiden måste du ha följande resurser.
* Ett nyckelvalv. Du kan använda ett befintligt nyckelvalv eller skapa ett nytt genom att följa stegen i någon av dessa snabbstarter:
   - [Skapa ett nyckelvalv med Azure CLI](../general/quick-create-cli.md)
   - [Skapa ett nyckelvalv med Azure PowerShell](../general/quick-create-powershell.md)
   - [Skapa ett nyckelvalv med Azure Portal](../general/quick-create-portal.md).
*   Du måste aktivera DigiCert CertCentral-kontot. [Registrera dig](https://www.digicert.com/account/signup/) för ditt CertCentral-konto.
*   Behörigheter på administratörsnivå i dina konton.


### <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har följande information till hands från ditt DigiCert CertCentral-konto:
-   CertCentral-konto-ID
-   Organisations-id
-   API-nyckel

## <a name="adding-certificate-authority-in-key-vault"></a>Lägga till certifikatutfärdare i Key Vault 
När du har samlat in ovanstående information från DigiCert CertCentral-kontot kan du nu lägga till DigiCert i listan över certifikatutfärdare i nyckelvalvet.

### <a name="azure-portal"></a>Azure Portal

1.  Om du vill lägga till DigiCert-certifikatutfärdare navigerar du till nyckelvalvet som du vill lägga till DigiCert. 
2.  På sidan Key Vault egenskaper väljer du **Certifikat**.
3.  Välj **fliken Certifikatutfärdare.** ![ välj certifikatutfärdare](../media/certificates/how-to-integrate-certificate-authority/select-certificate-authorities.png)
4.  Välj **alternativet Lägg** till.
 ![lägga till certifikatutfärdare](../media/certificates/how-to-integrate-certificate-authority/add-certificate-authority.png)
5.  På skärmen **Skapa en certifikatutfärdare** väljer du följande värden:
    -   **Namn:** Lägg till ett identifierbart Utfärdarnamn. Exempel på DigicertCA
    -   **Provider:** Välj DigiCert på menyn.
    -   **Konto-ID:** Ange ditt DigiCert CertCentral-konto-ID
    -   **Kontolösenord:** Ange DEN API-nyckel som du genererade i ditt DigiCert CertCentral-konto
    -   **Organisations-ID:** Ange OrgID som samlats in från DigiCert CertCentral Account 
    -   Klicka på **Skapa**.
   
6.  Du ser att DigicertCA nu har lagts till i listan Certifikatutfärdare.


### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell används för att skapa och hantera Azure-resurser med hjälp av kommandon eller skript. Azure är Azure Cloud Shell, en interaktiv gränssnittsmiljö som du kan använda via Azure Portal i webbläsaren.

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modul version 1.0.0 eller senare. Skriv `$PSVersionTable.PSVersion` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzAccount` för att skapa en anslutning till Azure.

```azurepowershell-interactive
Login-AzAccount
```

1.  Skapa en **resursgrupp**

Skapa en Azure-resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

2. Skapa en **Key Vault**

Du måste använda ett unikt namn för ditt nyckelvalv. Här är "Contoso-Vaultname" namnet på den Key Vault i den här guiden.

- **Valvnamn** Contoso-Vaultname.
- **Resursgruppsnamn** ContosoResourceGroup.
- **Plats** EastUS.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vaultname' -ResourceGroupName 'ContosoResourceGroup' -Location 'EastUS'
```

3. Definiera variabler för information som samlas in från DigiCert CertCentral-kontot.

- Definiera **variabeln Konto-ID**
- Definiera **variabeln organisations-ID**
- Definiera **API-nyckelvariabel**

```azurepowershell-interactive
$accountId = "myDigiCertCertCentralAccountID"
$org = New-AzKeyVaultCertificateOrganizationDetail -Id OrganizationIDfromDigiCertAccount
$secureApiKey = ConvertTo-SecureString DigiCertCertCentralAPIKey -AsPlainText –Force
```

4. Ange **Issuer (Utfärdare).** Då lägger du till Digicert som certifikatutfärdare i nyckelvalvet. Mer information om parametrarna finns [här](/powershell/module/az.keyvault/Set-AzKeyVaultCertificateIssuer)
```azurepowershell-interactive
Set-AzKeyVaultCertificateIssuer -VaultName "Contoso-Vaultname" -Name "TestIssuer01" -IssuerProvider DigiCert -AccountId $accountId -ApiKey $secureApiKey -OrganizationDetails $org -PassThru
```

5. **Ange princip för certifikatet och utfärdande certifikat från** DigiCert direkt i Key Vault.

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "TestIssuer01" -ValidityInMonths 12 -RenewAtNumberOfDaysBeforeExpiry 60
Add-AzKeyVaultCertificate -VaultName "Contoso-Vaultname" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

Certifikatet har nu utfärdats av Digicert CA i angivna Key Vault den här integreringen.

## <a name="troubleshoot"></a>Felsöka

Om det utfärdade certifikatet har statusen "disabled" (inaktiverad)  i Azure Portal du certifikatåtgärden för att granska DigiCert-felmeddelandet för certifikatet.

 ![Certifikatåtgärd](../media/certificates/how-to-integrate-certificate-authority/certificate-operation-select.png)

Felmeddelandet "Utför en sammanslagning för att slutföra den här certifikatbegäran".
Du måste sammanfoga CSR som signerats av certifikatutfärdaren för att slutföra den här begäran. Lär dig mer [här](./create-certificate-signing-request.md)

Mer information finns i [Certifikatåtgärder i Key Vault REST API referens](/rest/api/keyvault). Information om hur du etablerar behörigheter [finns i Valv – Skapa eller Uppdatera](/rest/api/keyvault/vaults/createorupdate) och [valv – Uppdatera åtkomstprincip.](/rest/api/keyvault/vaults/updateaccesspolicy)

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

- Kan jag generera ett digicert jokerteckencertifikat via KeyVault? 
   Ja. Det beror på hur du har konfigurerat ditt Digicert-konto.
- Hur skapar jag **OV-SSL- eller EV-SSL-certifikat** med DigiCert? 
   Key Vault stöder skapande av OV- och EV SSL-certifikat. När du skapar ett certifikat klickar du på Avancerad principkonfiguration och anger sedan certifikattypen. Värden som stöds är: OV-SSL, EV-SSL
   
   Du skulle kunna skapa den här typen av certifikat i nyckelvalvet om ditt Digicert-konto tillåter det. För den här typen av certifikat utförs valideringen av DigiCert, och supportteamet skulle kunna hjälpa dig på bästa sätt med lösningen om verifieringen misslyckas. Du kan lägga till ytterligare information när du skapar ett certifikat genom att definiera dem i subjectName.

Exempel
    ```SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
    ```
   
- Finns det en tidsfördröjning när du skapar digicert-certifikat via integrering jämfört med att hämta certifikat direkt?
   Nej. När du skapar ett certifikat är det verifieringsprocessen som kan ta tid och verifieringen är beroende av digiCert-processen nedan.


## <a name="next-steps"></a>Nästa steg

- [Autentisering, begäranden och svar](../general/authentication-requests-and-responses.md)
- [Utvecklarguide för Key Vault](../general/developers-guide.md)