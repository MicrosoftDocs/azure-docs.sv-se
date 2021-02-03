---
title: Konfigurera ett nätverks labb med Azure Lab Services och GNS3 | Microsoft Docs
description: Lär dig hur du konfigurerar ett labb med hjälp av Azure Lab Services att lära sig nätverk med GNS3.
ms.topic: article
ms.date: 01/19/2021
ms.openlocfilehash: dec5dea13d5a89536a06da45fc57d33881a9b3ad
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500518"
---
# <a name="set-up-a-lab-to-teach-a-networking-class"></a>Konfigurera ett labb för att lära en nätverks klass 
Den här artikeln visar hur du konfigurerar en klass som fokuserar på att tillåta studenter att emulera, konfigurera, testa och felsöka virtuella och verkliga nätverk med [gns3](https://www.gns3.com/) -programvara. 

Den här artikeln innehåller två huvud avsnitt. Det första avsnittet beskriver hur du skapar klass rummets labb. I det andra avsnittet beskrivs hur du skapar en mall med kapslad virtualisering aktive rad och med GNS3 installerat och konfigurerat.

## <a name="lab-configuration"></a>Labb konfiguration
Du måste ha en Azure-prenumeration för att komma igång för att kunna konfigurera det här labbet. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar. När du har skaffat en Azure-prenumeration kan du antingen skapa ett nytt labb konto i Azure Lab Services eller använda ett befintligt konto. Se följande självstudie för att skapa ett nytt labb konto: [självstudier för att konfigurera ett labb konto](tutorial-setup-lab-account.md).

Följ [den här självstudien](tutorial-setup-classroom-lab.md) för att skapa ett nytt labb och Använd sedan följande inställningar:

| Storlek för virtuell dator | Bild |
| -------------------- | ----- | 
| Stor (kapslad virtualisering) | Windows 10 Pro, version 1909 |

## <a name="template-machine"></a>Mall dator 

När du har skapat mallen startar du datorn och ansluter till den för att slutföra följande tre huvudsakliga uppgifter. 
 
1. Förbered mal Machine för kapslad virtualisering.
2. Installera GNS3.
3. Skapa kapslad virtuell GNS3-dator i Hyper-V.
4. Konfigurera GNS3 för att använda Windows Hyper-V VM.
5. Lägg till lämpliga anordningar.
6. Publicera mall.


### <a name="prepare-template-machine-for-nested-virtualization"></a>Förbered mall för kapslad virtualisering
- Följ anvisningarna i [den här artikeln](how-to-enable-nested-virtualization-template-vm.md) för att förbereda din virtuella mall för en kapslad virtualisering. 

### <a name="install-gns3"></a>Installera GNS3
- Följ anvisningarna för att [Installera gns3 i Windows](https://docs.gns3.com/docs/getting-started/installation/windows).  Se till att du inkluderar att installera den **virtuella datorn gns3** i komponent dialog rutan.

![SelectGNS3vm](./media/class-type-networking-gns3/gns3-select-vm.png)

Slutligen når du GNS3 VM-valet. Se till att välja alternativet **Hyper-V** .

![SelectHyperV](./media/class-type-networking-gns3/gns3-vm-hyper-v.png)

  Med det här alternativet hämtas PowerShell-skriptet och VHD-filerna för att skapa den virtuella GNS3-datorn i Hyper-V Manager. Fortsätt att installera med standardvärdena. **När installationen är klar startar du inte gns3**.

### <a name="create-gns3-vm"></a>Skapa GNS3 VM
När installationen har slutförts laddas en zip-fil **"GNS3.VM.Hyper-V.2.2.17.zip"** ned till samma mapp som installations filen, som innehåller enheterna och PowerShell-skriptet för att skapa den virtuella Hyper-V-datorn.
- **Extrahera alla** på GNS3.VM.Hyper-V.2.2.17.zip.  Den här åtgärden extraherar enheterna och PowerShell-skriptet för att skapa den virtuella datorn.
- **Kör med PowerShell** på PowerShell-skriptet "create-vm.ps1" genom att högerklicka på filen.
- En ändrings förfrågan för körnings principen kan visas. Ange "Y" för att köra skriptet.

![PSExecutionPolicy](./media/class-type-networking-gns3/powershell-execution-policy-change.png)

- När skriptet har slutförts kan du kontrol lera att den virtuella datorn "GNS3 VM" har skapats i Hyper-V Manager.

### <a name="configure-gns3-to-use-hyper-v-vm"></a>Konfigurera GNS3 för att använda virtuell Hyper-V-dator
Nu när GNS3 är installerat och den virtuella GNS3-datorn har lagts till kan du starta GNS3 för att länka samman de två.  [Installations guiden för gns3 startar automatiskt.](https://docs.gns3.com/docs/getting-started/setup-wizard-gns3-vm#local-gns3-vm-setup-wizard).  
- Använd **Kör-apparater från den virtuella datorn.** .  Använd standardinställningarna för resten av guiden tills du har nått **verktyget VMware vmrun kan inte hittas.** error.

![VMWareError](./media/class-type-networking-gns3/gns3-vmware-vmrun-tool-not-found.png)

- Välj **OK** och **Avbryt** guiden.
- För att slutföra anslutningen till den virtuella Hyper-V-datorn öppnar du **Redigera**  ->  **Inställningar**  ->  **gns3 VM** och väljer **Aktivera den virtuella gns3-datorn** och väljer alternativet **Hyper-v** .
 
![EnableGNS3VMs](./media/class-type-networking-gns3/gns3-preference-vm.png)

### <a name="add-appropriate-appliances"></a>Lägg till lämpliga anordningar

Nu ska du lägga till lämpliga [enheter för klassen.](https://docs.gns3.com/docs/using-gns3/beginners/install-from-marketplace)

### <a name="publish-template"></a>Publicera mall

Nu när mallen VM är korrekt konfigurerad och klar för publicering finns det några viktiga punkter att kontrol lera.
- Kontrol lera att den virtuella datorn GNS3 är avstängd eller avstängd.  Att publicera medan den virtuella datorn fortfarande körs skadar den virtuella datorn.
- Genom att stänga GNS3, publicera medan och köra kan du leda till oönskade sid effekter.
- Rensa installationsfiler eller andra onödiga filer.

## <a name="cost"></a>Cost  

Om du vill uppskatta kostnaden för det här labbet kan du använda följande exempel: 
 
För en klass av 25 studenter med 20 timmar schemalagda klass tider och 10 timmars kvot för läxor eller tilldelningar, skulle priset för labbet vara: 

25 studenter * (20 + 10) timmar * 84 lab-enheter * 0,01 USD per timme = 630 USD. 

**Viktigt:** Kostnads uppskattning är till exempel endast syfte.  Aktuell information om priser finns i [Azure Lab Services priser](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Slutsats
Den här artikeln gick dig genom stegen för att skapa ett labb för nätverks konfiguration med hjälp av GNS3.

## <a name="next-steps"></a>Nästa steg
Nästa steg är vanliga för att ställa in labb:

- [Lägg till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-postregistrering länkar till studenter](how-to-configure-student-usage.md#send-invitations-to-users).