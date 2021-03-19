---
title: Hantera app-grupper för Windows Virtual Desktop Portal – Azure
description: Hantera Windows-appar för virtuella skriv bord med Azure Portal.
author: Heidilohr
ms.topic: tutorial
ms.date: 10/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ec26f021ffa581b0713973904c97349df83a08ef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91930279"
---
# <a name="tutorial-manage-app-groups-with-the-azure-portal"></a>Självstudie: hantera program grupper med Azure Portal

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/manage-app-groups-2019.md).

Standard-app-gruppen som skapas för en ny Windows-pool för virtuella skriv bord publicerar också hela Skriv bordet. Dessutom kan du skapa en eller flera RemoteApp-programgrupper för poolen. Följ den här självstudien för att skapa en RemoteApp-app-grupp och publicera enskilda Start meny-appar.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en RemoteApp-grupp.
> * Bevilja åtkomst till RemoteApp-program.

## <a name="create-a-remoteapp-group"></a>Skapa en RemoteApp-grupp

Om du redan har skapat en adresspool och en session som är värd för en virtuell dator med hjälp av Azure Portal eller PowerShell kan du lägga till program grupper från Azure Portal med följande process:

1.  Logga in på [Azure-portalen](https://portal.azure.com/).
   
    >[!NOTE]
    > Om du loggar in på US Gov portal går du till [https://portal.azure.us/](https://portal.azure.us/) i stället.

2.  Sök efter och välj **virtuellt skriv bord i Windows**.

3. Du kan lägga till en program grupp direkt eller så kan du lägga till den från en befintlig värdbaserad pool. Välj ett alternativ nedan:

    - Välj **program grupper** i menyn på vänster sida av sidan och välj sedan **+ Lägg till**.

    - Välj **pooler** i menyn på vänster sida av skärmen, Välj namnet på värddatorn, Välj **program grupper** i menyn på vänster sida och välj sedan **+ Lägg till**. I det här fallet är värd poolen redan vald på fliken grundläggande.

4. På fliken **grundläggande** väljer du den **prenumeration** och **resurs grupp** som du vill skapa app-gruppen för. Du kan också välja att skapa en ny resurs grupp i stället för att välja en befintlig.

5. Välj den **modempool** som ska associeras med program gruppen från den nedrullningsbara menyn.

    >[!NOTE]
    >Du måste välja den modempool som är associerad med program gruppen. App-grupper har appar eller skriv bord som hanteras från en sessions värd och sessions-värdar är en del av värd-pooler. App-gruppen måste vara kopplad till en adresspool när den skapas.

    > [!div class="mx-imgBorder"]
    > ![En skärm bild av fliken grundläggande i Azure Portal.](media/basics-tab.png)

6. Välj **RemoteApp** under **program grupps typ** och ange ett namn för RemoteApp.

      > [!div class="mx-imgBorder"]
      > ![En skärm bild av fälten för program grupp typ. "RemoteApp" är markerat.](media/remoteapp-button.png)

7.  Välj **Nästa: fliken tilldelningar >** .

8.  Om du vill tilldela enskilda användare eller användar grupper till app-gruppen väljer du **+ Lägg till Azure AD-användare eller användar grupper**.

9.  Välj de användare som du vill ska ha åtkomst till apparna. Du kan välja en eller flera användare och användar grupper.

     > [!div class="mx-imgBorder"]
     > ![En skärm bild av menyn för val av användare.](media/select-users.png)

10.  Välj **Välj**.

11.  Välj **Nästa: program >** och välj sedan **+ Lägg till program**.

12.  Så här lägger du till ett program på Start-menyn:

      - Under **program källa** väljer du **Start-menyn** på den nedrullningsbara menyn. Välj sedan programmet i den nedrullningsbara menyn under **program**.

     > [!div class="mx-imgBorder"]
     > ![En skärm bild av skärmen Lägg till program med Start-menyn markerad.](media/add-app-start.png)

      - I **visnings namn** anger du namnet på det program som ska visas för användaren på klienten.

      - Lämna de andra alternativen som de är och välj **Spara**.

13.  Så här lägger du till ett program från en specifik fil Sök väg:

      - Under **program källa** väljer du **fil Sök väg** på den nedrullningsbara menyn.

      - I **program Sök väg** anger du sökvägen till programmet på den värd för sessionen som registrerats för den associerade poolen.

      - Ange programmets information i fälten **program namn**, **visnings namn**, **sökväg** och **ikon index** .

      - Välj **Spara**.

     > [!div class="mx-imgBorder"]
     > ![En skärm bild av sidan Lägg till program med fil Sök väg vald.](media/add-app-file.png)

14.  Upprepa den här processen för alla program som du vill lägga till i program gruppen.

15.  Välj nästa **: >arbets yta**.

16.  Om du vill registrera app-gruppen på en arbets yta väljer du **Ja** för **Registrera program grupp**. Om du hellre vill registrera app-gruppen vid ett senare tillfälle väljer du **Nej**.

17.  Om du väljer **Ja** kan du välja en befintlig arbets yta för att registrera din app-grupp på.

       >[!NOTE]
       >Du kan bara registrera app-gruppen på arbets ytor som har skapats på samma plats som poolen. Dessutom. Om du tidigare har registrerat en annan app-grupp från samma adresspool som den nya app-gruppen till en arbets yta, kommer den att markeras och du kan inte redigera den. Alla app-grupper från en adresspool måste vara registrerade på samma arbets yta.

     > [!div class="mx-imgBorder"]
     > ![En skärm bild av sidan Registrera program grupp för en befintlig arbets yta. Host-poolen är förvalt.](media/register-existing.png)

18.  Om du vill skapa taggar för att göra din arbets yta lätt att organisera, väljer du **Nästa: taggar >** och anger dina taggnamn.

19.  När du är klar väljer du **Granska + skapa**.

20.  Vänta en stund tills verifierings processen har slutförts. När du är färdig väljer du **skapa** för att distribuera din app-grupp.

Distributions processen gör följande saker för dig:

- Skapa gruppen RemoteApp-app.
- Lägg till de valda apparna i app-gruppen.
- Publicera app-gruppen som publicerats till användare och användar grupper som du har valt.
- Registrera app-gruppen, om du har valt att göra det.
- Skapa en länk till en Azure Resource Manager-mall baserat på din konfiguration som du kan hämta och spara för senare.

>[!IMPORTANT]
>Du kan bara skapa 200-program grupper för varje Azure Active Directory klient organisation. Vi har lagt till den här gränsen på grund av tjänst begränsningar för hämtning av feeds för våra användare. Den här begränsningen gäller inte för app-grupper som skapats i Windows Virtual Desktop (klassisk).

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skapar en app-grupp, fyller den med RemoteApp-program och tilldelar användare till app-gruppen. Information om hur du skapar en pool för validerings värdar finns i följande självstudie. Du kan använda en pool för validerings värdar för att övervaka tjänst uppdateringar innan du återställer dem till din produktions miljö.

> [!div class="nextstepaction"]
> [Skapa en värdpool för att verifiera tjänstuppdateringar](./create-validation-host-pool.md)
