---
title: Hantera volymer på virtuell StorSimple-matris | Microsoft Docs
description: Beskriver StorSimple-Enhetshanteraren och förklarar hur du använder den för att hantera volymer på din StorSimple-virtuella matris.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: caa6a26b-b7ba-4a05-b092-1a79450225cf
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 79e3ce8c1605e5d68ff44901f53854d2f5f10abc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96014388"
---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>Använda StorSimple Device Manager-tjänsten för att hantera volymer i StorSimple Virtual Array

## <a name="overview"></a>Översikt

I den här självstudien beskrivs hur du använder tjänsten StorSimple Enhetshanteraren för att skapa och hantera volymer på din StorSimple-virtuella matris.

Tjänsten StorSimple Enhetshanteraren är ett tillägg i Azure Portal som gör att du kan hantera din StorSimple-lösning från ett enda webb gränssnitt. Förutom att hantera resurser och volymer kan du använda tjänsten StorSimple Enhetshanteraren för att visa och hantera enheter, Visa aviseringar och Visa och hantera säkerhets kopierings principer och säkerhets kopierings katalogen.

## <a name="volume-types"></a>Volym typer

StorSimple-volymer kan vara:

* **Lokalt fäst**: data i de här volymerna förblir i matrisen alltid och spiller inte i molnet.
* **Skiktat**: data i dessa volymer kan spilla till molnet. När du skapar en nivå volym etablerades cirka 10% av utrymmet på den lokala nivån och 90% av utrymmet i molnet. Om du till exempel har etablerad en volym på 1 TB skulle 100 GB finnas i det lokala utrymmet och 900 GB används i molnet när data nivåerna. Detta innebär i sin tur att om du tar bort allt lokalt utrymme på enheten kan du inte etablera en nivå volym (eftersom 10% som krävs på den lokala nivån inte är tillgänglig).

### <a name="provisioned-capacity"></a>Etablerad kapacitet
Se följande tabell för den högsta etablerade kapaciteten för varje volymtyp.

| **Gräns för identifierare**                                       | **Gränserna**     |
|------------------------------------------------------------|---------------|
| Minsta storlek på en nivå volym                            | 500 GB        |
| Maximal storlek på en nivå volym                            | 5 TB          |
| Minsta storlek på en lokalt fäst volym                    | 50 GB         |
| Maximal storlek på en lokalt fäst volym                    | 200 GB        |

## <a name="the-volumes-blade"></a>Bladet volymer
Menyn **volymer** på bladet StorSimple service Summary visar listan över lagrings volymer på en specifik StorSimple-matris och gör att du kan hantera dem.

![Bladet volymer](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

En volym består av en serie attribut:

* **Volym namn** – ett beskrivande namn som måste vara unikt och som hjälper dig att identifiera volymen.
* **Status** – kan vara online eller offline. Om en volym är offline är den inte synlig för initierare (servrar) som har behörighet att använda volymen.
* **Typ** – anger om volymen är i **nivå** av (standard) eller **lokalt fäst**.
* **Kapacitet** – anger mängden data som används jämfört med den totala mängden data som kan lagras av initieraren (servern).
* **Säkerhets kopiering** – i händelse av den virtuella StorSimple-matrisen aktive ras alla volymer automatiskt för säkerhets kopiering.
* **Anslutna värdar** – anger initierare (servrar) som har åtkomst till den här volymen.

![Information om volymer](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

Följ anvisningarna i den här självstudien för att utföra följande uppgifter:

* Lägg till en volym
* Ändra en volym
* Koppla från en volym
* Ta bort en volym

## <a name="add-a-volume"></a>Lägg till en volym

1. Från bladet StorSimple service Summary klickar du på **+ Lägg till volym** från kommando fältet. Då öppnas bladet **Lägg till volym** .
   
    ![Skärm bild som visar knappen Lägg till volym och fönstret Lägg till volym.](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. Gör följande på bladet **Lägg till volym** :
   
   * Ange ett unikt namn för din volym i fältet **volym namn** . Namnet måste vara en sträng som innehåller mellan 3 och 127 tecken.
   * I list rutan **typ** anger du om du vill skapa en **nivåAD** eller **lokalt fäst** volym. För arbets belastningar som kräver lokala garantier, låg latens och högre prestanda, väljer du **lokalt fästa volym**. För alla andra data väljer du **nivå** volym.
   * I fältet **kapacitet** anger du storleken på volymen. En nivå volym måste vara mellan 500 GB och 5 TB och en lokalt fäst volym måste vara mellan 50 GB och 500 GB.
   * * Klicka på **anslutna värdar**, Välj en åtkomst kontroll post (ACR) som motsvarar den iSCSI-initierare som du vill ansluta till den här volymen och klicka sedan på **Välj**.
3. Om du vill lägga till en ny ansluten värd klickar du på **Lägg till ny**, anger ett namn för värden och dess iSCSI-kvalificerade namn (IQN) och klickar sedan på **Lägg till**.
   
    ![Skärm bild som visar fönstret anslutna värdar där du kan lägga till nya.](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. Klicka på **skapa** när du är klar med konfigurationen av volymen. En volym skapas med de angivna inställningarna och du får ett meddelande om att det har skapats. Som standard kommer säkerhets kopiering att aktive ras för volymen.
5. Du kan kontrol lera att volymen har skapats genom att gå till bladet **volymer** . Du bör se volymen som visas.
   
    ![Volym skapandet lyckades](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>Ändra en volym

Ändra en volym när du behöver ändra de värdar som har åtkomst till volymen. Det går inte att ändra andra attribut för en volym när volymen har skapats.

#### <a name="to-modify-a-volume"></a>Ändra en volym

1. Från inställningen **volymer** på bladet StorSimple service Summary väljer du den virtuella matris som du vill ändra den volym som du vill ändra finns i.
2. **Välj** volymen och klicka på **anslutna värdar** för att visa den aktuella anslutna värden och ändra den till en annan server.
   
    ![Redigera volym](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. Spara ändringarna genom att klicka på **Spara** kommando fältet. De angivna inställningarna tillämpas och du får ett meddelande.

## <a name="take-a-volume-offline"></a>Koppla från en volym

Du kan behöva koppla från en volym när du planerar att ändra den eller ta bort den. När en volym är offline är den inte tillgänglig för Läs-och Skriv behörighet. Du måste ta volymen offline på värden och på enheten.

#### <a name="to-take-a-volume-offline"></a>Koppla från en volym

1. Kontrol lera att volymen i fråga inte används innan du tar den offline.
2. Ta volymen offline på värden först. Detta eliminerar eventuell risk för skadade data på volymen. Mer information om de olika stegen finns i anvisningarna för värd operativ systemet.
3. När volymen på värden är offline tar du volymen i matrisen offline genom att utföra följande steg:
   
   * Från inställningen **volymer** på bladet StorSimple service Summary väljer du den virtuella matris som du vill koppla från.
   * **Välj** volymen och klicka på **...** (alternativt högerklicka på den här raden) och välj **ta offline** i snabb menyn.
     
        ![Offline-volym](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * Granska informationen i bladet **ta offline** och bekräfta ditt godkännande av åtgärden. Klicka på koppla **från offline** för att ta volymen offline. Ett meddelande visas om att åtgärden pågår.
   * Du kan kontrol lera att volymen har kopplats från genom att gå till bladet **volymer** . Du bör se statusen för volymen som offline.
     
       ![Offline-volym bekräftelse](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>Ta bort en volym

> [!IMPORTANT]
> Du kan bara ta bort en volym om den är offline.
> 
> 

Utför följande steg för att ta bort en volym.

#### <a name="to-delete-a-volume"></a>Ta bort en volym

1. Från inställningen **volymer** på bladet StorSimple service Summary väljer du den virtuella matris som du vill ta bort den volym som du vill ta bort.
2. **Välj** volymen och klicka på **...** (alternativt högerklicka på den här raden) och välj **ta bort** på snabb menyn.
   
    ![Ta bort volym](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. Kontrol lera status för den volym som du vill ta bort. Om den volym som du vill ta bort inte är offline kopplar du först från genom att följa stegen i koppla från [en volym](#take-a-volume-offline).
4. När du uppmanas att bekräfta i bladet **ta bort** godkänner du bekräftelsen och klickar på **ta bort**. Volymen tas nu bort och **volym bladet visar den uppdaterade** listan över volymer i den virtuella matrisen.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [klonar en StorSimple volym](storsimple-virtual-array-clone.md).

