---
title: Om Azure Key Vault certifikatförnyelse
description: Den här artikeln beskriver hur du förnyar Azure Key Vault certifikat.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 082b0fd4d3324502516dcd2b45b9ad16a919c773
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749230"
---
# <a name="renew-your-azure-key-vault-certificates"></a>Förnya dina Azure Key Vault certifikat

Med Azure Key Vault kan du enkelt etablera, hantera och distribuera digitala certifikat för ditt nätverk och aktivera säker kommunikation för dina program. Mer information om certifikat finns i [Om Azure Key Vault certifikat.](./about-certificates.md)

Genom att använda kortvariga certifikat eller genom att öka frekvensen för certifikatrotation kan du förhindra obehöriga användare att komma åt dina program.

Den här artikeln beskriver hur du förnyar Azure Key Vault certifikat.

## <a name="get-notified-about-certificate-expiration"></a>Få ett meddelande om förfallodatum för certifikat
Om du vill få ett meddelande om livscykelhändelser för certifikat måste du lägga till certifikatkontakt. Certifikatkontakter innehåller kontaktinformation för att skicka meddelanden som utlöses av certifikatlivshändelser. Kontaktinformationen delas av alla certifikat i nyckelvalvet. Ett meddelande skickas till alla angivna kontakter för en händelse för alla certifikat i nyckelvalvet.

### <a name="steps-to-set-certificate-notifications"></a>Steg för att ange certifikatmeddelanden:
Lägg först till en certifikatkontakt i nyckelvalvet. Du kan lägga till med Azure Portal eller PowerShell-cmdleten [`Add-AzureKeyVaultCertificateContact`](/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact) .

För det andra konfigurerar du när du vill få ett meddelande om att certifikatet upphör att gälla. Information om hur du konfigurerar livscykelattributen för certifikatet [finns i Konfigurera automatiskrotation av certifikat i Key Vault](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate).

Om ett certifikats princip är inställd på automatisk förnyelse skickas ett meddelande om följande händelser.

- Innan certifikatet förnyas
- Efter certifikatförnyelse anger om certifikatet har förnyats, eller om det uppstod ett fel, som kräver manuell förnyelse av certifikatet.  

  När en certifikatprincip som är inställd på att förnyas manuellt (endast e-post) skickas ett meddelande när det är dags att förnya certifikatet.  

I Key Vault finns det tre typer av certifikat:
-    Certifikat som skapas med en integrerad certifikatutfärdare (CA), till exempel DigiCert eller GlobalSign
-    Certifikat som skapas med en icke-integrerad certifikatutfärdare
-    Själv signerade certifikat

## <a name="renew-an-integrated-ca-certificate"></a>Förnya ett integrerat CA-certifikat 
Azure Key Vault hanterar hela underhållet av certifikat som utfärdats av betrodda Microsoft-certifikatutfärdare DigiCert och GlobalSign. Lär dig hur du [integrerar en betrodd certifikatutfärdare med Key Vault](./how-to-integrate-certificate-authority.md).

## <a name="renew-a-nonintegrated-ca-certificate"></a>Förnya ett icke-integrerat CA-certifikat 
Genom att Azure Key Vault certifikat kan du importera certifikat från valfri certifikatutfärdare, en förmån som gör att du kan integrera med flera Azure-resurser och göra distributionen enkel. Om du är orolig över att förlora kontrollen över förfallodatumen för ditt certifikat, eller om du har upptäckt att ett certifikat redan har upphört att gälla, kan ditt nyckelvalv hjälpa dig att hålla dig uppdaterad. För icke-integrerade CA-certifikat kan du med nyckelvalvet konfigurera e-postaviseringar som snart upphör att gälla. Sådana meddelanden kan även anges för flera användare.

> [!IMPORTANT]
> Ett certifikat är ett versionsobjekt. Om den aktuella versionen upphör att gälla måste du skapa en ny version. Konceptuellt är varje ny version ett nytt certifikat som består av en nyckel och en blob som binder nyckeln till en identitet. När du använder en certifikatutfärdare som inte är partner genererar nyckelvalvet ett nyckel/värde-par och returnerar en certifikatsigneringsbegäran (CSR).

Så här förnyar du ett icke-integrerat CA-certifikat:

1. Logga in på Azure Portal och öppna sedan det certifikat som du vill förnya.
1. I certifikatfönstret väljer du **Ny version.**
1. Välj **Certifikatåtgärd**.
1. Välj **Ladda ned CSR** för att ladda ned en CSR-fil till din lokala enhet.
1. Skicka CSR till valfri certifikatutfärdare för att signera begäran.
1. Ta tillbaka den signerade begäran och välj **Sammanfoga CSR** i samma certifikatåtgärdsfönster.

> [!NOTE]
> Det är viktigt att koppla den signerade CSR:en med samma CSR-begäran som du skapade. Annars matchar inte nyckeln.

Mer information om hur du skapar en ny CSR finns i [Skapa och sammanfoga en CSR i Key Vault]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal).

## <a name="renew-a-self-signed-certificate"></a>Förnya ett själv signerat certifikat

Azure Key Vault hanterar även autorenewal av själv signerade certifikat. Mer information om hur du ändrar utfärdandeprincipen och uppdaterar ett certifikats livscykelattribut finns i [Konfigurera automatiskrotation av certifikat i Key Vault](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate).

## <a name="troubleshoot"></a>Felsöka
* Om det utfärdade certifikatet har *inaktiverats* i Azure Portal går du till **Certifikatåtgärd** för att visa certifikatets felmeddelande.
* Feltypen "CSR som används för att hämta certifikatet har redan använts. Försök att generera ett nytt certifikat med en ny CSR."
  Gå till avsnittet Avancerad princip i certifikatet och kontrollera om **alternativet "återanvändningsnyckel vid förnyelse"** är inaktiverat.


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Hur testar jag funktionen för automatiskrotation för certifikatet?**

Skapa ett själv signerat certifikat med en giltighet på **1** månad och ange sedan livslängdsåtgärden för rotation till **1 %**. Du bör kunna visa certifikatversionshistoriken som skapas under de närmaste dagarna.
  
**Kommer taggarna att replikeras efter ett autorenewal av certifikatet?**

Ja, taggarna replikeras efter autorenewal.

## <a name="next-steps"></a>Nästa steg
*    [Integrera Key Vault med DigiCert-certifikatutfärdare](how-to-integrate-certificate-authority.md)
*    [Självstudie: Konfigurera automatisk certifikatrotation i Key Vault](tutorial-rotate-certificates.md)
