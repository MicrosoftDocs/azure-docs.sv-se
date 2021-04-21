---
title: Vad är Windows Virtual Desktop? – Azure
description: En översikt över Windows Virtual Desktop.
author: Heidilohr
ms.topic: overview
ms.date: 09/14/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: a6d98f827c39f973714441e308dcc4f2bd061c9b
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835586"
---
# <a name="what-is-windows-virtual-desktop"></a>Vad är Windows Virtual Desktop?

Windows Virtual Desktop är en tjänst för skrivbords- och appvirtualisering som körs i molnet.

Det här kan du göra när du kör Windows Virtual Desktop på Azure:

* Konfigurera en flersessionsdistribution Windows 10 som ger en fullständig Windows 10 med skalbarhet
* Virtualisera Microsoft 365 Apps för företag och optimera den för körning i virtuella scenarier med flera användare
* Förse virtuella Windows 7-datorer med kostnadsfria utökade säkerhetsuppdateringar
* Ta dina befintliga Fjärrskrivbordstjänster (RDS) och Windows Server-datorer och -appar till valfri dator
* Virtualisera både stationära datorer och appar
* Hantera Windows 10,Windows Server, Windows 7-datorer och Windows 7-appar med en enhetlig hanteringsupplevelse

## <a name="introductory-video"></a>Introduktionsvideo

Lär dig Windows Virtual Desktop, varför det är unikt och vad som är nytt i den här videon:

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Fler videor om Windows Virtual Desktop finns i [vår spellista](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev).

## <a name="key-capabilities"></a>De viktigaste funktionerna

Med Windows Virtual Desktop kan du konfigurera en skalbar och flexibel miljö:

* Skapa en fullständig skrivbordsvirtualiseringsmiljö i din Azure-prenumeration utan att behöva köra några ytterligare gateway-servrar.
* Publicera så många värdpooler som du behöver för att hantera dina olika arbetsbelastningar.
* Ta med din egen avbildning för produktionsarbetsbelastningar eller testa från Azure-galleriet.
* Minska kostnaderna med poolresurser med flera sessioner. Med den nya Windows 10 Enterprise-funktionen för flera sessioner som är exklusiv för Windows Virtual Desktop- och RDSH-rollen (Remote Desktop Session Host) på Windows Server kan du avsevärt minska antalet virtuella datorer och operativsystemkostnader samtidigt som du tillhandahåller samma resurser till dina användare.
* Ange individuellt ägarskap via personliga (beständiga) skrivbord.

Du kan distribuera och hantera virtuella skrivbord:

* Använd Azure Portal, Windows Virtual Desktop PowerShell och REST-gränssnitt för att konfigurera värdpoolerna, skapa appgrupper, tilldela användare och publicera resurser.
* Publicera fullständiga skrivbord eller enskilda fjärrappar från en enda värdpool, skapa enskilda appgrupper för olika uppsättningar av användare eller till och med tilldela användare till flera appgrupper för att minska antalet bilder.
* När du hanterar din miljö kan du använda inbyggd delegerad åtkomst för att tilldela roller och samla in diagnostik för att förstå olika konfigurations- eller användarfel.
* Använd den nya diagnostiktjänsten för att felsöka fel.
* Hantera endast avbildningen och virtuella datorer, inte infrastrukturen. Du behöver inte personligen hantera fjärrskrivbordsrollerna som du gör med Fjärrskrivbordstjänster, bara de virtuella datorerna i din Azure-prenumeration.

Du kan också tilldela och ansluta användare till dina virtuella skrivbord:

* När de har tilldelats kan användarna starta Windows Virtual Desktop klient för att ansluta användare till sina publicerade Windows-datorer och -program. Anslut från valfri enhet via antingen ett inbyggt program på enheten eller Windows Virtual Desktop HTML5-webbklienten.
* Upprätta användare säkert via omvända anslutningar till tjänsten, så att du aldrig behöver lämna några inkommande portar öppna.

## <a name="requirements"></a>Krav

Det finns några saker du behöver för att konfigurera Windows Virtual Desktop och ansluta användarna till sina Windows-datorer och -program.

Vi stöder följande operativsystem, så se [](https://azure.microsoft.com/pricing/details/virtual-desktop/) till att du har rätt licenser för dina användare baserat på skrivbordet och de appar som du planerar att distribuera:

|Operativsystem|Nödvändig licens|
|---|---|
|Windows 10 Enterprise flera sessioner eller Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F3, Business Premium<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F3, Business Premium<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|CAL (Client Access License) för RDS med Software Assurance|

Infrastrukturen behöver följande för att stödja Windows Virtual Desktop:

* En [Azure Active Directory](../active-directory/index.yml).
* En Windows Server Active Directory synkroniseras med Azure Active Directory. Du kan konfigurera detta med Azure AD Connect (för hybridorganisationer) eller Azure AD Domain Services (för hybrid- eller molnorganisationer).
  * En Windows Server AD synkroniseras med Azure Active Directory. Användaren kommer från en Windows Server AD och den virtuella Windows Virtual Desktop är ansluten till Windows Server AD domän.
  * En Windows Server AD synkroniseras med Azure Active Directory. Användaren kommer från en Windows Server AD och den virtuella Windows Virtual Desktop datorn är ansluten till Azure AD Domain Services domän.
  * En Azure AD Domain Services domän. Användaren kommer från en Azure Active Directory och den virtuella Windows Virtual Desktop är ansluten till Azure AD Domain Services domän.
* En Azure-prenumeration, som är överordnad till samma Azure AD-klientorganisation, som innehåller ett virtuellt nätverk som antingen innehåller eller är anslutet till Windows Server Active Directory eller Azure AD DS instansen.

Användarkrav för att ansluta till Windows Virtual Desktop:

* Användaren måste komma från samma Active Directory som är ansluten till Azure AD. Windows Virtual Desktop stöder inte B2B- eller MSA-konton.
* Det UPN som du använder för att prenumerera på Windows Virtual Desktop måste finnas i Den Active Directory-domän som den virtuella datorn är ansluten till.

De virtuella Azure-datorer som du skapar Windows Virtual Desktop måste vara:

* [Standard-domän-ansluten](../active-directory-domain-services/compare-identity-solutions.md) eller [Hybrid AD-ansluten](../active-directory/devices/hybrid-azuread-join-plan.md). Virtuella datorer kan inte vara Azure AD-ansluten.
* Kör någon av följande [OS-avbildningar som stöds.](#supported-virtual-machine-os-images)

>[!NOTE]
>Om du behöver en Azure-prenumeration kan du [registrera dig för en kostnadsfri utvärderingsversion på en månad.](https://azure.microsoft.com/free/) Om du använder den kostnadsfria utvärderingsversionen av Azure bör du använda Azure AD Domain Services att synkronisera Windows Server Active Directory med Azure Active Directory.

En lista över URL:er som du bör avblockera för Windows Virtual Desktop distributionen ska fungera som avsett finns i vår [lista över url-adresser som krävs.](safe-url-list.md)

Windows Virtual Desktop består av de Windows-skrivbord och appar som du levererar till användare och hanteringslösningen, som finns som en tjänst i Azure av Microsoft. Stationära datorer och appar kan distribueras på virtuella datorer (VM) i valfri Azure-region, och hanteringslösningen och data för dessa virtuella datorer kommer att finnas i USA. Detta kan resultera i dataöverföring till USA.

Kontrollera att nätverket uppfyller följande krav för optimala prestanda:

* Svarstiden för tur och retur (RTT) från klientens nätverk till Azure-regionen där värdpooler har distribuerats bör vara mindre än 150 ms. Använd [Upplevelseuppskattning för att visa](https://azure.microsoft.com/services/virtual-desktop/assessment) anslutningshälsan och den rekommenderade Azure-regionen.
* Nätverkstrafik kan flöda utanför lands-/regionsgränser när virtuella datorer som är värdar för skrivbord och appar ansluter till hanteringstjänsten.
* För att optimera nätverksprestanda rekommenderar vi att sessionsvärdens virtuella datorer är samplacerade i samma Azure-region som hanteringstjänsten.

Du kan se en typisk arkitekturkonfiguration Windows Virtual Desktop för företaget i [vår arkitektur.](/azure/architecture/example-scenario/wvd/windows-virtual-desktop)

## <a name="supported-remote-desktop-clients"></a>Fjärrskrivbordsklienter som stöds

Följande fjärrskrivbordsklienter stöder Windows Virtual Desktop:

* [Windows-skrivbordet](connect-windows-7-10.md)
* [Webb](connect-web.md)
* [macOS](connect-macos.md)
* [iOS](connect-ios.md)
* [Android](connect-android.md)
* Microsoft Store Klient

> [!IMPORTANT]
> Windows Virtual Desktop stöder inte RemoteApp- och skrivbordsanslutningar (RADC) eller MSTSC-klienten (Anslutning till fjärrskrivbord).

Mer information om URL:er som du måste avblockera för att använda klienterna finns i [listan Över säkra URL:er.](safe-url-list.md)

## <a name="supported-virtual-machine-os-images"></a>OPERATIVSYSTEMavbildningar för virtuella datorer som stöds

Windows Virtual Desktop stöder följande x64-operativsystemavbildningar:

* Windows 10 Enterprise flera sessioner, version 1809 eller senare
* Windows 10 Enterprise version 1809 eller senare (endast Halvårskanal)
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

Windows Virtual Desktop stöder inte x86 (32-bitars), Windows 10 Enterprise N, Windows 10 Pro eller Windows 10 Enterprise KN-operativsystemavbildningar. Windows 7 stöder inte heller VHD- eller VHDX-baserade profillösningar som hanteras på hanterade Azure Storage på grund av en begränsning av sektorstorlek.

Tillgängliga automatiserings- och distributionsalternativ beror på vilket operativsystem och vilken version du väljer, som du ser i följande tabell:

|Operativsystem|Azure Image Gallery|Manuell distribution av virtuella datorer|Azure Resource Manager mallintegrering|Etablera värdpooler på Azure Marketplace|
|--------------------------------------|:------:|:------:|:------:|:------:|
|Windows 10 Enterprise (flera sessioner), version 2004|Ja|Ja|Ja|Ja|
|Windows 10 Enterprise (flera sessioner), version 1909|Ja|Ja|Ja|Ja|
|Windows 10 Enterprise (flera sessioner), version 1903|Ja|Ja|Inga|Inga|
|Windows 10 Enterprise (flera sessioner), version 1809|Ja|Ja|Inga|Inga|
|Windows 7 Enterprise|Ja|Ja|Inga|Inga|
|Windows Server 2019|Ja|Ja|Inga|Inga|
|Windows Server 2016|Ja|Ja|Ja|Ja|
|Windows Server 2012 R2|Ja|Ja|Inga|Nej|

## <a name="next-steps"></a>Nästa steg

Om du använder en Windows Virtual Desktop (klassisk) kan du komma igång med självstudien på [Skapa en klientorganisation i Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).

Om du använder Windows Virtual Desktop med Azure Resource Manager måste du skapa en värdpool i stället. Gå till följande självstudie för att komma igång.

> [!div class="nextstepaction"]
> [Skapa en värdpool med Azure-portalen](create-host-pools-azure-marketplace.md)
