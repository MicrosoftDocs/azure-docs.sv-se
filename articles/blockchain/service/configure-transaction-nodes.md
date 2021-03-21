---
title: Konfigurera noder i Azure blockchain service Transactions
description: Så här konfigurerar du Azure blockchain service Transaction Nodes
ms.date: 11/20/2019
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: 0b659fa9f15cd41e9e55de526747d470046cafa4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101091267"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Konfigurera noder i Azure blockchain service Transactions

Transaction Nodes används för att skicka blockchain-transaktioner till Azure blockchain-tjänsten via en offentlig slut punkt. Transaction-noden default innehåller den privata nyckeln för det Ethereum-konto som är registrerat på blockchain och därför inte kan tas bort.

Så här visar du information om standardnoden transaktioner:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Navigera till din Azure blockchain service-medlem. Välj **Transaction Nodes**.

    ![Välj noden standard transaktion](./media/configure-transaction-nodes/nodes.png)

    Översikts information inkluderar offentliga slut punkts adresser och offentlig nyckel.

## <a name="create-transaction-node"></a>Skapa Transaction-nod

Du kan lägga till upp till nio ytterligare transaktionsloggfiler till din blockchain-medlem, för totalt 10 transaktions noder. Genom att lägga till Transaction Nodes kan du öka skalbarheten eller distribuera belastningen. Du kan till exempel ha en Transaction Node-slutpunkt för olika klient program.

Så här lägger du till en Transaction-nod:

1. I Azure Portal navigerar du till din Azure blockchain service-medlem och väljer **Transaction nodes > Lägg till**.
1. Slutför inställningarna för den nya Transaction-noden.

    ![Lägg till Transaction-nod](./media/configure-transaction-nodes/add-node.png)

    | Inställning | Beskrivning |
    |---------|-------------|
    | Name | Nodnamn för transaktion. Namnet används för att skapa DNS-adressen för Transaction Node-slutpunkten. Till exempel `newnode-myblockchainmember.blockchain.azure.com`. Det går inte att ändra nodnamn när det har skapats. |
    | Lösenord | Ange ett starkt lösen ord. Använd lösen ordet för att komma åt Transaction Node-slutpunkten med grundläggande autentisering.

1. Välj **Skapa**.

    Det tar cirka 10 minuter att tillhandahålla en ny transaktions nod. Ytterligare transaktions noder debiteras för kostnader. Mer information om kostnader finns i [priser för Azure](https://aka.ms/ABSPricing).

## <a name="endpoints"></a>Slutpunkter

Transaction Nodes har ett unikt DNS-namn och offentliga slut punkter.

Så här visar du slut punkts information för en Transaction-nod:

1. I Azure Portal navigerar du till en av dina Azure blockchain service member Transaction-noder och väljer **Översikt**.

    ![Skärm dum par visar en översikt över Transaction Nodes för en blockchain-medlem.](./media/configure-transaction-nodes/endpoints.png)

Transaction Node-slutpunkter är säkra och kräver autentisering. Du kan ansluta till en transaktions slut punkt med Azure AD-autentisering, HTTPS Basic-autentisering och använda en åtkomst nyckel över HTTPS eller WebSocket över TLS.

### <a name="azure-active-directory-access-control"></a>Azure Active Directory åtkomst kontroll

Slutpunkter för Azure blockchain service Transaction-stöd Azure Active Directory (Azure AD)-autentisering. Du kan bevilja Azure AD-användare, grupp och tjänstens huvud namn åtkomst till din slut punkt.

Så här beviljar du Azure AD Access Control till din slut punkt:

1. I Azure Portal navigerar du till din Azure blockchain service-medlem och väljer **Transaction nodes > åtkomst kontroll (IAM) > Lägg till > Lägg till roll tilldelning**.
1. Skapa en ny roll tilldelning för en användare, grupp eller tjänstens huvud namn (program roller).

    ![Lägg till IAM-rollen](./media/configure-transaction-nodes/add-role.png)

    | Inställning | Action |
    |---------|-------------|
    | Roll | Välj **ägare**, **deltagare** eller **läsare**.
    | Tilldela åtkomst till | Välj **Azure AD-användare, grupp eller tjänstens huvud namn**.
    | Välj | Sök efter användaren, gruppen eller tjänstens huvud namn som du vill lägga till.

1. Välj **Spara** för att lägga till roll tilldelningen.

Mer information om Azure AD Access Control finns i [tilldela Azure-roller med hjälp av Azure Portal](../../role-based-access-control/role-assignments-portal.md)

Mer information om hur du ansluter med Azure AD-autentisering finns i [ansluta till din nod med AAD-autentisering](configure-aad.md).

### <a name="basic-authentication"></a>Grundläggande autentisering

För HTTPS grundläggande autentisering skickas användar namn och lösen ord som autentiseringsuppgifter i HTTPS-huvudet för begäran till slut punkten.

Du kan visa en nodens grundläggande autentiserings slut punkts information i Azure Portal. Navigera till en av dina Azure blockchain service member Transaction-noder och välj **grundläggande autentisering** i inställningar.

![Grundläggande autentisering](./media/configure-transaction-nodes/basic.png)

Användar namnet är namnet på noden och kan inte ändras.

Om du vill använda URL: en ersätter du \<password\> med lösen ordet som angavs när noden etablerades. Du kan uppdatera lösen ordet genom att välja **Återställ lösen ord**.

### <a name="access-keys"></a>Åtkomstnycklar

För åtkomst nyckel autentisering inkluderas åtkomst nyckeln i slut punkts-URL: en. När Transaction-noden är etablerad genereras två åtkomst nycklar. Antingen åtkomst nyckeln kan användas för autentisering. Med två nycklar kan du ändra och rotera nycklar.

Du kan visa information om åtkomst nyckeln för en Transaction Node och kopiera slut punkts adresser som innehåller åtkomst nycklarna. Navigera till en av dina Azure blockchain service member Transaction-noder och välj **åtkomst nycklar** i inställningar.

### <a name="firewall-rules"></a>Brandväggsregler

Brand Väggs regler gör att du kan begränsa de IP-adresser som kan försöka autentisera till din Transaction-nod.  Om inga brand Väggs regler har kon figurer ATS för din Transaction-nod, kan den inte användas av någon part.  

Om du vill visa en nods brand Väggs regler navigerar du till en av dina Azure blockchain service member Transaction-noder och väljer **brand Väggs regler** i inställningar.

Du kan lägga till brand Väggs regler genom att ange ett regel namn, en Start-IP-adress och en sista IP-adress i rutnätet för **brand Väggs regler** .

![Brandväggsregler](./media/configure-transaction-nodes/firewall-rules.png)

För att aktivera:

* **Enskild IP-adress:** Konfigurera samma IP-adress för start-och slut-IP-adresserna.
* **IP-adressintervall:** Konfigurera Start-och slut-IP-adressintervall. Ett intervall som börjar på 10.221.34.0 och slutar på 10.221.34.255 skulle till exempel aktivera hela 10.221.34.xxx-undernätet.
* **Tillåt alla IP-adresser:** Konfigurera den första IP-adressen till 0.0.0.0 och den sista IP-adressen till 255.255.255.255.

## <a name="connection-strings"></a>Anslutningssträngar

Syntaxen för anslutnings strängen för Transaction-noden anges för grundläggande autentisering eller med åtkomst nycklar. Anslutnings strängar som innehåller åtkomst nycklar över HTTPS och WebSockets tillhandahålls.

Du kan visa en Transaction Nodes anslutnings strängar och kopiera slut punkts adresser. Navigera till en av dina Azure blockchain service member Transaction-noder och välj **anslutnings strängar** i inställningar.

![Anslutningssträngar](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Exempelkod

Exempel kod ges för att snabbt kunna ansluta till Transaction-noden via Web3, Nethereum, Web3js och Truffle.

Du kan visa en Transaction Nodes exempel anslutnings kod och kopiera den till användning med populära utvecklarverktyg. Gå till en av dina Azure blockchain service member Transaction-noder och välj **exempel kod** i inställningar.

Välj fliken Web3, Nethereum, Truffle eller Web3j för att Visa kod exemplet som du vill använda.

![Exempelkod](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera Transaction Nodes med Azure CLI](manage-cli.md)
