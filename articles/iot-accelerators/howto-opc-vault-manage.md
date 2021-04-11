---
title: Så här hanterar du OPC Vault Certificate service – Azure | Microsoft Docs
description: Hantera OPC-valvets rot certifikat och användar behörigheter.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 0829d4b3fca068ddb0db2df53dd635ab7ad80bed
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646371"
---
# <a name="manage-the-opc-vault-certificate-service"></a>Hantera certifikat tjänsten för OPC-valvet

> [!IMPORTANT]
> Medan vi uppdaterar den här artikeln kan du läsa mer i [Azures industriella IoT](https://azure.github.io/Industrial-IoT/) .

I den här artikeln förklaras de administrativa uppgifterna för certifikat hanterings tjänsten för OPC Vault i Azure. Den innehåller information om hur du förnyar certifikat från certifikat utfärdare, hur du förnyar listan över återkallade certifikat (CRL) och hur du beviljar och återkallar användar åtkomst.

## <a name="create-or-renew-the-root-ca-certificate"></a>Skapa eller förnya rot certifikat utfärdarens certifikat

När du har distribuerat OPC-valvet måste du skapa rot certifikat utfärdarens certifikat. Utan ett giltigt CA-certifikat kan du inte signera eller utfärda program certifikat. Se [certifikat](howto-opc-vault-secure-ca.md#certificates) för att hantera dina certifikat med rimliga, säkra livs längder. Förnya certifikat utfärdarens CA-certifikat efter halva livs längden. När du förnyar bör du även tänka på att den konfigurerade livs längden för ett nytt signerat program certifikat inte överskrider livs längden för utfärdarens CA-certifikat.
> [!IMPORTANT]
> Administratörs rollen krävs för att skapa eller förnya utfärdarens CA-certifikat.

1. Öppna certifikat tjänsten på `https://myResourceGroup-app.azurewebsites.net` och logga in.
2. Gå till **certifikat grupper**.
3. Det finns en lista över standard certifikat. Välj **Redigera**.
4. I **Redigera certifikat grupps information** kan du ändra ämnes namn och livs längd för certifikat utfärdaren och program certifikaten. Ämnet och livs längden bör endast anges en gång innan det första CA-certifikatet utfärdas. Livs längds ändringar under åtgärder kan resultera i inkonsekventa livs längder för utfärdade certifikat och CRL: er.
5. Ange ett giltigt ämne (till exempel `CN=My CA Root, O=MyCompany, OU=MyDepartment` ).<br>
   > [!IMPORTANT]
   > Om du ändrar ämnet måste du förnya utfärdarens certifikat, annars kommer tjänsten inte att kunna signera program certifikat. Syftet med konfigurationen kontrol leras mot ämnet för det aktiva utfärdarcertifikatet. Om ämnena inte matchar, nekas certifikat signering.
6. Välj **Spara**.
7. Om du stöter på ett "förbud"-fel nu har dina användarautentiseringsuppgifter inte administratörs behörighet för att ändra eller skapa ett nytt rot certifikat. Som standard har den användare som distribuerade tjänsten administratörs-och signerings roller med tjänsten. Andra användare måste läggas till i rollen god kännare, skrivare eller administratör, efter vad som är lämpligt i program registreringen i Azure Active Directory (Azure AD).
8. Välj **information**. Den uppdaterade informationen bör visas.
9. Välj **förnya CA-certifikat** för att utfärda det första utfärdarens CA-certifikat, eller för att förnya utfärdarens certifikat. Välj sedan **OK**.
10. Efter några sekunder visas **certifikat information**. Om du vill hämta det senaste CA-certifikatet och CRL för distribution till dina OPC UA-program väljer du **utfärdare** eller **CRL**.

Nu är OPC UA Certificate Management-tjänsten redo att utfärda certifikat för OPC UA-program.

## <a name="renew-the-crl"></a>Förnya CRL

Förnyelse av CRL är en uppdatering som bör distribueras till program med jämna mellanrum. OPC UA-enheter, som har stöd för X509-tillägget för CRL-distributions platsen, kan uppdatera CRL: en direkt från mikrotjänstens slut punkt. Andra OPC UA-enheter kan behöva manuella uppdateringar eller uppdateras med hjälp av GDS-serverns push-tillägg (*) för att uppdatera listan över betrodda certifikat med certifikaten och listorna över återkallade certifikat.

I följande arbets flöde återkallas alla certifikat begär anden i de borttagna tillstånden i listor över återkallade certifikat, som motsvarar certifikat utfärdarens CA-certifikat för vilka de har utfärdats. Versions numret för CRL: en ökas med 1. <br>
> [!NOTE]
> Alla utfärdade CRL: er är giltiga tills certifikat UTFÄRDARens certifikat upphör att gälla. Detta beror på att OPC UA-specifikationen inte kräver en obligatorisk, deterministisk distributions modell för CRL.

> [!IMPORTANT]
> Administratörs rollen krävs för att förnya utfärdarens CRL.

1. Öppna certifikat tjänsten på `https://myResourceGroup.azurewebsites.net` och logga in.
2. Gå till sidan **certifikat grupper** .
3. Välj **information**. Detta bör Visa aktuellt certifikat och CRL-information.
4. Välj Uppdatera lista över återkallade **certifikat (CRL)** för att utfärda en uppdaterad lista över återkallade certifikat för alla aktiva UTFÄRDARCERTIFIKAT i OPC Vault-lagringen.
5. Efter några sekunder visas **certifikat information**. Om du vill hämta det senaste CA-certifikatet och CRL för distribution till dina OPC UA-program väljer du **utfärdare** eller **CRL**.

## <a name="manage-user-roles"></a>Hantera användarroller

Du hanterar användar roller för mikrotjänsten OPC Vault i Azure AD Enterprise-programmet. En detaljerad beskrivning av roll definitionerna finns i [roller](howto-opc-vault-secure-ca.md#roles).

Som standard kan en autentiserad användare i klienten logga in i tjänsten som en läsare. Högre privilegierade roller kräver manuell hantering i Azure Portal eller med hjälp av PowerShell.

### <a name="add-user"></a>Lägg till användare

1. Öppna Azure-portalen.
2. Gå till **Azure Active Directory**  >  **företags program**.
3. Välj registreringen av OPC Vault mikrotjänst (som standard `resourceGroupName-service` ).
4. Gå till **användare och grupper**.
5. Välj **Lägg till användare**.
6. Välj eller Bjud in användaren för tilldelning till en speciell roll.
7. Välj rollen för användarna.
8. Välj **Tilldela**.
9. För användare i rollen administratör eller god kännare, Fortsätt att lägga till Azure Key Vault åtkomst principer.

### <a name="remove-user"></a>Ta bort användare

1. Öppna Azure-portalen.
2. Gå till **Azure Active Directory**  >  **företags program**.
3. Välj registreringen av OPC Vault mikrotjänst (som standard `resourceGroupName-service` ).
4. Gå till **användare och grupper**.
5. Välj en användare med en roll som ska tas bort och välj sedan **ta bort**.
6. För borttagna användare i rollen administratör eller god kännare tar du även bort dem från Azure Key Vault-principer.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Lägg till princip för användar åtkomst i Azure Key Vault

Ytterligare åtkomst principer krävs för god kännare och administratörer.

Som standard har tjänst identiteten endast begränsade behörigheter att komma åt Key Vault, för att förhindra utökade åtgärder eller ändringar som ska utföras utan personifiering av användare. De grundläggande tjänst behörigheterna är get och list, för både hemligheter och certifikat. För hemligheter finns det bara ett undantag: tjänsten kan ta bort en privat nyckel från den hemliga lagrings platsen när den har godkänts av en användare. Alla andra åtgärder kräver personifierade behörigheter för användaren.

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>För en god kännare-roll måste följande behörigheter läggas till i Key Vault

1. Öppna Azure-portalen.
2. Gå till OPC-valvet `resourceGroupName` som används under distributionen.
3. Gå till Key Vault `resourceGroupName-xxxxx` .
4. Gå till **åtkomst principer**.
5. Välj **Lägg till ny**.
6. Hoppa över mallen. Det finns ingen mall som matchar kraven.
7. Välj **Välj huvud** konto och välj den användare som ska läggas till eller bjuda in en ny användare till klienten.
8. Välj följande **nyckel behörigheter**: **Get**, **list** och **Sign**.
9. Välj följande **hemliga behörigheter**: **Get**, **list**, **set** och **Delete**.
10. Välj följande **certifikat behörigheter**: **Get** och **list**.
11. Välj **OK** och välj **Spara**.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>För en administratörs roll måste följande behörigheter läggas till i Key Vault

1. Öppna Azure-portalen.
2. Gå till OPC-valvet `resourceGroupName` som används under distributionen.
3. Gå till Key Vault `resourceGroupName-xxxxx` .
4. Gå till **åtkomst principer**.
5. Välj **Lägg till ny**.
6. Hoppa över mallen. Det finns ingen mall som matchar kraven.
7. Välj **Välj huvud** konto och välj den användare som ska läggas till eller bjuda in en ny användare till klienten.
8. Välj följande **nyckel behörigheter**: **Get**, **list** och **Sign**.
9. Välj följande **hemliga behörigheter**: **Get**, **list**, **set** och **Delete**.
10. Välj följande **certifikat behörigheter**: **Hämta**, **lista**, **Uppdatera**, **skapa** och **Importera**.
11. Välj **OK** och välj **Spara**.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Ta bort princip för användar åtkomst från Azure Key Vault

1. Öppna Azure-portalen.
2. Gå till OPC-valvet `resourceGroupName` som används under distributionen.
3. Gå till Key Vault `resourceGroupName-xxxxx` .
4. Gå till **åtkomst principer**.
5. Hitta användaren som du vill ta bort och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar certifikat och användare i OPC-valvet kan du:

> [!div class="nextstepaction"]
> [Säker kommunikation av OPC-enheter](howto-opc-vault-secure.md)
