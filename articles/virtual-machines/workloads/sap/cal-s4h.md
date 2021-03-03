---
title: Distribuera SAP S/4HANA eller BW/4HANA på en virtuell Azure-dator | Microsoft Docs
description: Distribuera SAP S/4HANA eller BW/4HANA på en virtuell Azure-dator
author: hermanndms
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 09/15/2016
ms.author: hermannd
ms.reviewer: cynthn
ms.openlocfilehash: 4b739efcf2695a5385351f531efb6201bbea1cd5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671932"
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>Distribuera SAP S/4HANA eller BW/4HANA på Azure
Den här artikeln beskriver hur du distribuerar S/4HANA på Azure med hjälp av SAP-biblioteket för Cloud Library (SAP CAL) 3,0. Om du vill distribuera andra SAP HANA-baserade lösningar, till exempel BW/4HANA, följer du samma steg.

> [!NOTE]
> Mer information om SAP CAL finns på webbplatsen för [SAP Cloud Library Library](https://cal.sap.com/) . SAP innehåller också en blogg om [SAP Cloud-Programbiblioteket 3,0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).
> 
> [!NOTE]
> Från och med den 29 maj 2017 kan du använda Azure Resource Manager distributions modell utöver den enklare klassiska distributions modellen för att distribuera SAP CAL. Vi rekommenderar att du använder den nya distributions modellen för Resource Manager och ignorerar den klassiska distributions modellen.

## <a name="step-by-step-process-to-deploy-the-solution"></a>Steg för steg-process för att distribuera lösningen

Följande sekvens med skärm bilder visar hur du distribuerar S/4HANA på Azure med hjälp av SAP CAL. Processen fungerar på samma sätt för andra lösningar, t. ex. BW/4HANA.

På sidan **lösningar** visas några av de SAP Cal Hana-baserade lösningar som är tillgängliga på Azure. **SAP S/4HANA 1610-FPS01, Fully-Activated-** installationen finns på den mittersta raden:

![SAP CAL-lösningar](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>Skapa ett konto i SAP CAL
1. För att logga in på SAP CAL för första gången använder du din SAP S-användare eller andra användare som har registrerats med SAP. Definiera sedan ett SAP CAL-konto som används av SAP CAL för att distribuera enheter på Azure. I konto definitionen måste du:

    a. Välj distributions modell på Azure (Resource Manager eller klassisk).

    b. Ange din Azure-prenumeration. Ett SAP CAL-konto kan bara tilldelas till en prenumeration. Om du behöver mer än en prenumeration måste du skapa ett annat SAP CAL-konto.

    c. Ge SAP CAL-behörighet att distribuera till din Azure-prenumeration.

   > [!NOTE]
   >  Nästa steg visar hur du skapar ett SAP CAL-konto för Resource Manager-distributioner. Om du redan har ett SAP CAL-konto som är länkat till den klassiska distributions modellen, *måste* du följa de här stegen för att skapa ett nytt SAP Cal-konto. Det nya SAP CAL-kontot måste distribueras i Resource Manager-modellen.

1. Skapa ett nytt SAP CAL-konto. På sidan **konton** visas tre alternativ för Azure: 

    a. **Microsoft Azure (klassisk)** är den klassiska distributions modellen och är inte längre önskad.

    b. **Microsoft Azure** är den nya distributions modellen för Resource Manager.

    c. **Windows Azure som drivs av 21Vianet** är ett alternativ i Kina som använder den klassiska distributions modellen.

    Om du vill distribuera i Resource Manager-modellen väljer du **Microsoft Azure**.

    ![Information om SAP CAL-konto](./media/cal-s4h/s4h-pic-2a.png)

1. Ange det ID för Azure **-prenumeration** som finns på Azure Portal.

   ![SAP CAL-konton](./media/cal-s4h/s4h-pic3c.png)

1. Om du vill ge SAP-CAL att distribuera till den Azure-prenumeration som du har definierat klickar du på **auktorisera**. Följande sida visas på fliken webbläsare:

   ![Internet Explorer Cloud Services-inloggning](./media/cal-s4h/s4h-pic4c.png)

1. Om det finns fler än en användare i listan väljer du den Microsoft-konto som är länkad till som medadministratör för den Azure-prenumeration som du har valt. Följande sida visas på fliken webbläsare:

   ![Bekräftelse av Internet Explorer Cloud Services](./media/cal-s4h/s4h-pic5a.png)

1. Klicka på **Acceptera**. Om auktoriseringen lyckas visas en konto definition för SAP CAL igen. Efter en kort stund bekräftar ett meddelande att auktoriseringen lyckades.

1. Om du vill tilldela det nya SAP CAL-kontot till användaren anger du ditt **användar-ID** i text rutan till höger och klickar på **Lägg till**.

   ![Konto till användar Association](./media/cal-s4h/s4h-pic8a.png)

1. Klicka på **Granska** om du vill koppla ditt konto till den användare som du använder för att logga in på SAP Cal. 
 
1. Klicka på **skapa** om du vill skapa kopplingen mellan din användare och det nyligen skapade SAP Cal-kontot.

   ![Användare till SAP-konto koppling för CAL](./media/cal-s4h/s4h-pic9b.png)

Du har skapat ett SAP CAL-konto som kan:

- Använd Resource Manager-distributionsmodellen.
- Distribuera SAP-system i din Azure-prenumeration.

Nu kan du börja distribuera S/4HANA till din användar prenumeration i Azure.

> [!NOTE]
> Innan du fortsätter bör du ta reda på om du har Azure vCPU-kvoter för virtuella datorer i Azure H-serien. För närvarande använder SAP CAL H-seriens virtuella datorer i Azure för att distribuera några av de SAP HANA-baserade lösningarna. Din Azure-prenumeration har kanske inte några vCPU-kvoter i H-serien för H-serien. I så fall kan du behöva kontakta Azure-supporten för att få en kvot på minst 16 H-seriens virtuella processorer.
> 
> [!NOTE]
> När du distribuerar en lösning på Azure i SAP CAL kanske du upptäcker att du bara kan välja en Azure-region. Om du vill distribuera till andra Azure-regioner än den som föreslås av SAP CAL måste du köpa en CAL-prenumeration från SAP. Du kan också behöva öppna ett meddelande med SAP för att ditt CAL-konto ska kunna levereras till andra Azure-regioner än de som ursprungligen föreslagits.

### <a name="deploy-a-solution"></a>Distribuera en lösning

Nu ska vi distribuera en lösning från sidan **lösningar** i SAP Cal. SAP CAL har två sekvenser att distribuera:

- En grundläggande sekvens som använder en sida för att definiera det system som ska distribueras
- En avancerad sekvens som ger dig vissa val av VM-storlekar 

Vi demonstrerar den grundläggande sökvägen till distributionen här.

1. På sidan **konto information** måste du:

    a. Välj ett SAP CAL-konto. (Använd ett konto som är associerat med distribution med distributions modellen för Resource Manager.)

    b. Ange ett instans **namn**.

    c. Välj en Azure- **region**. SAP CAL föreslår en region. Om du behöver en annan Azure-region och du inte har en prenumeration på SAP CAL måste du beställa en CAL-prenumeration med SAP.

    d. Ange ett huvud **lösen ord** för lösningen med åtta eller nio tecken. Lösen ordet används för administratörerna för de olika komponenterna.

   ![SAP CAL Basic-läge: skapa instans](./media/cal-s4h/s4h-pic10a.png)

1. Klicka på **skapa** och i meddelande rutan som visas klickar du på **OK**.

   ![SAP CAL-kompatibla VM-storlekar](./media/cal-s4h/s4h-pic10b.png)

1. I dialog rutan **privat nyckel** klickar du på **Arkiv** för att lagra den privata nyckeln i SAP Cal. Klicka på **Hämta** för att använda lösen ords skydd för den privata nyckeln. 

   ![Offentlig SAP CAL-nyckel](./media/cal-s4h/s4h-pic10c.png)

1. Läs **varnings** meddelandet för SAP CAL och klicka på **OK**.

   ![SAP CAL-varning](./media/cal-s4h/s4h-pic10d.png)

    Nu sker distributionen. Efter en stund visas statusen aktiv och klar för användning, beroende på lösningens storlek och komplexitet (SAP CAL tillhandahåller en uppskattning).

1. Om du vill hitta de virtuella datorer som samlats in med andra tillhör ande resurser i en resurs grupp går du till Azure Portal: 

   ![SAP CAL-objekt som distribuerats i den nya portalen](./media/cal-s4h/sapcaldeplyment_portalview.png)

1. På SAP CAL-portalen visas statusen som **aktiv**. Klicka på **Anslut** för att ansluta till lösningen. Olika alternativ för att ansluta till de olika komponenterna distribueras i den här lösningen.

   ![SAP CAL-instanser](./media/cal-s4h/active_solution.png)

1. Innan du kan använda något av alternativen för att ansluta till de distribuerade systemen klickar du på **komma igång guide**. 

   ![Anslut till instansen](./media/cal-s4h/connect_to_solution.png)

    Dokumentationen namnger användarna för var och en av anslutnings metoderna. Lösen orden för dessa användare anges till det huvud lösen ord som du definierade i början av distributions processen. I dokumentationen visas andra mer funktionella användare med sina lösen ord, som du kan använda för att logga in på det distribuerade systemet. 

    Om du t. ex. använder det SAP-GUI som är förinstallerat på Windows Remote Desktop-datorn kan S/4-systemet se ut så här:

   ![SM50 i det förinstallerade SAP-ANVÄNDARGRÄNSSNITTET](./media/cal-s4h/gui_sm50.png)

    Eller om du använder DBACockpit kan instansen se ut så här:

   ![SM50 i DBACockpit SAP GUI](./media/cal-s4h/dbacockpit.png)

Inom några timmar distribueras en felfri SAP S/4-installation i Azure.

Om du har köpt en SAP CAL-prenumeration stöder SAP fullständigt distributioner via SAP CAL på Azure. Support kön är BC-VCM-CAL.







