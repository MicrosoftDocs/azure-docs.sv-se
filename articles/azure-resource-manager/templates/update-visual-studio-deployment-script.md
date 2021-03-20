---
title: Uppdatera Visual Studios skript för mall distribution för att använda AZ PowerShell
description: Uppdatera Visual Studio Template Deployment-skriptet från AzureRM till AZ PowerShell
author: cweining
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: cweining
ms.openlocfilehash: 357e0289f3237ed32b0801280316225ba5530282
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "76963877"
---
# <a name="update-visual-studio-template-deployment-script-to-use-az-powershell-module"></a>Uppdatera Visual Studio-mall för distribution av skript för att använda AZ PowerShell-modulen

Visual Studio 16,4 stöder användning av AZ PowerShell-modulen i skriptet för mall-distribution. Visual Studio installerar dock inte den modulen automatiskt. Om du vill använda AZ-modulen måste du vidta fyra steg:

1. [Avinstallera AzureRM-modul](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module)
1. [Installera AZ-modul](/powershell/azure/install-az-ps)
1. Uppdatera Visual Studio till 16,4
1. Uppdatera distributions skriptet i projektet.

## <a name="update-visual-studio-to-164"></a>Uppdatera Visual Studio till 16,4

Uppdatera installationen av Visual Studio till version 16,4 eller senare. Under uppgraderingen kontrollerar du att komponenten Azure PowerShell inte är markerad. Eftersom du har installerat AZ-modulen via galleriet vill du inte installera om AzureRM-modulen.

Om du redan har uppgraderat till 16,4 och Azure PowerShell komponenten har marker ATS kan du avinstallera den genom att köra Visual Studio Installer. Välj inte Azure PowerShell-komponenten i Azure-arbetsbelastningen eller på sidan enskilda komponenter.

## <a name="update-the-deployment-script-in-your-project"></a>Uppdatera distributions skriptet i projektet

Ersätt alla förekomster av strängen "AzureRm" med "AZ" i distributions skriptet. Du kan referera till revisioner i det här [registret](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) för att se ändringarna. Mer information om hur du uppgraderar skript till AZ-modulen finns i [migrera Azure PowerShell från AzureRM till AZ](/powershell/azure/migrate-from-azurerm-to-az).

## <a name="next-steps"></a>Nästa steg

Information om hur du använder Visual Studio-projektet finns i [skapa och Distribuera Azure Resource Group-projekt via Visual Studio](create-visual-studio-deployment-project.md).
