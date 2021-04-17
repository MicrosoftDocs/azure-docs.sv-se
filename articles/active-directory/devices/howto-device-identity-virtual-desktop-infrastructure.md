---
title: Enhetsidentitet och skrivbordsvirtualisering – Azure Active Directory
description: Lär dig hur VDI och Azure AD-enhetsidentiteter kan användas tillsammans
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
ms.openlocfilehash: 6c1d78094effe6919587f24c2262612e4fab347d
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575385"
---
# <a name="device-identity-and-desktop-virtualization"></a>Enhetsidentitet och skrivbordsvirtualisering

Administratörer distribuerar ofta VDI-plattformar (Virtual Desktop Infrastructure) som är värdar för Windows-operativsystem i sina organisationer. Administratörer distribuerar VDI för att:

- Effektivisera hanteringen.
- Minska kostnaderna genom konsolidering och centralisering av resurser.
- Leverera slutanvändarmobilitet och friheten att komma åt virtuella skrivbord när som helst, var som helst och på valfri enhet.

Det finns två primära typer av virtuella skrivbord:

- Permanent
- Icke-beständig

Beständiga versioner använder en unik skrivbordsavbildning för varje användare eller en pool med användare. Dessa unika skrivbord kan anpassas och sparas för framtida användning. 

Icke-beständiga versioner använder en samling datorer som användare kan komma åt efter behov. Dessa icke-beständiga skrivbord återställs till sitt ursprungliga tillstånd, om Windows är aktuellt<sup>1</sup> sker detta när en virtuell dator går igenom en process för avstängning/omstart/OS-återställning och om Windows är nere på nivå<sup>2</sup> sker detta när en användare loggar ut.

Det har skett en ökning av icke-beständiga VDI-distributioner allt eftersom distansarbete fortsätter att vara den nya normen. När kunder distribuerar icke-beständiga VDI är det viktigt att se till att du hanterar enhetsomsättning som kan orsakas av frekvent enhetsregistrering utan att ha en lämplig strategi för hantering av enhetens livscykel.

> [!IMPORTANT]
> Om du inte hanterar enhetsomsättningen kan du öka trycket på klientorganisationens kvotanvändning och eventuell risk för avbrott i tjänsten om klientkvoten tar slut. Du bör följa de riktlinjer som dokumenteras nedan när du distribuerar icke-beständiga VDI-miljöer för att undvika den här situationen.

Den här artikeln beskriver Microsofts vägledning till administratörer om stöd för enhetsidentitet och VDI. Mer information om enhetsidentitet finns i artikeln [Vad är en enhetsidentitet?](overview.md)

## <a name="supported-scenarios"></a>Scenarier som stöds

Innan du konfigurerar enhetsidentiteter i Azure AD för din VDI-miljö bör du bekanta dig med de scenarier som stöds. Tabellen nedan visar vilka etableringsscenarier som stöds. Etablering i det här sammanhanget innebär att en administratör kan konfigurera enhetsidentiteter i stor skala utan att kräva interaktion från slutanvändaren.

| Enhetsidentitetstyp | Infrastruktur för identiteter | Windows-enheter | VDI-plattformsversion | Stöds |
| --- | --- | --- | --- | --- |
| Hybrid Azure AD-ansluten | Federerad<sup>3</sup> | Aktuell Windows- och Windows-nednivå | Permanent | Yes |
|   |   | Windows-aktuell | Icke-beständig | Ja<sup>5</sup> |
|   |   | Äldre Windows-enheter | Icke-beständig | Ja<sup>6</sup> |
|   | Hanterad<sup>4</sup> | Aktuell Windows- och Windows-nednivå | Permanent | Yes |
|   |   | Windows-aktuell | Icke-beständig | No |
|   |   | Äldre Windows-enheter | Icke-beständig | Ja<sup>6</sup> |
| Azure AD-ansluten | Federerade | Windows-aktuell | Permanent | No |
|   |   |   | Icke-beständig | No |
|   | Hanterad | Windows aktuellt | Permanent | No |
|   |   |   | Icke-beständig | No |
| Azure AD-registrerad | Federerad/hanterad | Aktuell/windows-nednivå för Windows | Beständig/icke-beständig | Ej tillämpligt |

<sup>1</sup> **Aktuella Windows-enheter** representerar Windows 10, Windows Server 2016 v1803 eller senare och Windows Server 2019.
<sup>2</sup> **Windows-enheter** på lägre nivå representerar Windows 7, Windows 8.1, Windows Server 2008 R2, Windows Server 2012 och Windows Server 2012 R2. Supportinformation om Windows 7 finns i [Support for Windows 7 is ending (Stöd för Windows 7 upphör).](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support) Supportinformation om Windows Server 2008 R2 finns i Förbereda för Support för [Windows Server 2008.](https://www.microsoft.com/cloud-platform/windows-server-2008)

<sup>3</sup> En **infrastrukturmiljö för federerad** identitet representerar en miljö med en identitetsprovider som AD FS identitetsprovider eller annan IDP från tredje part.

<sup>4</sup>  En hanterad identitetsinfrastrukturmiljö representerar en miljö med Azure AD som identitetsprovider distribuerad med antingen lösenordshasharsynkronisering [(PHS)](../hybrid/whatis-phs.md) eller direktautentisering [(PTA)](../hybrid/how-to-connect-pta.md) med sömlös enkel [inloggning](../hybrid/how-to-connect-sso.md).

<sup>5</sup> **Stöd för beständighet för Windows kräver ytterligare** överväganden enligt dokumenten nedan i vägledningsavsnittet. Det här scenariot kräver Windows 10 1803, Windows Server 2019 eller Windows Server (halvårskanal) från och med version 1803

<sup>6</sup> **Stöd för icke-persistence för windows på lägre nivå** kräver ytterligare överväganden enligt dokumenten nedan i vägledningsavsnittet.


## <a name="microsofts-guidance"></a>Microsofts vägledning

Administratörer bör referera till följande artiklar, baserat på deras identitetsinfrastruktur, för att lära sig hur du konfigurerar Azure AD-hybridanslutningar.

- [Konfigurera hybrid Azure Active Directory-anslutning för en federerad miljö](hybrid-azuread-join-federated-domains.md)
- [Konfigurera hybridanslutningar Azure Active Directory för hanterad miljö](hybrid-azuread-join-managed-domains.md)

### <a name="non-persistent-vdi"></a>Icke-beständig VDI

Vid distribution av icke-beständiga VDI rekommenderar Microsoft att IT-administratörer implementerar vägledningen nedan. Om du inte gör det resulterar det i att din katalog har många inaktuella Hybrid Azure AD-anslutna enheter som har registrerats från din icke-beständiga VDI-plattform, vilket resulterar i ökat tryck på din klientkvot och risk för avbrott i tjänsten på grund av att klientkvoten tar slut.

- Om du förlitar dig på systemförberedelseverktyget (sysprep.exe) och om du använder en för-Windows 10 1809-avbildning för installation kontrollerar du att avbildningen inte kommer från en enhet som redan har registrerats med Azure AD som Hybrid Azure AD-ansluten.
- Om du förlitar dig på en ögonblicksbild av en virtuell dator (VM) för att skapa ytterligare virtuella datorer kontrollerar du att ögonblicksbilden inte kommer från en virtuell dator som redan har registrerats med Azure AD som Hybrid Azure AD-anslutning.
- Skapa och använd ett prefix för visningsnamnet (t.ex. NPVDI-) på datorn som anger att skrivbordet är icke-beständigt VDI-baserat.
- För nednivå i Windows:
   - Implementera **kommandot autoworkplacejoin /leave** som en del av utloggningsskriptet. Det här kommandot ska utlösas i kontexten för användaren och ska köras innan användaren har loggat ut helt och medan det fortfarande finns nätverksanslutning.
- För Windows som är aktuella i en federerad miljö (t.ex. AD FS):
   - Implementera **dsregcmd /join som** en del av den virtuella datorns startsekvens/ordning och innan användaren loggar in.
   - **KÖR INTE** dsregcmd /leave som en del av avstängningen/omstarten av den virtuella datorn.
- Definiera och implementera en process för [att hantera inaktuella enheter.](manage-stale-devices.md)
   - När du har en strategi för att identifiera icke-beständiga Hybrid Azure AD-anslutna enheter (t.ex. med prefix för datorvisningsnamn) bör du vara mer aggressiv när du rensar dessa enheter för att säkerställa att din katalog inte används med många inaktuella enheter.
   - För icke-beständiga VDI-distributioner på aktuell och äldre Windows-nivå bör du ta bort enheter som har **ApproximateLastLogonTimestamp** som är äldre än 15 dagar.

> [!NOTE]
> Om du vill förhindra ett enhetskopplingstillstånd när du använder icke-beständigT VDI måste du se till att följande registernyckel har angetts:  
> `HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin: "BlockAADWorkplaceJoin"=dword:00000001`    
>
> Kontrollera att du kör Windows 10 version 1803 eller senare.  
>
> Roaming av data under `%localappdata%` sökvägen stöds inte. Om du väljer att flytta innehåll under kontrollerar du att innehållet i följande mappar och registernycklar `%localappdata%` **aldrig** lämnar enheten under något villkor. Exempel: Profilmigreringsverktygen måste hoppa över följande mappar och nycklar:
>
> * `%localappdata%\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy`
> * `%localappdata%\Packages\Microsoft.Windows.CloudExperienceHost_cw5n1h2txyewy`
> * `%localappdata%\Packages\<any app package>\AC\TokenBroker`
> * `%localappdata%\Microsoft\TokenBroker`
> * `HKEY_CURRENT_USER\SOFTWARE\Microsoft\IdentityCRL`
> * `HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\AAD`
>


### <a name="persistent-vdi"></a>Beständig VDI

När du distribuerar beständig VDI rekommenderar Microsoft att IT-administratörer implementerar vägledningen nedan. Om du inte gör det uppstår problem med distribution och autentisering. 

- Om du förlitar dig på systemförberedelseverktyget (sysprep.exe) och om du använder en för-Windows 10 1809-avbildning för installation, kontrollerar du att avbildningen inte kommer från en enhet som redan har registrerats med Azure AD som Hybrid Azure AD-ansluten.
- Om du förlitar dig på en ögonblicksbild av en virtuell dator (VM) för att skapa ytterligare virtuella datorer kontrollerar du att ögonblicksbilden inte kommer från en virtuell dator som redan har registrerats med Azure AD som Hybrid Azure AD-anslutning.

Dessutom rekommenderar vi att du implementerar en process för att [hantera inaktuella enheter.](manage-stale-devices.md) Detta säkerställer att katalogen inte används med många inaktuella enheter om du regelbundet återställer dina virtuella datorer.
 
## <a name="next-steps"></a>Nästa steg

[Konfigurera hybrid Azure Active Directory-anslutning för federerad miljö](hybrid-azuread-join-federated-domains.md)
