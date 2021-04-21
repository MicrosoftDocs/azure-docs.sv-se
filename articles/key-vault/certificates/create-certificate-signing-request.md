---
title: Skapa och sammanfoga en CSR i Azure Key Vault
description: Lär dig hur du skapar och sammanfogar en CSR i Azure Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: a4d079855e5aa05adb84b62d686d9f386608f7bb
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752146"
---
# <a name="create-and-merge-a-csr-in-key-vault"></a>Skapa och sammanfoga en CSR i Key Vault

Azure Key Vault stöd för lagring av digitala certifikat som utfärdats av en certifikatutfärdare (CA). Den har stöd för att skapa en certifikatsigneringsförfrågan (CSR) med ett privat/offentligt nyckelpar. CSR kan signeras av en certifikatutfärdare (en intern företags-CA eller en extern offentlig certifikatutfärdare). En CSR är ett meddelande som du skickar till en certifikatutfärdare för att begära ett digitalt certifikat.

Mer allmän information om certifikat finns i [Azure Key Vault certifikat](./about-certificates.md).

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="add-certificates-in-key-vault-issued-by-partnered-cas"></a>Lägga till certifikat Key Vault utfärdade av partner-CERTIFIKAT

Key Vault samarbetar med följande certifikatutfärdare för att förenkla skapandet av certifikat.

|Leverantör|Certifikattyp|Konfigurationskonfiguration  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault erbjuder OV- eller EV SSL-certifikat med DigiCert| [Integreringsguide](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault erbjuder OV- eller EV SSL-certifikat med GlobalSign| [Integreringsguide](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## <a name="add-certificates-in-key-vault-issued-by-non-partnered-cas"></a>Lägga till certifikat Key Vault certifikat som utfärdats av icke-partnerbaserade certifikat

Följ dessa steg om du vill lägga till ett certifikat från certifikatutfärdare som inte är partner med Key Vault. (Till exempel är GoDaddy inte en betrodd certifikatutfärdare Key Vault certifikatutfärdare.)

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Gå till nyckelvalvet som du vill lägga till certifikatet i.
1. På egenskapssidan väljer du **Certifikat.**
1. Välj **fliken Generera/importera.**
1. På skärmen **Skapa ett** certifikat väljer du följande värden:
    - **Metod för att skapa certifikat:** Generera.
    - **Certifikatnamn:** ContosoManualCSRCertificate.
    - **Typ av certifikatutfärdare (CA): Certifikat** utfärdat av en icke-integrerad certifikatutfärdare.
    - **Ämne:** `"CN=www.contosoHRApp.com"` .
     > [!NOTE]
     > Om du använder ett relativt unikt namn (RDN) som har ett kommatecken (,) i värdet omsluter du det värde som innehåller specialtecknet med dubbla citattecken. 
     >
     > Exempelpost till **Ämne:**`DC=Contoso,OU="Docs,Contoso",CN=www.contosoHRApp.com`
     >
     > I det här exemplet innehåller RDN `OU` ett värde med ett kommatecken i namnet. Resultatet för är `OU` **Docs, Contoso**.
1. Välj de andra värdena som du vill och välj sedan **Skapa för** att lägga till certifikatet i **listan** Certifikat.

    ![Skärmbild av certifikategenskaperna](../media/certificates/create-csr-merge-csr/create-certificate.png)  

1. I listan **Certifikat** väljer du det nya certifikatet. Certifikatets aktuella tillstånd är **inaktiverat** eftersom det inte har utfärdats av certifikatutfärdaren ännu.
1. På fliken **Certifikatåtgärd** väljer du Ladda ned **CSR.**

   ![Skärmbild som visar knappen Ladda ned CSR.](../media/certificates/create-csr-merge-csr/download-csr.png)

1. Be CERTIFIKATutfärdaren signera CSR (.csr).
1. När begäran har signerats väljer du **Sammanfoga signerad begäran** på **fliken Certifikatåtgärd** för att lägga till det signerade certifikatet i Key Vault.

Certifikatbegäran har nu sammanfogats.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Skapa en certifikatprincip. Eftersom certifikatutfärdaren som valts i det här scenariot inte är partner är **IssuerName** inställt på **Okänd** och Key Vault inte registrerar eller förnyar certifikatet.

   ```azure-powershell
   $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
   ```
     > [!NOTE]
     > Om du använder ett relativt unikt namn (RDN) som har ett kommatecken (,) i värdet använder du enkla citattecken för det fullständiga värdet eller värdeuppsättningen och omsluter värdet som innehåller specialtecknet med dubbla citattecken. 
     >
     >Exempelpost till **SubjectName:** `$policy = New-AzKeyVaultCertificatePolicy -SubjectName 'OU="Docs,Contoso",DC=Contoso,CN=www.contosoHRApp.com' -ValidityInMonths 1  -IssuerName Unknown` . I det här exemplet `OU` läses värdet som **Docs, Contoso**. Det här formatet fungerar för alla värden som innehåller ett kommatecken.
     > 
     > I det här exemplet innehåller RDN `OU` ett värde med ett kommatecken i namnet. Resultatet för är `OU` **Docs, Contoso**.

1. Skapa CSR.

   ```azure-powershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

1. Be CERTIFIKATutfärdaren signera CSR. `$csr.CertificateSigningRequest`är den grundläggande kodade CSR:en för certifikatet. Du kan dumpa den här bloben på utfärdarens webbplats för certifikatbegäran. Det här steget varierar från CA till CA. Leta upp cans riktlinjer för hur du utför det här steget. Du kan också använda verktyg som certreq eller openssl för att få CSR signerad och slutföra processen med att generera ett certifikat.

1. Sammanfoga den signerade begäran i Key Vault. När certifikatbegäran har signerats kan du sammanslå den med det första privata/offentliga nyckelparet som skapades i Azure Key Vault.

    ```azure-powershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

Certifikatbegäran har nu sammanfogats.

---

## <a name="add-more-information-to-the-csr"></a>Lägga till mer information i CSR

Om du vill lägga till mer information när du skapar CSR definierar du den i **SubjectName**. Du kanske vill lägga till information som:
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
> Om du begär ett domänvalideringscertifikat (DV) med ytterligare information kan certifikatutfärdaren avvisa begäran om den inte kan verifiera all information i begäran. Den ytterligare informationen kan vara lämpligare om du begär ett certifikat för organisationsvalidering (OV).

## <a name="faqs"></a>Vanliga frågor och svar

- Hur gör jag för att övervaka eller hantera min CSR?

     Se [Övervaka och hantera skapande av certifikat.](./create-certificate-scenarios.md)

- Vad händer om jag ser feltypen "Den offentliga nyckeln för slutentitetscertifikatet i det angivna X.509-certifikatinnehållet matchar inte den offentliga delen av den **angivna privata nyckeln. Kontrollera om certifikatet är giltigt?**

     Det här felet uppstår om du inte slår samman den signerade CSR:en med samma CSR-begäran som du initierade. Varje ny CSR som du skapar har en privat nyckel som måste matcha när du sammanfogar den signerade begäran.

- Kommer en CSR att slås samman, kommer den att slå samman hela kedjan?

     Ja, hela kedjan sammanfogas, förutsatt att användaren har tagit tillbaka en .p7b-fil för att sammanfoga.

- Vad händer om certifikatet som utfärdats har inaktiverad status i Azure Portal?

     Visa fliken **Certifikatåtgärd** för att granska felmeddelandet för certifikatet.

- Vad händer om jag **ser feltypen "Det angivna ämnesnamnet är inte ett giltigt X500-namn"?**

     Det här felet kan inträffa **om SubjectName** innehåller specialtecken. Se anteckningar i Azure Portal och PowerShell.

---

## <a name="next-steps"></a>Nästa steg

- [Autentisering, begäranden och svar](../general/authentication-requests-and-responses.md)
- [Utvecklarguide för Key Vault](../general/developers-guide.md)
- [Azure Key Vault REST API referens](/rest/api/keyvault)
- [Valv – Skapa eller uppdatera](/rest/api/keyvault/vaults/createorupdate)
- [Valv – Uppdatera åtkomstprincip](/rest/api/keyvault/vaults/updateaccesspolicy)