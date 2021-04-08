---
title: 'Självstudie: initiera maskin vara – Azure FXT Edge-filer'
description: Lär dig hur du ansluter till noden maskin vara och anger ett första lösen ord på Azure FXT Edge-noder.
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 79d908077d3bd66a3ed86759e37574383358881b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92218858"
---
# <a name="tutorial-set-hardware-passwords"></a>Självstudie: Ange maskin varu lösen ord

Första gången du ska sätta igång en Azure FXT Edge-nod måste du ange ett rot lösen ord. Maskinvarukonfigurationerna levereras inte med ett standard lösen ord.

Nätverks portarna är inaktiverade tills lösen ordet har angetts och rot användaren loggar in.

Utför det här steget när du har installerat och kablaget noden, men innan du försöker skapa klustret.

I den här självstudien beskrivs hur du ansluter till noden maskin vara och anger lösen ordet. Här beskrivs också hur du lägger till ett lösen ord för BIOS-installationen för att skydda noden.

I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
>
> * Anslut ett tangent bord och övervaka till noden och slå på det
> * Ange ett lösen ord för BIOS-installation
> * Ange lösen ord för iDRAC-porten och rot användaren på den här noden
> * Logga in som rot

Upprepa de här stegen för varje nod som du ska använda i klustret.

Den här självstudien tar cirka 15 minuter att slutföra.

## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar den här självstudien utför du följande steg:

* [Installera](fxt-install.md) varje Azure FXT Edge-nod i ett utrustnings rack och koppla ström kablar och nätverks åtkomst enligt beskrivningen i den [tidigare kursen](fxt-network-power.md).
* Hitta ett USB-kopplat tangent bord och en VGA-ansluten Övervakare som du kan ansluta till de andra maskin varu noderna. (Nodens serie port är inaktiv innan du anger lösen ordet.)

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>Ansluta ett tangent bord och övervaka till noden

Anslut en bildskärm och ett tangent bord fysiskt till noden Azure FXT Edge-filer.

* Anslut övervakaren till VGA-porten.
* Anslut tangent bordet till någon av USB-portarna.

Använd det här referens diagrammet för att hitta portarna på bak sidan av chassit.

> [!NOTE]
> Den seriella porten är inaktiv tills du har angett lösen ordet.

![diagram över bak sidan av Azure FXT Edge-filer med serie-, VGA-och USB-portar märkta](media/fxt-back-serial-vga-usb.png)

Du kan använda en KVM-växel om du vill ansluta mer än en nod till samma kring utrustning.

Starta noden genom att trycka på strömbrytaren längst fram.

![diagram över fram sidan av Azure FXT Edge-filer – rund ström knapp är märkt nära det övre högra hörnet](media/fxt-front-annotated.png)

## <a name="create-a-bios-setup-password"></a>Skapa ett lösen ord för BIOS-installation

Ett lösen ord för BIOS-installationen skyddar nodens BIOS-inställningar från oavsiktliga eller obehöriga ändringar. Det här lösen ordet behövs inte för att skapa ett kluster, men det rekommenderas starkt som en del av säkerhets strategin för klustret.

Så här skapar du ett lösen ord för BIOS-installationen:

1. Aktivera eller starta om noden och tryck direkt på F2 för att öppna system konfigurations verktyget.

1. På skärmen **Systeminställningar på huvud menyn** väljer du **system** säkerhet för BIOS-  >  **systemet**.

1. Kontrol lera att inställningen för **lösen ords status** är **upplåst**.

1. Använd fältet **Konfigurera lösen ord** för att ange lösen ordet. (Du kan också ange ett BIOS-lösenord för systemet från den här skärmen om du vill använda ett.)

1. Tryck på ESC för att återgå till **systemets BIOS** -skärm och tryck sedan på ESC igen. Ett meddelande visas där du ombeds att spara ändringarna. Om systemet inte startas om automatiskt startar du om det för att komma till den vanliga start skärmen.<!-- how to exit this mode/do you need to reboot to get to the initial setup screen? -->

## <a name="set-initial-passwords"></a>Ange ursprungliga lösen ord

Noden Azure FXT Edge-filer kommer att skriva ut olika meddelanden till övervakaren vid start. Efter en liten stund visas ett första installations fönster som detta:

```
------------------------------------------------------
        Microsoft FXT node initial setup
------------------------------------------------------
Password Setup
---------------
Enter a password to set the iDRAC and temporary root password.
Minimum password length is 8.

NOTE: This does not set a BIOS setup password. For security,
Microsoft recommends using a BIOS setup password, restricting
physical access to the node, and other measures. Learn more at
https://aka.ms/fxt-security.

Enter new password:

```

Det lösen ord som du anger används för två saker:

* Det är det tillfälliga rot lösen ordet för den här Azure FXT Edge-noden.

  Det här lösen ordet ändras när du skapar ett kluster med hjälp av den här noden, eller när du lägger till den här noden i klustret. Lösen ordet för kluster hantering (kopplat till användaren ``admin`` ) är också rot lösen ordet för alla noder i ett kluster.

* Det är det långsiktiga lösen ordet för iDRAC/IPMI-porten för maskin varu hantering.

  Se till att du kommer ihåg lösen ordet om du skulle behöva logga in med IPMI senare för att felsöka ett maskin varu problem.

Ange och bekräfta lösen ordet:

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

När du har angett lösen ordet fortsätter systemet att starta. När den är klar får du en ``login:`` uppvarning.

## <a name="sign-in-as-root"></a>Logga in som rot

Logga in ``root`` med det lösen ord som du nyss angav.

```
login: root
Password:**********
```

När du har loggat in som rot är nätverks portarna aktiva och kommer att kontakta DHCP-servern för IP-adresser.

## <a name="next-steps"></a>Nästa steg

Noden är klar att ingå i ett kluster. Du kan använda den för att skapa ett Azure FXT Edge-kluster, eller så kan du [lägga till det i ett befintligt kluster](fxt-add-nodes.md).

> [!div class="nextstepaction"]
> [Skapa ett kluster](fxt-cluster-create.md)
