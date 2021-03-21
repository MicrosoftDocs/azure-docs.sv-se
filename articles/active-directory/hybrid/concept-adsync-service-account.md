---
title: 'Azure AD Connect: ADSync-tjänstkonto | Microsoft Docs'
description: I det här avsnittet beskrivs ADSync-tjänstekontot och det finns metod tips för kontot.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8dddfb8426b769c06cb5b7494431b7eee34dbf9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94410903"
---
# <a name="adsync-service-account"></a>ADSync-tjänstkonto
Azure AD Connect installerar en lokal tjänst som dirigerar synkronisering mellan Active Directory och Azure Active Directory.  Microsoft Azure AD Sync-synkroniseringstjänsten (ADSync) körs på en server i din lokala miljö.  Autentiseringsuppgifterna för tjänsten ställs in som standard i Express installationer, men kan anpassas för att uppfylla organisationens säkerhets krav.  Dessa autentiseringsuppgifter används inte för att ansluta till dina lokala skogar eller Azure Active Directory.

Att välja ADSync-tjänstkontot är ett viktigt planerings beslut att fatta innan du installerar Azure AD Connect.  Om du försöker ändra autentiseringsuppgifterna efter installationen leder det till att tjänsten inte startar, att förlora åtkomsten till databasen och att det inte går att autentisera med dina anslutna kataloger (Azure och AD DS).  Ingen synkronisering sker förrän de ursprungliga autentiseringsuppgifterna har återställts.

## <a name="the-default-adsync-service-account"></a>Standard kontot för ADSync-tjänsten

När körs på en medlems Server körs AdSync-tjänsten i kontexten för ett virtuellt tjänst konto (VSA).  På grund av en produkt begränsning skapas ett anpassat tjänst konto när det installeras på en domänkontrollant.  Om kontot Express-inställningar inte uppfyller organisationens säkerhets krav distribuerar du Azure AD Connect genom att välja alternativet Anpassa.  Välj sedan alternativet tjänst konto som uppfyller organisationens krav.

>[!NOTE]
>Standard tjänst kontot när det installeras på en domänkontrollant har formatet domän \ AAD_InstallationIdentifier.  Lösen ordet för det här kontot genereras slumpmässigt och ger stora utmaningar för återställning och lösen ords rotation.  Microsoft rekommenderar att du anpassar tjänst kontot under den första installationen på en domänkontrollant för att använda antingen ett fristående eller grupphanterat tjänst konto (sMSA/gMSA)

|Azure AD Connect plats|Tjänst konto skapat|
|-----|-----|
|Medlems Server|NT-SERVICE\ADSync|
|Domänkontrollant|Domän \ AAD_74dc30c01e80 (se OBS!)|

## <a name="custom-adsync-service-accounts"></a>Anpassade ADSync-tjänstekonton
Microsoft rekommenderar att du kör ADSync-tjänsten i kontexten för antingen ett virtuellt tjänst konto eller ett fristående eller grupphanterat tjänst konto.  Domän administratören kan också välja att skapa ett tjänst konto som tillhandahålls för att uppfylla dina särskilda organisations säkerhets krav.   Om du vill anpassa det tjänst konto som används under installationen väljer du alternativet Anpassa på sidan Express inställningar nedan.   Följande alternativ är tillgängliga:

- Standard konto – Azure AD Connect etablerar tjänst kontot enligt beskrivningen ovan
- hanterat tjänst konto – Använd en fristående eller grupp MSA som tillhandahålls av administratören
- domän konto – Använd ett domän tjänst konto som tillhandahålls av administratören

![Skärm bild av alternativ knapparna "anpassa" eller "Använd Express inställningar" på sidan Azure AD Connect Express-inställningar.](media/concept-adsync-service-account/adsync1.png)

![Skärm bild av sidan för Azure AD Connect installera nödvändiga komponenter med alternativet att använda ett befintligt hanterat tjänst konto valt.](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>Diagnostisera ändringar i ADSync-tjänstkontot
Om du ändrar autentiseringsuppgifterna för ADSync-tjänsten efter installationen leder det till att tjänsten inte startar, förlorar åtkomst till databasen för synkronisering och att den inte kan autentiseras med dina anslutna kataloger (Azure och AD DS).  Det räcker inte att bevilja databas åtkomst till det nya ADSync-tjänstkontot för att kunna återställas från det här problemet. Ingen synkronisering sker förrän de ursprungliga autentiseringsuppgifterna har återställts.

ADSync-tjänsten utfärdar ett meddelande om fel nivå till händelse loggen när det inte går att starta.  Innehållet i meddelandet varierar beroende på om den inbyggda databasen (LocalDB) eller en fullständig SQL används.  Följande är exempel på de händelse logg poster som kan finnas.

### <a name="example-1"></a>Exempel 1

Det gick inte att hitta krypterings nycklarna för AdSync-tjänsten och den har återskapats.  Synkroniseringen utförs inte förrän problemet har åtgärd ATS.

Genom att felsöka det här problemet blir Microsoft Azure AD synkrona krypterings nycklar otillgängliga om autentiseringsuppgifterna för AdSync-tjänstens inloggning har ändrats.  Om autentiseringsuppgifterna har ändrats använder du tjänst programmet för att ändra inloggnings kontot till det ursprungligen konfigurerade värdet (t. ex. NT SERVICE\AdSync) och startar om tjänsten.  Detta kommer omedelbart att återställa korrekt drift av AdSync-tjänsten.

Mer information finns i följande [artikel](./whatis-hybrid-identity.md) .

### <a name="example-2"></a>Exempel 2

Det gick inte att starta tjänsten eftersom det inte gick att upprätta en anslutning till den lokala databasen (LocalDB).

Felsöka det här problemet. Microsoft Azure AD Sync-tjänsten kommer att förlora behörighet att komma åt den lokala databas leverantören om autentiseringsuppgifterna för AdSync-tjänstens inloggnings information har ändrats.  Om autentiseringsuppgifterna har ändrats använder du tjänst programmet för att ändra inloggnings kontot till det ursprungligen konfigurerade värdet (t. ex. NT SERVICE\AdSync) och startar om tjänsten.  Detta kommer omedelbart att återställa korrekt drift av AdSync-tjänsten.

Mer information finns i följande [artikel](./whatis-hybrid-identity.md) .

Ytterligare information följande fel information returnerades av providern:
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).