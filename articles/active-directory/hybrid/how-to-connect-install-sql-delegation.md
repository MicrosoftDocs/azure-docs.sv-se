---
title: Installera Azure AD Connect med hjälp av SQL-delegerad administratörs behörighet | Microsoft Docs
description: I det här avsnittet beskrivs en uppdatering av Azure AD Connect som gör det möjligt att installera med ett konto som bara har SQL dbo-behörighet.
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: jparsons
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f082ec896bf0542b63c8c1d0257679681334050
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85358675"
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>Installerar Azure AD Connect med SQL-delegerade administratörsbehörigheter
Före den senaste Azure AD Connect-versionen stöds inte administrativ delegering vid distribution av konfigurationer som kräver SQL.  Användare som ville installera Azure AD Connect som krävs för att ha Server administratörs behörighet (SA) på SQL-servern.

Med den senaste versionen av Azure AD Connect kan etableringen av databasen nu utföras out-of-band av SQL-administratören och sedan installeras av Azure AD Connect-administratören med databas ägar rättigheter.

## <a name="before-you-begin"></a>Innan du börjar
Om du vill använda den här funktionen måste du vara medveten om att det finns flera rörliga delar och att var och en kan omfatta en annan administratör i din organisation.  I följande tabell sammanfattas de enskilda rollerna och deras respektive uppgifter i Distribuera Azure AD Connect med den här funktionen.

|Roll|Beskrivning|
|-----|-----|
|Domän-eller skogs AD-administratör|Skapar det tjänst konto för domän nivå som används av Azure AD Connect för att köra Sync-tjänsten.  Mer information om tjänst konton finns i [konton och behörigheter](reference-connect-accounts-permissions.md).
|SQL-administratör|Skapar ADSync-databasen och ger inloggnings-och dbo-åtkomst till Azure AD Connect administratör och det tjänst konto som skapats av domän-/skogs administratören.|
Azure AD Connect administratör|Installerar Azure AD Connect och anger tjänst kontot vid anpassad installation.

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>Steg för att installera Azure AD Connect med hjälp av SQL-delegerade behörigheter
Använd följande steg för att etablera databasen out-of-band och installera Azure AD Connect med databas ägar behörigheter.

>[!NOTE]
>Även om det inte krävs, rekommenderar vi **starkt** att Latin1_General_CI_AS sortering är valt när databasen skapas.


1. Be SQL-administratören att skapa ADSync-databasen med en Skift läges okänslig sorterings ordning **(Latin1_General_CI_AS)**.  Databasen måste ha namnet **ADSync**.  Återställnings modellen, kompatibilitetsnivån och inne slutnings typen uppdateras till rätt värden när Azure AD Connect installeras.  Sorterings ordningen måste dock anges korrekt av SQL-administratören annars Azure AD Connect blockerar installationen.  För att återställa SA måste du ta bort och återskapa databasen.
 
   ![Sortering](./media/how-to-connect-install-sql-delegation/sql4.png)
2. Ge Azure AD Connect-administratören och domän tjänst kontot följande behörigheter:
   - SQL-inloggning 
   - behörigheter för **databas ägare (dbo)** .
 
   ![Behörigheter](./media/how-to-connect-install-sql-delegation/sql3a.png)

   >[!NOTE]
   >Azure AD Connect stöder inte inloggningar med ett kapslat medlemskap.  Det innebär att ditt Azure AD Connect administratörs konto och domän tjänst kontot måste kopplas till en inloggning som har tilldelats dbo-rättigheter.  Det får inte bara vara medlem i en grupp som har tilldelats till en inloggning med dbo-rättigheter.

3. Skicka ett e-postmeddelande till Azure AD Connect administratören som anger det SQL Server-och instans namn som ska användas när du installerar Azure AD Connect.

## <a name="additional-information"></a>Ytterligare information
När databasen har skapats kan Azure AD Connect-administratören installera och konfigurera lokal synkronisering i sin bekvämlighet.

Om SQL-administratören har återställt ADSync-databasen från en tidigare Azure AD Connect säkerhets kopiering måste du installera den nya Azure AD Connect servern genom att använda en befintlig databas. Mer information om hur du installerar Azure AD Connect med en befintlig databas finns i [installera Azure AD Connect med en befintlig ADSync-databas](how-to-connect-install-existing-database.md).

## <a name="next-steps"></a>Nästa steg
- [Komma igång med Azure AD Connect med standardinställningar](how-to-connect-install-express.md)
- [Anpassad installation av Azure AD Connect](how-to-connect-install-custom.md)
- [Installera Azure AD Connect med en befintlig ADSync-databas](how-to-connect-install-existing-database.md)  
