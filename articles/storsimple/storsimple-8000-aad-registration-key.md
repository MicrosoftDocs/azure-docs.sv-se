---
title: Azure AD-autentisering för StorSimple 8000 i Enhetshanteraren
description: Förklarar hur du använder AAD-baserad autentisering för din tjänst, genererar en ny registrerings nyckel och utför manuell registrering av enheterna.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: b09d68e7859a787c05a2fc62294f081c4345ae08
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99805048"
---
# <a name="use-azure-active-directory-ad-authentication-for-your-storsimple"></a>Använd Azure Active Directory autentisering (AD) för din StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Översikt

Tjänsten StorSimple Enhetshanteraren körs i Microsoft Azure och ansluter till flera StorSimple-enheter. Till dags att StorSimple Enhetshanteraren tjänsten har använt en Access Control tjänst (ACS) för att autentisera tjänsten på StorSimple-enheten. ACS-mekanismen inkommer snart och ersätts av en Azure Active Directory (AAD)-autentisering. Mer information finns i följande meddelanden om föråldrad ACS och användning av AAD-autentisering.

- [Framtiden för Azure ACS är Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [Kommande ändringar i Microsoft Access Control Service](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

I den här artikeln beskrivs information om AAD-autentiseringen och den associerade nya tjänst registrerings nyckeln och ändringar av brand Väggs reglerna som är tillämpliga för StorSimple-enheterna. Informationen i den här artikeln gäller endast StorSimple 8000-seriens enheter.

AAD-autentiseringen sker i StorSimple 8000-serien med uppdatering 5 eller senare. På grund av introduktionen av AAD-autentisering sker ändringarna i:

- URL-mönster för brand Väggs regler.
- Tjänst registrerings nyckel.

Dessa ändringar beskrivs i detalj i följande avsnitt.

## <a name="url-changes-for-aad-authentication"></a>URL-ändringar för AAD-autentisering

För att säkerställa att tjänsten använder AAD-baserad autentisering måste alla användare inkludera de nya autentiserings-URL: erna i brand Väggs reglerna.

Om du använder StorSimple 8000-serien ser du till att följande URL ingår i brand Väggs reglerna:

| URL-mönster                         | Moln | Komponent/funktion         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Azure, offentlig |AAD-autentiseringstjänst      |
| `https://login.microsoftonline.us` | US Government |AAD-autentiseringstjänst      |

En fullständig lista över URL-mönster för StorSimple 8000-serien finns i [URL-mönster för brand Väggs regler](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules).

Om URL: en för autentisering inte ingår i brand Väggs reglerna bortom utgångs datumet och enheten kör uppdatering 5, ser användarna en URL-avisering. Användarna måste ta med den nya autentiserings-URL: en. Om enheten kör en version före uppdatering 5 ser användarna en pulsslags avisering. I varje fall kan StorSimple-enheten inte autentisera med tjänsten och tjänsten kan inte kommunicera med enheten.

## <a name="device-version-and-authentication-changes"></a>Enhets version och verifierings ändringar

Om du använder en StorSimple 8000-serien het använder du följande tabell för att avgöra vilken åtgärd du behöver utföra baserat på enhetens program varu version som du kör.

| Om enheten körs| Vidta följande åtgärder                                    |
|--------------------------|------------------------|
| Uppdatering 5 eller senare och enheten är offline. <br> Du ser en varning om att URL: en inte är godkänd.|1. ändra brand Väggs reglerna så att de inkluderar autentiserings-URL: en. Se [URL: er för autentisering](#url-changes-for-aad-authentication).<br>2. [Hämta registrerings nyckeln för AAD från tjänsten](#aad-based-registration-keys).<br>3. [Anslut till Windows PowerShell-gränssnittet för enheten StorSimple 8000-serien](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).<br>4. Använd `Redo-DeviceRegistration` cmdlet för att registrera enheten via Windows PowerShell. Ange den nyckel du fick i föregående steg.|
| Uppdatera 5 eller senare och enheten online.| Ingen åtgärd krävs.                                       |
| Uppdatering 4 eller tidigare och enheten är offline. |1. ändra brand Väggs reglerna så att de inkluderar autentiserings-URL: en.<br>2. [Ladda ned uppdatering 5 till katalog servern](storsimple-8000-install-update-5.md#download-updates-for-your-device).<br>3. [Använd uppdatering 5 via snabb korrigerings metoden](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix).<br>4. [Hämta registrerings nyckeln för AAD från tjänsten](#aad-based-registration-keys).<br>5. [Anslut till Windows PowerShell-gränssnittet för enheten StorSimple 8000-serien](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>6. Använd `Redo-DeviceRegistration` cmdleten för att registrera enheten via Windows PowerShell. Ange den nyckel du fick i föregående steg.|
| Uppdatering 4 eller tidigare och enheten är online. |Ändra brand Väggs reglerna så att de inkluderar autentiserings-URL: en.<br> Installera uppdatering 5 via Azure Portal.              |
| Fabriks återställning till en version före uppdatering 5.      |Portalen visar en AAD-baserad registrerings nyckel medan enheten kör äldre program vara. Följ stegen i föregående scenario när enheten kör uppdatering 4 eller tidigare.              |

## <a name="aad-based-registration-keys"></a>AAD-baserade registrerings nycklar

Från och med uppdatering 5 för enheter med StorSimple 8000-serien används nya AAD-baserade registrerings nycklar. Du använder registrerings nycklarna för att registrera din StorSimple-Enhetshanteraren-tjänst med enheten.

Du kan inte använda de nya registrerings nycklarna för AAD-tjänsten om du använder en StorSimple 8000-serie med uppdatering 4 eller tidigare (inklusive en äldre enhet som aktive ras nu).
I det här scenariot måste du återskapa tjänst registrerings nyckeln. När du återskapar nyckeln används den nya nyckeln för att registrera alla efterföljande enheter. Den gamla nyckeln är inte längre giltig.

- Den nya registrerings nyckeln för AAD upphör att gälla efter tre dagar.
- Registrerings nycklarna för AAD fungerar endast med StorSimple 8000-seriens enheter som kör uppdatering 5 eller senare.
- Registrerings nycklarna för AAD är längre än motsvarande registrerings nycklar för ACS.

Utför följande steg för att skapa en registrerings nyckel för AAD-tjänsten.

#### <a name="to-generate-the-aad-service-registration-key"></a>Så här skapar du registrerings nyckeln för AAD-tjänsten

1. I **StorSimple Enhetshanteraren** går du till **hanterings &gt;** **nycklar**. Du kan också söka efter _nycklar_ med hjälp av Sök fältet.
    
2. Klicka på **generera nyckel**.

    ![Klicka på återskapa](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Kopiera den nya nyckeln. Den äldre nyckeln fungerar inte längre.

    ![Bekräfta återskapande](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > Om du skapar en StorSimple Cloud Appliance på den tjänst som är registrerad på din StorSimple 8000-serie enhet genererar du inte en registrerings nyckel medan skapandet pågår. Vänta tills skapandet har slutförts och generera sedan registrerings nyckeln.

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du distribuerar [StorSimple 8000-serien het](storsimple-8000-deployment-walkthrough-u2.md).
