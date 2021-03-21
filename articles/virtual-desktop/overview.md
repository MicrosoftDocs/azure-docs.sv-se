---
title: Vad är Windows Virtual Desktop? – Azure
description: En översikt över virtuella Windows-datorer.
author: Heidilohr
ms.topic: overview
ms.date: 09/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 36a15560b88c823ff2ae41f160839796bf21e4f8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98730793"
---
# <a name="what-is-windows-virtual-desktop"></a>Vad är Windows Virtual Desktop?

Windows Virtual Desktop är en Skriv bords-och app Virtualization-tjänst som körs i molnet.

Det här kan du göra när du kör Windows Virtual Desktop på Azure:

* Konfigurera en Windows 10-distribution med flera sessioner som ger en fullständig Windows 10 med skalbarhet
* Virtualisera Microsoft 365 appar för företag och optimera den för att köras i virtuella scenarier med flera användare
* Tillhandahålla virtuella Windows 7-datorer med kostnads fria utökade säkerhets uppdateringar
* Ta med dina befintliga Fjärrskrivbordstjänster (RDS) och Windows Server-datorer och appar till valfri dator
* Virtualisera både Station ära datorer och appar
* Hantera Windows 10-, Windows Server-och Windows 7-datorer och-appar med en enhetlig hanterings upplevelse

## <a name="introductory-video"></a>Introduktions video

Lär dig om virtuella Windows-datorer, varför det är unikt och vad som är nytt i den här videon:

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Mer information om virtuella Windows-datorer finns i [vår spelnings lista](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev).

## <a name="key-capabilities"></a>De viktigaste funktionerna

Med Windows Virtual Desktop kan du konfigurera en skalbar och flexibel miljö:

* Skapa en fullständig Virtualization-miljö för skriv bord i din Azure-prenumeration utan att behöva köra några ytterligare Gateway-servrar.
* Publicera så många värdbaserade pooler som du behöver för att hantera dina olika arbets belastningar.
* Ta med din egen bild för produktions arbets belastningar eller test från Azure-galleriet.
* Minska kostnaderna med poolbaserade resurser för flera sessioner. Med den nya kapaciteten för Windows 10 Enterprise multi-session exklusiv till Windows Virtual Desktop och värd för fjärrskrivbordssession (RDSH) i Windows Server kan du minska antalet virtuella datorer och operativ system (OS) samtidigt som du fortfarande tillhandahåller samma resurser för dina användare.
* Ge individuella ägarskap via personliga (beständiga) Skriv bord.

Du kan distribuera och hantera virtuella skriv bord:

* Använd Azure Portal, Windows Virtual Desktop PowerShell och REST-gränssnitt för att konfigurera värd grupper, skapa program grupper, tilldela användare och publicera resurser.
* Publicera hela Skriv bordet eller enskilda fjärrappar från en enda adresspool, skapa enskilda grupp grupper för olika uppsättningar med användare eller till och med tilldela användare till flera app-grupper för att minska antalet avbildningar.
* När du hanterar din miljö kan du använda inbyggd delegerad åtkomst för att tilldela roller och samla in diagnostik för att förstå olika konfigurations-eller användar fel.
* Använd den nya diagnostik tjänsten för att felsöka fel.
* Hantera bara avbildningen och virtuella datorer, inte infrastrukturen. Du behöver inte personligen hantera de fjärr skrivbords roller som du gör med Fjärrskrivbordstjänster, bara de virtuella datorerna i din Azure-prenumeration.

Du kan också tilldela och ansluta användare till dina virtuella skriv bord:

* När de har tilldelats kan användarna starta en virtuell Windows-klient för att ansluta användare till publicerade Windows-datorer och program. Anslut från valfri enhet via antingen ett inbyggt program på din enhet eller på Windows Virtual Desktop HTML5 webb klient.
* Upprätta en säker anslutning till tjänsten på ett säkert sätt, så att du aldrig behöver lämna några öppna inkommande portar.

## <a name="requirements"></a>Krav

Det finns några saker du behöver för att konfigurera virtuella Windows-datorer och ansluta dina användare till sina Windows-datorer och program.

Vi stöder följande operativ system, så se till att du har [lämpliga licenser](https://azure.microsoft.com/pricing/details/virtual-desktop/) för dina användare baserat på Skriv bordet och appar som du planerar att distribuera:

|Operativsystem|Nödvändig licens|
|---|---|
|Windows 10 Enterprise multi-session eller Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F3, Business Premium<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F3, Business Premium<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|Klient åtkomst licens för fjärr skrivbords tjänster med Software Assurance|

Infrastrukturen behöver följande saker för att stödja Windows Virtual Desktop:

* En [Azure Active Directory](../active-directory/index.yml).
* En Windows Server-Active Directory som synkroniseras med Azure Active Directory. Du kan konfigurera detta med Azure AD Connect (för Hybrid organisationer) eller Azure AD Domain Services (för Hybrid-eller moln organisationer).
  * En Windows Server AD-synkronisering med Azure Active Directory. Användaren är inloggad från Windows Server AD och den virtuella Windows-datorn för virtuella datorer är ansluten till Windows Server AD-domän.
  * En Windows Server AD-synkronisering med Azure Active Directory. Användaren är inloggad från Windows Server AD och den virtuella Windows-datorn för virtuella datorer är ansluten till Azure AD Domain Services domän.
  * En Azure AD Domain Services domän. Användaren är inloggad från Azure Active Directory och den virtuella Windows Virtual Desktop-datorn är ansluten till Azure AD Domain Services domän.
* En Azure-prenumeration, som är överordnad till samma Azure AD-klient, som innehåller ett virtuellt nätverk som antingen innehåller eller är anslutet till Windows Server Active Directory eller Azure AD DS-instansen.

Användar krav för att ansluta till virtuella Windows-datorer:

* Användaren måste ha en källa från samma Active Directory som är ansluten till Azure AD. Windows Virtual Desktop stöder inte B2B-eller MSA-konton.
* Det UPN som du använder för att prenumerera på Windows Virtual Desktop måste finnas i den Active Directory domän som den virtuella datorn är ansluten till.

De virtuella Azure-datorer som du skapar för virtuella Windows-datorer måste vara:

* [Standard domän ansluten](../active-directory-domain-services/compare-identity-solutions.md) eller [hybrid AD-ansluten](../active-directory/devices/hybrid-azuread-join-plan.md). Virtuella datorer kan inte vara Azure AD-anslutna.
* Köra en av följande [OS-avbildningar som stöds](#supported-virtual-machine-os-images).

>[!NOTE]
>Om du behöver en Azure-prenumeration kan du [Registrera dig för en kostnads fri utvärderings version av en månad](https://azure.microsoft.com/free/). Om du använder den kostnads fria utvärderings versionen av Azure bör du använda Azure AD Domain Services för att hålla Windows Server-Active Directory synkroniserad med Azure Active Directory.

För en lista över URL: er som ska avblockeras för distribution av virtuella Windows-datorer för att fungera korrekt, se vår [lista över säkra URL](safe-url-list.md): er.

Windows Virtual Desktop består av Windows-datorer och appar som du levererar till användare och hanterings lösningen, som är värdbaserad som en tjänst på Azure av Microsoft. Skriv bord och appar kan distribueras på virtuella datorer i valfri Azure-region och hanterings lösningen och data för dessa virtuella datorer finns i USA. Detta kan leda till att data överförs till USA.

För bästa prestanda bör du kontrol lera att nätverket uppfyller följande krav:

* Svars tid för tur och retur från klientens nätverk till den Azure-region där värdbaserade pooler har distribuerats måste vara mindre än 150 ms. Använd [Föruppskattnings](https://azure.microsoft.com/services/virtual-desktop/assessment) funktionen för att visa din anslutnings hälsa och den rekommenderade Azure-regionen.
* Nätverks trafiken kan flöda utanför lands-/region gränser när virtuella datorer som är värdar för Station ära datorer och appar ansluter till hanterings tjänsten.
* För att optimera för nätverks prestanda rekommenderar vi att de virtuella datorerna i samordnad i samma Azure-region som hanterings tjänsten.

Du kan se en typisk arkitektur inställning av Windows Virtual Desktop för företaget i vår [arkitektur enhetsplattformar](/azure/architecture/example-scenario/wvd/windows-virtual-desktop).

## <a name="supported-remote-desktop-clients"></a>Fjärr skrivbords klienter som stöds

Följande fjärr skrivbords klienter stöder virtuellt skriv bord i Windows:

* [Windows-skrivbordet](connect-windows-7-10.md)
* [Webb](connect-web.md)
* [macOS](connect-macos.md)
* [iOS](connect-ios.md)
* [Android](connect-android.md)
* Microsoft Store-klient

> [!IMPORTANT]
> Windows Virtual Desktop stöder inte RADC-klienten (RemoteApp-och Desktop Connections) eller Anslutning till fjärrskrivbord-klienten (MSTSC).

Om du vill veta mer om webb adresser måste du häva blockeringen för att använda klienterna i [listan säker URL](safe-url-list.md).

## <a name="supported-virtual-machine-os-images"></a>OS-avbildningar för virtuella datorer som stöds

Windows Virtual Desktop stöder följande x64-operativ system avbildningar:

* Windows 10 Enterprise multi-session, version 1809 eller senare
* Windows 10 Enterprise, version 1809 eller senare
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

Windows Virtual Desktop stöder inte x86 (32-bitars), Windows 10 Enterprise N, Windows 10 Pro eller Windows 10 Enterprise KN-operativsystem avbildningar. Windows 7 stöder inte heller några VHD-eller VHDX-baserade profil lösningar som finns på hanterade Azure Storage på grund av en begränsning för sektor storlek.

Tillgängliga alternativ för Automation och distribution beror på vilket operativ system och vilken version du väljer, som du ser i följande tabell:

|Operativsystem|Azures avbildnings Galleri|Manuell distribution av virtuella datorer|Azure Resource Manager mall-integrering|Etablera värdbaserade pooler på Azure Marketplace|
|--------------------------------------|:------:|:------:|:------:|:------:|
|Windows 10 Enterprise (multi-session), version 2004|Ja|Ja|Ja|Ja|
|Windows 10 Enterprise (multi-session), version 1909|Ja|Ja|Ja|Ja|
|Windows 10 Enterprise (multi-session), version 1903|Ja|Ja|Inga|Inga|
|Windows 10 Enterprise (multi-session), version 1809|Ja|Ja|Inga|Inga|
|Windows 7 Enterprise|Ja|Ja|Inga|Inga|
|Windows Server 2019|Ja|Ja|Inga|Inga|
|Windows Server 2016|Ja|Ja|Ja|Ja|
|Windows Server 2012 R2|Ja|Ja|Inga|Nej|

## <a name="next-steps"></a>Nästa steg

Om du använder Windows Virtual Desktop (klassisk) kan du komma igång med vår självstudie i [skapa en klient i Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).

Om du använder det virtuella Windows-skrivbordet med Azure Resource Manager-integrering måste du skapa en adresspool i stället. Gå till följande självstudie för att komma igång.

> [!div class="nextstepaction"]
> [Skapa en värdpool med Azure-portalen](create-host-pools-azure-marketplace.md)