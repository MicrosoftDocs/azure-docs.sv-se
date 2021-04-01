---
title: Säkerhet för Azure blockchain service
description: Data åtkomst och säkerhets koncept i Azure blockchain service
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 989d9f2afad30517a85185878d694c0b6640e987
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "80879606"
---
# <a name="azure-blockchain-service-security"></a>Säkerhet för Azure blockchain service

Azure blockchain service använder flera Azure-funktioner för att hålla dina data säkra och tillgängliga. Data skyddas med hjälp av isolering, kryptering och autentisering.

## <a name="isolation"></a>Isolering

Azure blockchain service-resurser är isolerade i ett privat virtuellt nätverk. Varje transaktion och validerings nod är en virtuell dator (VM). Virtuella datorer i ett virtuellt nätverk kan inte kommunicera direkt med virtuella datorer i ett annat virtuellt nätverk. Isolering garanterar att kommunikationen förblir privat i det virtuella nätverket. Mer information om hur du isolerar virtuella Azure-nätverk finns [i isolera i det offentliga Azure-molnet](../../security/fundamentals/isolation-choices.md#networking-isolation).

![VNET-diagram](./media/data-security/vnet.png)

## <a name="encryption"></a>Kryptering

Användar data lagras i Azure Storage. Användar data krypteras i rörelse och i vila för säkerhet och konfidentialitet. Mer information finns i: [Azure Storage säkerhets guide](../../storage/blobs/security-recommendations.md).

## <a name="authentication"></a>Autentisering

Transaktioner kan skickas till blockchain-noder via en RPC-slutpunkt. Klienterna kommunicerar med en Transaction-nod med en omvänd proxyserver som hanterar användarautentisering och krypterar data över TLS.

![Autentiseringsschema](./media/data-security/authentication.png)

Det finns tre lägen för autentisering för RPC-åtkomst.

### <a name="basic-authentication"></a>Grundläggande autentisering

Grundläggande autentisering använder ett HTTP-authentication-huvud som innehåller användar namnet och lösen ordet. Användar namnet är namnet på blockchain-noden. Lösen ordet anges vid etablering av en medlem eller nod. Du kan ändra lösen ordet med hjälp av Azure Portal eller CLI.

### <a name="access-keys"></a>Åtkomstnycklar

Åtkomst nycklar använder en slumpmässigt genererad sträng som ingår i slut punktens URL. Med två åtkomst nycklar kan du aktivera nyckel rotation. Nycklar kan återskapas från Azure Portal och CLI.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD) använder sig av en anspråksbaserad autentiseringsmekanism där användaren autentiseras av Azure AD med hjälp av autentiseringsuppgifter för Azure AD-användare. Azure AD tillhandahåller molnbaserad identitets hantering och gör det möjligt för kunder att använda en enda identitet i hela företags-och åtkomst program i molnet. Azure blockchain-tjänsten integreras med Azure AD och aktiverar ID-Federation, enkel inloggning och Multi-Factor Authentication. Du kan tilldela användare, grupper och program roller i din organisation för blockchain-medlem och Node-åtkomst.

Azure AD client-proxyn finns på [GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases). Klientens proxyserver dirigerar användaren till inloggnings sidan för Azure AD och erhåller en Bearer-token vid lyckad autentisering. Därefter ansluter användaren ett Ethereum-klientprogram, till exempel Geth eller Truffle till klientens proxys slut punkt. När en transaktion skickas infogar klientcachen-token i HTTP-huvudet och den omvända proxyn verifierar token med OAuth-protokollet.

## <a name="keys-and-ethereum-accounts"></a>Nycklar och Ethereum-konton

När du konfigurerar en Azure blockchain service-medlem skapas ett Ethereum-konto och ett offentligt och privat nyckel par. Den privata nyckeln används för att skicka transaktioner till blockchain. Ethereum-kontot är de sista 20 byten i den offentliga nyckelns hash. Ethereum-kontot kallas även för en plån bok.

Det privata och offentliga nyckel paret lagras som en KeyFile i JSON-format. Den privata nyckeln krypteras med det lösen ord som angavs när blockchain-redovisnings tjänsten skapas.

Privata nycklar används för att signera transaktioner digitalt. I privata Blockchains representerar ett smart kontrakt som signerats av en privat nyckel identiteten för undertecknaren. För att verifiera att signaturen är giltig kan mottagaren jämföra den offentliga nyckeln för undertecknaren med adressen som beräknas från signaturen.

Constellation-nycklar används för att unikt identifiera en kvorumresurs. Constellation nycklar genereras vid tidpunkten för nodens etablering och anges i parametern privateFor för en privat transaktion i kvorum.

## <a name="next-steps"></a>Nästa steg

Se [så här konfigurerar du Azure Active Directory åtkomst för Azure blockchain-tjänsten](configure-aad.md).
