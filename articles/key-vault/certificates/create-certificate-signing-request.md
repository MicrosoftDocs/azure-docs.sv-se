---
title: Skapa och sammanfoga en CSR i Azure Key Vault
description: Lär dig hur du skapar och sammanfogar en CSR i Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: aa631f4c505200c2c8abc67d4e22ffbab23e015c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789034"
---
# <a name="create-and-merge-a-csr-in-key-vault"></a>Skapa och slå samman en CSR i Key Vault

Azure Key Vault stöder lagring av digitala certifikat som utfärdats av en certifikat utfärdare (CA). Den har stöd för att skapa en certifikat signerings förfrågan (CSR) med ett privat/offentligt nyckel par. CSR kan signeras av en certifikat utfärdare (en intern företags certifikat utfärdare eller en extern offentlig certifikat utfärdare). En CSR är ett meddelande som du skickar till en certifikat utfärdare för att begära ett digitalt certifikat.

Mer allmän information om certifikat finns i [Azure Key Vault certifikat](./about-certificates.md).

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="add-certificates-in-key-vault-issued-by-partnered-cas"></a>Lägg till certifikat i Key Vault utfärdade av partner certifikat utfärdare

Key Vault partner med följande certifikat utfärdare för att förenkla skapande av certifikat.

|Leverantör|Certifikattyp|Konfigurations konfiguration  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault erbjuder OV eller EV SSL-certifikat med DigiCert| [Integrations guide](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault erbjuder OV eller EV SSL-certifikat med GlobalSign| [Integrations guide](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## <a name="add-certificates-in-key-vault-issued-by-non-partnered-cas"></a>Lägg till certifikat i Key Vault utfärdade av certifikat utfärdare som inte är partner

Följ dessa steg om du vill lägga till ett certifikat från certifikat utfärdare som inte är partner med Key Vault. (Till exempel är GoDaddy inte en betrodd Key Vault CA.)

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Gå till det nyckel valv som du vill lägga till certifikatet i.
1. På sidan Egenskaper väljer du **certifikat**.
1. Välj fliken **generera/importera** .
1. På skärmen **skapa ett certifikat** väljer du följande värden:
    - **Metod för att skapa certifikat**: skapa.
    - **Certifikat namn**: ContosoManualCSRCertificate.
    - **Typ av certifikat utfärdare (ca)**: certifikat utfärdat av en icke-integrerad certifikat utfärdare.
    - **Ämne**: `"CN=www.contosoHRApp.com"` .
     > [!NOTE]
     > Om du använder ett relativt unikt namn (RDN) som har ett kommatecken (,) i värdet omsluter du värdet som innehåller specialtecknet i dubbla citat tecken. 
     >
     > Exempel post till **ämne**: `DC=Contoso,OU="Docs,Contoso",CN=www.contosoHRApp.com`
     >
     > I det här exemplet innehåller RDN `OU` ett värde med ett kommatecken i namnet. Resultatet för `OU` är **dokument, contoso**.
1. Välj de andra värdena som du vill och välj sedan **skapa** för att lägga till certifikatet i listan **certifikat** .

    ![Skärm bild av certifikat egenskaperna](../media/certificates/create-csr-merge-csr/create-certificate.png)  

1. Välj det nya certifikatet i listan **certifikat** . Certifikatets aktuella tillstånd är **inaktiverat** eftersom det ännu inte har utfärdats av ca: n.
1. På fliken **certifikat åtgärd** väljer du **Hämta CSR**.

   ![Skärm bild som visar knappen Hämta CSR.](../media/certificates/create-csr-merge-csr/download-csr.png)

1. Be CA: en att signera CSR (. CSR).
1. När begäran har signerats väljer du **koppla signerad begäran** på fliken **certifikat åtgärd** för att lägga till det signerade certifikatet i Key Vault.

Certifikatbegäran har nu slagits samman.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Skapa en certifikat princip. Eftersom den certifikat utfärdare som valts i det här scenariot inte är en partner, är **IssuerName** inställd på **okänd** och Key Vault inte registrera eller förnya certifikatet.

   ```azure-powershell
   $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
   ```
     > [!NOTE]
     > Om du använder ett relativt unikt namn (RDN) som har ett kommatecken (,) i värdet använder du enkla citat tecken för det fullständiga värdet eller värde uppsättningen, och omsluter värdet som innehåller det specialtecken som innehåller det särskilda värdet med dubbla citat tecken. 
     >
     >Exempel post till **SubjectName**: `$policy = New-AzKeyVaultCertificatePolicy -SubjectName 'OU="Docs,Contoso",DC=Contoso,CN=www.contosoHRApp.com' -ValidityInMonths 1  -IssuerName Unknown` . I det här exemplet `OU` läses värdet som **dokument, contoso**. Det här formatet fungerar för alla värden som innehåller kommatecken.
     > 
     > I det här exemplet innehåller RDN `OU` ett värde med ett kommatecken i namnet. Resultatet för `OU` är **dokument, contoso**.

1. Skapa CSR.

   ```azure-powershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

1. Be CA: en att registrera CSR. `$csr.CertificateSigningRequest`Är den grundläggande kodade CSR-koden för certifikatet. Du kan dumpa denna blob till utfärdarens webbplats för certifikat förfrågningar. Det här steget varierar från CA till CA. Leta upp din CAs rikt linjer för hur du utför det här steget. Du kan också använda verktyg som CertReq eller openssl för att få CSR-signerad och slutför processen för att skapa ett certifikat.

1. Sammanfoga den signerade begäran i Key Vault. När certifikatbegäran har signerats kan du sammanfoga den med det första privata/offentliga nyckel paret som skapats i Azure Key Vault.

    ```azure-powershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

Certifikatbegäran har nu slagits samman.

---

## <a name="add-more-information-to-the-csr"></a>Lägg till mer information i CSR

Om du vill lägga till mer information när du skapar CSR, definierar du den i **SubjectName**. Du kanske vill lägga till information som:
- Land
- Stad/plats
- Region/provins
- Organisation
- Organisationsenhet

Exempel

   ```azure-powershell
   SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
   ```

> [!NOTE]
> Om du begär ett certifikat för domän validering (DV) med ytterligare information kan certifikat utfärdaren avvisa begäran om den inte kan verifiera all information i begäran. Ytterligare information kan vara lämpligare om du begär ett OV-certifikat (Organization Validation).

## <a name="faqs"></a>Vanliga frågor och svar

- Hur gör jag för att övervaka eller hantera min CSR?

     Se [övervaka och hantera certifikat skapande](./create-certificate-scenarios.md).

- Vad händer om jag ser **fel typ "den offentliga nyckeln för certifikatet för slutanvändare i det angivna X. 509-certifikatets innehåll stämmer inte överens med den offentliga delen av den angivna privata nyckeln. Kontrol lera om certifikatet är giltigt**?

     Det här felet uppstår om du inte sammanfogar den signerade kund service representanten med samma CSR-begäran som du har initierat. Varje ny CSR som du skapar har en privat nyckel som måste matcha när du kopplar den signerade begäran.

- När en kund service representant slås samman sammanställs hela kedjan?

     Ja, den sammanfogar hela kedjan, förutsatt att användaren har återställt en. P7B-fil som ska sammanfogas.

- Vad händer om certifikatet som utfärdas har statusen inaktiverat i Azure Portal?

     Visa fliken **certifikat åtgärd** för att granska fel meddelandet för det certifikatet.

- Vad händer om jag ser **fel typen "ämnes namnet som angavs är inte ett giltigt X500-namn"**?

     Det här felet kan inträffa om **SubjectName** innehåller specialtecken. Se kommentarer i Azure Portal-och PowerShell-instruktioner.

---

## <a name="next-steps"></a>Nästa steg

- [Autentisering, begär Anden och svar](../general/authentication-requests-and-responses.md)
- [Utvecklarguide för Key Vault](../general/developers-guide.md)
- [Azure Key Vault REST API referens](/rest/api/keyvault)
- [Valv – skapa eller uppdatera](/rest/api/keyvault/vaults/createorupdate)
- [Valv – uppdatera åtkomst princip](/rest/api/keyvault/vaults/updateaccesspolicy)