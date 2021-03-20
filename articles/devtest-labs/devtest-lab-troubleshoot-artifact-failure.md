---
title: Diagnostisera artefakt fel i en Azure DevTest Labs virtuell dator
description: DevTest Labs tillhandahåller information som du kan använda för att diagnostisera ett artefakt fel. Den här artikeln visar hur du felsöker artefakt fel.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 440ce6a537ac8d6a21ae8010bfbb3c38a82bf01e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85480821"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnostisera artefakt fel i labbet 
När du har skapat en artefakt kan du kontrol lera om den har lyckats eller misslyckats. Artefakt loggar i Azure DevTest Labs innehåller information som du kan använda för att diagnostisera ett artefakt fel. Det finns ett par alternativ för att Visa artefakt logg information för en virtuell Windows-dator:

* I Azure-portalen
* I den virtuella datorn

> [!NOTE]
> För att säkerställa att fel identifieras korrekt och förklaras är det viktigt att artefakten har rätt struktur. Information om hur du skapar en artefakt korrekt finns i [skapa anpassade artefakter](devtest-lab-artifact-author.md). Du kan se ett exempel på en korrekt strukturerad artefakt genom att titta på [test parameter typer](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) artefakt.

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Felsöka artefakt fel med hjälp av Azure Portal

1. I listan över resurser i Azure Portal väljer du ditt labb.
2. Välj den virtuella Windows-dator som innehåller den artefakt som du vill undersöka.
3. I den vänstra panelen under **Allmänt** väljer du **artefakter**. En lista över artefakter som är associerade med den virtuella datorn visas. Namnet på artefakten och artefakt statusen anges.

   ![Artefaktstatus](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure-new.png)

4. Välj en artefakt som visar statusen **misslyckades** . Artefakten öppnas. Ett tilläggs meddelande som innehåller information om fel i artefakten visas.

   ![Artefakt fel meddelande](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Felsök artefakt fel inifrån den virtuella datorn

1. Logga in på den virtuella datorn som innehåller den artefakt som du vill diagnostisera.
2. Gå till C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension \\ *1,9*\Status, där *1,9* är Azures anpassade skript tilläggs versions nummer.

   ![Status filen](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status-new.png)

3. Öppna **status** filen.

Instruktioner för hur du hittar loggfilerna på en virtuell **Linux** -dator finns i följande artikel: [Använd Azures anpassade skript tillägg version 2 med virtuella Linux-datorer](../virtual-machines/extensions/custom-script-linux.md#troubleshooting)


## <a name="related-blog-posts"></a>Relaterade blogg inlägg
* [Anslut en virtuell dator till en befintlig Active Directory domän med hjälp av en Resource Manager-mall i DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [lägger till en git-lagringsplats i ett labb](devtest-lab-add-artifact-repo.md).

