---
title: Azure AD Connect Sync service-funktioner och-konfiguration | Microsoft Docs
description: Beskriver funktioner på tjänst sidan för Azure AD Connect Sync-tjänsten.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 213aab20-0a61-434a-9545-c4637628da81
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/18/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 261ab5d0f039705a2566b7c28ff4c06778bb661a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94410546"
---
# <a name="azure-ad-connect-sync-service-features"></a>Tjänstfunktioner för Azure AD Connect-synkronisering

Synkroniseringsfunktionen i Azure AD Connect har två komponenter:

* Den lokala komponenten med namnet **Azure AD Connect Sync**, kallas även **Sync-motor**.
* Tjänsten som finns i Azure AD kallas även **Azure AD Connect Sync-tjänst**

I det här avsnittet beskrivs hur följande funktioner i **tjänsten Azure AD Connect Sync** fungerar och hur du kan konfigurera dem med hjälp av Windows PowerShell.

Dessa inställningar konfigureras av [Azure Active Directory-modulen för Windows PowerShell](/previous-versions/azure/jj151815(v=azure.100)). Ladda ned och installera den separat från Azure AD Connect. De cmdletar som beskrivs i det här avsnittet introducerades i [2016 mars-utgåvan (build 9031,1)](https://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Om du inte har de cmdlets som beskrivs i det här avsnittet, eller om de inte genererar samma resultat, kontrollerar du att du kör den senaste versionen.

Om du vill se konfigurationen i din Azure AD-katalog kör du `Get-MsolDirSyncFeatures` .  
![Get-MsolDirSyncFeatures-resultat](./media/how-to-connect-syncservice-features/getmsoldirsyncfeatures.png)

Många av de här inställningarna kan bara ändras av Azure AD Connect.

Följande inställningar kan konfigureras av `Set-MsolDirSyncFeature` :

| DirSyncFeature | Kommentar |
| --- | --- |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) |Tillåter att objekt ansluts i userPrincipalName förutom primär SMTP-adress. |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) |Tillåter att Synkroniseringsmotorn uppdaterar userPrincipalName-attributet för hanterade/licensierade (icke-federerade) användare. |

När du har aktiverat en funktion kan den inte inaktive ras igen.

> [!NOTE]
> Från 24 augusti 2016 är funktionen *duplicerat attribut återhämtning* aktive rad som standard för nya Azure AD-kataloger. Den här funktionen kommer också att distribueras och aktive ras på kataloger som skapats före detta datum. Du får ett e-postmeddelande när din katalog kommer att få den här funktionen aktive rad.
> 
> 

Följande inställningar konfigureras av Azure AD Connect och kan inte ändras av `Set-MsolDirSyncFeature` :

| DirSyncFeature | Kommentar |
| --- | --- |
| DeviceWriteback |[Azure AD Connect: aktiverar tillbakaskrivning av enhet](how-to-connect-device-writeback.md) |
| DirectoryExtensions |[Azure AD Connect synkronisering: Katalog tillägg](how-to-connect-sync-feature-directory-extensions.md) |
| [DuplicateProxyAddressResiliency <br/> DuplicateUPNResiliency](#duplicate-attribute-resiliency) |Tillåter att ett attribut placeras i karantän när det är en dubblett av ett annat objekt, i stället för att hela objektet skickas under exporten. |
| Hash-synkronisering av lösenord |[Implementera hash-synkronisering av lösen ord med Azure AD Connect Sync](how-to-connect-password-hash-synchronization.md) |
|Direktautentisering|[Användarinloggning med Azure Active Directory-direktautentisering](how-to-connect-pta.md)|
| UnifiedGroupWriteback |Tillbakaskrivning av grupp|
| UserWriteback |Stöds inte för närvarande. |

## <a name="duplicate-attribute-resiliency"></a>Återhämtning av duplicerat attribut

I stället för att inte etablera objekt med duplicerade UPN/proxyAddresses, är det duplicerade attributet "i karantän" och ett tillfälligt värde tilldelas. När konflikten är löst ändras det tillfälliga UPN-värdet automatiskt till rätt värde. Mer information finns i [Identitetssynkronisering och duplicerat attribut återhämtning](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>UserPrincipalName, mjuk matchning

När den här funktionen är aktive rad är mjuk matchning aktiverat för UPN förutom den [primära SMTP-adressen](https://support.microsoft.com/kb/2641663), som alltid är aktive rad. Mjuk matchning används för att matcha befintliga moln användare i Azure AD med lokala användare.

Den här funktionen är användbar om du behöver matcha lokala AD-konton med befintliga konton som skapats i molnet och du inte använder Exchange Online. I det här scenariot har du vanligt vis ingen anledning att ange attributet SMTP i molnet.

Den här funktionen är aktive ras som standard för nyligen skapade Azure AD-kataloger. Du kan se om den här funktionen är aktive rad för dig genom att köra:  

```powershell
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Om den här funktionen inte är aktive rad för din Azure AD-katalog kan du aktivera den genom att köra:  

```powershell
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>Synkronisera userPrincipalName-uppdateringar

Tidigare har uppdateringar av det UserPrincipalName-attribut som använder synkroniseringstjänsten från lokal plats blockerats, om inte båda villkoren var sanna:

* Användaren hanteras (icke-federerad).
* Användaren har inte tilldelats någon licens.

> [!NOTE]
> Från mars 2019 tillåts synkronisering av UPN-ändringar för federerade användar konton.
> 

Om du aktiverar den här funktionen kan Synkroniseringsmotorn uppdatera userPrincipalName när den ändras lokalt och du använder lösen ords-hash-synkronisering eller direktautentisering.

Den här funktionen är aktive ras som standard för nyligen skapade Azure AD-kataloger. Du kan se om den här funktionen är aktive rad för dig genom att köra:  

```powershell
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Om den här funktionen inte är aktive rad för din Azure AD-katalog kan du aktivera den genom att köra:  

```powershell
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

När du har aktiverat den här funktionen kommer befintliga userPrincipalName-värden att förbli i befintligt skick. Vid nästa ändring av userPrincipalName-attributet lokalt kommer den normala delta-synkroniseringen för användarna att uppdatera UPN.  

## <a name="see-also"></a>Se även

* [Azure AD Connect synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).