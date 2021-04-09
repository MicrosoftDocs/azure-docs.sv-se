---
title: Azure Key Vault grundläggande autentisering
description: Lär dig mer om hur nyckel valvets autentiseringspaket fungerar
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 09/25/2020
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 25f00024fb7371fd08bf6c4ceec3177cfaca029b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103572816"
---
# <a name="key-vault-authentication-fundamentals"></a>Grundläggande om Key Vault-autentisering

Med Azure Key Vault kan du lagra och hantera autentiseringsuppgifter på ett säkert sätt, till exempel hemligheter, nycklar och certifikat i en central och säker moln lagrings plats. Key Vault eliminerar behovet av att lagra autentiseringsuppgifter i dina program. Dina program kan autentiseras för Key Vault vid körning för att hämta autentiseringsuppgifter.

Som administratör kan du noggrant styra vilka användare och program som har åtkomst till ditt nyckel valv och du kan begränsa och granska de åtgärder de utför. Det här dokumentet beskriver de grundläggande begreppen i åtkomst modellen för Key Vault. Det ger dig en introduktion till kunskaps nivån och visar hur du kan autentisera en användare eller ett program till nyckel valvet från början till slut.

## <a name="required-knowledge"></a>Nödvändig kunskap

Det här dokumentet förutsätter att du är bekant med följande begrepp. Om du inte är bekant med någon av dessa begrepp följer du hjälp länkarna innan du fortsätter.

* Azure Active Directory [länk](../../active-directory/fundamentals/active-directory-whatis.md)
* [Länk](./authentication.md#app-identity-and-security-principals) till säkerhets objekt

## <a name="key-vault-configuration-steps-summary"></a>Sammanfattning av Key Vault konfigurations steg

1. Registrera din användare eller ditt program i Azure Active Directory som säkerhets objekt.
1. Konfigurera en roll tilldelning för säkerhets objekt i Azure Active Directory.
1. Konfigurera åtkomst principer för nyckel valv för ditt säkerhets objekt.
1. Konfigurera Key Vault brand Väggs åtkomst till ditt nyckel valv (valfritt).
1. Testa säkerhets objektets förmåga att komma åt Key Vault.

## <a name="register-a-user-or-application-in-azure-active-directory-as-a-security-principal"></a>Registrera en användare eller ett program i Azure Active Directory som säkerhets objekt

När en användare eller ett program gör en begäran till nyckel valvet måste begäran först autentiseras av Azure Active Directory. För att detta ska fungera måste användaren eller programmet registreras i Azure Active Directory som säkerhets objekt.

Följ dokumentations länkarna nedan för att förstå hur du registrerar en användare eller ett program i Azure Active Directory.
**Se till att du skapar ett lösen ord för användar registrering och en klient hemlighet eller autentiseringsuppgifter för klient certifikat för program.**

* Registrera en användare i Azure Active Directory [länk](../../active-directory/fundamentals/add-users-azure-active-directory.md)
* Registrera ett program i Azure Active Directory- [länk](../../active-directory/develop/quickstart-register-app.md)

## <a name="assign-your-security-principal-a-role"></a>Tilldela säkerhets objekt en roll

Du kan använda rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att tilldela behörigheter till säkerhets objekt. Dessa behörigheter kallas roll tilldelningar.

I samband med Key Vault bestämmer dessa roll tilldelningar säkerhets objektets åtkomst nivå till hanterings planet (även kallat kontroll plan) för nyckel valvet. Dessa roll tilldelningar ger inte åtkomst till data planet hemligheter direkt, men de ger åtkomst till att hantera egenskaper för nyckel valvet. Till exempel är en användare eller ett program som har tilldelats en **läsar roll** inte tillåten att göra ändringar i inställningarna för brand vägg för nyckel valv, medan en användare eller ett program som har tilldelats en **deltagar roll** kan göra ändringar. Ingen av rollerna har direkt åtkomst för att utföra åtgärder på hemligheter, nycklar och certifikat, till exempel att skapa eller hämta sitt värde tills de tilldelas åtkomst till nyckel valvets data plan. Detta beskrivs i nästa steg.

>[!IMPORTANT]
> Även om användare med rollen deltagare eller ägare inte har åtkomst för att utföra åtgärder på hemligheter som lagras i Key Vault som standard, ger rollen deltagare och ägare behörighet att lägga till eller ta bort åtkomst principer till hemligheter som lagras i Key Vault. Därför kan en användare med dessa roll tilldelningar ge sig själva åtkomst till hemligheter i nyckel valvet. Därför rekommenderar vi att endast administratörer har åtkomst till deltagar-eller ägar rollerna. Användare och program som bara behöver hämta hemligheter från Key Vault ska beviljas rollen läsare. **Mer information finns i nästa avsnitt.**

>[!NOTE]
> När du tilldelar en roll tilldelning till en användare på den Azure Active Directory klient nivån, kommer den här uppsättningen behörigheter att trickle ned till alla prenumerationer, resurs grupper och resurser inom tilldelningens omfattning. Om du vill följa huvud kontot för lägsta behörighet kan du göra den här roll tilldelningen till ett mer detaljerat omfång. Du kan till exempel tilldela en användare en läsar roll på prenumerations nivå och en ägar roll för ett enda nyckel valv. Gå till inställningarna för identitets åtkomst hantering (IAM) för en prenumeration, resurs grupp eller nyckel valv för att skapa en roll tilldelning i en mer detaljerad omfattning.

* Lär dig mer om [länken](../../role-based-access-control/built-in-roles.md) för Azure roles
* Lär dig mer om att tilldela eller ta bort roll tilldelnings [länken](../../role-based-access-control/role-assignments-portal.md)

## <a name="configure-key-vault-access-policies-for-your-security-principal"></a>Konfigurera åtkomst principer för nyckel valv för ditt säkerhets objekt

Innan du beviljar åtkomst för dina användare och program för att komma åt Key Vault är det viktigt att förstå de olika typerna av åtgärder som kan utföras i ett nyckel valv. Det finns två huvudsakliga typer av nyckel valvs åtgärder, hanterings plan (kallas även kontroll plan) åtgärder och data Plans åtgärder.

I den här tabellen visas flera exempel på de olika åtgärder som styrs av hanterings planet jämfört med data planet. Åtgärder som ändrar egenskaperna för nyckel valvet är hanterings Plans åtgärder. Åtgärder som ändrar eller hämtar värdet för hemligheter lagrade i Key Vault är data Plans åtgärder.

|Hanterings Plans åtgärder (exempel)|Data Plans åtgärder (exempel)|
| --- | --- |
| Skapa Key Vault | Skapa en nyckel, ett hemligt certifikat
| Ta bort Key Vault | Ta bort en nyckel, ett hemligt certifikat
| Lägga till eller ta bort Key Vault roll tilldelningar | Lista och hämta värden för nycklar, hemligheter, certifikat
| Lägga till eller ta bort Key Vault åtkomst principer | Säkerhets kopiering och återställning av nycklar, hemligheter, certifikat
| Ändra Key Vault brand Väggs inställningar | Förnya nycklar, hemligheter, certifikat
| Ändra Key Vault återställnings inställningar | Rensa eller återställa mjuka borttagna nycklar, hemligheter, certifikat
| Ändra inställningar för Key Vault diagnostikloggar

### <a name="management-plane-access--azure-active-directory-role-assignments"></a>Hanterings plan åtkomst & Azure Active Directory roll tilldelningar

Azure Active Directory roll tilldelningar beviljar åtkomst för att utföra hanterings plan åtgärder på ett nyckel valv. Den här åtkomsten beviljas vanligt vis till användare, inte för program. Du kan begränsa vilka hanterings Plans åtgärder en användare kan utföra genom att ändra en användares roll tilldelning.

Om du till exempel tilldelar en användare en Key Vault läsar roll till en användare kan de se egenskaperna för nyckel valvet, till exempel åtkomst principer, men de kan inte göra några ändringar. Genom att tilldela en användare får du en ägar roll fullständig åtkomst till inställningar för hanterings plan för nyckel valv.

Roll tilldelningar kontrol leras på bladet Key Vault Access Control (IAM). Om du vill att en viss användare ska ha åtkomst till en läsare eller som administratör för flera Key Vault-resurser, kan du skapa en roll tilldelning på valvet, resurs gruppen eller prenumerations nivån och roll tilldelningen läggs till i alla resurser inom tilldelningens omfattning.

Åtkomst till data planet eller åtkomst för att utföra åtgärder på nycklar, hemligheter och certifikat som lagras i Key Vault kan läggas till på ett av två sätt.

### <a name="data-plane-access-option-1-classic-key-vault-access-policies"></a>Åtkomst alternativ för data planet 1: principer för klassisk Key Vault åtkomst

Åtkomst principer för nyckel valv ger användare och program åtkomst för att utföra data Plans åtgärder i ett nyckel valv.

> [!NOTE]
> Den här åtkomst modellen är inte kompatibel med Azure RBAC för Key Vault (alternativ 2) som beskrivs nedan. Du måste välja ett. Du har möjlighet att välja det här alternativet när du klickar på fliken åtkomst princip i nyckel valvet.

Klassiska åtkomst principer är detaljerade, vilket innebär att du kan tillåta eller neka varje enskild användares eller Programs möjlighet att utföra enskilda åtgärder i ett nyckel valv. Några exempel:

* Säkerhets objekt 1 kan utföra alla nyckel åtgärder, men får inte utföra någon hemlig eller certifikat åtgärd.
* Säkerhets objekt 2 kan visa och läsa alla nycklar, hemligheter och certifikat, men kan inte utföra några åtgärder för att skapa, ta bort eller förnya.
* Säkerhets objekt 3 kan säkerhetskopiera och återställa alla hemligheter, men kan inte läsa själva värdet för själva hemligheterna.

De klassiska åtkomst principerna tillåter dock inte behörigheter per objekt nivå och tilldelade behörigheter tillämpas på omfånget för ett enskilt nyckel valv. Om du till exempel beviljar åtkomst principen "hemligt get" till ett säkerhets objekt i ett särskilt nyckel valv, har säkerhetsobjektet möjlighet att hämta alla hemligheter i det specifika nyckel valvet. Detta "Get Secret"-behörighet kommer dock inte att utökas automatiskt till andra nyckel valv och måste tilldelas uttryckligen.

> [!IMPORTANT]
> Klassiska åtkomst principer för nyckel valv och Azure Active Directory roll tilldelningar är oberoende av varandra. Genom att tilldela ett säkerhets objekt rollen "deltagare" på en prenumerations nivå tillåts inte automatiskt säkerhets objektets möjlighet att utföra data Plans åtgärder på varje nyckel valv inom prenumerationens omfattning. Säkerhets objekt måste fortfarande beviljas eller ge sig själva åtkomst princip behörighet för att utföra data Plans åtgärder.

### <a name="data-plane-access-option-2--azure-rbac-for-key-vault-preview"></a>Åtkomst alternativ för data planet 2: Azure RBAC för Key Vault (för hands version)

Ett nytt sätt att bevilja åtkomst till Key Vault-dataplanen är via rollbaserad åtkomst kontroll i Azure (Azure RBAC) för Key Vault.

> [!NOTE]
> Den här åtkomst modellen är inte kompatibel med klassiska åtkomst principer för Key Vault som visas ovan. Du måste välja ett. Du har möjlighet att välja det här alternativet när du klickar på fliken åtkomst princip i nyckel valvet.

Key Vault roll tilldelningar är en uppsättning inbyggda roll tilldelningar i Azure som omfattar vanliga uppsättningar behörigheter som används för att komma åt nycklar, hemligheter och certifikat. Den här behörighets modellen aktiverar också ytterligare funktioner som inte är tillgängliga i den klassiska åtkomst princip modellen för nyckel valv.

* Azure RBAC-behörigheter kan hanteras i stor skala genom att låta användare ha de här rollerna tilldelade till en prenumeration, resurs grupp eller enskild nyckel valv nivå. En användare får data planet behörighet till alla nyckel valv inom ramen för Azure RBAC-tilldelningen. Detta eliminerar behovet av att tilldela enskilda åtkomst princip behörigheter per användare/program per nyckel valv.

* Azure RBAC-behörigheter är kompatibla med Privileged Identity Management eller PIM. På så sätt kan du konfigurera just-in-Time-åtkomst-kontroller för privilegierade roller som Key Vault administratör. Detta är en bästa säkerhets rutin och följer huvud kontot för minsta behörighet genom att ta bort den ständiga åtkomsten till dina nyckel valv.

Mer information om Azure RBAC för Key Vault finns i följande dokument:

* Azure RBAC för Key Vault [länk](./secure-your-key-vault.md#management-plane-and-azure-rbac)
* [Länk](../../role-based-access-control/built-in-roles.md#key-vault-administrator) till Azure RBAC för Key Vault roller

## <a name="configure-key-vault-firewall"></a>Konfigurera Key Vault-brandvägg

Som standard tillåter Key Vault trafik från det offentliga Internet att skicka når ditt nyckel valv via en offentlig slut punkt. För ett extra säkerhets lager kan du konfigurera Azure Key Vault brand väggen för att begränsa åtkomsten till den offentliga slut punkten för Key Vault.

Aktivera nyckel valvs brand vägg genom att klicka på fliken nätverk i Key Vault-portalen och välj alternativ knappen för att tillåta åtkomst från: "privat slut punkt och valda nätverk". Om du väljer att aktivera nyckel valvets brand vägg, är det här de sätt som du kan tillåta trafik via nyckel valvs brand väggen.

* Lägg till IPv4-adresser i listan över tillåtna brand väggar för Key Vault. Det här alternativet fungerar bäst för program som har statiska IP-adresser.

* Lägg till ett virtuellt nätverk i Key Vault-brandväggen. Det här alternativet fungerar bäst för Azure-resurser som har dynamiska IP-adresser som Virtual Machines. Du kan lägga till Azure-resurser i ett virtuellt nätverk och lägga till det virtuella nätverket i listan över tillåtna brand Väggs tjänster för Key Vault. Det här alternativet använder en tjänst slut punkt som är en privat IP-adress i det virtuella nätverket. Detta ger ytterligare ett skydds lager så att ingen trafik mellan Key Vault och ditt virtuella nätverk dirigeras via det offentliga Internet. Läs mer om tjänst slut punkten i följande dokumentation. [Operationsföljdslänkkod](./network-security.md)

* Lägg till en privat länk anslutning till nyckel valvet. Med det här alternativet ansluts ditt virtuella nätverk direkt till en viss instans av nyckel valvet, vilket effektivt tar ditt nyckel valv i det virtuella nätverket. Mer information om hur du konfigurerar en privat slut punkts anslutning till Key Vault finns i följande [länk](./private-link-service.md)

## <a name="test-your-service-principals-ability-to-access-key-vault"></a>Testa tjänst huvud huvudets förmåga att komma åt Key Vault

När du har följt alla steg ovan kommer du att kunna ställa in och hämta hemligheter från nyckel valvet.

### <a name="authentication-process-for-users-examples"></a>Autentiseringsprocess för användare (exempel)

* Användare kan logga in på Azure Portal att använda Key Vault. [Snabb start för Key Vault Portal](./quick-create-portal.md)

* Användaren kan använda Azure CLI för att använda Key Vault. [Snabb start för Azure CLI Key Vault](./quick-create-cli.md)

* Användaren kan använda nyckel valvet Azure PowerShell. [Snabb start för Key Vault Azure PowerShell](./quick-create-powershell.md)

### <a name="azure-active-directory-authentication-process-for-applications-or-services-examples"></a>Azure Active Directory autentiseringsprocess för program eller tjänster (exempel)

* Ett program ger en klient hemlighet och ett klient-ID i en funktion för att hämta en Azure Active Directory-token. 

* Ett program ger ett certifikat för att hämta en Azure Active Directory-token. 

* En Azure-resurs använder MSI-autentisering för att hämta en Azure Active Directory-token. 

* Läs mer om [länken](../../active-directory/managed-identities-azure-resources/overview.md) för MSI-autentisering

### <a name="authentication-process-for-application-python-example"></a>Autentiseringsprocess för program (python-exempel)

Använd följande kod exempel för att testa om ditt program kan hämta en hemlighet från ditt nyckel valv med hjälp av tjänstens huvud namn som du har konfigurerat.

>[!NOTE]
>Det här exemplet är endast för demonstrations-och test syfte. **Använd inte autentisering av klient hemlighet i produktion** Detta är inte en säker design praxis. Du bör använda klient certifikat eller MSI-autentisering som bästa praxis.

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

Mer information om Key Vault-autentisering finns i följande dokument. [Autentisering av Key Vault](./authentication.md)
