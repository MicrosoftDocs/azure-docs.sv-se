---
title: Windows Virtual Desktop bifoga msix-appens ordlista – Azure
description: En ordlista med MSIX-appen bifogar termer och begrepp.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: c5c596735ad91f38d5ba4217135a9373d2856182
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538456"
---
# <a name="msix-app-attach-glossary"></a>Bifoga MSIX-appordlistor

Den här artikeln är en lista över definitioner för viktiga termer och begrepp som rör msix-app bifoga.

## <a name="msix-container"></a>MSIX-container

En MSIX-container är den plats där MSIX-appar körs. Mer information finns i [MSIX-containrar.](/windows/msix/msix-container)

## <a name="msix-application"></a>MSIX-program 

Ett program som lagras i en . MSIX-fil.

## <a name="msix-package"></a>MSIX-paket 

Ett MSIX-paket är en MSIX-fil eller ett MSIX-program.

## <a name="msix-share"></a>MSIX-resurs

En MSIX-resurs är en nätverksresurs som innehåller utökade MSIX-paket. MSIX-resurser måste ha stöd för SMB 3 eller senare. Filresurser måste också vara tillgängliga för Virtual Machines (VM) i värdpoolens systemkonto. MSIX-paket mellanfasas från MSIX-resursen utan att behöva flytta programfiler till systemenheten. 

## <a name="msix-image"></a>MSIX-avbildning

En MSIX-avbildning är en VHD-, VHDx- eller CIM-fil som innehåller ett eller flera MSIX-paketerade program. Varje program levereras i MSIX-avbildningen med verktyget MSIXMGR.

## <a name="repackage"></a>Paketera om

Ompaketering tar ett icke-MSIX-program och konverterar det till MSIX med hjälp av MSIX Packaging Tool (MPT). Mer information finns i Översikt [över MSIX-paketeringsverktyget.](/windows/msix/packaging-tool/tool-overview)

## <a name="expand-an-msix-package"></a>Expandera ett MSIX-paket

Att expandera ett MSIX-paket är en process i flera steg. Expansionen tar MSIX-filen och placerar innehållet i en VHD(x) eller CIM-fil. 

Så här expanderar du ett MSIX-paket:

1. Hämta ett MSIX-paket (MSIX-fil).
2. Byt namn på MSIX-filen till en .zip-fil.
3. Packa upp den resulterande ZIP-filen i en mapp.
4. Skapa en virtuell hårddisk som har samma storlek som mappen.
5. Montera den virtuella hårddisken.
6. Initiera en disk.
7. Skapa en partition.
8. Formatera partitionen.
9. Kopiera det uppackade innehållet till den virtuella hårddisken.
10. Använd verktyget MSIXMGR för att tillämpa ACL:er på innehållet i den virtuella hårddisken.
11. Demontera VHD(x) eller [CIM](#cim).

## <a name="upload-an-msix-package"></a>Ladda upp ett MSIX-paket 

Att ladda upp ett MSIX-paket innebär att ladda upp VHD(x) eller [CIM](#cim) som innehåller ett expanderat MSIX-paket till MSIX-resursen.

I Windows Virtual Desktop sker uppladdningar en gång per MSIX-resurs. När du har överfört ett paket kan alla värdpooler i samma prenumeration referera till det.

## <a name="add-an-msix-package"></a>Lägga till ett MSIX-paket

När Windows Virtual Desktop lägger till ett MSIX-paket länkas det till en värdpool.

## <a name="publish-an-msix-package"></a>Publicera ett MSIX-paket 

I Windows Virtual Desktop måste ett publicerat MSIX-paket tilldelas till en Active Directory-domän-tjänst (AD DS) eller en Azure Active Directory-användare (Azure AD) eller en användargrupp.

## <a name="staging"></a>Mellanlagring

Mellanlagring omfattar två saker:

- Montera VHD(x) eller [CIM på den](#cim) virtuella datorn.
- Meddela operativsystemet att MSIX-paketet är tillgängligt för registrering.

## <a name="registration"></a>Registrering

Registreringen gör ett mellanspelat MSIX-paket tillgängligt för dina användare. Registreringen är per användare. Om du inte uttryckligen har registrerat en app för den specifika användaren kan de inte köra appen.

Det finns två typer av registrering: regelbundna och fördröjda.

### <a name="regular-registration"></a>Regelbunden registrering

Vid vanlig registrering är varje program som tilldelats en användare helt registrerad. Registreringen sker när användaren loggar in på sessionen, vilket kan påverka den tid det tar för användaren att börja använda Windows Virtual Desktop.

### <a name="delayed-registration"></a>Fördröjd registrering

Vid fördröjd registrering registreras endast delvis varje program som tilldelats användaren. Partiell registrering innebär att Start-menyn och filassociationer med dubbelklickan är registrerade. Registreringen sker när användaren loggar in på sessionen, så den har minimal påverkan på den tid det tar att börja använda Windows Virtual Desktop. Registreringen slutförs bara när användaren kör programmet i MSIX-paketet.

Fördröjd registrering är för närvarande standardkonfigurationen för msix-app bifoga.

## <a name="deregistration"></a>Återkallande

Avregistrering tar bort ett registrerat men icke-kört MSIX-paket för en användare. Avregistrering sker när användaren loggar ut från sessionen. Under avregistreringen kopplar MSIX-appen programdata som är specifika för användaren till den lokala användarprofilen.

## <a name="destage"></a>Destage

Med destaging meddelas operativsystemet att ett MSIX-paket eller program som för närvarande inte körs och inte mellandelade för någon användare kan demonteras. Detta tar bort all referens till den i operativsystemet.

## <a name="cim"></a>Cim

. CIM är ett nytt filnamnstillägg som är associerat med CimFS (Composite Image Files System). Montering och demontering av CIM-filer är snabbare än VHD-filer. CIM förbrukar också mindre processorkraft och minne än VHD.

En CIM-fil är en fil med en . CIM-tillägg som innehåller metadata och minst sex ytterligare filer som innehåller faktiska data. Filerna i CIM-filen har inga tillägg. Följande tabell är en lista över exempelfiler som du hittar i en CIM:

| Filnamn | Anknytning | Storlek |
|-----------|-----------|------|
| Vsc | Cim | 1 kB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | NA | 27 kB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | NA | 20 kB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | NA | 42 kB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | NA | 428 kB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | NA | 217 kB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | NA | 264 132 KB |

Följande tabell är en prestandajämförelse mellan VHD och CimFS. Dessa siffror var resultatet av en testkörning med fem hundra 300 MB filer i varje format som körs på en DSv4-dator.

|  Specifikationer                          | VHD                    | CimFS   |
|---------------------------------|--------------------------|-----------|
| Genomsnittlig monteringstid     | 356 ms                     | 255 ms      |
| Genomsnittlig demonteringstid   | 1615 ms                    | 36 ms       |
| Minnesförbrukning | 6 % (av 8 GB)                      | 2 % (av 8 GB)       |
| CPU (topp i antal)          | Maxade ut flera gånger | Ingen påverkan |

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om MSIX-appen bifoga kan du läsa vår översikt och [vanliga](what-is-app-attach.md) [frågor och svar.](app-attach-faq.md) Annars kan du komma igång [med Konfigurera app attach](app-attach.md).
