---
title: PowerShell för hantering av StorSimple-enheter
description: Lär dig hur du använder Windows PowerShell för StorSimple för att hantera din StorSimple-enhet.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/25/2021
ms.author: alkohli
ms.openlocfilehash: e41d2e531a051738a31325b4ea33961bfb39e7f9
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98808032"
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Använda Windows PowerShell för StorSimple för att administrera din enhet

## <a name="overview"></a>Översikt

Windows PowerShell för StorSimple tillhandahåller ett kommando rads gränssnitt som du kan använda för att hantera din Microsoft Azure StorSimple-enhet. Som namnet antyder är det ett Windows PowerShell-baserat kommando rads gränssnitt som är byggt i en begränsad körnings utrymme. Från användarens perspektiv på kommando raden visas ett begränsat körnings utrymme som en begränsad version av Windows PowerShell. Samtidigt som några av de grundläggande funktionerna i Windows PowerShell upprätthålls, har det här gränssnittet andra, dedikerade cmdlets som är kopplade till att hantera din Microsoft Azure StorSimple-enhet.

Den här artikeln beskriver Windows PowerShell för StorSimple funktioner, inklusive hur du kan ansluta till det här gränssnittet och innehåller länkar till stegvisa procedurer eller arbets flöden som du kan utföra med hjälp av det här gränssnittet. Arbets flödena innehåller anvisningar om hur du registrerar din enhet, konfigurerar nätverks gränssnittet på enheten, installerar uppdateringar som kräver att enheten är i underhålls läge, ändrar enhetens tillstånd och felsöker eventuella problem som kan uppstå.

När du har läst den här artikeln kommer du att kunna:

* Anslut till din StorSimple-enhet med hjälp av Windows PowerShell för StorSimple.
* Administrera din StorSimple-enhet med hjälp av Windows PowerShell för StorSimple.
* Få hjälp i Windows PowerShell för StorSimple.

> [!NOTE]
> * Med Windows PowerShell för StorSimple-cmdletar kan du hantera din StorSimple-enhet från en serie konsol eller via fjärr anslutning via Windows PowerShell-fjärrkommunikation. Mer information om var och en av de enskilda cmdletar som kan användas i det här gränssnittet finns i [cmdlet-referens för Windows PowerShell för StorSimple](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps&preserve-view=true).
> * De Azure PowerShell StorSimple-cmdletarna är en annan samling cmdletar som gör att du kan automatisera StorSimple service nivå-och migreringsåtgärder från kommando raden. Mer information om Azure PowerShell-cmdletar för StorSimple finns i [referens för Azure StorSimple-cmdlet](/powershell/module/servicemanagement/azure.service/?view=azuresmps-4.0.0&viewFallbackFrom=azuresmps-3.7.0&preserve-view=true#azure).


Du kan komma åt Windows PowerShell för StorSimple med någon av följande metoder:

* [Ansluta till StorSimple enhets serie konsol](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Fjärrans luta till StorSimple med Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Ansluta till Windows PowerShell för StorSimple via enhetens serie konsol

Du kan [Ladda ned SparaTillFil](https://www.putty.org/) eller liknande program för terminalemulering för att ansluta till Windows PowerShell för StorSimple. Du måste konfigurera SparaTillFil särskilt för att få åtkomst till den Microsoft Azure StorSimple enheten. Följande avsnitt innehåller detaljerade anvisningar om hur du konfigurerar SparaTillFil och ansluter till enheten. Olika meny alternativ i serie konsolen förklaras också.

### <a name="putty-settings"></a>PuTTY-inställningar

Se till att du använder följande inställningar för SparaTillFil för att ansluta till Windows PowerShell-gränssnittet från serie konsolen.

#### <a name="to-configure-putty"></a>Så här konfigurerar du SparaTillFil

1. Välj **tangent bord** i rutan **kategori** i dialog rutan SparaTillFil- **omkonfiguration** .
2. Se till att följande alternativ (standardinställningarna när du startar en ny session) är markerade.
   
   | Tangent bords objekt | Välj |
   | --- | --- |
   | Backsteg-nyckel |Kontroll –? (127) |
   | Home-och end-nycklar |Standard |
   | Funktions tangenter och knapps ATS |ESC [n ~ |
   | Ursprungligt tillstånd för markör nycklar |Normal |
   | Ursprungligt tillstånd för numeriskt tangent bord |Normal |
   | Aktivera extra tangent bords funktioner |Control-Alt skiljer sig från AltGr |
   
    ![Inställningar för SparaTillFil som stöds](./media/storsimple-windows-powershell-administration/IC740877.png)
3. Klicka på **Applicera**.
4. I fönstret **kategori** väljer du **översättning**.
5. Välj **UTF-8** i list rutan **teckenuppsättning för fjärrobjekt** .
6. Under **hantering av linje ritnings tecken** väljer du **Använd Unicode-linje ritning kod punkter**. Följande skärm bild visar rätt val av SparaTillFil.
   
    ![UTF-inställningar](./media/storsimple-windows-powershell-administration/IC740878.png)
7. Klicka på **Applicera**.

Du kan nu använda SparaTillFil för att ansluta till enhetens serie konsol genom att utföra följande steg.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>Om serie konsolen

När du använder Windows PowerShell-gränssnittet för din StorSimple-enhet via serie konsolen visas ett informations meddelande, följt av meny alternativ.

Informations meddelandet innehåller grundläggande information om StorSimple-enheten, till exempel modell, namn, installerad program version och status för den kontroll enhet som du ansluter till. Följande bild visar ett exempel på ett informations meddelande.

![Serie informations meddelande](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> Du kan använda informations meddelandet för att identifiera om den enhet som du är ansluten till är _aktiv_ eller _passiv_.

Följande bild visar de olika körnings utrymme-alternativ som är tillgängliga i menyn för serie konsolen.

![Registrera din enhet 2](./media/storsimple-windows-powershell-administration/IC740906.png)

Du kan välja bland följande inställningar:

1. **Logga in med fullständig åtkomst.**
   Med det här alternativet kan du ansluta (med rätt autentiseringsuppgifter) till **SSAdminConsole** -körnings utrymme på den lokala styrenheten. (Den lokala styrenheten är den kontroll enhet som du för närvarande använder via serie konsolen för din StorSimple-enhet.) Det här alternativet kan också användas för att ge Microsoft Support åtkomst till obegränsade körnings utrymme (en supportbegäran) för att felsöka eventuella enhets problem. När du har använt alternativ 1 för att logga in kan du låta Microsoft Support-teknikern komma åt obegränsade körnings utrymme genom att köra en angiven cmdlet. Mer information finns i [starta en support-session](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).
   
2. **Logga in på peer-styrenhet med fullständig åtkomst.**
   Det här alternativet är detsamma som alternativ 1, förutom att du kan ansluta (med rätt autentiseringsuppgifter) till **SSAdminConsole** -körnings utrymme på peer-styrenheten. Eftersom StorSimple-enheten är en enhet med hög tillgänglighet med två kontrollanter i en aktiv-passiv konfiguration, refererar peer till den andra kontrollanten i enheten som du ansluter till via serie konsolen).
   I likhet med alternativ 1 kan det här alternativet också användas för att ge Microsoft Support åtkomst till obegränsade körnings utrymme på en peer-styrenhet.

3. **Anslut med begränsad åtkomst.**
   Det här alternativet används för att få åtkomst till Windows PowerShell-gränssnittet i begränsat läge. Du uppmanas inte att ange autentiseringsuppgifter. Det här alternativet ansluter till en mer begränsad körnings utrymme jämfört med alternativ 1 och 2.  Några av de uppgifter som är tillgängliga via alternativ 1 som *inte kan* utföras i den här körnings utrymme är:
   
   * Återställ fabriks inställningarna
   * Ändra lösen ordet
   * Aktivera eller inaktivera support åtkomst
   * Tillämpa uppdateringar
   * Installera snabb korrigeringar

     > [!NOTE]
     > Detta är det bästa alternativet om du har glömt enhetens administratörs lösen ord och inte kan ansluta via alternativ 1 eller 2.

4. **Ändra språk.**
   Med det här alternativet kan du ändra visnings språket för Windows PowerShell-gränssnittet. De språk som stöds är engelska, japanska, ryska, franska, södra koreanska, spanska, italienska, tyska, kinesiska och portugisiska.

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Fjärrans luta till StorSimple med hjälp av Windows PowerShell för StorSimple

Du kan använda Windows PowerShell-fjärrkommunikation för att ansluta till din StorSimple-enhet. När du ansluter på det här sättet visas ingen meny. (Du ser bara en meny om du använder serie konsolen på enheten för att ansluta. Fjärr anslutning tar dig direkt till motsvarigheten till "alternativ 1 – fullständig åtkomst" i serie konsolen.) Med Windows PowerShell-fjärrkommunikation ansluter du till en speciell körnings utrymme. Du kan också ange visnings språk.

Visnings språket är oberoende av språket som du anger med hjälp av alternativet **ändra språk** i menyn för serie konsolen. Fjärrpowershell hämtar automatiskt de nationella inställningarna för enheten som du ansluter från om ingen har angetts.

> [!NOTE]
> Om du arbetar med Microsoft Azure virtuella värdar och StorSimple-moln enheter kan du använda Windows PowerShell-fjärrkommunikation och den virtuella värden för att ansluta till moln installationen. Om du har konfigurerat en resurs plats på den värd där du vill spara information från Windows PowerShell-sessionen, bör du vara medveten om att _alla_ huvud konton endast innehåller autentiserade användare. Om du har konfigurerat delningen för att tillåta åtkomst av _alla_ och du ansluter utan att ange autentiseringsuppgifter, används det oautentiserade anonyma objektet och du får ett fel meddelande. För att åtgärda det här problemet måste du aktivera gäst kontot på resurs värden och sedan ge gäst kontot fullständig åtkomst till resursen, eller så måste du ange giltiga autentiseringsuppgifter tillsammans med Windows PowerShell-cmdleten.


Du kan använda HTTP eller HTTPS för att ansluta via Windows PowerShell-fjärrkommunikation. Följ anvisningarna i följande Självstudier:

* [Fjärrans luta via HTTP](storsimple-8000-remote-connect.md#connect-through-http)
* [Fjärrans luta via HTTPS](storsimple-8000-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Säkerhets överväganden för anslutning

När du bestämmer dig för att ansluta till Windows PowerShell för StorSimple bör du tänka på följande faktorer:

* Anslutning direkt till enhetens serie konsol är säker, men det går inte att ansluta till serie konsolen över nätverks växlar. Var försiktig med säkerhets risken när du ansluter till enhets serie över nätverks växlar.
* Att ansluta via en HTTP-session kan erbjuda mer säkerhet än att ansluta via serie konsolen över nätverket. Även om en HTTP-session inte är den säkraste anslutnings metoden, är den acceptabel för betrodda nätverk.
* Anslutning via en HTTPS-session är den säkraste och rekommenderade alternativet.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Administrera din StorSimple-enhet med hjälp av Windows PowerShell för StorSimple

I följande tabell visas en översikt över alla vanliga hanterings uppgifter och komplexa arbets flöden som kan utföras i Windows PowerShell-gränssnittet på din StorSimple-enhet. Klicka på lämplig post i tabellen om du vill ha mer information om varje arbets flöde.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Windows PowerShell för StorSimple arbets flöden

| Om du vill göra det här... | Använd den här proceduren. |
| --- | --- |
| Registrera din enhet |[Konfigurera och registrera enheten med hjälp av Windows PowerShell för StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| Konfigurera webbproxy</br>Visa webbproxy-inställningar |[Konfigurera webbproxy för din StorSimple-enhet](storsimple-8000-configure-web-proxy.md) |
| Ändra inställningarna för nätverks gränssnittet på enheten för DATA 0 |[Ändra nätverks gränssnittet för DATA 0 för din StorSimple-enhet](storsimple-8000-modify-data-0.md) |
| Stoppa en kontroll enhet </br> Starta om eller stänga av en styrenhet </br> Stänga av en enhet</br>Återställa enheten till fabriksinställningarna |[Hantera enhets styrenheter](storsimple-8000-manage-device-controller.md) |
| Installera uppdateringar och snabb korrigeringar för underhålls läge |[Uppdatera enheten](storsimple-update-device.md) |
| Ange underhålls läge </br>Avsluta underhålls läge |[StorSimple enhets lägen](storsimple-8000-device-modes.md) |
| Skapa ett support paket</br>Dekryptera och redigera ett support paket |[Skapa och hantera ett support paket](storsimple-8000-create-manage-support-package.md) |
| Starta en support-session</br> |[Starta en support-session i Windows PowerShell för StorSimple](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Få hjälp i Windows PowerShell för StorSimple

I Windows PowerShell för StorSimple är cmdlet-hjälpen tillgänglig. En online-uppdaterad version av den här hjälpen är också tillgänglig, som du kan använda för att uppdatera hjälpen i systemet.

Att få hjälp i det här gränssnittet liknar att få hjälp i Windows PowerShell, och de flesta av de Help-relaterade cmdletarna fungerar. Du kan hitta hjälp för Windows PowerShell online: [Microsoft. PowerShell. Core](/powershell/module/Microsoft.PowerShell.Core/).

<!--The following is a brief description of the types of Help for this Windows PowerShell interface, including how to update the Help. - OK to remove? Transition not needed.-->

### <a name="to-get-help-for-a-cmdlet"></a>För att få hjälp med en cmdlet

* Använd följande kommando för att få hjälp med en cmdlet eller funktion: `Get-Help <cmdlet-name>`
* Använd föregående cmdlet med parametern för att få onlinehjälp för alla cmdlet: ar `-Online` : `Get-Help <cmdlet-name> -Online`
* För fullständig hjälp kan du använda- `–Full` parametern, och till exempel använder du `–Examples` parametern.

### <a name="to-update-help"></a>Så här uppdaterar du hjälpen

Du kan enkelt uppdatera hjälpen i Windows PowerShell-gränssnittet. Utför följande steg för att uppdatera hjälpen i systemet.

#### <a name="to-update-cmdlet-help"></a>Så här uppdaterar du cmdlet-hjälpen
1. Starta Windows PowerShell med alternativet **Kör som administratör** .
2. Skriv följande i kommando tolken:  `Update-Help`
3. De uppdaterade hjälpfilerna kommer att installeras.
4. När hjälpfilerna har installerats skriver `Get-Help Get-Command` du: om du vill visa en lista över cmdletar för vilka hjälpen är tillgänglig.

> [!NOTE]
> Om du vill hämta en lista över alla tillgängliga cmdlets i en körnings utrymme loggar du in på motsvarande meny alternativ och kör `Get-Command` cmdleten.


## <a name="next-steps"></a>Nästa steg

Om du får problem med din StorSimple-enhet när du utför något av ovanstående arbets flöden, se [verktyg för fel sökning av StorSimple-distributioner](storsimple-8000-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).