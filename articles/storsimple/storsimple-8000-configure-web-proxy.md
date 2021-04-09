---
title: Konfigurera webbproxy för StorSimple 8000-seriens enhet | Microsoft Docs
description: Lär dig hur du använder Windows PowerShell för StorSimple för att konfigurera webbproxy-inställningar för din StorSimple-enhet.
services: storsimple
documentationcenter: ''
author: alkohli
manager: ''
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: alkohli
ms.openlocfilehash: 48671e7558ea1bd613d33372c96fa3c563407e81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017227"
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>Konfigurera webbproxy för din StorSimple-enhet

## <a name="overview"></a>Översikt

I den här självstudien beskrivs hur du använder Windows PowerShell för StorSimple för att konfigurera och Visa inställningar för webbproxy för din StorSimple-enhet. Webbproxy-inställningarna används av StorSimple-enheten vid kommunikation med molnet. En webbproxyserver används för att lägga till ytterligare ett lager med säkerhet, filtrera innehåll, cache för att under lätta bandbredds kraven eller till och med analys.

Vägledningen i den här självstudien gäller endast för fysiska enheter i StorSimple 8000-serien. Webbproxy-konfiguration stöds inte på StorSimple Cloud Appliance (8010 och 8020).

Web Proxy är en _valfri_ konfiguration för din StorSimple-enhet. Du kan bara konfigurera webbproxy via Windows PowerShell för StorSimple. Konfigurationen är en två stegs process på följande sätt:

1. Du måste först konfigurera webbproxyinställningar via installations guiden eller Windows PowerShell för StorSimple-cmdletar.
2. Sedan aktiverar du de konfigurerade webbproxy-inställningarna via Windows PowerShell för StorSimple-cmdletar.

När webbproxy-konfigurationen är klar kan du Visa de konfigurerade webbproxyinställningarna i både tjänsten Microsoft Azure StorSimple Enhetshanteraren och Windows PowerShell för StorSimple.

När du har läst den här självstudien kommer du att kunna:

* Konfigurera webbproxy med hjälp av installations guiden och cmdletar.
* Aktivera webbproxy med hjälp av-cmdletar.
* Visa webbproxy-inställningar i Azure Portal.
* Felsök fel under webbproxy-konfigurationen.


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Konfigurera webbproxy via Windows PowerShell för StorSimple

Du kan använda något av följande för att konfigurera inställningar för webbproxy:

* Installations guiden hjälper dig att följa konfigurations stegen.
* Cmdletar i Windows PowerShell för StorSimple.

Var och en av dessa metoder beskrivs i följande avsnitt.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Konfigurera webbproxy via installations guiden

Använd installations guiden för att vägleda dig genom stegen för webbproxy-konfiguration. Utför följande steg för att konfigurera webbproxy på enheten.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Konfigurera webbproxy via installations guiden

1. I menyn serie konsol väljer du alternativ 1, **loggar in med fullständig åtkomst** och anger **enhetens administratörs lösen ord**. Skriv följande kommando för att starta en session med installations guiden:
   
    `Invoke-HcsSetupWizard`
2. Om det här är första gången du har använt installations guiden för enhets registrering måste du konfigurera alla nödvändiga nätverks inställningar tills du når webbproxy-konfigurationen. Om enheten redan har registrerats accepterar du alla konfigurerade nätverks inställningar tills du når webbproxy-konfigurationen. Skriv **Ja** i installations guiden när du uppmanas att konfigurera webbproxy-inställningar.
3. För **Webbproxyserverns URL** anger du IP-adressen eller det fullständigt kvalificerade domän namnet (FQDN) för webbproxyservern och det TCP-portnummer som du vill att enheten ska använda vid kommunikation med molnet. Använd följande format:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    TCP-portnummer 8080 anges som standard.
4. Välj autentiseringstyp som **NTLM**, **Basic** eller **none**. Basic är den minst säkra autentiseringen för konfigurationen av proxyservern. NT LAN Manager (NTLM) är ett mycket säkert och komplext autentiseringsprotokoll som använder ett 3-vägs meddelande system (ibland fyra om ytterligare integritet krävs) för att autentisera en användare. Standardautentiseringen är NTLM. Mer information finns i [grundläggande](https://hc.apache.org/httpclient-3.x/authentication.html) och [NTLM-autentisering](https://hc.apache.org/httpclient-3.x/authentication.html). 
   
   > [!IMPORTANT]
   > **I StorSimple Enhetshanteraren-tjänsten fungerar inte enhets övervaknings diagrammen när Basic-eller NTLM-autentisering är aktiverat i enhetens konfiguration för proxyservern. För att övervaknings diagrammen ska fungera måste autentiseringen vara inställd på ingen.**
  
5. Om du har aktiverat autentiseringen anger du ett **användar namn för webbproxy** och ett **webbproxy-lösenord**. Du måste också bekräfta lösen ordet.
   
    ![Konfigurera webbproxy på StorSimple-Device1](./media/storsimple-configure-web-proxy/IC751830.png)

Om du registrerar din enhet för första gången fortsätter du med registreringen. Om din enhet redan har registrerats avslutas guiden. De konfigurerade inställningarna sparas.

Web Proxy är nu aktiverat. Du kan hoppa över steget [Aktivera webbproxy](#enable-web-proxy) och gå direkt till [Visa webbproxyinställningar i Azure Portal](#view-web-proxy-settings-in-the-azure-portal).

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Konfigurera webbproxy via Windows PowerShell för StorSimple-cmdletar

Ett annat sätt att konfigurera webbproxy-inställningar är via Windows PowerShell för StorSimple-cmdletar. Konfigurera webbproxy genom att utföra följande steg.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Konfigurera webbproxy via-cmdletar
1. I menyn serie konsol väljer du alternativ 1, **loggar in med fullständig åtkomst**. Ange **enhetens administratörs lösen ord** när du uppmanas att göra det. Standard lösen ordet är `Password1` .
2. Skriv följande i kommandotolken:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Ange och bekräfta lösen ordet när du uppmanas till det.
   
    ![Konfigurera webbproxy på StorSimple-Device3](./media/storsimple-configure-web-proxy/IC751831.png)

Webbproxyn har nu kon figurer ATS och måste aktive ras.

## <a name="enable-web-proxy"></a>Aktivera webbproxy

Web Proxy är inaktive rad som standard. När du har konfigurerat webbproxyinställningarna på din StorSimple-enhet använder du Windows PowerShell för StorSimple för att aktivera webbproxyinställningarna.

> [!NOTE]
> **Det här steget krävs inte om du använde installations guiden för att konfigurera webbproxy. Webbproxy aktive ras automatiskt som standard efter en installations guide-session.**


Utför följande steg i Windows PowerShell för StorSimple för att aktivera webbproxy på enheten:

#### <a name="to-enable-web-proxy"></a>Så här aktiverar du webbproxy
1. I menyn serie konsol väljer du alternativ 1, **loggar in med fullständig åtkomst**. Ange **enhetens administratörs lösen ord** när du uppmanas att göra det. Standard lösen ordet är `Password1` .
2. Skriv följande i kommandotolken:
   
    `Enable-HcsWebProxy`
   
    Du har nu aktiverat webbproxy-konfigurationen på din StorSimple-enhet.
   
    ![Konfigurera webbproxy på StorSimple-Device4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-portal"></a>Visa webbproxy-inställningar i Azure Portal

Webbproxy-inställningarna konfigureras via Windows PowerShell-gränssnittet och kan inte ändras från portalen. Du kan dock visa de här konfigurerade inställningarna i portalen. Utför följande steg för att Visa webbproxy.

#### <a name="to-view-web-proxy-settings"></a>Visa webbproxy-inställningar
1. Gå till **StorSimple Enhetshanteraren tjänst > enheter**. Markera och klicka på en enhet och gå sedan till **enhets inställningar > nätverk**.

    ![Klicka på nätverk](./media/storsimple-8000-configure-web-proxy/view-web-proxy-1.png)

2. Klicka på panelen **webbproxy** i bladet **nätverks inställningar** .

    ![Klicka på webbproxy](./media/storsimple-8000-configure-web-proxy/view-web-proxy-2.png)

3. Granska de konfigurerade webbproxyinställningarna på din StorSimple-enhet på bladet **webbproxy** .
   
    ![Visa webbproxy-inställningar](./media/storsimple-8000-configure-web-proxy/view-web-proxy-3.png)


## <a name="errors-during-web-proxy-configuration"></a>Fel under webbproxy-konfiguration

Om webbproxy-inställningarna är felaktigt konfigurerade visas fel meddelanden för användaren i Windows PowerShell för StorSimple. I följande tabell förklaras några av de här fel meddelandena, deras sannolika orsaker och rekommenderade åtgärder.

| Serie nummer | HRESULT-felkod | Möjlig rotor saken | Rekommenderad åtgärd |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |Kommandot körs från den passiva styrenheten och kan inte kommunicera med den aktiva styrenheten. |Kör kommandot på den aktiva kontroll enheten. Om du vill köra kommandot från den passiva styrenheten måste du åtgärda anslutningen från passiv till aktiv kontroll enhet. Du måste engagera Microsoft Support om den här anslutningen är bruten. |
| 2. |0x800710dd-åtgärds-ID: n är inte giltig |Proxyinställningar stöds inte på StorSimple Cloud Appliance. |Proxyinställningar stöds inte på StorSimple Cloud Appliance. Dessa kan bara konfigureras på en fysisk StorSimple-enhet. |
| 3. |0x80070057-ogiltig parameter |En av de parametrar som angavs för proxyinställningarna är inte giltig. |URI: n har inte angetts i rätt format. Använd följande format: `http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706ba – RPC-servern är inte tillgänglig |Rotor saken är något av följande:</br></br>Klustret är inte igång. </br></br>Datapath-tjänsten körs inte.</br></br>Kommandot körs från passiv styrenhet och kan inte kommunicera med den aktiva styrenheten. |Engagera Microsoft Support för att säkerställa att klustret är igång och att Datapath-tjänsten körs.</br></br>Kör kommandot från den aktiva kontroll enheten. Om du vill köra kommandot från den passiva styrenheten måste du se till att den passiva styrenheten kan kommunicera med den aktiva styrenheten. Du måste engagera Microsoft Support om den här anslutningen är bruten. |
| 5. |0x800706be-RPC-anropet misslyckades |Klustret är nere. |Engagera Microsoft Support för att säkerställa att klustret är igång. |
| 6. |0x8007138f-kluster resursen hittades inte |Det gick inte att hitta plattforms tjänstens kluster resurs. Detta kan inträffa när installationen inte var korrekt. |Du kan behöva göra en fabriks återställning på enheten. Du kan behöva skapa en plattforms resurs. Kontakta Microsoft-supporten om du vill ha hjälp. |
| 7. |0x8007138c-kluster resurs inte online |Plattforms-eller Datapath kluster resurser är inte online. |Kontakta Microsoft Support för att se till att Datapath-och plattforms tjänst resursen är online. |

> [!NOTE]
> * Listan över fel meddelanden är inte fullständig.
> * Fel som rör webbproxyinställningar visas inte i Azure Portal i Enhetshanterarens tjänsten för StorSimple. Om det uppstår ett problem med webbproxy när konfigurationen har slutförts ändras enhetens status till **offline** i den klassiska portalen. |

## <a name="next-steps"></a>Nästa steg
* Om du får problem när du distribuerar enheten eller konfigurerar webbproxyinställningar, se [fel sökning av distribution av StorSimple-enheter](./storsimple-8000-troubleshoot-deployment.md).
* Om du vill lära dig hur du använder StorSimple Enhetshanteraren-tjänsten går du till [använda StorSimple Enhetshanteraren-tjänsten för att administrera StorSimple-enheten](storsimple-8000-manager-service-administration.md).