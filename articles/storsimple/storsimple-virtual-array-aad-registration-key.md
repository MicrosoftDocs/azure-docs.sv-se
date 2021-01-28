---
title: Ny autentisering för virtuella StorSimple-matriser
description: Lär dig om AAD-autentisering, den associerade nya tjänst registrerings nyckeln och ändringar av brand Väggs reglerna som är tillämpliga för StorSimple-enheterna.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 75332498ac59dc46a7a079eff4c25e02b2a6cb9b
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986945"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Använd den nya autentiseringen för din StorSimple

## <a name="overview"></a>Översikt

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Tjänsten StorSimple Enhetshanteraren körs i Microsoft Azure och ansluter till flera virtuella StorSimple-matriser. Till dags att StorSimple Enhetshanteraren tjänsten har använt en Access Control tjänst (ACS) för att autentisera tjänsten på StorSimple-enheten. ACS-mekanismen inkommer snart och ersätts av en Azure Active Directory (AAD)-autentisering.

Informationen i den här artikeln gäller endast virtuella matriser för StorSimple 1200-serien. I den här artikeln beskrivs information om AAD-autentiseringen och den associerade nya tjänst registrerings nyckeln och ändringar av brand Väggs reglerna som är tillämpliga för StorSimple-enheterna.

AAD-autentiseringen sker i StorSimple virtuella matriser (modell 1200) som kör uppdatering 1 eller senare.

På grund av introduktionen av AAD-autentisering sker ändringarna i:

- URL-mönster för brand Väggs regler.
- Tjänst registrerings nyckel.

Dessa ändringar beskrivs i detalj i följande avsnitt.

## <a name="url-changes-for-aad-authentication"></a>URL-ändringar för AAD-autentisering

För att säkerställa att tjänsten använder AAD-baserad autentisering måste alla användare inkludera de nya autentiserings-URL: erna i brand Väggs reglerna.

Om du använder StorSimple virtuell matris kontrollerar du att följande URL ingår i brand Väggs reglerna:

| URL-mönster                         | Moln | Komponent/funktion         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Azure, offentlig |AAD-autentiseringstjänst      |
| `https://login.microsoftonline.us` | US Government |AAD-autentiseringstjänst      |

En fullständig lista över URL-mönster för virtuella StorSimple-matriser finns i [URL-mönster för brand Väggs regler](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).

Om autentiserings-URL: en inte ingår i brand Väggs reglerna bortom utgångs datumet, ser användarna en kritisk varning om att deras StorSimple-enhet inte kunde autentiseras med tjänsten. Tjänsten kommer inte att kunna kommunicera med enheten. Om användarna ser den här aviseringen måste de inkludera den nya autentiserings-URL: en. Om du vill ha mer information om aviseringen går du till [använda aviseringar för att övervaka din StorSimple-enhet](storsimple-virtual-array-manage-alerts.md#networking-alerts).

## <a name="device-version-and-authentication-changes"></a>Enhets version och verifierings ändringar

Om du använder en virtuell StorSimple-matris kan du använda följande tabell för att avgöra vilken åtgärd du behöver utföra baserat på enhetens program varu version som du kör.

| Om enheten körs  | Vidta följande åtgärder                                    |
|----------------------------|--------------------------------------------------------------|
| Uppdatera 1,0 eller senare och är offline. <br> Du ser en varning om att URL: en inte är allowlisted.| 1. ändra brand Väggs reglerna så att de inkluderar autentiserings-URL: en. Se [URL: er för autentisering](#url-changes-for-aad-authentication). <br> 2. [Hämta registrerings nyckeln för AAD från tjänsten](#aad-based-registration-keys). <br> 3. Utför steg 1-5 för att [ansluta till Windows PowerShell-gränssnittet för den virtuella matrisen](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br> 4. Använd `Invoke-HcsReRegister` cmdlet för att registrera enheten via Windows PowerShell. Ange den nyckel du fick i föregående steg.|
| Uppdatera 1,0 eller senare och enheten är online.| Ingen åtgärd krävs.                                       |
| Uppdatering 0,6 eller tidigare och enheten är offline. | 1. [Hämta uppdatering 1,0 via katalog servern](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix).<br>2. [installera uppdatering 1,0 via det lokala webb gränssnittet](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix).<br>3. [Hämta registrerings nyckeln för AAD från tjänsten](#aad-based-registration-keys). <br>4. Utför steg 1-5 för att [ansluta till Windows PowerShell-gränssnittet för den virtuella matrisen](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br>5. Använd `Invoke-HcsReRegister` cmdleten för att registrera enheten via Windows PowerShell. Ange den nyckel du fick i föregående steg.|
| Uppdatering 0,6 eller tidigare och enheten är online | Ändra brand Väggs reglerna så att de inkluderar autentiserings-URL: en.<br> Installera uppdatering 1,0 via Azure Portal. |

## <a name="aad-based-registration-keys"></a>AAD-baserade registrerings nycklar

Från och med uppdatering 1,0 för virtuella StorSimple-matriser används nya AAD-baserade registrerings nycklar. Du använder registrerings nycklarna för att registrera din StorSimple-Enhetshanteraren-tjänst med enheten.

Du kan inte använda de nya registrerings nycklarna för AAD-tjänsten om du använder en virtuell StorSimple-matris som kör uppdatering 0,6 eller tidigare. Du måste återskapa tjänst registrerings nyckeln. När du återskapar nyckeln används den nya nyckeln för att registrera alla efterföljande enheter. Den gamla nyckeln är inte längre giltig.

- Den nya registrerings nyckeln för AAD upphör att gälla efter tre dagar.
- Registrerings nycklarna för AAD fungerar bara med StorSimple 1200-seriens virtuella matriser som kör uppdatering 1 eller senare. Registrerings nyckeln för AAD från en StorSimple 8000-serie enhet fungerar inte.
- Registrerings nycklarna för AAD är längre än motsvarande registrerings nycklar för ACS.

Utför följande steg för att skapa en registrerings nyckel för AAD-tjänsten.

#### <a name="to-generate-the-aad-service-registration-key"></a>Så här skapar du registrerings nyckeln för AAD-tjänsten

1. I **StorSimple Enhetshanteraren** går du till **hanterings &gt;** **nycklar**.
    
    ![Gå till nycklar](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. Klicka på **generera nyckel**.

    ![Klicka på återskapa](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Kopiera den nya nyckeln. Den äldre nyckeln fungerar inte längre.

    ![Bekräfta återskapande](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om hur du distribuerar [virtuell StorSimple-matris](storsimple-virtual-array-deploy1-portal-prep.md)
