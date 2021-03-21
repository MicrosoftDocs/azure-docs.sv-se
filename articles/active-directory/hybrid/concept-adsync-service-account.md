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
ms.date: 03/17/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb23d79caa6964c3f61fbb84c8b8f229f475b8ab
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722165"
---
# <a name="adsync-service-account"></a>ADSync-tjänstkonto
Azure AD Connect installerar en lokal tjänst som dirigerar synkronisering mellan Active Directory och Azure Active Directory.  Microsoft Azure AD Sync-synkroniseringstjänsten (ADSync) körs på en server i din lokala miljö.  Autentiseringsuppgifterna för tjänsten ställs in som standard i Express installationer, men kan anpassas för att uppfylla organisationens säkerhets krav.  Dessa autentiseringsuppgifter används inte för att ansluta till dina lokala skogar eller Azure Active Directory.

Att välja ADSync-tjänstkontot är ett viktigt planerings beslut att fatta innan du installerar Azure AD Connect.  Om du försöker ändra autentiseringsuppgifterna efter installationen leder det till att tjänsten inte startar, att förlora åtkomsten till databasen och att det inte går att autentisera med dina anslutna kataloger (Azure och AD DS).  Ingen synkronisering sker förrän de ursprungliga autentiseringsuppgifterna har återställts.

Synkroniseringstjänsten kan köras under olika konton. Den kan köras under ett virtuellt tjänst konto (VSA), ett hanterat tjänst konto (gMSA/sMSA) eller ett vanligt användar konto. De alternativ som stöds har ändrats med 2017 april release och 2021 mars Azure AD Connect när du gör en ny installation. Om du uppgraderar från en tidigare version av Azure AD Connect är dessa ytterligare alternativ inte tillgängliga. 


|Typ av konto|Installations alternativ|Beskrivning| 
|-----|------|-----|
|Virtuellt tjänst konto|Express och anpassad, 2017 april och senare| Ett virtuellt tjänst konto används för alla Express installationer, förutom för installationer på en domänkontrollant. När du använder anpassad installation är det standard alternativet om inte ett annat alternativ används.| 
|Hanterat tjänstkonto|Anpassad, 2017 april och senare|Om du använder en fjärran sluten SQL Server rekommenderar vi att du använder ett grupphanterat tjänst konto. |
|Hanterat tjänstkonto|Express och anpassad, 2021 mars och senare|Ett fristående hanterat tjänst konto med ADSyncMSA_ skapas under installationen av Express installationer när de installeras på en domänkontrollant. När du använder anpassad installation är det standard alternativet om inte ett annat alternativ används.|
|Användarkonto|Express och anpassad, 2017 april till 2021 mars|Ett användar konto som har prefixet AAD_ skapas under installationen för Express installationer när de installeras på en domänkontrollant. När du använder anpassad installation är det standard alternativet om inte ett annat alternativ används.|
|Användarkonto|Express och anpassad, 2017 mars och tidigare|Ett användar konto som har prefixet AAD_ skapas under installationen för Express installationer. När du använder anpassad installation kan du ange ett annat konto.| 

>[!IMPORTANT]
> Om du använder Connect med en version från 2017 mars eller tidigare bör du inte återställa lösen ordet för tjänst kontot eftersom Windows förstör krypterings nycklarna av säkerhets skäl. Du kan inte ändra kontot till något annat konto utan att installera om Azure AD Connect. Om du uppgraderar till en version från 2017 april eller senare, stöds det att ändra lösen ordet för tjänst kontot, men du kan inte ändra det konto som används. 

> [!IMPORTANT]
> Du kan bara ange tjänst kontot vid första installationen. Det finns inte stöd för att ändra tjänst kontot när installationen har slutförts. Om du behöver ändra lösen ordet för tjänst kontot, stöds detta och du hittar anvisningar [här](how-to-connect-sync-change-serviceacct-pass.md).

Följande är en tabell med alternativen standard, Recommended och Supported för kontot Sync service. 

Förklaring: 

- **Fet** anger standard alternativet och det rekommenderade alternativet i de flesta fall. 
- *Kursiv* anger det rekommenderade alternativet när det inte är standard alternativet. 
- Alternativ som inte stöds av fet stil 
- Lokalt konto – lokalt användar konto på servern 
- Domän konto – domän användar konto 
- sMSA- [fristående hanterat tjänst konto](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))
- gMSA- [grupphanterat tjänst konto](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)) 

 ||**LocalDB </br> Express**|**LocalDB/LocalSQL </br> Custom**|**Fjärr-SQL </br> anpassad**|
|-----|-----|-----|-----|
|**domänansluten dator**|**ATTRIBUTET**|**ATTRIBUTET**</br> *sMSA*</br> *gMSA*</br> Lokalt konto</br> Domänkonto| *gMSA* </br>Domänkonto|
|Domänkontrollant| **sMSA**|**sMSA** </br>*gMSA*</br> Domänkonto|*gMSA*</br>Domänkonto| 

## <a name="virtual-service-account"></a>Virtuellt tjänst konto 

Ett virtuellt tjänst konto är en särskild typ av hanterat lokalt konto som inte har ett lösen ord och som hanteras automatiskt av Windows. 

 ![Virtuellt tjänst konto](media/concept-adsync-service-account/account-1.png)

Det virtuella tjänst kontot är avsett att användas med scenarier där Synkroniseringsmotorn och SQL finns på samma server. Om du använder fjärr-SQL rekommenderar vi att du använder ett grupphanterat tjänst konto i stället. 

Det går inte att använda det virtuella tjänst kontot på en domänkontrollant på grund av problem med [Windows Data Protection API (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx) . 

## <a name="managed-service-account"></a>Hanterat tjänstkonto 

Om du använder en fjärran sluten SQL Server rekommenderar vi att du använder ett grupphanterat tjänst konto. Mer information om hur du förbereder din Active Directory för grupphanterade tjänst konton finns i [Översikt över grupphanterade tjänst konton](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)). 

Om du vill använda det här alternativet väljer du **Använd ett befintligt tjänst konto** på sidan [installera nödvändiga komponenter](how-to-connect-install-custom.md#install-required-components) och väljer **hanterat tjänst konto**. 

 ![hanterat tjänst konto](media/concept-adsync-service-account/account-2.png)

Det finns också stöd för att använda ett fristående hanterat tjänst konto. Dessa kan dock endast användas på den lokala datorn och det finns ingen förmån att använda dem över det virtuella standard kontot. 

### <a name="auto-generated-standalone-managed-service-account"></a>Automatiskt genererat fristående hanterat tjänst konto 

Om du installerar Azure AD Connect på en domänkontrollant skapas ett fristående hanterat tjänst konto av installations guiden (om du inte anger det konto som ska användas i anpassade inställningar). Kontot har prefix **ADSyncMSA_** och används för den faktiska synkroniseringstjänsten som ska köras som. 

Det här kontot är ett hanterat domän konto som inte har ett lösen ord och som hanteras automatiskt av Windows. 

Det här kontot är avsett att användas med scenarier där Synkroniseringsmotorn och SQL finns på domänkontrollanten. 

## <a name="user-account"></a>Användarkonto 

Ett lokalt tjänst konto skapas av installations guiden (om du inte anger vilket konto som ska användas i anpassade inställningar). Kontot har prefix AAD_ och används för den faktiska synkroniseringstjänsten som ska köras som. Om du installerar Azure AD Connect på en domänkontrollant skapas kontot i domänen. AAD_ tjänst kontot måste finnas i domänen om: 
- du använder en fjärrserver som kör SQL Server 
- du använder en proxy som kräver autentisering 

 ![användar konto](media/concept-adsync-service-account/account-3.png)

Kontot skapas med ett långt komplext lösen ord som inte upphör att gälla. 

Det här kontot används för att lagra lösen ord för andra konton på ett säkert sätt. De andra konto lösen orden lagras krypterade i-databasen. De privata nycklarna för krypterings nycklarna skyddas med kryptering av kryptografiska tjänsters hemliga nycklar med hjälp av Windows Data Protection API (DPAPI). 

Om du använder en fullständig SQL Server är tjänst kontot DBO för den skapade databasen för Synkroniseringsmotorn. Tjänsten fungerar inte som avsedd för andra behörigheter. En SQL-inloggning skapas också. 

Kontot beviljas också behörighet till filer, register nycklar och andra objekt som är relaterade till Synkroniseringsmotorn. 


## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
