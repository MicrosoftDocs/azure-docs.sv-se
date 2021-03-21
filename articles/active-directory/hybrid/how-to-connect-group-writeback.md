---
title: 'Azure AD Connect: tillbakaskrivning av grupp'
description: I den här artikeln beskrivs tillbakaskrivning av en grupp i Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 06/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c506c87ad5901754175f18e6b50bc6ed46a3c19
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98246918"
---
# <a name="azure-ad-connect-group-writeback"></a>Tillbakaskrivning av Azure AD Connects grupp

Med tillbakaskrivning av grupper kan kunderna utnyttja moln grupper för sina hybrid behov. Om du använder funktionen Microsoft 365 grupper kan du låta dessa grupper visas i din lokala Active Directory. Det här alternativet är **bara** tillgängligt om du har Exchange installerat i din lokala Active Directory.

## <a name="pre-requisites"></a>Förutsättningar
Följande förutsättningar måste vara uppfyllda för att det ska gå att aktivera tillbakaskrivning av gruppen.
- Azure Active Directory Premium licenser för din klient.
- En konfigurerad hybrid distribution mellan din lokala Exchange-organisation och Microsoft 365 och verifierade att den fungerar korrekt.
- Installerat en version av Exchange lokalt som stöds
- Konfigurerad enkel inloggning med Azure Active Directory Connect 

## <a name="enable-group-writeback"></a>Aktivera tillbakaskrivning av grupper
Använd följande steg för att aktivera tillbakaskrivning av grupp:

1. Öppna guiden Azure AD Connect, Välj **Konfigurera** och klicka sedan på **Nästa**.
2. Välj **Anpassa synkroniseringsalternativ** och klicka sedan på **Nästa**.
3. Ange dina autentiseringsuppgifter på sidan **Anslut till Azure AD** . Klicka på **Nästa**.
4. På sidan **valfria funktioner** kontrollerar du att de tidigare konfigurerade alternativen fortfarande är markerade.
5. Välj **tillbakaskrivning av grupp** och klicka sedan på **Nästa**.
6. På **sidan tillbakaskrivning** väljer du en Active Directory ORGANISATIONSENHET (OU) för att lagra objekt som synkroniseras från Microsoft 365 till din lokala organisation och klickar sedan på **Nästa**.
7. På sidan **klar** att konfigurera klickar du på **Konfigurera**.
8. När guiden är klar klickar du på **Avsluta** på sidan konfigurationen har slutförts.
9. Öppna Windows PowerShell som administratör på Azure Active Directory Connect-servern och kör följande kommandon.

```Powershell
$AzureADConnectSWritebackAccountDN =  <MSOL_ account DN>
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"

# To grant the <MSOL_account> permission to all domains in the forest:
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN $AzureADConnectSWritebackAccountDN

# To grant the <MSOL_account> permission to specific OU (eg. the OU chosen to writeback Office 365 Groups to):
$GroupWritebackOU = <DN of OU where groups are to be written back to>
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN $AzureADConnectSWritebackAccountDN -ADObjectDN $GroupWritebackOU
```

Mer information om hur du konfigurerar Microsoft 365 grupper finns i [konfigurera Microsoft 365 grupper med lokal Exchange hybrid](/exchange/hybrid-deployment/set-up-microsoft-365-groups#enable-group-writeback-in-azure-ad-connect).

## <a name="disabling-group-writeback"></a>Inaktiverar tillbakaskrivning av grupp
Gör så här om du vill inaktivera tillbakaskrivning av grupp: 


1. Starta guiden Azure Active Directory Connect och gå till sidan Ytterligare aktiviteter. Välj aktiviteten **Anpassa synkroniseringsalternativ** och klicka på **Nästa**.
2. Avmarkera tillbakaskrivning av grupp på sidan **valfria funktioner** .  Du får en varning om att du vet att grupper kommer att tas bort.  Klicka på **Ja**.
   >[!IMPORTANT]
   > Om du inaktiverar tillbakaskrivning av grupp kommer alla grupper som tidigare har skapats med den här funktionen att tas bort från din lokala Active Directory vid nästa synkronisering. 

   ![Avmarkera kryss rutan](media/how-to-connect-group-writeback/group2.png)
  
3. Klicka på **Nästa**.
4. Klicka på **Konfigurera**.

 >[!NOTE]
 > Om du inaktiverar grupp-tillbakaskrivning anges alla flaggor för att importera och fullständiga synkroniseringar till "true" på Azure Active Directory-anslutningen, vilket leder till att regeln ändras till att spridas genom nästa synkronisering och ta bort de grupper som tidigare skrevs tillbaka till din Active Directory.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
