---
title: Azure AD Connect-och användar sekretess | Microsoft Docs
description: Det här dokumentet beskriver hur du hämtar GDPR-kompatibilitet med Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f1d1bf35d03f0f7c4d11e4cc7e9b6f98f38d8ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997706"
---
# <a name="user-privacy-and-azure-ad-connect"></a>Användar sekretess och Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Den här artikeln handlar om Azure AD Connect-och användar sekretess.  Information om Azure AD Connect Health och användar sekretess finns [här](reference-connect-health-user-privacy.md).

Förbättra användar sekretessen för Azure AD Connect installationer på två sätt:

1.  På begäran extraherar du data för en person och tar bort data från den personen från installationerna
2.  Se till att inga data behålls efter 48 timmar.

Azure AD Connect-teamet rekommenderar det andra alternativet eftersom det är mycket enklare att implementera och underhålla.

En Azure AD Connect Sync-Server lagrar följande användar sekretess data:
1.  Data om en person i **Azure AD Connect-databasen**
2.  Data i **Windows-händelseloggar** som kan innehålla information om en person
3.  Data i **Azure AD Connect installations logg filen** som kan innehålla en person

Azure AD Connect kunder bör använda följande rikt linjer när de tar bort användar data:
1.  Ta bort innehållet i mappen som innehåller Azure AD Connect installations loggen regelbundet, minst var 48: e timme
2.  Den här produkten kan också skapa händelse loggar.  Om du vill veta mer om loggar för händelse loggar kan du läsa [dokumentationen här](/windows/win32/wes/windows-event-log).

Data om en person tas bort automatiskt från Azure AD Connect-databasen när personens data tas bort från käll systemet där det kom från. Ingen speciell åtgärd från administratörer krävs för att vara GDPR-kompatibel.  Det kräver dock att Azure AD Connect data synkroniseras med data källan minst varannan dag.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Ta bort innehållet i mappen Azure AD Connect installations logg filen
Kontrol lera och ta bort innehållet i **c:\programdata\aadconnect** -mappen regelbundet, förutom **PersistedState.Xml** -filen. Den här filen upprätthåller tillstånd för den tidigare installationen av Azure A Connect och används när en uppgraderings installation utförs. Den här filen innehåller inte några data om en person och bör inte tas bort.

>[!IMPORTANT]
>Ta inte bort PersistedState.xml-filen.  Den här filen innehåller ingen användar information och behåller den tidigare installationens tillstånd.

Du kan antingen granska och ta bort filerna med hjälp av Utforskaren i Windows, eller så kan du använda ett skript som följande för att utföra nödvändiga åtgärder:


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>Schemalägg skriptet så att det körs var 48: e timme
Använd följande steg för att schemalägga skriptet så att det körs var 48: e timme.

1.  Spara skriptet i en fil med fil namns tillägget **&#46;ps1** och öppna sedan kontroll panelen och klicka på **system och säkerhet**.
    ![System](./media/reference-connect-user-privacy/gdpr2.png)

2.  Under administrations verktyg-rubriken klickar du på **schemalägga aktiviteter**.
    ![Aktivitet](./media/reference-connect-user-privacy/gdpr3.png)
3.  I Schemaläggaren högerklickar du på **aktivitets schema bibliotek** och klickar på **skapa grundläggande aktivitet...**
4.  Ange namnet på den nya aktiviteten och klicka på **Nästa**.
5.  Välj **dagligen** för aktivitets utlösaren och klicka på **Nästa**.
6.  Ställ in upprepningen på **2 dagar** och klicka på **Nästa**.
7.  Välj **starta ett program** som åtgärd och klicka på **Nästa**.
8.  Skriv **PowerShell** i rutan för programmet/skriptet och i rutan **Lägg till argument (valfritt)**, ange den fullständiga sökvägen till skriptet som du skapade tidigare och klicka sedan på **Nästa**.
9.  Nästa skärm visar en sammanfattning av den uppgift som du håller på att skapa. Verifiera värdena och klicka på **Slutför** för att skapa uppgiften.



## <a name="next-steps"></a>Nästa steg
* [Läs sekretess policyn för Microsoft på säkerhets Center](https://www.microsoft.com/trustcenter)
* [Azure AD Connect Health-och användar sekretess](reference-connect-health-user-privacy.md)