---
title: Hantera StorSimple virtuella vektor resurser | Microsoft Docs
description: Beskriver StorSimple-Enhetshanteraren och förklarar hur du använder den för att hantera resurser på din StorSimple-virtuella matris.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 0a799c83-fde5-4f3f-af0e-67535d1882b6
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 340b910319cabe3379bdb1bad1c09bc71c17f072
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994952"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>Använda StorSimple Device Manager-tjänsten för att hantera resurser i StorSimple Virtual Array

## <a name="overview"></a>Översikt

I den här självstudien beskrivs hur du använder tjänsten StorSimple Enhetshanteraren för att skapa och hantera resurser på din StorSimple-virtuella matris.

Tjänsten StorSimple Enhetshanteraren är ett tillägg i Azure Portal som gör att du kan hantera din StorSimple-lösning från ett enda webb gränssnitt. Förutom att hantera resurser och volymer kan du använda tjänsten StorSimple Enhetshanteraren för att visa och hantera enheter, Visa aviseringar, hantera säkerhets kopierings principer och hantera säkerhets kopierings katalogen.

## <a name="share-types"></a>Resurs typer

StorSimple-resurser kan vara:

* **Lokalt fäst**: data i dessa resurser förblir i matrisen alltid och spiller inte i molnet.
* **Skiktat**: data i dessa resurser kan spilla till molnet. När du skapar en nivå resurs, tillhandahålls cirka 10% av utrymmet på den lokala nivån och 90% av utrymmet i molnet. Om du till exempel har etablerad en 1 TB-resurs skulle 100 GB finnas i det lokala utrymmet och 900 GB används i molnet när data nivåerna. Detta innebär i sin tur att om du tar bort allt lokalt utrymme på enheten kan du inte etablera en nivå resurs (eftersom 10% som krävs på den lokala nivån inte är tillgänglig).

### <a name="provisioned-capacity"></a>Etablerad kapacitet

Se följande tabell för den högsta etablerade kapaciteten för varje resurs typ.

| **Gräns för identifierare** | **Gränserna** |
| --- | --- |
| Minsta storlek på en nivå resurs |500 GB |
| Maximal storlek för en nivå resurs |20 TB |
| Minsta storlek på en lokalt fäst resurs |50 GB |
| Maximal storlek på en lokalt fäst resurs |2 TB |

## <a name="the-shares-blade"></a>Bladet resurser

**Delnings** -menyn i bladet StorSimple service Summary visar listan över lagrings resurser på en specifik StorSimple-matris och gör att du kan hantera dem.

![Bladet resurser](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

En resurs består av en serie attribut:

* **Resurs namn** – ett beskrivande namn som måste vara unikt och som hjälper dig att identifiera resursen.
* **Status** – kan vara online eller offline. Om en resurs är offline kommer användare av resursen inte att kunna komma åt den.
* **Typ** – anger om resursen är i **nivå** av (standard) eller **lokalt fäst**.
* **Kapacitet** – anger mängden data som används jämfört med den totala mängden data som kan lagras på resursen.
* **Beskrivning** – en valfri inställning som hjälper till att beskriva resursen.
* **Behörigheter** – NTFS-behörigheter till resursen som kan hanteras via Utforskaren i Windows.
* **Säkerhets kopiering** – i händelse av den virtuella StorSimple-matrisen aktive ras alla resurser automatiskt för säkerhets kopiering.

![Information om resurser](./media/storsimple-virtual-array-manage-shares/share-details.png)

Följ anvisningarna i den här självstudien för att utföra följande uppgifter:

* Lägga till en resurs
* Ändra en resurs
* Koppla från en resurs
* Ta bort en resurs

## <a name="add-a-share"></a>Lägga till en resurs

1. Klicka på **+ Lägg till resurs** från kommando fältet på bladet StorSimple service Summary. Då öppnas bladet **Lägg till resurs** .

    ![Lägg till resurs](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. Gör följande på bladet **Lägg till resurs** :
   
   1. I fältet **resurs namn** anger du ett unikt namn för din resurs. Namnet måste vara en sträng som innehåller mellan 3 och 127 tecken.

   2. En valfri **Beskrivning** av resursen. Beskrivningen hjälper till att identifiera resurs ägare.

   3. I list rutan **typ** anger du om du vill **skapa en delad** eller **lokalt fäst** resurs. För arbets belastningar som kräver lokala garantier, låg latens och högre prestanda, Välj **lokalt fäst resurs**. För alla andra data väljer du **nivå** resurs.

   4. I fältet **kapacitet** anger du resursens storlek. En nivå resurs måste vara mellan 500 GB och 20 TB och en lokalt fäst resurs måste vara mellan 50 GB och 2 TB.

   5. I fältet **ange fullständiga standard behörigheter till** tilldelar du behörigheterna till användaren eller gruppen som har åtkomst till den här resursen. Ange namnet på användaren eller användar gruppen i _john@contoso.com_ formatet. Vi rekommenderar att du använder en användar grupp (i stället för en enskild användare) för att tillåta administratörs behörighet att få åtkomst till dessa resurser. När du har tilldelat behörigheterna här kan du sedan använda Utforskaren till att ändra dessa behörigheter.
3. Klicka på **skapa** när du är klar med konfigurationen av resursen. En resurs skapas med de angivna inställningarna och ett meddelande visas. Som standard kommer säkerhets kopiering att aktive ras för resursen.
4. Du bekräftar att resursen har skapats genom att gå till bladet **resurser** . Du bör se resursen som visas.
   
    ![Resurs skapandet lyckades](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>Ändra en resurs

Ändra en resurs när du behöver ändra resursens beskrivning. Inga andra resurs egenskaper kan ändras när resursen har skapats.

#### <a name="to-modify-a-share"></a>Ändra en resurs

1. Från inställningen **resurser** på bladet StorSimple service Summary väljer du den virtuella matris där resursen som du vill ändra finns.
2. **Välj** resursen för att visa den aktuella beskrivningen och ändra den.
3. Spara ändringarna genom att klicka på **Spara** kommando fältet. De angivna inställningarna tillämpas och du får ett meddelande.
   
    ![ Redigera resurs](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>Koppla från en resurs

Du kan behöva koppla från en resurs när du planerar att ändra den eller ta bort den. När en resurs är offline är den inte tillgänglig för Läs-och Skriv behörighet. Du måste ta resursen offline på värden och på enheten.

#### <a name="to-take-a-share-offline"></a>Koppla från en resurs

1. Kontrol lera att resursen i fråga inte används innan du tar den offline.
2. Ta resursen i matrisen offline genom att utföra följande steg:
   
    1. Från inställningen **resurser** på bladet StorSimple service Summary väljer du den virtuella matris där resursen som du vill ta offline finns.

    2. **Välj** resursen och klicka på **...** (alternativt högerklicka på den här raden) och välj **ta offline** i snabb menyn.
     
        ![Offline-resurs](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. Granska informationen i bladet **ta offline** och bekräfta ditt godkännande av åtgärden. Klicka på koppla **från offline** för att koppla från resursen. Ett meddelande visas om att åtgärden pågår.

    4. Du kan kontrol lera att resursen har kopplats från genom att gå till bladet **resurser** . Du bör se statusen för resursen som offline.

## <a name="delete-a-share"></a>Ta bort en resurs

> [!IMPORTANT]
> Du kan bara ta bort en resurs om den är offline.


Utför följande steg för att ta bort en resurs.

#### <a name="to-delete-a-share"></a>Ta bort en resurs

1. Från inställningen **resurser** på bladet StorSimple service Summary väljer du den virtuella matris där resursen som du vill ta bort finns.
2. **Välj** resursen och klicka på **...** (alternativt högerklicka på den här raden) och välj **ta bort** på snabb menyn.
   
    ![Ta bort resurs](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. Kontrol lera statusen för den resurs som du vill ta bort. Om resursen som du vill ta bort inte är offline tar du den offline först. Följ stegen i koppla [från en resurs](#take-a-share-offline).
4. När du uppmanas att bekräfta i bladet **ta bort** godkänner du bekräftelsen och klickar på **ta bort**. Resursen tas nu bort och bladet **resurser** visar den uppdaterade listan över resurser i den virtuella matrisen.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [klonar en StorSimple-resurs](storsimple-virtual-array-clone.md).

