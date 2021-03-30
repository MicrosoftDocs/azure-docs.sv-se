---
title: Ange obligatoriska artefakter för din Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du anger obligatoriska artefakter som måste installeras innan du installerar eventuella användarspecifika artefakter på virtuella datorer (VM) i labbet.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0677f8bea35cb34735fdcf34e717eea349fad8bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85480311"
---
# <a name="specify-mandatory-artifacts-for-your-lab-in-azure-devtest-labs"></a>Ange obligatoriska artefakter för ditt labb i Azure DevTest Labs
Som ägare till ett labb kan du ange obligatoriska artefakter som tillämpas på alla datorer som skapas i labbet. Tänk dig ett scenario där du vill att varje dator i labbet ska vara ansluten till företagets nätverk. I det här fallet skulle varje labb användare behöva lägga till en domän kopplings artefakt när den virtuella datorn skapas för att kontrol lera att datorn är ansluten till företags domänen. Med andra ord måste labb användare i princip skapa en ny dator om de glömmer att tillämpa obligatoriska artefakter på datorn. Som labb ägare gör du domän kopplings artefakten som en obligatorisk artefakt i labbet. Det här steget ser till att varje dator är ansluten till företags nätverket och sparar tid och ansträngning för dina labb användare.
 
Andra obligatoriska artefakter kan innehålla ett gemensamt verktyg som ditt team använder, eller ett plattforms oberoende säkerhets paket som varje dator behöver ha som standard, osv. I korthet måste all vanlig program vara som varje dator i labbet har blivit en obligatorisk artefakt. Om du skapar en anpassad avbildning från en dator som har obligatoriska artefakter som tillämpas på den och sedan skapar en ny dator från avbildningen, tillämpas de obligatoriska artefakterna på datorn när du skapar den. Det innebär också att även om den anpassade avbildningen är gammal, varje gång du skapar en dator från den, tillämpas den senast uppdaterade versionen av obligatoriska artefakter under skapande flödet. 
 
Endast artefakter som saknar parametrar stöds som obligatoriska. Din labb användare behöver inte ange ytterligare parametrar under skapandet av labbet och därmed göra processen för att skapa en virtuell dator enkel. 

## <a name="specify-mandatory-artifacts"></a>Ange obligatoriska artefakter
Du kan välja obligatoriska artefakter för Windows-och Linux-datorer separat. Du kan också ändra ordningen på artefakterna beroende på i vilken ordning du vill att de ska tillämpas. 

1. På Start sidan i labbet väljer du **konfiguration och principer** under **Inställningar**. 
3. Välj **obligatoriska artefakter** under **externa resurser**. 
4. Välj **Redigera** i avsnittet **Windows** eller **Linux** . I det här exemplet används **Windows** -alternativet. 

    ![Sidan obligatoriska artefakter – knappen Redigera](media/devtest-lab-mandatory-artifacts/mandatory-artifacts-edit-button.png)
4. Välj en artefakt. I det här exemplet används **7-zip-** alternativ. 
5. På sidan **Lägg till artefakt** väljer du **Lägg till**. 

    ![Sidan obligatoriska artefakter – Lägg till 7-zip](media/devtest-lab-mandatory-artifacts/add-seven-zip.png)
6. Om du vill lägga till en annan artefakt väljer du artikeln och väljer **Lägg till**. Det här exemplet lägger till **krom** som den andra obligatoriska artefakten.

    ![Sidan obligatoriska artefakter – Lägg till Chrome](media/devtest-lab-mandatory-artifacts/add-chrome.png)
7. På sidan **obligatoriska artefakter** visas ett meddelande som anger antalet valda artefakter. Om du klickar på meddelandet visas de artefakter som du har valt. Välj **Spara** för att spara. 

    ![Sidan obligatoriska artefakter – Spara artefakter](media/devtest-lab-mandatory-artifacts/save-artifacts.png)
8. Upprepa stegen för att ange obligatoriska artefakter för virtuella Linux-datorer. 
    
    ![Sidan obligatoriska artefakter – Windows-och Linux-artefakter](media/devtest-lab-mandatory-artifacts/windows-linux-artifacts.png)
9. Om du vill **ta bort** en artefakt från listan väljer du **... (tre punkter)** i slutet av raden och välj **ta bort**. 
10. Om du vill **ändra ordning** på artefakter i listan, håller du mus pekaren över artefakten och väljer **... (tre punkter)** som visas i början av raden och dra objektet till den nya positionen. 
11. Om du vill spara obligatoriska artefakter i labbet väljer du **Spara**. 

    ![Sidan obligatoriska artefakter – Spara artefakter i labbet](media/devtest-lab-mandatory-artifacts/save-to-lab.png)
12. Stäng sidan **konfiguration och principer** (Välj **X** i det övre högra hörnet) för att gå tillbaka till start sidan för ditt labb.  

## <a name="delete-a-mandatory-artifact"></a>Ta bort en obligatorisk artefakt
Om du vill ta bort en obligatorisk artefakt från ett labb gör du följande: 

1. Välj **konfiguration och principer** under **Inställningar**. 
2. Välj **obligatoriska artefakter** under **externa resurser**. 
3. Välj **Redigera** i avsnittet **Windows** eller **Linux** . I det här exemplet används **Windows** -alternativet. 
4. Välj meddelandet med antalet obligatoriska artefakter högst upp. 

    ![Sidan obligatoriska artefakter – Välj meddelandet](media/devtest-lab-mandatory-artifacts/select-message-artifacts.png)
5. På sidan **valda artefakter** väljer du **... (tre punkter)** för att artefakten ska tas bort och välj **ta bort**. 
    
    ![Sidan obligatoriska artefakter – ta bort artefakt](media/devtest-lab-mandatory-artifacts/remove-artifact.png)
6. Klicka på **OK** för att stänga sidan **valda artefakter** . 
7. Välj **Spara** på sidan **obligatoriska artefakter** .
8. Upprepa stegen för **Linux** -avbildningar om det behövs. 
9. Välj **Spara** för att spara alla ändringar i labbet. 

## <a name="view-mandatory-artifacts-when-creating-a-vm"></a>Visa obligatoriska artefakter när du skapar en virtuell dator
Som en labb användare kan du nu visa listan över obligatoriska artefakter när du skapar en virtuell dator i labbet. Du kan inte redigera eller ta bort obligatoriska artefakter som angetts i labbet av din labb ägare.

1. På Start sidan för ditt labb väljer du **Översikt** på menyn.
2. Om du vill lägga till en virtuell dator i labbet väljer du **+ Lägg till**. 
3. Välj en **bas avbildning**. I det här exemplet används **Windows Server, version 1709**.
4. Observera att du ser ett meddelande om **artefakter** med antalet obligatoriska artefakter som valts. 
5. Välj **artefakter**. 
6. Bekräfta att du ser de **obligatoriska artefakterna** du angav i Labbets konfiguration och principer. 

    ![Skapa en virtuell dator – obligatoriska artefakter](media/devtest-lab-mandatory-artifacts/create-vm-artifacts.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [lägger till en git-artefakt i ett labb](devtest-lab-add-artifact-repo.md).

