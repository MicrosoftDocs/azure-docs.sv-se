---
title: Om förnyelse av Azure Key Vault certifikat
description: Den här artikeln beskriver hur du förnyar Azure Key Vault certifikat.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 3809fa9e1ce17a5a0c3cf333ac20ef543db4b5a7
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588811"
---
# <a name="renew-your-azure-key-vault-certificates"></a>Förnya dina Azure Key Vault certifikat

Med Azure Key Vault kan du enkelt etablera, hantera och distribuera digitala certifikat för ditt nätverk och aktivera säker kommunikation för dina program. Mer information om certifikat finns i [om Azure Key Vault certifikat](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates).

Genom att använda kortsiktiga certifikat eller genom att öka frekvensen för certifikat rotation kan du förhindra att obehöriga användare får åtkomst till dina program.

Den här artikeln beskriver hur du förnyar Azure Key Vault certifikat.

## <a name="get-notified-about-certificate-expirations"></a>Få meddelanden om förfallo datum för certifikat
Om du vill bli meddelad när dina certifikat håller på att gå ut, gör du följande:

Lägg först till en certifikat kontakt i nyckel valvet med PowerShell-cmdleten [Add-AzureKeyVaultCertificateContact](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact?view=azurermps-6.13.0).

Sedan konfigurerar du när du vill få information om förfallo datum för certifikatet. Information om hur du konfigurerar livs cykel attribut för certifikatet finns i [Konfigurera autorotation av certifikat i Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate).

I Key Vault finns det tre kategorier certifikat:
-   Certifikat som har skapats med en integrerad certifikat utfärdare (CA), till exempel DigiCert eller GlobalSign
-   Certifikat som skapas med en certifikat utfärdare som inte är integrerad
-   Självsignerade certifikat

## <a name="renew-an-integrated-ca-certificate"></a>Förnya ett integrerat CA-certifikat 
Azure Key Vault hanterar det heltäckande underhållet av certifikat som utfärdats av betrodda Microsoft Certificate-myndigheter DigiCert och GlobalSign. Lär dig hur du [integrerar en betrodd certifikat utfärdare med Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/how-to-integrate-certificate-authority).

## <a name="renew-a-nonintegrated-ca-certificate"></a>Förnya ett certifikat som inte är integrerat 
Genom att använda Azure Key Vault kan du importera certifikat från alla CA: er, en förmån som gör att du kan integrera med flera Azure-resurser och göra distributionen enklare. Om du oroar dig för att förlora spårning av certifikatets giltighets datum eller, om du har upptäckt att ett certifikat redan har gått ut, kan ditt nyckel valv hjälpa dig att hålla dig uppdaterad. För certifikat som inte är integrerade kan du med nyckel valvet konfigurera e-postmeddelanden som snart upphör att gälla. Sådana meddelanden kan också ställas in för flera användare.

> [!IMPORTANT]
> Ett certifikat är ett versions objekt. Om den aktuella versionen upphör att gälla måste du skapa en ny version. Varje ny version är konceptuellt ett nytt certifikat som består av en nyckel och en blob som binder nyckeln till en identitet. När du använder en certifikat utfärdare som inte är partner genererar nyckel valvet ett nyckel/värde-par och returnerar en certifikat signerings förfrågan (CSR).

Om du vill förnya ett certifikat som inte är integrerat gör du följande:

1. Logga in på Azure Portal och öppna sedan det certifikat som du vill förnya.
1. I fönstret certifikat väljer du **ny version**.
1. Välj **certifikat åtgärd**.
1. Välj **Hämta CSR** för att ladda ned en CSR-fil till den lokala enheten.
1. Skicka kund service representanten till ditt val av CA för att signera begäran.
1. Ta tillbaka den signerade begäran och välj **sammanfoga CSR** i samma certifikat åtgärds fönster.

> [!NOTE]
> Det är viktigt att slå samman den signerade kund service representanten med samma CSR-begäran som du skapade. Annars matchar inte nyckeln.

Mer information om hur du skapar en ny CSR finns [i skapa och slå samman en CSR i Key Vault]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal).

## <a name="renew-a-self-signed-certificate"></a>Förnya ett självsignerat certifikat

Azure Key Vault hanterar också automatisk förnyelse av självsignerade certifikat. Om du vill veta mer om hur du ändrar utgivnings principen och uppdaterar ett certifikats livs cykel attribut, se [Konfigurera autorotation av certifikat i Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate).

## <a name="troubleshoot"></a>Felsöka
Om det utfärdade certifikatet har *inaktiverats* i Azure Portal går du till **certifikat åtgärd** för att se certifikatets fel meddelande.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Hur kan jag testa funktionen för autorotation i certifikatet?**

Skapa ett certifikat med en giltighets tid på **1 månad**och ange sedan livs längds åtgärden för rotation vid **1%**. Med den här inställningen roteras certifikatet var 7,2: e timme.
  
**Kommer taggarna att replikeras efter autoförnyelse av certifikatet?**

Ja, taggarna replikeras efter autoförnyelse.

## <a name="next-steps"></a>Nästa steg
*   [Integrera Key Vault med certifikat utfärdare för DigiCert](how-to-integrate-certificate-authority.md)
*   [Självstudie: Konfigurera autorotation av certifikat i Key Vault](tutorial-rotate-certificates.md)
