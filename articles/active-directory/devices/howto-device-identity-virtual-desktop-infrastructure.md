---
title: Enhets identitet och skriv bords virtualisering – Azure Active Directory
description: Lär dig hur VDI och Azure AD-enhets identiteter kan användas tillsammans
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: cfea22c10d98adf3b8c89491c248bf7a934ba1ed
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104798892"
---
# <a name="device-identity-and-desktop-virtualization"></a>Enhets identitet och skriv bords virtualisering

Administratörer distribuerar ofta VDI-plattformar (Virtual Desktop Infrastructure) som är värd för Windows-operativsystem i sina organisationer. Administratörer distribuerar VDI till:

- Effektivisera hanteringen.
- Minska kostnaderna genom konsolidering och centralisering av resurser.
- Leverera slutanvändarnas rörlighet och friheten att komma åt virtuella skriv bord när som helst, var som helst, på valfri enhet.

Det finns två primära typer av virtuella skriv bord:

- Permanent
- Icke-beständig

I beständiga versioner används en unik Skriv bords avbildning för varje användare eller pool med användare. Dessa unika skriv bord kan anpassas och sparas för framtida användning. 

Icke-beständiga versioner använder en samling skriv bord som användare kan komma åt vid behov. Dessa icke-beständiga skriv bord återställs till sitt ursprungliga tillstånd, om Windows Current<sup>1</sup> inträffar när en virtuell dator går igenom en omstart/omstart/OS-återställnings process och i händelse av Windows-nivå<sup>2</sup> sker detta när en användare loggar ut.

Det har uppstått en ökning av icke-beständiga VDI-distributioner eftersom Fjärråtgärden fortfarande är den nya normen. När kunderna distribuerar icke-permanent VDI är det viktigt att se till att du hanterar enhets omsättningen som kan bero på frekvent enhets registrering utan att ha rätt strategi för livs cykel hantering av enheter.

> [!IMPORTANT]
> Det går inte att hantera enhets omsättningen, kan leda till ökad påverkan på användningen av din klient kvot användning och potentiell risk för tjänst avbrott om du får slut på klient kvoten. Följ anvisningarna som beskrivs nedan när du distribuerar icke-permanenta VDI-miljöer för att undvika den här situationen.

Den här artikeln beskriver Microsofts vägledning för administratörer om stöd för enhets identiteter och VDI. Mer information om enhets identitet finns i artikeln [Vad är en enhets identitet](overview.md).

## <a name="supported-scenarios"></a>Scenarier som stöds

Innan du konfigurerar enhets identiteter i Azure AD för din VDI-miljö kan du bekanta dig med de scenarier som stöds. Tabellen nedan visar vilka etablerings scenarier som stöds. Etableringen i den här kontexten innebär att en administratör kan konfigurera enhets identiteter i skala utan att behöva göra någon slut användar interaktion.

| Enhets identitets typ | Infrastruktur för identiteter | Windows-enheter | VDI-plattforms version | Stöds |
| --- | --- | --- | --- | --- |
| Hybrid Azure AD-ansluten | Federerad<sup>3</sup> | Windows aktuella och Windows-äldre | Permanent | Ja |
|   |   | Windows-aktuell | Icke-beständig | Ja<sup>5</sup> |
|   |   | Äldre Windows-enheter | Icke-beständig | Ja<sup>6</sup> |
|   | Hanterad<sup>4</sup> | Windows aktuella och Windows-äldre | Permanent | Ja |
|   |   | Windows-aktuell | Icke-beständig | Inga |
|   |   | Äldre Windows-enheter | Icke-beständig | Ja<sup>6</sup> |
| Azure AD-ansluten | Federerade | Windows-aktuell | Permanent | Inga |
|   |   |   | Icke-beständig | Inga |
|   | Hanterad | Windows-aktuell | Permanent | Inga |
|   |   |   | Icke-beständig | Inga |
| Azure AD-registrerad | Federerad/hanterad | Windows aktuella/Windows-äldre | Beständiga/icke-beständiga | Ej tillämpligt |

<sup>1</sup> **Windows aktuella** enheter representerar Windows 10, Windows Server 2016 V1803 eller senare och Windows Server 2019.
<sup>2</sup> **Windows-enheter på Windows-nivå** representerar windows 7, Windows 8,1, Windows Server 2008 R2, Windows Server 2012 och Windows Server 2012 R2. Information om support för Windows 7 finns i [Support för Windows 7](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support). Information om support för Windows Server 2008 R2 finns i [förbereda för Windows server 2008-slut för support](https://www.microsoft.com/cloud-platform/windows-server-2008).

<sup>3</sup> en **federerad** identitets infrastruktur miljö representerar en miljö med en identitets leverantör som AD FS eller andra IdP från tredje part.

<sup>4</sup> en **hanterad** identitets infrastruktur miljö representerar en miljö med Azure AD som identitets leverantören distribuerad med antingen [PHS (Password hash Sync)](../hybrid/whatis-phs.md) eller [direktautentisering (PTA)](../hybrid/how-to-connect-pta.md) med [sömlös enkel inloggning](../hybrid/how-to-connect-sso.md).

<sup>5</sup> **icke-beständiga stöd för Windows aktuell** kräver ytterligare överväganden som dokumenteras nedan i avsnittet om vägledning. Det här scenariot kräver Windows 10 1803, Windows Server 2019 eller Windows Server (halvårs kanal) som startar version 1803

<sup>6</sup> **beständigt stöd för Windows-äldre** versioner kräver ytterligare överväganden som dokumenteras nedan i avsnittet om vägledning.


## <a name="microsofts-guidance"></a>Microsofts vägledning

Administratörer ska referera till följande artiklar, baserat på deras identitets infrastruktur, för att lära dig hur du konfigurerar hybrid Azure AD Join.

- [Konfigurera hybrid Azure Active Directory anslutning för federerad miljö](hybrid-azuread-join-federated-domains.md)
- [Konfigurera hybrid Azure Active Directorys anslutning för hanterad miljö](hybrid-azuread-join-managed-domains.md)

### <a name="non-persistent-vdi"></a>Icke-beständig VDI

När du distribuerar icke-permanent VDI rekommenderar Microsoft att IT-administratörer implementerar vägledningen nedan. Om du inte gör det så kommer din katalog ha många inaktuella hybrid Azure AD-anslutna enheter som har registrerats från den icke-permanenta VDI-plattformen, vilket leder till ökat tryck på din klient kvot och risken för avbrott i tjänsten på grund av att klient kvoten har överskridits.

- Om du förlitar dig på system förberedelse verktyget (sysprep.exe) och om du använder en avbildning med tidigare versioner än Windows 10 1809 för installation kontrollerar du att avbildningen inte är från en enhet som redan är registrerad i Azure AD som en hybrid Azure AD-ansluten.
- Om du förlitar dig på en ögonblicks bild av en virtuell dator (VM) för att skapa ytterligare virtuella datorer, se till att ögonblicks bilden inte är från en virtuell dator som redan är registrerad i Azure AD som en hybrid Azure AD-anslutning.
- Skapa och Använd ett prefix för visnings namnet (t. ex. NPVDI-) för datorn som visar att Skriv bordet är icke-beständig VDI-baserad.
- För Windows på nivån:
   - Implementera **autoworkplacejoin/Leave** -kommandot som en del av utloggnings skriptet. Det här kommandot bör utlösas i kontexten för användaren och bör köras innan användaren har loggat ut helt och trots att det fortfarande finns en nätverks anslutning.
- För Windows-Current i en federerad miljö (t. ex. AD FS):
   - Implementera **dsregcmd-/Join** som en del av startsekvensen för virtuella datorer.
   - Kör **inte** dsregcmd/Leave som en del av processen för avstängning/omstart av datorn.
- Definiera och implementera process för att [Hantera inaktuella enheter](manage-stale-devices.md).
   - När du har en strategi för att identifiera dina icke-beständiga Azure AD-anslutna enheter (t. ex. genom att använda prefixet för datorns visnings namn) bör du vara mer aggressiv vid rensning av dessa enheter för att se till att din katalog inte används med massor av inaktuella enheter.
   - För icke-beständiga VDI-distributioner på aktuella och äldre Windows-nivåer, bör du ta bort enheter som har **ApproximateLastLogonTimestamp** äldre än 15 dagar.

### <a name="persistent-vdi"></a>Beständig VDI

När du distribuerar beständig VDI rekommenderar Microsoft att IT-administratörer implementerar vägledningen nedan. Om du inte gör det leder det till problem med distribution och autentisering. 

- Om du förlitar dig på system förberedelse verktyget (sysprep.exe) och om du använder en avbildning med tidigare versioner än Windows 10 1809 för installation kontrollerar du att avbildningen inte är från en enhet som redan är registrerad i Azure AD som en hybrid Azure AD-ansluten.
- Om du förlitar dig på en ögonblicks bild av en virtuell dator (VM) för att skapa ytterligare virtuella datorer, se till att ögonblicks bilden inte är från en virtuell dator som redan är registrerad i Azure AD som en hybrid Azure AD-anslutning.

Dessutom rekommenderar vi att du implementerar processen för att [Hantera inaktuella enheter](manage-stale-devices.md). Detta säkerställer att katalogen inte förbrukas med massor av inaktuella enheter om du regelbundet återställer dina virtuella datorer.
 
## <a name="next-steps"></a>Nästa steg

[Konfigurera hybrid Azure Active Directorys anslutning för federerad miljö](hybrid-azuread-join-federated-domains.md)
