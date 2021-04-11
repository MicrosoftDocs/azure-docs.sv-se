---
title: Windows Virtual Desktop MSIX-appen Attach ordbok – Azure
description: En ord lista med MSIX-appen bifogar termer och begrepp.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: deb02c161dfe41bf79b7905fde23678425aaf55f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448380"
---
# <a name="msix-app-attach-glossary"></a>Ord lista för MSIX-appen

Den här artikeln är en lista över definitioner för viktiga termer och begrepp som rör MSIX app Attach.

## <a name="msix-container"></a>MSIX-behållare

En MSIX-behållare är där MSIX-appar körs. Läs mer i MSIX- [behållare](/windows/msix/msix-container).

## <a name="msix-application"></a>MSIX-program 

Ett program som är lagrat i en. MSIX-fil.

## <a name="msix-package"></a>MSIX-paket 

Ett MSIX-paket är en MSIX-fil eller ett program.

## <a name="msix-share"></a>MSIX-resurs

En MSIX-resurs är en nätverks resurs som innehåller expanderade MSIX-paket. MSIX-resurser stöder SMB 3 eller senare. Programmen hämtas stegvis från den här MSIX-resursen utan att du behöver flytta programfiler till system enheten.

## <a name="msix-image"></a>MSIX-bild

En MSIX-avbildning är en VHD-, VHDx-eller CIM-fil som innehåller en eller flera MSIX-paketerade program. Varje program levereras i MSIX-avbildningen med hjälp av MSIXMGR-verktyget.

## <a name="repackage"></a>Paketera om

Ompackningen tar ett icke-MSIX program och konverterar det till MSIX med hjälp av MSIX packnings verktyg (MPT). Mer information finns i [Översikt över MSIX packning Tool](/windows/msix/packaging-tool/tool-overview).

## <a name="expand-an-msix-package"></a>Expandera ett MSIX-paket

Att expandera ett MSIX-paket är en process i flera steg. Expansion tar MSIX-filen och placerar innehållet i en VHD (x) eller CIM-fil. 

Expandera ett MSIX-paket:

1. Hämta ett MSIX-paket (MSIX-fil).
2. Byt namn på filen MSIX till en. zip-fil.
3. Zippa upp den resulterande zip-filen i en mapp.
4. Skapa en virtuell hård disk som har samma storlek som mappen.
5. Montera den virtuella hård disken.
6. Initiera en disk.
7. Skapa en partition.
8. Formatera partitionen.
9. Kopiera det zippade innehållet till den virtuella hård disken.
10. Använd MSIXMGR-verktyget för att tillämpa ACL: er på innehållet i den virtuella hård disken.
11. Demontera VHD (x) eller [CIM](#cim).

## <a name="upload-an-msix-package"></a>Ladda upp ett MSIX-paket 

Att ladda upp ett MSIX-paket innebär att ladda upp den virtuella hård disken (x) eller [CIM](#cim) som innehåller ett expanderat MSIX-paket till MSIX-resursen.

I Windows Virtual Desktop sker uppladdningen en gång per MSIX-resurs. När du har laddat upp ett paket kan alla lagringspooler i samma prenumeration referera till den.

## <a name="add-an-msix-package"></a>Lägg till ett MSIX-paket

I virtuella Windows-skrivbord länkar du till ett MSIX-paket till en adresspool.

## <a name="publish-an-msix-package"></a>Publicera ett MSIX-paket 

I Windows Virtual Desktop måste ett publicerat MSIX-paket tilldelas till en Active Directory-domän tjänst (AD DS) eller Azure Active Directory (Azure AD) användare eller användar grupp.

## <a name="staging"></a>Mellanlagring

Mellanlagringen omfattar två saker:

- Montera VHD (x) eller [CIM](#cim) på den virtuella datorn.
- Meddelar det operativ system att MSIX-paketet är tillgängligt för registrering.

## <a name="registration"></a>Registrering

Registreringen gör ett mellanlagrat MSIX-paket tillgängligt för dina användare. Registrering sker per användare. Om du inte uttryckligen har registrerat en app för den aktuella användaren kan de inte köra appen.

Det finns två typer av registrering: reguljär och fördröjd.

### <a name="regular-registration"></a>Vanlig registrering

Vid vanlig registrering registreras varje program som är kopplad till en användare fullständigt. Registreringen sker under den tid då användaren loggar in till sessionen, vilket kan påverka den tid det tar för dem att börja använda Windows Virtual Desktop.

### <a name="delayed-registration"></a>Fördröjd registrering

I fördröjd registrering är varje program som är tilldelad användaren bara delvis registrerad. Partiell registrering innebär att panelen Start-menyn och dubbelklickar på fil associationer registreras. Registreringen sker när användaren loggar in på sin session, så den har minimal påverkan på den tid det tar att börja använda det virtuella Windows-skrivbordet. Registreringen slutförs endast när användaren kör programmet i MSIX-paketet.

Fördröjd registrering är för närvarande standard konfigurationen för MSIX app Attach.

## <a name="deregistration"></a>Avregistrering

Deregistrering tar bort ett registrerat men icke-MSIX paket för en användare. Avregistrering sker när användaren loggar ut från sessionen. Under avregistreringen bifogar MSIX-appen push-programdata som är specifika för användaren i den lokala användar profilen.

## <a name="destage"></a>Destage

Destaging meddelar det operativ system att ett MSIX-paket eller program som för närvarande inte körs och inte är mellanlagrat för någon användare kan demonteras. Detta tar bort alla referenser till den i operativ systemet.

## <a name="cim"></a>OBJEKTHANTERAREN

. CIM är ett nytt fil namns tillägg som är associerat med sammansatta avbildningsfiler system (CimFS). Det går snabbare att montera och demontera CIM-filer på en VHD-fil. CIM förbrukar också mindre processor och minne än VHD.

En CIM-fil är en fil med en. CIM-tillägg som innehåller metadata och minst sex ytterligare filer som innehåller faktiska data. Filerna i CIM-filen har inte tillägg. Följande tabell är en lista över exempel-filer som du hittar i en CIM:

| Filnamn | Anknytning | Storlek |
|-----------|-----------|------|
| VSC | OBJEKTHANTERAREN | 1 kB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | NA | 27 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | NA | 20 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | NA | 42 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | NA | 428 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | NA | 217 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | NA | 264 132 KB |

Följande tabell är en prestanda jämförelse mellan VHD-och CimFS. De här talen är resultatet av ett test som kör med 500 300 MB filer i varje format som körs på en DSv4 dator.

|  Specifikationer                          | VHD                    | CimFS   |
|---------------------------------|--------------------------|-----------|
| Genomsnittlig monterings tid     | 356 MS                     | 255 MS      |
| Genomsnittligt avmonterings tid   | 1615 MS                    | 36 MS       |
| Minnesförbrukning | 6% (av 8 GB)                      | 2% (av 8 GB)       |
| CPU (Count-insamling)          | Överutnyttjade ut flera gånger | Ingen påverkan |

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om MSIX app Attach kan du kolla vår [Översikt](what-is-app-attach.md) och [vanliga frågor och svar](app-attach-faq.md). Annars kan du komma igång med [Konfigurera appen Anslut](app-attach.md).
