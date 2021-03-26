---
title: Autentiseringsmetod för OATH-token – Azure Active Directory
description: Lär dig mer om att använda OATH-token i Azure Active Directory för att hjälpa till att förbättra och säkra inloggnings händelser
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: b535d59376de455962d367a8ad6f02a037649d9d
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557895"
---
# <a name="authentication-methods-in-azure-active-directory---oath-tokens"></a>Autentiseringsmetoder i Azure Active Directory-OATH-token 

OATH-TOTP mobilapp (Time-based ett Time-lösenordet) är en öppen standard som anger hur eng ång slö sen ord (eng ång slö sen ord) genereras. OATH-TOTP mobilapp kan implementeras med hjälp av antingen program vara eller maskin vara för att generera koderna. Azure AD har inte stöd för OATH-HOTP, en annan kodgenerering standard.

## <a name="oath-software-tokens"></a>OATH-programvaru-token

OATH-token för program vara är vanligt vis program som Microsoft Authenticator-appen och andra Authenticator-appar. Azure AD genererar den hemliga nyckeln eller dirigeringen som inmatas i appen och används för att generera varje eng ång slö sen ord.

Authenticator-appen genererar automatiskt koder när de konfigureras att utföra push-meddelanden så att en användare har en säkerhets kopia även om deras enheter inte har någon anslutning. Program från tredje part som använder OATH-TOTP mobilapp för att generera koder kan också användas.

Vissa TOTP mobilapp-token för OATH är programmerbara, vilket innebär att de inte har en hemlig nyckel eller ett förprogrammerat dirigerings-. Dessa programmerbara maskinvaru-token kan konfigureras med hjälp av den hemliga nyckeln eller dirigeringen som hämtats från installations flödet för programtoken. Kunder kan köpa dessa token från den leverantör de väljer och använda den hemliga nyckeln eller dirigeringen i deras leverantörs konfigurations process.

## <a name="oath-hardware-tokens-preview"></a>OATH-token för maskin vara (för hands version)

Azure AD stöder användningen av OATH-TOTP mobilapp SHA-1-token som uppdaterar koder var 30: e sekund eller 60 sekunder. Kunder kan köpa dessa token från den leverantör de väljer.

OATH TOTP mobilapp-token levereras vanligt vis med en hemlig nyckel eller dirigeras, förprogrammeras i token. De här nycklarna måste vara inmatade i Azure AD enligt beskrivningen i följande steg. Hemliga nycklar är begränsade till 128 tecken, som kanske inte är kompatibla med alla tokens. Den hemliga nyckeln får bara innehålla tecknen *a-z* eller *a-z* och siffror *2-7* och måste kodas i *Base32*.

Programmerbara OATH TOTP mobilapp-maskinvaru-token som kan dirigeras om kan också konfigureras med Azure AD i installations flödet för programtoken.

OATH-token för OATH-enheter stöds som en del av en offentlig för hands version. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.

![Laddar upp OATH-token till bladet MFA OATH-token](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

När token har hämtats måste de överföras i ett fil format med kommaavgränsade värden (CSV), inklusive UPN, serie nummer, hemlig nyckel, tidsintervall, tillverkare och modell, som du ser i följande exempel:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,2234567abcdef1234567abcdef,60,Contoso,HardwareKey
```  

> [!NOTE]
> Se till att du inkluderar rubrik raden i CSV-filen. Om ett UPN har ett enda citat, kan du kringgå det med ett annat enkelt citat tecken. Om UPN till exempel är My ' user@domain.com , ändrar du det till mitt ' ' user@domain.com när du laddar upp filen.

När den är korrekt formaterad som en CSV-fil kan en global administratör logga in på Azure Portal, navigera till **Azure Active Directory > säkerhet > MFA > Oath-token** och ladda upp den resulterande CSV-filen.

Beroende på storleken på CSV-filen kan det ta några minuter att bearbeta. Välj knappen **Uppdatera** för att hämta aktuell status. Om det finns fel i filen kan du ladda ned en CSV-fil som visar eventuella fel som du kan lösa. Fält namnen i den hämtade CSV-filen skiljer sig från den överförda versionen.  

När eventuella fel har åtgärd ATS kan administratören aktivera varje nyckel genom att välja **Aktivera** för token och ange det eng ång slö sen ord som visas i token. Du kan aktivera maximalt 200 OATH-token var 5: e minut. 

Användare kan ha en kombination av upp till fem OATH-token eller Authenticator-program, till exempel Microsoft Authenticator-appen, som har kon figurer ATS för användning när som helst.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du konfigurerar autentiseringsmetoder med hjälp av [Microsoft Graph REST API beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).
