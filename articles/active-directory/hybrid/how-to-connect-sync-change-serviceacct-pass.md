---
title: 'Azure AD Connect synkronisering: ändra ADSync-tjänstkontot | Microsoft Docs'
description: I det här ämnes dokumentet beskrivs krypterings nyckeln och hur du kan överge den när lösen ordet har ändrats.
services: active-directory
keywords: Azure AD Sync Service-konto, lösen ord
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/17/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8778e50dcb881647696c6e901bf1058b9d6ac43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720346"
---
# <a name="changing-the-adsync-service-account-password"></a>Ändra lösen ordet för ADSync-tjänstkontot
Om du ändrar lösen ordet för ADSync-tjänstkontot kan inte synkroniseringstjänsten starta korrekt förrän du har övergivit krypterings nyckeln och initierat om lösen ordet för ADSync-tjänstkontot. 

>[!IMPORTANT]
> Om du använder Connect med en version från 2017 mars eller tidigare bör du inte återställa lösen ordet för tjänst kontot eftersom Windows förstör krypterings nycklarna av säkerhets skäl. Du kan inte ändra kontot till något annat konto utan att installera om Azure AD Connect. Om du uppgraderar till en version från 2017 april eller senare, stöds det att ändra lösen ordet för tjänst kontot, men du kan inte ändra det konto som används. 

Azure AD Connect, som en del av Synchronization Services, använder en krypterings nyckel för att lagra lösen orden för AD DS-anslutningsprogrammet och ADSync-tjänstkontot.  Dessa konton krypteras innan de lagras i-databasen. 

Krypterings nyckeln som används skyddas med hjälp av [Windows Data Protection (DPAPI)](/previous-versions/ms995355(v=msdn.10)). DPAPI skyddar krypterings nyckeln med **ADSync-tjänstkontot**. 

Om du behöver ändra lösen ordet för tjänst kontot kan du använda procedurerna i att [överge krypterings nyckeln för ADSync-tjänstekontot](#abandoning-the-adsync-service-account-encryption-key) för att åstadkomma detta.  Dessa procedurer bör också användas om du behöver överge krypterings nyckeln av någon anledning.

## <a name="issues-that-arise-from-changing-the-password"></a>Problem som uppstår vid ändring av lösen ordet
Det finns två saker som du måste utföra när du ändrar lösen ordet för tjänst kontot.

Först måste du ändra lösen ordet under Windows Service Control Manager.  Tills det här problemet har lösts visas följande fel:


- Om du försöker starta synkroniseringstjänsten i Windows Service Control Manager får du ett fel meddelande om att **Windows inte kunde starta tjänsten Microsoft Azure AD Sync på den lokala datorn**. **Fel 1069: tjänsten startade inte på grund av ett inloggnings fel.**"
- Under Windows Loggboken innehåller system händelse loggen ett fel med **händelse-ID 7038** och meddelandet "**ADSync-tjänsten kunde inte logga in med det aktuella konfigurerade lösen ordet på grund av följande fel: användar namnet eller lösen ordet är felaktigt.**"

För det andra, under vissa villkor, kan synkroniseringstjänsten inte längre hämta krypterings nyckeln via DPAPI om lösen ordet uppdateras. Utan krypterings nyckeln kan inte synkroniseringstjänsten dekryptera de lösen ord som krävs för att synkronisera till/från lokala AD och Azure AD.
Du kommer att se fel som:

- Om du försöker starta synkroniseringstjänsten under Windows Service Control Manager och det inte går att hämta krypterings nyckeln, Miss lyckas fel meddelandet "det<strong>gick inte att starta Microsoft Azure AD synkronisering på den lokala datorn. Mer information hittar du i systemets händelse logg. Om detta är en tjänst som inte kommer från Microsoft kontaktar du tjänst leverantören och refererar till tjänstspecifik felkod-21451857952</strong>. "
- Under Windows Loggboken innehåller program händelse loggen ett fel med **händelse-ID 6028** och fel meddelandet *"det går inte att få åtkomst till serverns krypterings nyckel."*

För att säkerställa att du inte får de här felen följer du procedurerna i att [överge krypterings nyckeln för ADSync-tjänstekontot](#abandoning-the-adsync-service-account-encryption-key) när du ändrar lösen ordet.
 
## <a name="abandoning-the-adsync-service-account-encryption-key"></a>Avbryta krypterings nyckeln för ADSync-tjänstekontot
>[!IMPORTANT]
>Följande procedurer gäller endast för Azure AD Connect build 1.1.443.0 eller äldre. Detta kan inte användas för nyare versioner av Azure AD Connect, eftersom att överge krypterings nyckeln hanteras av Azure AD Connect när du ändrar lösen ordet för AD Sync-tjänstkontot så att följande steg inte behövs i de nyare versionerna.   

Använd följande procedurer för att överge krypterings nyckeln.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>Vad du gör om du behöver överge krypterings nyckeln

Om du behöver överge krypterings nyckeln använder du följande procedurer för att göra detta.

1. [Stoppa synkroniseringstjänsten](#stop-the-synchronization-service)

1. [Överge den befintliga krypterings nyckeln](#abandon-the-existing-encryption-key)

2. [Ange lösen ordet för AD DS Connector-kontot](#provide-the-password-of-the-ad-ds-connector-account)

3. [Initiera lösen ordet för ADSync-tjänstkontot](#reinitialize-the-password-of-the-adsync-service-account)

4. [Starta synkroniseringstjänsten](#start-the-synchronization-service)

#### <a name="stop-the-synchronization-service"></a>Stoppa synkroniseringstjänsten
Först kan du stoppa tjänsten i Windows Service Control Manager.  Kontrol lera att tjänsten inte körs när du försöker stoppa den.  Om det är det, väntar du tills det är klart och stoppar det.


1. Gå till Windows Service Control Manager (START → tjänster).
2. Välj **Microsoft Azure AD synkronisering** och klicka på stoppa.

#### <a name="abandon-the-existing-encryption-key"></a>Överge den befintliga krypterings nyckeln
Överge den befintliga krypterings nyckeln så att den nya krypterings nyckeln kan skapas:

1. Logga in på Azure AD Connect servern som administratör.

2. Starta en ny PowerShell-session.

3. Navigera till mapp: `'$env:ProgramFiles\Microsoft Azure AD Sync\bin\'`

4. Kör kommandot: `./miiskmu.exe /a`

![Skärm bild som visar PowerShell när du har kört kommandot.](./media/how-to-connect-sync-change-serviceacct-pass/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-connector-account"></a>Ange lösen ordet för AD DS Connector-kontot
Eftersom befintliga lösen ord som lagras i databasen inte längre kan dekrypteras måste du ange synkroniseringstjänsten med lösen ordet för AD DS-anslutningsprogrammet. Synkroniseringstjänsten krypterar lösen ord med den nya krypterings nyckeln:

1. Starta Synchronization Service Manager (START → synkroniseringstjänst).
</br>![Synkronisera Service Manager](./media/how-to-connect-sync-change-serviceacct-pass/startmenu.png)  
2. Gå till fliken **anslutningar** .
3. Välj den **AD-anslutning** som motsvarar din lokala AD. Om du har fler än en AD-koppling upprepar du följande steg för var och en av dem.
4. Under **åtgärder** väljer du **Egenskaper**.
5. I popup-dialogrutan väljer **du Anslut till Active Directory skog**:
6. Ange lösen ordet för AD DS-kontot i text rutan **lösen ord** . Om du inte känner till lösen ordet måste du ange det som ett känt värde innan du utför det här steget.
7. Klicka på **OK** för att spara det nya lösen ordet och stänga popup-dialogrutan.
![Skärm bild som visar sidan "Anslut till Active Directory skog" i fönstret "egenskaper".](./media/how-to-connect-sync-change-serviceacct-pass/key6.png)

#### <a name="reinitialize-the-password-of-the-adsync-service-account"></a>Initiera lösen ordet för ADSync-tjänstkontot
Du kan inte ange lösen ordet för Azure AD-tjänstkontot direkt till synkroniseringstjänsten. I stället måste du använda cmdlet **Add-ADSyncAADServiceAccount** för att initiera om Azure AD-tjänstkontot. -Cmdleten återställer konto lösen ordet och gör det tillgängligt för synkroniseringstjänsten:

1. Starta en ny PowerShell-session på Azure AD Connect servern.
2. Kör cmdlet `Add-ADSyncAADServiceAccount` .
3. I popup-dialog rutan anger du Azure AD global admin-autentiseringsuppgifter för din Azure AD-klient.
![Verktyget Azure AD Connect Sync Encryption Key](./media/how-to-connect-sync-change-serviceacct-pass/key7.png)
4. Om det lyckas visas PowerShell-Kommandotolken.

#### <a name="start-the-synchronization-service"></a>Starta synkroniseringstjänsten
Nu när synkroniseringstjänsten har åtkomst till krypterings nyckeln och alla lösen ord som krävs kan du starta om tjänsten i Windows Service Control Manager:


1. Gå till Windows Service Control Manager (START → tjänster).
2. Välj **Microsoft Azure AD synkronisering** och klicka på Starta om.

## <a name="next-steps"></a>Nästa steg
**Översikts avsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)

* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
