---
title: Windows Virtual Desktop (klassisk) Azure Resource Manager – Azure
description: Så här skapar du en adresspool i Windows Virtual Desktop (klassisk) med en Azure Resource Manager-mall.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b9999ae92840d79bb19464216c0f28504011b3f3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88008329"
---
# <a name="create-a-host-pool-in-windows-virtual-desktop-classic-with-an-azure-resource-manager-template"></a>Skapa en adresspool i Windows Virtual Desktop (klassisk) med en Azure Resource Manager-mall

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer (klassisk), vilket inte stöder Azure Resource Manager virtuella Skriv bords objekt i Windows.

Värdbaserade pooler är en samling av en eller flera identiska virtuella datorer i Windows-miljöer för virtuella Skriv bords klienter. Varje adresspool kan innehålla en app-grupp som användare kan interagera med på samma sätt som på ett fysiskt skriv bord.

Följ anvisningarna i det här avsnittet för att skapa en adresspool för en Windows-klient för virtuella skriv bord med en Azure Resource Manager-mall från Microsoft. I den här artikeln får du lära dig hur du skapar en adresspool i Windows Virtual Desktop, skapar en resurs grupp med virtuella datorer i en Azure-prenumeration, ansluter de virtuella datorerna till AD-domänen och registrerar de virtuella datorerna med Windows Virtual Desktop.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Vad du behöver för att köra Azure Resource Manager-mallen

Se till att du känner till följande saker innan du kör Azure Resource Manager-mallen:

- Var källan till den avbildning du vill använda finns. Är det från Azure Gallery eller är det anpassat?
- Autentiseringsuppgifterna för din domän anslutning.
- Dina Windows-autentiseringsuppgifter för virtuella skriv bord.

När du skapar en Windows-pool för virtuella Skriv bords värdar med Azure Resource Manager-mallen kan du skapa en virtuell dator från Azure-galleriet, en hanterad avbildning eller en ohanterad avbildning. Mer information om hur du skapar VM-avbildningar finns i [förbereda en Windows VHD-eller VHDX-överföring till Azure](../../virtual-machines/windows/prepare-for-upload-vhd-image.md) och [skapa en hanterad avbildning av en generaliserad virtuell dator i Azure](../../virtual-machines/windows/capture-image-resource.md).

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Kör Azure Resource Manager-mallen för att skapa en ny värdbaserad pool

Starta genom att gå till [den här GitHub-URL: en](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool).

### <a name="deploy-the-template-to-azure"></a>Distribuera mallen till Azure

Om du distribuerar i en Enterprise-prenumeration kan du rulla nedåt och välja **distribuera till Azure** och sedan gå vidare fylla i parametrarna baserat på din avbildnings källa.

Om du distribuerar i en prenumeration på Cloud Solution Provider följer du de här stegen för att distribuera till Azure:

1. Rulla nedåt och högerklicka på **distribuera till Azure** och välj sedan **Kopiera länk plats**.
2. Öppna en text redigerare som anteckningar och klistra in länken där.
3. Höger efter " https://portal.azure.com/ " och innan hashtagg (#) anger ett @-tecken följt av klient domän namnet. Här är ett exempel på det format som du bör använda: `https://portal.azure.com/@Contoso.onmicrosoft.com#create/` .
4. Logga in på Azure Portal som en användare med administratörs-/deltagar behörighet för Cloud Solution Provider-prenumerationen.
5. Klistra in länken som du kopierade till text redigeraren i adress fältet.

Information om vilka parametrar du bör ange för ditt scenario finns i [Readme-filen](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md)för Windows Virtual Desktop. Readme-filen uppdateras alltid med de senaste ändringarna.

## <a name="assign-users-to-the-desktop-application-group"></a>Tilldela användare till program gruppen Skriv bord

När GitHub Azure Resource Manager-mallen har slutförts tilldelar du användar åtkomst innan du börjar testa hela sessionen på dina virtuella datorer.

Börja med att [Hämta och importera Windows Virtual Desktop PowerShell-modulen](/powershell/windows-virtual-desktop/overview/) som ska användas i PowerShell-sessionen om du inte redan gjort det.

Om du vill tilldela användare till gruppen Skriv bords program öppnar du ett PowerShell-fönster och kör denna cmdlet för att logga in på Windows-miljön för virtuella skriv bord:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Efter det lägger du till användare i program gruppen Skriv bord med denna cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Användarens UPN ska matcha användarens identitet i Azure Active Directory (till exempel user1@contoso.com ). Om du vill lägga till flera användare måste du köra denna cmdlet för varje användare.

När du har slutfört de här stegen kan användare som har lagts till i gruppen Skriv bord logga in på Windows Virtual Desktop med stöd för fjärr skrivbords klienter och se en resurs för en session.

>[!IMPORTANT]
>Vi rekommenderar att du inte öppnar den inkommande port 3389 på dina virtuella datorer för att skydda din Windows-miljö för virtuella skriv bord i Azure. Virtuella Windows-datorer kräver inte en öppen inkommande port 3389 för att användare ska kunna komma åt värddatorns virtuella datorer. Om du måste öppna port 3389 för fel söknings syfte rekommenderar vi att du använder [just-in-Time VM-åtkomst](../../security-center/security-center-just-in-time.md).