---
title: 'Distribuera SAP IDE: er EHP7 SP3 för SAP ERP 6,0 på Azure | Microsoft Docs'
description: 'Distribuera SAP IDE: er EHP7 SP3 för SAP ERP 6,0 på Azure'
author: hermanndms
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 09/16/2016
ms.author: hermannd
ms.openlocfilehash: 5184df38b6a6b2757a5d5f7c0970258657cf5c0f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670998"
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>Distribuera SAP IDE: er EHP7 SP3 för SAP ERP 6,0 på Azure
I den här artikeln beskrivs hur du distribuerar ett SAP IDE: er-system som körs med SQL Server och Windows-operativsystemet på Azure via bibliotek för SAP Cloud Library (SAP CAL) 3,0. Skärm bilderna visar steg för steg-processen. Följ samma steg om du vill distribuera en annan lösning.

Börja med SAP CAL genom att gå till webbplatsen [SAP Cloud Library Library Library](https://cal.sap.com/) . SAP har också en blogg om den nya [SAP Cloud-biblioteket 3,0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 

> [!NOTE]
> Från och med den 29 maj 2017 kan du använda Azure Resource Manager distributions modell utöver den enklare klassiska distributions modellen för att distribuera SAP CAL. Vi rekommenderar att du använder den nya distributions modellen för Resource Manager och ignorerar den klassiska distributions modellen.

Om du redan har skapat ett SAP CAL-konto som använder den klassiska modellen *måste du skapa ett annat SAP Cal-konto*. Det här kontot behöver endast distribueras till Azure med hjälp av Resource Manager-modellen.

När du har loggat in på SAP CAL, leder den första sidan vanligt vis till **lösnings** sidan. Lösningarna som erbjuds i SAP-CAL ökar ständigt, så du kan behöva rulla en aning lite lite för att hitta den lösning som du vill ha. Den markerade Windows-baserade SAP IDE: er-lösningen som är tillgänglig exklusivt i Azure demonstrerar distributions processen:

![SAP CAL-lösningar](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>Skapa ett konto i SAP CAL
1. För att logga in på SAP CAL för första gången använder du din SAP S-användare eller andra användare som har registrerats med SAP. Definiera sedan ett SAP CAL-konto som används av SAP CAL för att distribuera enheter på Azure. I konto definitionen måste du:

    a. Välj distributions modell på Azure (Resource Manager eller klassisk).

    b. Ange din Azure-prenumeration. Ett SAP CAL-konto kan bara tilldelas till en prenumeration. Om du behöver mer än en prenumeration måste du skapa ett annat SAP CAL-konto.
    
    c. Ge SAP CAL-behörighet att distribuera till din Azure-prenumeration.

   > [!NOTE]
   >  Nästa steg visar hur du skapar ett SAP CAL-konto för Resource Manager-distributioner. Om du redan har ett SAP CAL-konto som är länkat till den klassiska distributions modellen, *måste* du följa de här stegen för att skapa ett nytt SAP Cal-konto. Det nya SAP CAL-kontot måste distribueras i Resource Manager-modellen.

1. Om du vill skapa ett nytt SAP CAL-konto visar sidan **konton** två alternativ för Azure: 

    a. **Microsoft Azure (klassisk)** är den klassiska distributions modellen och är inte längre önskad.

    b. **Microsoft Azure** är den nya distributions modellen för Resource Manager.

    ![Skärm bilden visar S ett P CAL-konton med Microsoft Azure som kallas.](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    Om du vill distribuera i Resource Manager-modellen väljer du **Microsoft Azure**.

    ![Skärm bilden visar S ett P CAL-konton.](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Ange det ID för Azure **-prenumeration** som finns på Azure Portal. 

    ![Prenumerations-ID för SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Om du vill ge SAP-CAL att distribuera till den Azure-prenumeration som du har definierat klickar du på **auktorisera**. Följande sida visas på fliken webbläsare:

    ![Internet Explorer Cloud Services-inloggning](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

1. Om det finns fler än en användare i listan väljer du den Microsoft-konto som är länkad till som medadministratör för den Azure-prenumeration som du har valt. Följande sida visas på fliken webbläsare:

    ![Bekräftelse av Internet Explorer Cloud Services](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

1. Klicka på **Acceptera**. Om auktoriseringen lyckas visas en konto definition för SAP CAL igen. Efter en kort stund bekräftar ett meddelande att auktoriseringen lyckades.

1. Om du vill tilldela det nya SAP CAL-kontot till användaren anger du ditt **användar-ID** i text rutan till höger och klickar på **Lägg till**. 

    ![Konto till användar Association](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

1. Klicka på **Granska** om du vill koppla ditt konto till den användare som du använder för att logga in på SAP Cal. 

1. Klicka på **skapa** om du vill skapa kopplingen mellan din användare och det nyligen skapade SAP Cal-kontot.

    ![Användare till konto-Association](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

Du har skapat ett SAP CAL-konto som kan:

- Använd Resource Manager-distributionsmodellen.
- Distribuera SAP-system i din Azure-prenumeration.

> [!NOTE]
> Innan du kan distribuera SAP IDE: er-lösningen som baseras på Windows och SQL Server kan du behöva registrera dig för en SAP CAL-prenumeration. Annars kan lösningen visas som **låst** på översikts sidan.

### <a name="deploy-a-solution"></a>Distribuera en lösning
1. När du har konfigurerat ett SAP CAL-konto väljer **du lösningen SAP IDE: er på Windows och SQL Server** lösning. Klicka på **skapa instans** och bekräfta villkoren för användning och villkor. 

1. På sidan **Basic-läge: skapa instans** måste du:

    a. Ange ett instans **namn**.

    b. Välj en Azure- **region**. Du kan behöva en SAP CAL-prenumeration för att få flera tillgängliga Azure-regioner.

    c.  Ange huvud **lösen ordet** för lösningen som det visas:

    ![SAP CAL Basic-läge: skapa instans](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

1. Klicka på **Skapa**. Efter en stund, beroende på lösningens storlek och komplexitet (SAP CAL tillhandahåller en uppskattning), visas statusen aktive ras och klar för användning: 

    ![SAP CAL-instanser](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

1. Om du vill hitta resurs gruppen och alla objekt som har skapats av SAP CAL går du till Azure Portal. Den virtuella datorn kan hittas från och med samma instans namn som angavs i SAP CAL.

    ![Resurs grupps objekt](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

1. Gå till de distribuerade instanserna på SAP CAL-portalen och klicka på **Anslut**. Följande popup-fönster visas: 

    ![Anslut till instansen](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

1. Innan du kan använda något av alternativen för att ansluta till de distribuerade systemen klickar du på **komma igång guide**. Dokumentationen namnger användarna för var och en av anslutnings metoderna. Lösen orden för dessa användare anges till det huvud lösen ord som du definierade i början av distributions processen. I dokumentationen visas andra mer funktionella användare med sina lösen ord, som du kan använda för att logga in på det distribuerade systemet.

    ![Dokumentation om SAP Welcome](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

Inom några timmar distribueras ett felfritt SAP IDE: er-system i Azure.

Om du har köpt en SAP CAL-prenumeration stöder SAP fullständigt distributioner via SAP CAL på Azure. Support kön är BC-VCM-CAL.

