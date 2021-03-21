---
title: Vanliga frågor och Azure Key Vault certifikat import
description: Få svar på vanliga frågor om att importera Azure Key Vault certifikat.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 78ec0af0acbb74436af16abba75d64de061d5268
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202173"
---
# <a name="importing-azure-key-vault-certificates-faq"></a>Vanliga frågor om import av Azure Key Vault-certifikat

I den här artikeln får du svar på vanliga frågor om hur du importerar Azure Key Vault certifikat.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Hur kan jag importera ett certifikat i Azure Key Vault?

För att importera ett certifikat kan Azure Key Vault acceptera två certifikat fil format: PEM och PFX. Även om det bara finns PEM-filer med den offentliga delen, behöver Key Vault bara godkänna en PEM-eller PFX-fil med en privat nyckel. Mer information finns i [Importera ett certifikat till Key Vault](./tutorial-import-certificate.md#import-a-certificate-to-key-vault).

### <a name="after-i-import-a-password-protected-certificate-to-key-vault-and-then-download-it-why-cant-i-see-the-password-thats-associated-with-it"></a>Varför kan jag inte se det lösen ord som är associerat med det när jag har importerat ett lösenordsskyddat certifikat till Key Vault och sedan laddar ned det?
    
När ett certifikat har importer ATS och skyddats i Key Vault sparas inte det associerade lösen ordet. Lösen ordet krävs bara en gång under import åtgärden. Detta är avsiktligt, men du kan alltid få certifikatet som en hemlighet och konvertera det från base64 till PFX genom att lägga till lösen ordet via [Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx).

### <a name="how-can-i-resolve-a-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-to-key-vault"></a>Hur kan jag lösa fel meddelandet "Felaktig parameter"? Vilka certifikat format stöds för import till Key Vault?

När du importerar ett certifikat måste du se till att nyckeln ingår i filen. Om du har en privat nyckel lagrad separat i ett annat format måste du kombinera nyckeln med certifikatet. Vissa certifikat utfärdare tillhandahåller certifikat i andra format. Innan du importerar certifikatet måste du därför kontrol lera att det är i antingen PEM-eller PFX-filformat och att nyckeln använder antingen Rivest – Shamir – Adleman (RSA) eller (Elliptic-Curve Cryptography Encryption). 

Mer information finns i krav på [certifikat krav](./certificate-scenarios.md#formats-of-import-we-support) och [certifikat nycklar](../keys/about-keys.md).

###  <a name="can-i-import-a-certificate-by-using-an-arm-template"></a>Kan jag importera ett certifikat med en ARM-mall?

Nej, det går inte att utföra certifikat åtgärder med hjälp av en Azure Resource Manager-mall (ARM). En rekommenderad lösning är att använda certifikat import metoderna i Azure API, Azure CLI eller PowerShell. Om du har ett befintligt certifikat kan du importera det som en hemlighet.

### <a name="when-i-import-a-certificate-via-the-azure-portal-i-get-a-something-went-wrong-error-how-can-i-investigate-further"></a>När jag importerar ett certifikat via Azure Portal får jag ett fel meddelande om att något har gått fel. Hur kan jag undersöka ytterligare?
    
Om du vill visa ett mer beskrivande fel importerar du certifikat filen med hjälp av [Azure CLI](/cli/azure/keyvault/certificate#az-keyvault-certificate-import) eller [PowerShell](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0).

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>Hur kan jag lösa "typ av fel: åtkomst nekad eller användare har inte behörighet att importera certifikat"?
    
Import åtgärden kräver att du ger användar behörighet att importera certifikatet under åtkomst principerna. Det gör du genom att gå till ditt nyckel valv, välja **åtkomst principer**  >  **Lägg till åtkomst princip**  >  **Välj certifikat behörighets**  >  **objekt**, söka efter användaren och sedan lägga till användarens e-postadress. 

Mer information om certifikat-relaterade åtkomst principer finns i [om Azure Key Vault certifikat](./about-certificates.md#certificate-access-control).


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>Hur kan jag lösa "typ av fel: en konflikt vid skapande av ett certifikat"?
    
Varje certifikat namn måste vara unikt. Ett certifikat med samma namn kan vara i ett läge med mjuk borttagning. Även när [ett certifikat skapas när ett certifikat](./about-certificates.md#composition-of-a-certificate)skapas, skapas en adresserad hemlighet med samma namn, så om det finns en annan nyckel eller hemlighet i nyckel valvet med samma namn som det som du försöker att ange för ditt certifikat, kommer det inte att gå att skapa certifikat och du måste antingen ta bort nyckeln eller hemligheten eller använda ett annat namn för certifikatet. 

Mer information finns i [Get Deleted Certificate-åtgärd](/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate).

### <a name="why-am-i-getting-error-type-char-length-is-too-long"></a>Varför får jag "fel typ: tecken längden är för lång"?
Det här felet kan bero på någon av två anledningar:    
* Certifikatets ämnes namn är begränsat till 200 tecken.
* Certifikat lösen ordet är begränsat till 200 tecken.


### <a name="error-the-specified-pem-x509-certificate-content-is-in-an-unexpected-format-please-check-if-certificate-is-in-valid-pem-format"></a>Fel: det angivna PEM X. 509-certifikatets innehåll är i ett oväntat format. Kontrol lera att certifikatet har ett giltigt PEM-format. "
Kontrol lera att innehållet i PEM-filen använder rad avgränsare i UNIX-format `(\n)`

### <a name="can-i-import-an-expired-certificate-to-azure-key-vault"></a>Kan jag importera ett utgånget certifikat för att Azure Key Vault?
    
Nej, det går inte att importera slut bara PFX-certifikat till Key Vault.

### <a name="how-can-i-convert-my-certificate-to-the-proper-format"></a>Hur kan jag konvertera mitt certifikat till rätt format?

Du kan be din certifikat utfärdare att tillhandahålla certifikatet i det format som krävs. Det finns även verktyg från tredje part som kan hjälpa dig att konvertera certifikatet till rätt format.

### <a name="can-i-import-certificates-from-non-partner-cas"></a>Kan jag importera certifikat från certifikat utfärdare som inte är partner?
Ja, du kan importera certifikat från alla CA: er, men nyckel valvet kan inte förnya dem automatiskt. Du kan ställa in påminnelser för att få meddelanden om förfallo datum för certifikatet.

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-autorenewal-feature-still-work"></a>Kommer funktionen för automatiskt förnyelse att fungera om jag importerar ett certifikat från en partner certifikat utfärdare?
Ja. När du har överfört certifikatet måste du ange autorotationen i certifikatets utgivnings princip. Inställningarna gäller tills nästa cykel eller certifikat version släpps.

### <a name="why-cant-i-see-the-app-service-certificate-that-i-imported-to-key-vault"></a>Varför kan jag inte se det App Service certifikat som jag har importerat till Key Vault? 
Om du har importerat certifikatet korrekt bör du kunna bekräfta det genom att gå till fönstret **hemligheter** .


## <a name="next-steps"></a>Nästa steg

- [Azure Key Vault certifikat](./about-certificates.md)
