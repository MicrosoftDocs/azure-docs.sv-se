---
title: Övervaka privat moln aktivitet
titleSuffix: Azure VMware Solution by CloudSimple
description: Beskriver den information som är tillgänglig för aktivitet i Azure VMware-lösningen av CloudSimple-miljön, inklusive aviseringar, händelser, uppgifter och granskning.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fb597cc71d228a1daaaa4e52d76b301764ff9292
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899311"
---
# <a name="monitor-vmware-solution-by-cloudsimple-activity"></a>Övervaka VMware-lösning per CloudSimple-aktivitet

CloudSimple aktivitets loggar ger en inblick i åtgärder som utförs på CloudSimple-portalen.  Listan innehåller aviseringar, händelser, uppgifter och granskning.  Använd aktivitets loggarna för att avgöra vem, när och vilka åtgärder som utfördes.  Aktivitets loggar innehåller inte några Läs åtgärder som utförs av en användare.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Få åtkomst till CloudSimple-portalen

Få åtkomst till [CloudSimple-portalen](access-cloudsimple-portal.md).

## <a name="activity-information"></a>Aktivitets information

Välj **aktivitet** på menyn på sidan för att komma åt aktivitets sidorna.

![Översikt över aktivitets Sidan](media/activity-page-overview.png)

Om du vill visa information om någon av aktiviteterna på sidan aktivitet väljer du aktiviteten. En informations panel öppnas till höger. Åtgärder i panelen beror på typen av aktivitet. Klicka på **X** för att stänga panelen.

Klicka på en kolumn rubrik för att sortera visningen.  Du kan filtrera kolumner för att visa vissa värden.  Hämta aktivitets rapporten genom att klicka på **Hämta som CSV** -ikon.

## <a name="alerts"></a>Aviseringar

Aviseringar är aviseringar om viktiga aktiviteter i din CloudSimple-miljö.  Aviseringar innehåller händelser som påverkar fakturerings-eller användar åtkomst.

Om du vill bekräfta aviseringar och ta bort dem från listan väljer du en eller flera i listan och klickar på **Bekräfta**.

Följande kolumner med information är tillgängliga för aviseringar. Klicka på **Redigera kolumner** och Välj kolumner som du vill visa.

| Kolumn | Beskrivning |
------------ | ------------- |
| Aviserings typ | Aviserings kategori.|
| Tid | Tiden då aviseringen inträffade. |
| Allvarlighetsgrad | Betydelsen av aviseringen.|
| Resursnamn | Namnet som tilldelats resursen, till exempel namnet på det privata molnet. |
| Resurstyp | Resurs kategori: privat moln, moln rack. |
| Resurs-ID | Identifierare för resursen. |
| Description | Beskrivning av vad som utlöste aviseringen. |
| Bekräftad | Indikerar om aviseringen har bekräftats. |

## <a name="events"></a>Händelser

Händelser visar användar-och system aktivitet på CloudSimple-portalen. På sidan händelser visas den aktivitet som är associerad med en speciell resurs och påverkannas allvarlighets grad.

Följande kolumner med information är tillgängliga för aviseringar. Klicka på **Redigera kolumner** och Välj kolumner som du vill visa.

| Kolumn | Beskrivning |
------------ | ------------- |
| Tid | Datum och tid då händelsen inträffade. |
| Händelsetyp | Numerisk kod som identifierar händelsen. |
| Allvarlighetsgrad | Allvarlighets grad för händelse.|
| Resursnamn | Namnet som tilldelats resursen, till exempel namnet på det privata molnet. |
| Resurstyp | Resurs kategori: privat moln, moln rack. |
| Description | Beskrivning av vad som utlöste aviseringen. |

## <a name="tasks"></a>Aktiviteter

Aktiviteter är privata moln aktiviteter som förväntas ta 30 sekunder eller mer att slutföra. (Aktiviteter som förväntas ta mindre än 30 sekunder rapporteras endast som händelser.) Öppna sidan aktiviteter för att spåra förloppet för aktiviteterna i ditt privata moln.

Följande kolumner med information är tillgängliga för aviseringar. Klicka på **Redigera kolumner** och Välj kolumner som du vill visa.

| Kolumn | Beskrivning |
------------ | ------------- |
| Aktivitets-ID | Unikt ID för uppgiften. |
| Åtgärd | Åtgärd som ska utföras av uppgiften. |
| Användare | Användaren har tilldelats för att slutföra uppgiften. |
| Resursnamn | Namn som tilldelats resursen. |
| Resurstyp | Resurs kategori: privat moln, moln rack. |
| Resurs-ID | Identifierare för resursen. |
| Start | Start tid för aktiviteten. |
| Slut | Slut tid för aktiviteten. |
| Status | Aktuell uppgifts status. |
| Förfluten tid | Tid som aktiviteten tog att slutföra (om den har slutförts) eller håller på att tas (om den pågår). |
| Description | Uppgifts beskrivning. |

## <a name="audit"></a>Granska

Gransknings loggar håller reda på användar aktivitet. Du kan använda gransknings loggar för att övervaka användar aktivitet för alla användare.

Följande kolumner med information är tillgängliga för aviseringar. Klicka på **Redigera kolumner** och Välj kolumner som du vill visa.

| Kolumn | Beskrivning |
------------ | ------------- |
| Tid | Tid för gransknings posten. |
| Åtgärd | Åtgärd som ska utföras av uppgiften. |
| Användare | Användaren har tilldelats uppgiften. |
| Resursnamn | Namn som tilldelats resursen. |
| Resurstyp | Resurs kategori: privat moln, moln rack. |
| Resurs-ID | Identifierare för resursen. |
| Resultat | Resultat av aktiviteten, till exempel **lyckad**. |
| Tidsåtgång | Tid för att slutföra uppgiften. |
| Description | Beskrivning av åtgärden. |

## <a name="next-steps"></a>Nästa steg

* [Använda virtuella VMware-datorer på Azure](quickstart-create-vmware-virtual-machine.md)
* Läs mer om [privata moln](cloudsimple-private-cloud.md)
