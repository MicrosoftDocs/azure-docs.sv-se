---
title: Autentisera till Azure Key Vault
description: Lär dig att autentisera till Azure Key Vault
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 06/08/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 7645600a476a1c2294ddd4a24fe01e2ffe51d5ac
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589991"
---
# <a name="authenticate-to-azure-key-vault"></a>Autentisera till Azure Key Vault

## <a name="overview"></a>Översikt

Azure Key Vault är en lösning för hemligheter som gör att du kan centralisera lagringen av program hemligheter och kontrol lera deras distribution. Azure Key Vault eliminerar behovet av att lagra autentiseringsuppgifter i program. Ditt program kan autentiseras för nyckel valvet för att hämta de autentiseringsuppgifter som krävs. Det här dokumentet beskriver grundläggande autentisering för nyckel valvet.

I det här dokumentet får du hjälp att förstå hur Key Vault-autentisering fungerar. I det här dokumentet förklaras verifierings flödet, hur du beviljar åtkomst till ditt nyckel valv och innehåller en självstudie för att hämta en lagrad hemlighet i Key Vault från ett exempel på python-program.

Det här dokumentet kommer att avse:

* Viktiga begrepp
* Registrering av säkerhets objekt
* Förstå Key Vault autentiseringspaket
* Bevilja tjänstens huvud namn åtkomst till Key Vault
* Självstudie (python)

## <a name="key-concepts"></a>Viktiga begrepp

### <a name="azure-active-directory-concepts"></a>Azure Active Directory begrepp

* Azure Active Directory (AAD) – Azure Active Directory (Azure AD) är Microsofts molnbaserade identitets-och åtkomst hanterings tjänst, som hjälper dina anställda att logga in och få åtkomst till resurser

* Roll definition – en roll definition är en samling behörigheter.  AAD har standard roller (ägare, deltagare eller läsare) som innehåller nivåer av behörigheter för att utföra åtgärder som läsa, skriva och ta bort på en Azure-resurs. Roller kan också vara anpassade definitioner som skapats av användare med detaljerad behörighet.

* Program registrering – när du registrerar ett Azure AD-program skapas två objekt i Azure AD-klienten, ett program objekt och ett huvud objekt för tjänsten. Överväg programobjektet som den globala åter givningen av programmet för användning över alla klienter och tjänstens huvud namn som den lokala åter givningen för användning i en specifik klient.

### <a name="security-principal-concepts"></a>Koncept för säkerhets objekt

* Säkerhets objekt – ett säkerhets objekt är ett objekt som representerar en användare, grupp, tjänstens huvud namn eller hanterad identitet som begär åtkomst till Azure-resurser.

* Användare – en person som har en profil i Azure Active Directory.

* Grupp – en uppsättning användare som skapas i Azure Active Directory. När du tilldelar en roll till en grupp får alla användare i gruppen den rollen.

* Tjänstens huvudnamn – en säkerhetsidentitet som används av program eller tjänster för att få åtkomst till specifika Azure-resurser. Du kan se det som en användaridentitet (användarnamn och lösenord eller certifikat) för ett program.

* Hanterad identitet – en identitet i Azure Active Directory som hanteras automatiskt av Azure.

* Objekt-ID (klient-ID) – en unik identifierare som genererats av Azure AD och som är kopplad till ett huvud namn för tjänsten under dess första etablering.

## <a name="security-principal-registration"></a>Registrering av säkerhets objekt

1. Administratör registrerar en användare eller ett program (tjänstens huvud namn) i Azure Active Directory.

2. Administratören skapar en Azure Key Vault och konfigurerar åtkomst principer (ACL: er).

3. Valfritt Administratören konfigurerar Azure Key Vault brand väggen.

![BILD](../media/authentication-1.png)

## <a name="understand-the-key-vault-authentication-flow"></a>Förstå Key Vault Authentication Flow

1. Ett tjänst huvud namn gör ett anrop för att autentisera till AAD detta kan ske på flera sätt:
    * En användare kan logga in på Azure Portal med ett användar namn och lösen ord.
    * Ett program använder ett klient-ID och visar en klient hemlighet eller ett klient certifikat för AAD
    * En Azure-resurs, till exempel en virtuell dator har en tilldelad MSI och kontaktar IMDS REST-slutpunkten för att få en åtkomsttoken.

2. Om autentiseringen till AAD lyckas kommer tjänstens huvud namn att beviljas en OAuth-token.
3. Tjänstens huvud namn gör ett anrop till Key Vault.
4. Azure Key Vault brand väggen avgör om anropet ska tillåtas.
    * Scenario 1: Key Vault brand väggen är inaktive rad, den offentliga slut punkten (URI) för nyckel valvet kan nås från det offentliga Internet. Anrop tillåts.
    * Scenario 2: anroparen är en Azure Key Vault betrodd tjänst. Vissa Azure-tjänster kan kringgå Key Vault-brandväggen om alternativet är markerat. [Lista över Key Vault betrodda tjänster](https://docs.microsoft.com/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services)
    * Scenario 3: anroparen visas i Azure Key Vault brand väggen via IP-adress, virtuellt nätverk eller tjänst slut punkt.
    * Scenario 4: anroparen kan uppnå Azure Key Vault över en konfigurerad anslutning till en privat länk.
    * Scenario 5: anroparen är inte auktoriserad och ett förbjudet svar returneras.
5. Key Vault gör ett anrop till AAD för att verifiera tjänstens huvud-åtkomsttoken.
6. Key Vault kontrollerar om tjänstens huvud namn har tillräckliga åtkomst princip behörigheter för att utföra den begärda åtgärden, i det här exemplet blir åtgärden hemlig.
7. Key Vault tillhandahåller hemligheten till tjänstens huvud namn.

![BILD](../media/authentication-2.png)

## <a name="grant-a-service-principal-access-to-key-vault"></a>Bevilja tjänstens huvud namn åtkomst till Key Vault

1. Skapa ett huvud namn för tjänsten om du inte redan har ett. [Skapa ett huvud namn för tjänsten](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)
2. Lägg till en roll tilldelning till tjänstens huvud namn i Azure Key Vault IAM-inställningar. Du kan lägga till förtilldelade roller för ägare, deltagare eller läsare. Du kan också skapa anpassade roller för tjänstens huvud namn. Du bör följa huvud kontot för minsta behörighet och endast ge den minsta åtkomst som krävs för tjänstens huvud namn. 
3.  Konfigurera nyckel valvs brand väggen. Du kan hålla Key Vault-brandväggen inaktive rad och tillåta åtkomst från det offentliga Internet (mindre säker, enklare att konfigurera). Du kan också begränsa åtkomsten till vissa IP-intervall, tjänst slut punkter, virtuella nätverk eller privata slut punkter (säkrare).
4.  Lägg till en åtkomst princip för tjänstens huvud namn, det här är en lista över åtgärder som tjänstens huvud namn kan utföra i nyckel valvet. Du bör använda huvud kontot för lägst privilegium och begränsa de åtgärder som tjänstens huvud namn kan utföra. Men om du inte ger tillräcklig behörighet nekas tjänstens huvud namn åtkomst.

## <a name="tutorial"></a>Självstudier

I den här självstudien får du lära dig hur du konfigurerar ett huvud namn för tjänsten för att autentisera till nyckel valv och hämta en hemlighet. 

### <a name="part-1--create-a-service-principal-in-the-azure-portal"></a>Del 1: skapa ett huvud namn för tjänsten i Azure Portal

1. Logga in på Azure Portal
1. Sök efter Azure Active Directory
1. Klicka på fliken "app-registreringar"
1. Klicka på "+ ny registrering"
1. Skapa ett namn för tjänstens huvud namn
1. Välj register

Nu har du ett registrerat huvud namn för tjänsten. Du kan visa den genom att välja "app Registration". Tjänstens huvud namn tilldelas nu ett klient-ID-GUID, Tänk på detta som "username" för tjänstens huvud namn. Nu måste vi skapa ett "lösen ord" för tjänstens huvud namn. du kan använda en klient hemlighet eller ett klient certifikat. Obs! det är inte säkert att använda en klient hemlighet för autentisering och bör endast användas i test syfte. I den här självstudien visas hur du använder ett klient certifikat.

### <a name="part-2-create-a-client-certificate-for-your-service-principal"></a>Del 2: skapa ett klient certifikat för tjänstens huvud namn

1. Skapa ett certifikat

    * Alternativ 1: skapa ett certifikat med [openssl](https://www.openssl.org/) (endast för test syfte, Använd inte självsignerade certifikat i produktion)

    ```console
    openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365
    ```

    * Alternativ 2: skapa ett certifikat med Key Vault. [Skapa ett certifikat i Azure Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#creating-your-first-key-vault-certificate)

1. Hämta certifikatet i PEM-format
1. Logga in på Azure Portal och navigera till Azure Active Directory
1. Klicka på "app-registreringar"
1. Välj tjänstens huvud namn som du skapade i del 1.
1. Klicka på fliken "certifikat och hemligheter" i tjänstens huvud namn
1. Ladda upp certifikatet med hjälp av knappen "Ladda upp certifikat"

### <a name="part-3-configure-an-azure-key-vault"></a>Del 3: Konfigurera en Azure Key Vault

1. Skapa en Azure Key Vault [länk](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault)

2. Konfigurera Key Vault IAM-behörigheter
    1. Navigera till ditt nyckel valv
    1. Välj fliken "Access Control (IAM)"
    1. Klicka på Lägg till roll tilldelning
    1. Välj rollen deltagare i list rutan
    1. Ange namnet eller klient-ID: t för tjänstens huvud namn som du skapade
    1. Klicka på Visa roll tilldelningar för att bekräfta att tjänstens huvud namn visas

3. Konfigurera åtkomst princip behörigheter för Key Vault
    1. Navigera till ditt nyckel valv
    1. Välj fliken "åtkomst principer" under "Inställningar"
    1. Välj länken "+ Lägg till åtkomst princip"
    1. Under List rutan hemliga behörigheter kontrollerar du behörigheterna "Get" och "List".
    1. Välj tjänstens huvud namn efter namn eller klient-ID.
    1. Välj "Lägg till"
    1. Välj "Spara"

4. Skapa en hemlighet i ditt nyckel valv.
    1. Navigera till ditt nyckel valv
    1. Klicka på fliken "hemligheter" under Inställningar
    1. Klicka på "+ generera/importera"
    1. Skapa ett namn för hemligheten, i det här exemplet ska jag namnge hemligheten "test"
    1. Skapa ett värde för hemligheten. i det här exemplet ska jag ange värdet "password123"

När du nu kör kod från din lokala dator kan du autentisera till Key Vault genom att hämta en åtkomsttoken genom att presentera klient-ID: t och sökvägen till certifikatet.

### <a name="part-4-retrieve-the-secret-from-your-azure-key-vault-in-an-application-python"></a>Del 4: Hämta hemligheten från din Azure Key Vault i ett program (python)

Använd följande kod exempel för att testa om ditt program kan hämta en hemlighet från ditt nyckel valv med hjälp av tjänstens huvud namn som du har konfigurerat. 

```python
from azure.keyvault.secrets import SecretClient
from azure.identity import CertificateCredential


tenant_id = ""                                             ##ENTER AZURE TENANT ID
vault_url = "https://{VAULT NAME}.vault.azure.net/"        ##ENTER THE URL OF YOUR KEY VAULT
client_id = ""                                             ##ENTER CLIENT ID OF SERVICE PRINCIPAL
cert_path = r"C:\Users\{USERNAME}\{PATH}\{CERT_NAME}.pem"  ##ENTER PATH TO CERTIFICATE

def main():

    #AUTHENTICATION TO AAD USING CLIENT ID AND CLIENT CERTIFICATE
    token = CertificateCredential(tenant_id= tenant_id, client_id=client_id, certificate_path=cert_path)

    #AUTHENTICATION TO KEY VAULT PRESENTING AAD TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    secret = client.get_secret('{SECRET_NAME}')            ##ENTER NAME OF SECRET IN KEY VAULT

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

![BILD](../media/authentication-3.png)


## <a name="next-steps"></a>Efterföljande moment

1. Lär dig hur du felsöker Key Vault-autentiseringsfel. [Key Vault fel söknings guide](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes)
