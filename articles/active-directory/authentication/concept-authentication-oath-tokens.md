---
title: Autentiseringsmetod för OATH-token – Azure Active Directory
description: Lär dig mer om att använda OATH-token Azure Active Directory för att förbättra och skydda inloggningshändelser
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99d0dd081e3e1a681ba55e3457b79a548d6b2bb7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530383"
---
# <a name="authentication-methods-in-azure-active-directory---oath-tokens"></a>Autentiseringsmetoder i Azure Active Directory – OATH-token 

OATH TOTP (tidsbaserat engångslösenord) är en öppen standard som anger hur engångslösenordskoder (OTP) genereras. OATH TOTP kan implementeras med antingen programvara eller maskinvara för att generera koderna. Azure AD stöder inte OATH HOTP, en annan standard för kodgenerering.

## <a name="oath-software-tokens"></a>OATH-programvarutoken

Oath-token för programvara är vanligtvis program som Microsoft Authenticator och andra autentiseringsappar. Azure AD genererar den hemliga nyckeln, eller seed, som matas in i appen och används för att generera varje engångslösenord.

Authenticator-appen genererar automatiskt koder när den har ställts in för att skicka push-meddelanden så att en användare får en säkerhetskopia även om enheten inte har någon anslutning. Program från tredje part som använder OATH TOTP för att generera koder kan också användas.

Vissa OATH TOTP-maskinvarutoken är programmerbara, vilket innebär att de inte har en hemlig nyckel eller förprogrammerat startprogram. Dessa programmerbara maskinvarutoken kan konfigureras med hjälp av den hemliga nyckeln eller start seed som hämtas från konfigurationsflödet för programvarutoken. Kunder kan köpa dessa token från leverantören och använda den hemliga nyckeln eller start seed i leverantörens konfigurationsprocess.

## <a name="oath-hardware-tokens-preview"></a>OATH-maskinvarutoken (förhandsversion)

Azure AD stöder användning av OATH-TOTP SHA-1-tokens som uppdaterar koder var 30:e eller 60:e sekund. Kunder kan köpa dessa token från valfri leverantör.

OATH TOTP-maskinvarutoken har vanligtvis en hemlig nyckel, eller seed, förprogrammerat i token. Dessa nycklar måste anges i Azure AD enligt beskrivningen i följande steg. Hemliga nycklar är begränsade till 128 tecken, vilket kanske inte är kompatibelt med alla token. Den hemliga nyckeln får bara innehålla tecknen *a-z* eller *A-Z* och siffrorna *2–7* och måste vara kodade i *Base32*.

Programmerbara OATH TOTP-maskinvarutoken som kan skickas igen kan också konfigureras med Azure AD i konfigurationsflödet för programvarutoken.

OATH-maskinvarutoken stöds som en del av en offentlig förhandsversion. Mer information om förhandsversioner finns i [Kompletterande användningsvillkor för Microsoft Azure förhandsversioner.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

![Ladda upp OATH-token till bladet MFA OATH-token](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

När token har köpts måste de laddas upp i ett filformat med kommaavgränsade värden (CSV), inklusive UPN, serienummer, hemlig nyckel, tidsintervall, tillverkare och modell, enligt följande exempel:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,2234567abcdef2234567abcdef,60,Contoso,HardwareKey
```  

> [!NOTE]
> Se till att du inkluderar rubrikraden i CSV-filen. Om ett UPN har ett enkelt citattecken kan du undvika det med ett annat enkelt citattecken. Om TILL exempel UPN är min' user@domain.com ändrar du det till my'' när du laddar upp user@domain.com filen.

När den är korrekt formaterad som en CSV-fil kan en global administratör logga in på Azure Portal, navigera till **Azure Active Directory > Security > MFA > OATH-token och** ladda upp den resulterande CSV-filen.

Beroende på CSV-filens storlek kan det ta några minuter att bearbeta. Välj knappen **Uppdatera** för att få den aktuella statusen. Om det finns några fel i filen kan du ladda ned en CSV-fil som visar eventuella fel som du kan lösa. Fältnamnen i den nedladdade CSV-filen skiljer sig från den uppladdade versionen.  

När eventuella fel har åtgärdats kan administratören aktivera  varje nyckel genom att välja Aktivera för token och ange OTP som visas på token. Du kan aktivera högst 200 OATH-token var femte minut. 

Användare kan ha en kombination av upp till fem OATH-maskinvarutoken eller autentiseringsprogram, till exempel Microsoft Authenticator-appen, som konfigurerats för användning när som helst. OATH-maskinvarutoken kan inte tilldelas till gästanvändare i resursklientorganisationen.

## <a name="next-steps"></a>Nästa steg

Läs mer om att konfigurera autentiseringsmetoder med [hjälp av Microsoft Graph REST API](/graph/api/resources/authenticationmethods-overview).
