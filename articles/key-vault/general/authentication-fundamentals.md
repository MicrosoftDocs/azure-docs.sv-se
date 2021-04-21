---
title: Azure Key Vault grundläggande om autentisering
description: Lär dig mer om hur autentiseringsmodellen i Key Vault fungerar
author: msmbaldwin
ms.author: mbaldwin
ms.date: 04/15/2021
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: f0cd2fb341dd790a7628cc3cf6a5bdd87d7c3687
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753424"
---
# <a name="key-vault-authentication-fundamentals"></a>Grundläggande om Key Vault-autentisering

Azure Key Vault kan du på ett säkert sätt lagra och hantera programautentiseringsuppgifter som hemligheter, nycklar och certifikat på en central och säker molnlagringsplats. Key Vault eliminerar behovet av att lagra autentiseringsuppgifter i dina program. Dina program kan autentisera till Key Vault vid körning för att hämta autentiseringsuppgifter.

Som administratör kan du strikt kontrollera vilka användare och program som kan komma åt ditt nyckelvalv och du kan begränsa och granska de åtgärder som de utför. I det här dokumentet beskrivs de grundläggande begreppen i nyckelvalvsåtkomstmodellen. Den ger dig en introduktionsnivå av kunskap och visar hur du kan autentisera en användare eller ett program till nyckelvalvet från början till slut.

## <a name="required-knowledge"></a>Nödvändig kunskap

Det här dokumentet förutsätter att du är bekant med följande begrepp. Om du inte är bekant med något av dessa begrepp följer du hjälplänkarna innan du fortsätter.

* Azure Active Directory [länk](../../active-directory/fundamentals/active-directory-whatis.md)
* Länk för [säkerhetsobjekt](./authentication.md#app-identity-and-security-principals)

## <a name="key-vault-configuration-steps-summary"></a>sammanfattning Key Vault konfigurationssteg

1. Registrera din användare eller ditt program i Azure Active Directory som säkerhetsobjekt.
1. Konfigurera en rolltilldelning för säkerhetsobjekt i Azure Active Directory.
1. Konfigurera åtkomstprinciper för nyckelvalvet för ditt säkerhetsobjekt.
1. Konfigurera Key Vault brandväggsåtkomst till ditt nyckelvalv (valfritt).
1. Testa säkerhetsobjekts förmåga att komma åt nyckelvalvet.

## <a name="register-a-user-or-application-in-azure-active-directory-as-a-security-principal"></a>Registrera en användare eller ett program Azure Active Directory som säkerhetsobjekt

När en användare eller ett program gör en begäran till nyckelvalvet måste begäran först autentiseras av Azure Active Directory. För att detta ska fungera måste användaren eller programmet registreras i Azure Active Directory som säkerhetsobjekt.

Följ dokumentationslänkarna nedan för att förstå hur du registrerar en användare eller ett program i Azure Active Directory.
**Se till att du skapar ett lösenord för användarregistrering och en klienthemlighet eller klientcertifikatsidentifiering för program.**

* Registrera en användare i Azure Active Directory [länk](../../active-directory/fundamentals/add-users-azure-active-directory.md)
* Registrera ett program i Azure Active Directory [länk](../../active-directory/develop/quickstart-register-app.md)

## <a name="assign-your-security-principal-a-role"></a>Tilldela säkerhetsobjekt en roll

Du kan använda rollbaserad åtkomstkontroll i Azure (Azure RBAC) för att tilldela behörigheter till säkerhetsobjekt. Dessa behörigheter kallas rolltilldelningar.

När det gäller nyckelvalv avgör dessa rolltilldelningar ett säkerhetsobjekts åtkomstnivå till hanteringsplanet (även kallat kontrollplan) för nyckelvalvet. Dessa rolltilldelningar ger inte direkt åtkomst till dataplanshemligheterna, men de ger åtkomst till att hantera egenskaper för nyckelvalvet. En användare eller ett  program som har tilldelats rollen Läsare kan till exempel inte göra  ändringar i brandväggsinställningarna för nyckelvalvet, medan en användare eller ett program som tilldelats rollen Deltagare kan göra ändringar. Ingen av rollerna har direkt åtkomst till att utföra åtgärder på hemligheter, nycklar och certifikat, till exempel att skapa eller hämta deras värde tills de har tilldelats åtkomst till nyckelvalvsdataplanet. Detta tas upp i nästa steg.

>[!IMPORTANT]
> Även om användare med rollen Deltagare eller Ägare inte har åtkomst till att utföra åtgärder på hemligheter som lagras i nyckelvalvet som standard, ger rollerna Deltagare och Ägare behörighet att lägga till eller ta bort åtkomstprinciper till hemligheter som lagras i nyckelvalvet. Därför kan en användare med dessa rolltilldelningar ge sig själva åtkomst till hemligheter i nyckelvalvet. Därför rekommenderar vi att endast administratörer har åtkomst till rollerna Deltagare eller Ägare. Användare och program som bara behöver hämta hemligheter från nyckelvalvet bör beviljas rollen Läsare. **Mer information finns i nästa avsnitt.**

>[!NOTE]
> När du tilldelar en rolltilldelning till en användare på Azure Active Directory-klientorganisationsnivå, kommer den här uppsättningen behörigheter att gå nedåt till alla prenumerationer, resursgrupper och resurser inom omfånget för tilldelningen. Om du vill följa huvudprincipen för minsta behörighet kan du göra den här rolltilldelningen till ett mer detaljerad omfång. Du kan till exempel tilldela en användare rollen Läsare på prenumerationsnivå och en ägarroll för ett enda nyckelvalv. Gå till inställningarna för Hantering av identitetsåtkomst (IAM) för en prenumeration, resursgrupp eller ett nyckelvalv för att göra en rolltilldelning med ett mer detaljerad omfång.

* Länk för mer information om [Azure-roller](../../role-based-access-control/built-in-roles.md)
* Mer information om hur du tilldelar eller tar bort [rolltilldelningar](../../role-based-access-control/role-assignments-portal.md)

## <a name="configure-key-vault-access-policies-for-your-security-principal"></a>Konfigurera åtkomstprinciper för nyckelvalvet för ditt säkerhetsobjekt

Innan du beviljar användare och program åtkomst till nyckelvalvet är det viktigt att förstå de olika typer av åtgärder som kan utföras på ett nyckelvalv. Det finns två huvudtyper för nyckelvalvsåtgärder, hanteringsplanåtgärder (kallas även kontrollplan) och dataplansåtgärder.

Den här tabellen visar flera exempel på de olika åtgärder som styrs av hanteringsplanet jämfört med dataplanet. Åtgärder som ändrar egenskaperna för nyckelvalvet är åtgärder på hanteringsplanet. Åtgärder som ändrar eller hämtar värdet för hemligheter som lagras i nyckelvalvet är dataplansåtgärder.

|Hanteringsplanåtgärder (exempel)|Dataplansåtgärder (exempel)|
| --- | --- |
| Skapa Key Vault | Skapa en nyckel, en hemlighet, ett certifikat
| Ta bort Key Vault | Ta bort en nyckel, en hemlighet, ett certifikat
| Lägga till eller ta Key Vault rolltilldelningar | Visa och hämta värden för nycklar, hemligheter, certifikat
| Lägga till eller Key Vault åtkomstprinciper | Säkerhetskopiering och återställning av nycklar, hemligheter, certifikat
| Ändra Key Vault brandväggsinställningar | Förnya nycklar, hemligheter, certifikat
| Ändra Key Vault återställningsinställningar | Rensa eller återställa mjukt borttagna nycklar, hemligheter, certifikat
| Ändra Key Vault diagnostikloggar

### <a name="management-plane-access--azure-active-directory-role-assignments"></a>Rolltilldelningar för & Azure Active Directory åtkomstplan

Azure Active Directory rolltilldelningar ger åtkomst till att utföra åtgärder på hanteringsplanet på ett nyckelvalv. Den här åtkomsten beviljas vanligtvis till användare, inte till program. Du kan begränsa vilka hanteringsplanåtgärder en användare kan utföra genom att ändra en användares rolltilldelning.

Om du till exempel tilldelar en användare en Key Vault-läsarroll till en användare kan de se egenskaperna för ditt nyckelvalv, till exempel åtkomstprinciper, men de kan inte göra några ändringar. Genom att tilldela en användare får en ägarroll fullständig åtkomst till att ändra inställningarna för nyckelvalvshanteringsplanet.

Rolltilldelningar styrs i bladet key vault Access Control (IAM). Om du vill att en viss användare ska ha åtkomst till att vara läsare eller administratör för flera nyckelvalvsresurser kan du skapa en rolltilldelning på valv-, resursgrupps- eller prenumerationsnivå, så läggs rolltilldelningen till i alla resurser inom omfånget för tilldelningen.

Åtkomst till dataplanet eller åtkomst för att utföra åtgärder på nycklar, hemligheter och certifikat som lagras i nyckelvalvet kan läggas till på ett av två sätt.

### <a name="data-plane-access-option-1-classic-key-vault-access-policies"></a>Dataplansåtkomst alternativ 1: Klassiska Key Vault åtkomstprinciper

Åtkomstprinciper för nyckelvalv ger användare och program åtkomst att utföra dataplansåtgärder på ett nyckelvalv.

> [!NOTE]
> Den här åtkomstmodellen är inte kompatibel med Azure RBAC för nyckelvalv (alternativ 2) som dokumenteras nedan. Du måste välja en. Du kan göra det här valet när du klickar på fliken Åtkomstprincip i nyckelvalvet.

Klassiska åtkomstprinciper är detaljerade, vilket innebär att du kan tillåta eller neka möjligheten för varje enskild användare eller program att utföra enskilda åtgärder i ett nyckelvalv. Några exempel:

* Säkerhetsobjekt 1 kan utföra valfri nyckelåtgärd, men får inte utföra några hemlighets- eller certifikatåtgärd.
* Säkerhetsobjekt 2 kan lista och läsa alla nycklar, hemligheter och certifikat, men kan inte utföra några åtgärder för att skapa, ta bort eller förnya.
* Säkerhetsobjekt 3 kan säkerhetskopiera och återställa alla hemligheter men kan inte läsa värdet för hemligheterna själva.

Klassiska åtkomstprinciper tillåter dock inte behörigheter på objektnivå, och tilldelade behörigheter tillämpas på omfånget för ett enskilt nyckelvalv. Om du till exempel ger åtkomstprincipen "Secret Get" behörighet till ett säkerhetsobjekt i ett visst nyckelvalv kan säkerhetsobjekt få alla hemligheter i det specifika nyckelvalvet. Den här behörigheten "Hämta hemlighet" utökas dock inte automatiskt till andra nyckelvalv och måste tilldelas explicit.

> [!IMPORTANT]
> Åtkomstprinciper och rolltilldelningar för det Azure Active Directory nyckelvalvet är oberoende av varandra. Om du tilldelar säkerhetsobjekt rollen Deltagare på prenumerationsnivå kan säkerhetsobjekt inte automatiskt utföra dataplansåtgärder på varje nyckelvalv inom prenumerationens omfång. Säkerhetsobjekt måste fortfarande beviljas eller ge sig själva behörighet för åtkomstprinciper för att utföra dataplansåtgärder.

### <a name="data-plane-access-option-2--azure-rbac-for-key-vault"></a>Alternativ 2 för dataplansåtkomst: Azure RBAC för Key Vault

Ett nytt sätt att bevilja åtkomst till nyckelvalvsdataplanet är via rollbaserad åtkomstkontroll i Azure (Azure RBAC) för nyckelvalvet.

> [!NOTE]
> Den här åtkomstmodellen är inte kompatibel med de klassiska åtkomstprinciper för Key Vault som visas ovan. Du måste välja en. Du har möjlighet att göra det här valet när du klickar på fliken Åtkomstprincip i nyckelvalvet.

Key Vault rolltilldelningar är en uppsättning inbyggda Azure-rolltilldelningar som omfattar gemensamma uppsättningar med behörigheter som används för åtkomst till nycklar, hemligheter och certifikat. Den här behörighetsmodellen möjliggör även ytterligare funktioner som inte är tillgängliga i den klassiska principmodellen för nyckelvalvsåtkomst.

* Azure RBAC-behörigheter kan hanteras i stor skala genom att tillåta att användarna tilldelas dessa roller på prenumerations-, resursgrupps- eller enskild nyckelvalvsnivå. En användare har dataplansbehörigheter till alla nyckelvalv inom omfånget för Azure RBAC-tilldelningen. Detta eliminerar behovet av att tilldela behörigheter för enskilda åtkomstprinciper per användare/program per nyckelvalv.

* Azure RBAC-behörigheter är kompatibla med Privileged Identity Management eller PIM. På så sätt kan du konfigurera just-in-time-åtkomstkontroller för privilegierade roller som Key Vault Administratör. Det här är en bästa säkerhetspraxis och följer huvudprincipen för minsta behörighet genom att eliminera stående åtkomst till dina nyckelvalv.

Mer information om Azure RBAC för Key Vault finns i följande dokument:

* Azure RBAC for [Key Vault länk](rbac-guide.md)
* Länk till Azure RBAC Key Vault [roller](../../role-based-access-control/built-in-roles.md#key-vault-administrator)

## <a name="configure-key-vault-firewall"></a>Konfigurera Key Vault Brandvägg

Som standard tillåter nyckelvalvet trafik från det offentliga Internet att skicka nå ditt nyckelvalv via en offentlig slutpunkt. Om du vill ha ytterligare ett säkerhetslager kan du konfigurera Azure Key Vault Firewall för att begränsa åtkomsten till den offentliga slutpunkten för nyckelvalvet.

Om du vill aktivera Key Vault-brandväggen klickar du på fliken Nätverk i nyckelvalvsportalen och väljer alternativknappen Tillåt åtkomst från: "Privat slutpunkt och valda nätverk". Om du väljer att aktivera nyckelvalvsbrandväggen kan du använda dessa metoder för att tillåta trafik genom Key Vault-brandväggen.

* Lägg till IPv4-adresser i listan över tillåtna Key Vault-brandväggar. Det här alternativet fungerar bäst för program som har statiska IP-adresser.

* Lägg till ett virtuellt nätverk i Key Vault-brandväggen. Det här alternativet fungerar bäst för Azure-resurser som har dynamiska IP-adresser, till exempel Virtual Machines. Du kan lägga till Azure-resurser i ett virtuellt nätverk och lägga till det virtuella nätverket i listan över tillåtna Key Vault-brandväggar. Det här alternativet använder en tjänstslutpunkt som är en privat IP-adress i det virtuella nätverket. Detta ger ett extra skyddslager så att ingen trafik mellan nyckelvalvet och ditt virtuella nätverk dirigeras via det offentliga Internet. Mer information om tjänstslutpunkter finns i följande dokumentation. [Länk](./network-security.md)

* Lägg till en privat länkanslutning till nyckelvalvet. Det här alternativet ansluter ditt virtuella nätverk direkt till en viss instans av nyckelvalvet, vilket effektivt tar nyckelvalvet in i det virtuella nätverket. Mer information om hur du konfigurerar en privat slutpunktsanslutning till nyckelvalv finns i följande [länk](./private-link-service.md)

## <a name="test-your-service-principals-ability-to-access-key-vault"></a>Testa tjänstens huvudnamns möjlighet att komma åt nyckelvalvet

När du har följt alla steg ovan kan du ange och hämta hemligheter från nyckelvalvet.

### <a name="authentication-process-for-users-examples"></a>Autentiseringsprocess för användare (exempel)

* Användare kan logga in på Azure Portal använda nyckelvalvet. [Key Vault portal snabbstart](./quick-create-portal.md)

* Användaren kan använda Azure CLI för att använda nyckelvalvet. [Key Vault Snabbstart för Azure CLI](./quick-create-cli.md)

* Användaren kan använda Azure PowerShell för att använda nyckelvalvet. [Key Vault Azure PowerShell snabbstart](./quick-create-powershell.md)

### <a name="azure-active-directory-authentication-process-for-applications-or-services-examples"></a>Azure Active Directory för program eller tjänster (exempel)

* Ett program tillhandahåller en klienthemlighet och ett klient-ID i en funktion för att hämta Azure Active Directory token. 

* Ett program tillhandahåller ett certifikat för att hämta en Azure Active Directory token. 

* En Azure-resurs använder MSI-autentisering för att hämta Azure Active Directory token. 

* Läs mer om [](../../active-directory/managed-identities-azure-resources/overview.md) MSI-autentiseringslänk

### <a name="authentication-process-for-application-python-example"></a>Autentiseringsprocess för program (Python-exempel)

Använd följande kodexempel för att testa om programmet kan hämta en hemlighet från nyckelvalvet med hjälp av tjänstens huvudnamn som du konfigurerade.

>[!NOTE]
>Det här exemplet är endast för demonstrations- och teständamål. **ANVÄND INTE AUTENTISERING MED KLIENTHEMLIGHET I PRODUKTION** Det här är inte en säker designmetod. Du bör använda klientcertifikat eller MSI-autentisering som bästa praxis.

```python
from azure.identity import ClientSecretCredential
from azure.keyvault.secrets import SecretClient

tenant_id = "{ENTER YOUR TENANT ID HERE}"                          ##ENTER AZURE TENANT ID
vault_url = "https://{ENTER YOUR VAULT NAME}.vault.azure.net/"     ##ENTER THE URL OF YOUR KEY VAULT
client_id = "{ENTER YOUR CLIENT ID HERE}"                          ##ENTER THE CLIENT ID OF YOUR SERVICE PRINCIPAL
cert_path = "{ENTER YOUR CLIENT SECRET HERE}"                      ##ENTER THE CLIENT SECRET OF YOUR SERVICE PRINCIPAL

def main():

    #AUTHENTICATION TO Azure Active Directory USING CLIENT ID AND CLIENT CERTIFICATE (GET Azure Active Directory TOKEN)
    token = ClientSecretCredential(tenant_id=tenant_id, client_id=client_id, client_secret=client_secret)

    #AUTHENTICATION TO KEY VAULT PRESENTING Azure Active Directory TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    #ENTER NAME OF A SECRET STORED IN KEY VAULT
    secret = client.get_secret('{SECRET_NAME}')

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

## <a name="next-steps"></a>Nästa steg

Mer information om nyckelvalvsautentisering finns i följande dokument. [Autentisering av Key Vault](./authentication.md)
