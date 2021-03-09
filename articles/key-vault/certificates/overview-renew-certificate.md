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
ms.openlocfilehash: c7948230164258aa785f3dd6c1f487c51ece9333
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487193"
---
# <a name="renew-your-azure-key-vault-certificates"></a>Förnya dina Azure Key Vault certifikat

Med Azure Key Vault kan du enkelt etablera, hantera och distribuera digitala certifikat för ditt nätverk och aktivera säker kommunikation för dina program. Mer information om certifikat finns i [om Azure Key Vault certifikat](./about-certificates.md).

Genom att använda kortsiktiga certifikat eller genom att öka frekvensen för certifikat rotation kan du förhindra att obehöriga användare får åtkomst till dina program.

Den här artikeln beskriver hur du förnyar Azure Key Vault certifikat.

## <a name="get-notified-about-certificate-expiration"></a>Få meddelanden om förfallo datum för certifikat
Om du vill bli informerad om certifikatets livs längd måste du lägga till certifikat kontakt. Certifikat kontakter innehåller kontakt information för att skicka meddelanden som utlöses av händelser för certifikat livs längd. Kontakt information delas av alla certifikat i nyckel valvet. Ett meddelande skickas till alla angivna kontakter för en händelse för ett certifikat i nyckel valvet.

### <a name="steps-to-set-certificate-notifications"></a>Steg för att ange certifikat meddelanden:
Lägg först till en certifikat kontakt i ditt nyckel valv. Du kan lägga till med Azure Portal eller PowerShell-cmdlet [`Add-AzureKeyVaultCertificateContact`](/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact?view=azurermps-6.13.0) .

Sedan konfigurerar du när du vill få information om förfallo datum för certifikatet. Information om hur du konfigurerar livs cykel attribut för certifikatet finns i [Konfigurera autorotation av certifikat i Key Vault](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate).

Om ett certifikats princip ställs in på automatisk förnyelse skickas ett meddelande vid följande händelser.

- Innan certifikat förnyas
- Efter förnyelse av certifikat, anger om certifikatet har förnyats eller om det uppstod ett fel, vilket kräver manuell förnyelse av certifikatet.  

  När en certifikat princip som anges ska förnyas manuellt (endast e-post) skickas ett meddelande när det är dags att förnya certifikatet.  

I Key Vault finns det tre kategorier certifikat:
-   Certifikat som har skapats med en integrerad certifikat utfärdare (CA), till exempel DigiCert eller GlobalSign
-   Certifikat som skapas med en certifikat utfärdare som inte är integrerad
-   Självsignerade certifikat

## <a name="renew-an-integrated-ca-certificate"></a>Förnya ett integrerat CA-certifikat 
Azure Key Vault hanterar det heltäckande underhållet av certifikat som utfärdats av betrodda Microsoft Certificate-myndigheter DigiCert och GlobalSign. Lär dig hur du [integrerar en betrodd certifikat utfärdare med Key Vault](./how-to-integrate-certificate-authority.md).

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

Azure Key Vault hanterar också automatisk förnyelse av självsignerade certifikat. Om du vill veta mer om hur du ändrar utgivnings principen och uppdaterar ett certifikats livs cykel attribut, se [Konfigurera autorotation av certifikat i Key Vault](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate).

## <a name="troubleshoot"></a>Felsöka
* Om det utfärdade certifikatet har *inaktiverats* i Azure Portal går du till **certifikat åtgärd** för att se certifikatets fel meddelande.
* Fel typ "CSR som användes för att hämta ditt certifikat har redan använts. Försök att generera ett nytt certifikat med en ny CSR. "
  Gå till avsnittet "avancerad princip" i certifikatet och kontrol lera om alternativet **"Återanvänd nyckel vid förnyelse"** är inaktiverat.


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Hur kan jag testa funktionen för autorotation i certifikatet?**

Skapa ett certifikat med en giltighets tid på **1 månad** och ange sedan livs längds åtgärden för rotation vid **1%**. Med den här inställningen roteras certifikatet var 7,2: e timme.
  
**Kommer taggarna att replikeras efter autoförnyelse av certifikatet?**

Ja, taggarna replikeras efter autoförnyelse.

## <a name="next-steps"></a>Nästa steg
*   [Integrera Key Vault med certifikat utfärdare för DigiCert](how-to-integrate-certificate-authority.md)
*   [Självstudie: Konfigurera autorotation av certifikat i Key Vault](tutorial-rotate-certificates.md)
