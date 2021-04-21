---
title: Vanliga frågor och svar – Azure Key Vault importera certifikat
description: Få svar på vanliga frågor om import Azure Key Vault certifikat.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 39fe4e77d701f8e9311ea343c88eb3b905496680
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749248"
---
# <a name="importing-azure-key-vault-certificates-faq"></a>Vanliga frågor och svar Azure Key Vault importera Azure Key Vault certifikat

Den här artikeln besvarar vanliga frågor om import Azure Key Vault certifikat.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Hur importerar jag ett certifikat i Azure Key Vault?

För en certifikatimportåtgärd Azure Key Vault två certifikatfilformat: PEM och PFX. Även om det finns PEM-filer med endast den offentliga delen Key Vault kräver och accepterar endast en PEM- eller PFX-fil med en privat nyckel. Mer information finns i Importera [ett certifikat till Key Vault](./tutorial-import-certificate.md#import-a-certificate-to-key-vault).

### <a name="after-i-import-a-password-protected-certificate-to-key-vault-and-then-download-it-why-cant-i-see-the-password-thats-associated-with-it"></a>När jag har importerat ett lösenordsskyddat certifikat till Key Vault och sedan laddar ned det, varför kan jag inte se lösenordet som är associerat med det?
     
När ett certifikat importeras och skyddas i Key Vault sparas inte dess associerade lösenord. Lösenordet krävs bara en gång under importen. Detta är design, men du kan alltid hämta certifikatet som en hemlighet och konvertera det från Base64 till PFX genom att lägga till lösenordet via [Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx).

### <a name="how-can-i-resolve-a-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-to-key-vault"></a>Hur kan jag lösa felet "Felaktig parameter"? Vilka certifikatformat stöds för import till Key Vault?

När du importerar ett certifikat måste du se till att nyckeln ingår i filen. Om du har en privat nyckel som lagras separat i ett annat format måste du kombinera nyckeln med certifikatet. Vissa certifikatutfärdare tillhandahåller certifikat i andra format. Innan du importerar certifikatet kontrollerar du därför att det är i PEM- eller PFX-filformat och att nyckeln använder antingen Rivest–Shamir–Adleman (RSA) eller ECC-kryptering (elliptic-curve cryptography). 

Mer information finns i [certifikatkrav och](./certificate-scenarios.md#formats-of-import-we-support) [krav på certifikatnyckel.](../keys/about-keys.md)

###  <a name="can-i-import-a-certificate-by-using-an-arm-template"></a>Kan jag importera ett certifikat med hjälp av en ARM-mall?

Nej, det går inte att utföra certifikatåtgärder med hjälp av en arm Azure Resource Manager mall (arm). En rekommenderad lösning är att använda certifikatimportmetoderna i Azure API, Azure CLI eller PowerShell. Om du har ett befintligt certifikat kan du importera det som en hemlighet.

### <a name="when-i-import-a-certificate-via-the-azure-portal-i-get-a-something-went-wrong-error-how-can-i-investigate-further"></a>När jag importerar ett certifikat via Azure Portal visas felet "Något gick fel". Hur kan jag undersöka vidare?
     
Om du vill visa ett mer beskrivande fel importerar du certifikatfilen med [hjälp av Azure CLI](/cli/azure/keyvault/certificate#az-keyvault-certificate-import) eller [PowerShell.](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate)

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>Hur kan jag lösa "Feltyp: Åtkomst nekad eller användaren har inte behörighet att importera certifikat"?
    
Importåtgärden kräver att du ger användaren behörighet att importera certifikatet under åtkomstprinciperna. Det gör du genom att gå till ditt nyckelvalv, välja Åtkomstprinciper Lägg till åtkomstprincip Välj certifikatbehörigheter, söka efter användaren och sedan lägga till  >    >    >  användarens e-postadress. 

Mer information om certifikatrelaterade åtkomstprinciper finns i [Om Azure Key Vault certifikat](./about-certificates.md#certificate-access-control).


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>Hur löser jag "Feltyp: Konflikt när jag skapar ett certifikat"?
    
Varje certifikatnamn måste vara unikt. Ett certifikat med samma namn kan vara i ett mjukt borttagna tillstånd. Enligt sammansättningen [](./about-certificates.md#composition-of-a-certificate)av ett certifikat skapar det, när ett nytt certifikat skapas, en adresserbar hemlighet med samma namn, så om det finns en annan nyckel eller hemlighet i nyckelvalvet med samma namn som det du försöker ange för certifikatet kommer skapandet av certifikatet att misslyckas och du måste antingen ta bort den nyckeln eller hemligheten eller använda ett annat namn för certifikatet. 

Mer information finns i [Åtgärden Hämta borttagna certifikat.](/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate)

### <a name="why-am-i-getting-error-type-char-length-is-too-long"></a>Varför får jag "Feltyp: teckenlängden är för lång"?
Det här felet kan orsakas av någon av två orsaker:    
* Certifikatets ämnesnamn är begränsat till 200 tecken.
* Certifikatets lösenord är begränsat till 200 tecken.


### <a name="error-the-specified-pem-x509-certificate-content-is-in-an-unexpected-format-please-check-if-certificate-is-in-valid-pem-format"></a>Fel "Det angivna PEM X.509-certifikatinnehållet har ett oväntat format. Kontrollera om certifikatet är i giltigt PEM-format."
Kontrollera att innehållet i PEM-filen använder radavgränsare i UNIX-format `(\n)`

### <a name="can-i-import-an-expired-certificate-to-azure-key-vault"></a>Kan jag importera ett utgånget certifikat till Azure Key Vault?
    
Nej, utgångna PFX-certifikat kan inte importeras till Key Vault.

### <a name="how-can-i-convert-my-certificate-to-the-proper-format"></a>Hur konverterar jag mitt certifikat till rätt format?

Du kan be certifikatutfärdaren att tillhandahålla certifikatet i det format som krävs. Det finns även verktyg från tredje part som kan hjälpa dig att konvertera certifikatet till rätt format.

### <a name="can-i-import-certificates-from-non-partner-cas"></a>Kan jag importera certifikat från certifikat från icke-partner-CERTIFIKAT
Ja, du kan importera certifikat från valfri certifikatutfärdare, men ditt nyckelvalv kan inte förnya dem automatiskt. Du kan ställa in påminnelser om att få ett meddelande om att certifikatet upphör att gälla.

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-autorenewal-feature-still-work"></a>Kommer funktionen autorenewal fortfarande att fungera om jag importerar ett certifikat från en partnercertifikatutfärdare?
Ja. När du har laddat upp certifikatet måste du ange autorotation i certifikatets utfärdandeprincip. Inställningarna gäller tills nästa cykel eller certifikatversion släpps.

### <a name="why-cant-i-see-the-app-service-certificate-that-i-imported-to-key-vault"></a>Varför kan jag inte se det App Service certifikat som jag har importerat till Key Vault? 
Om du har importerat certifikatet korrekt bör du kunna bekräfta det genom att gå till **fönstret** Hemligheter.


## <a name="next-steps"></a>Nästa steg

- [Azure Key Vault certifikat](./about-certificates.md)
