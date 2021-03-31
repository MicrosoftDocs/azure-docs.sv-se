---
title: 'Azure AD Connect: aktiverar tillbakaskrivning av enhet | Microsoft Docs'
description: Det här dokumentet innehåller information om hur du aktiverar tillbakaskrivning av enheter med Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: curtand
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/08/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d526394ac89e2d29b2002004736e8480bb15b954
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95973430"
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect: aktiverar tillbakaskrivning av enhet
> [!NOTE]
> En prenumeration på Azure AD Premium krävs för tillbakaskrivning av enhet.
> 
> 

Följande dokumentation innehåller information om hur du aktiverar funktionen tillbakaskrivning av enhet i Azure AD Connect. Tillbakaskrivning av enhet används i följande scenarier:

* Aktivera [Windows Hello för företag med distribution av hybrid certifikats förtroende](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust-prereqs#device-registration)
* Aktivera villkorlig åtkomst baserat på enheter till ADFS (2012 R2 eller senare) skyddade program (förtroenden för förlitande part).

Detta ger ytterligare säkerhet och garantier för att åtkomst till program endast beviljas till betrodda enheter. Mer information om villkorlig åtkomst finns i [hantera risker med villkorlig åtkomst](../conditional-access/overview.md) och [Konfigurera lokal villkorlig åtkomst med hjälp av Azure Active Directory Device Registration](../devices/overview.md).

> [!IMPORTANT]
> <li>Enheter måste finnas i samma skog som användarna. Eftersom enheter måste skrivas tillbaka till en enda skog, stöder den här funktionen för närvarande inte en distribution med flera användar skogar.</li>
> <li>Det går bara att lägga till ett konfigurations objekt för enhets registrering i den lokala Active Directory skogen. Den här funktionen är inte kompatibel med en topologi där den lokala Active Directory synkroniseras till flera Azure AD-kataloger.</li>

## <a name="part-1-install-azure-ad-connect"></a>Del 1: installera Azure AD Connect
Installera Azure AD Connect med anpassade eller Express inställningar. Microsoft rekommenderar att du börjar med att alla användare och grupper har synkroniserats innan du aktiverar tillbakaskrivning av enheten.

## <a name="part-2-enable-device-writeback-in-azure-ad-connect"></a>Del 2: Aktivera tillbakaskrivning av enhet i Azure AD Connect
1. Kör installations guiden igen. Välj **Konfigurera enhets alternativ** på sidan Ytterligare aktiviteter och klicka på **Nästa**. 

    ![Konfigurera enhets alternativ](./media/how-to-connect-device-writeback/deviceoptions.png)

    >[!NOTE]
    > De nya alternativen för att konfigurera enheten är bara tillgängliga i version 1.1.819.0 och senare.

2. På sidan enhets alternativ väljer du **Konfigurera tillbakaskrivning av enhet**. Alternativet för att **inaktivera tillbakaskrivning av enhet** är inte tillgängligt förrän tillbakaskrivning av enheten Aktiver ATS. Klicka på **Nästa** för att gå till nästa sida i guiden.
    ![Välj enhets åtgärd](./media/how-to-connect-device-writeback/configuredevicewriteback1.png)

3. På sidan tillbakaskrivning visas den angivna domänen som standardskog för tillbakaskrivning av enheter.
   ![Anpassad installation av tillbakaskrivning av enhets mål skog](./media/how-to-connect-device-writeback/writebackforest.png)

4. Sidan **enhets behållare** ger möjlighet att förbereda Active Directory genom att använda något av de två tillgängliga alternativen:

    a. **Ange autentiseringsuppgifter för företags administratör**: om företags administratörens autentiseringsuppgifter har angetts för den skog där enheterna behöver skrivas tillbaka, så förbereder Azure AD Connect skogen automatiskt under konfigurationen av tillbakaskrivning av enhet.

    b. **Ladda ned PowerShell-skript**: Azure AD Connect automatiskt genererar ett PowerShell-skript som kan förbereda Active Directory för tillbakaskrivning av enheter. Om autentiseringsuppgifter för företags administratören inte kan tillhandahållas i Azure AD Connect, rekommenderar vi att du hämtar PowerShell-skriptet. Ange det hämtade PowerShell-skriptet **CreateDeviceContainer.ps1** till företags administratören för den skog där enheterna skrivs tillbaka till.
    ![Förbered Active Directory-skog](./media/how-to-connect-device-writeback/devicecontainercreds.png)
    
    Följande åtgärder utförs för att förbereda Active Directory-skogen:
    * Om de inte redan finns skapar och konfigurerar nya behållare och objekt under CN = enhets registrerings konfiguration, CN = Services, CN = Configuration, [skog-DN].
    * Om de inte redan finns skapar och konfigurerar nya behållare och objekt under CN = RegisteredDevices, [domän-DN]. Enhets objekt kommer att skapas i den här behållaren.
    * Ställer in nödvändiga behörigheter på Azure AD Connector-kontot för att hantera enheter på din Active Directory.
    * Behöver endast köras på en skog, även om Azure AD Connect installeras på flera skogar.

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Verifiera att enheterna är synkroniserade med Active Directory
Tillbakaskrivning av enhet bör nu fungera korrekt. Tänk på att det kan ta upp till 3 timmar för enhets objekt att skrivas tillbaka till AD.  Verifiera att enheterna synkroniseras korrekt genom att göra följande när reglerna för synkronisering har slutförts:

1. Starta Active Directory Administrationscenter.
2. Expandera RegisteredDevices i den domän som är federerad.

   ![Active Directory registrerade enheter i administrations Center](./media/how-to-connect-device-writeback/devicewriteback5.png)

3. Aktuella registrerade enheter visas där.

   ![Lista med registrerade enheter i Active Directory administrations Center](./media/how-to-connect-device-writeback/devicewriteback6.png)

## <a name="enable-conditional-access"></a>Aktivera villkorlig åtkomst
Detaljerade anvisningar för att aktivera det här scenariot finns i [Konfigurera lokal villkorlig åtkomst med hjälp av Azure Active Directory Device Registration](../devices/overview.md).

## <a name="troubleshooting"></a>Felsökning
### <a name="the-writeback-checkbox-is-still-disabled"></a>Kryss rutan tillbakaskrivning är fortfarande inaktive rad
Om kryss rutan för tillbakaskrivning av enhet inte är aktive rad trots att du har följt stegen ovan, kommer följande steg att vägleda dig genom hur installations guiden verifieras innan rutan aktive ras.

Första saker:

* Skogen där enheterna finns måste ha skogs schemat uppgraderat till Windows 2012 R2-nivån så att objektet enhet och associerade attribut finns.
* Om installations guiden redan körs kommer inga ändringar att identifieras. I det här fallet slutför du installations guiden och kör den igen.
* Kontrol lera att det konto som du anger i initierings skriptet faktiskt är rätt användare som används av Active Directory-anslutningen. Följ dessa steg om du vill kontrol lera detta:
  * Från Start-menyn öppnar du **synkroniseringstjänsten**.
  * Öppna fliken **anslutningar** .
  * Hitta kopplingen med typen Active Directory Domain Services och välj den.
  * Under **åtgärder** väljer du **Egenskaper**.
  * Gå till **Anslut till Active Directory skog**. Kontrol lera att den domän och det användar namn som anges på den här skärmen matchar det konto som angetts för skriptet.
    ![Anslutnings konto i Sync-Service Manager](./media/how-to-connect-device-writeback/connectoraccount.png)

Verifiera konfigurationen i Active Directory:

* Kontrol lera att registrerings tjänsten för enheten finns på platsen nedan (CN = DeviceRegistrationService, CN = Device Registration Services, CN = Device Registration Configuration, CN = Services, CN = Configuration) under konfiguration namngivnings kontext.

![Felsöka, DeviceRegistrationService i konfigurations namn område](./media/how-to-connect-device-writeback/troubleshoot1.png)

* Kontrol lera att det bara finns ett konfigurations objekt genom att söka i konfigurations namn rymden. Ta bort dubbletten om det finns fler än en.

![Felsöka, Sök efter de duplicerade objekten](./media/how-to-connect-device-writeback/troubleshoot2.png)

* På objektet Device Registration Service kontrollerar du att attributet msDS-DeviceLocation finns och har ett värde. Sök efter den här platsen och kontrol lera att den finns med objectType msDS-DeviceContainer.

![Felsöka, msDS-DeviceLocation](./media/how-to-connect-device-writeback/troubleshoot3.png)

![Felsöka, RegisteredDevices objekt klass](./media/how-to-connect-device-writeback/troubleshoot4.png)

* Kontrol lera att kontot som används av Active Directory Connector har nödvändig behörighet på den registrerade enhets behållaren som hittades i föregående steg. Detta är den förväntade behörigheten för den här behållaren:

![Felsöka, kontrol lera behörigheter för container](./media/how-to-connect-device-writeback/troubleshoot5.png)

* Kontrol lera att Active Directory kontot har behörigheter för CN = enhets registrerings konfiguration, CN = Services, CN = Configuration-objekt.

![Felsöka, verifiera behörigheter för enhets registrerings konfiguration](./media/how-to-connect-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Ytterligare information
* [Hantera risker med villkorlig åtkomst](../conditional-access/overview.md)
* [Konfigurera lokal villkorlig åtkomst med hjälp av Azure Active Directory Device Registration](../devices/overview.md)

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).