---
title: Konfigurera ett labb för att lära databas hantering för Relations databaser | Microsoft Docs
description: Lär dig hur du konfigurerar ett labb för att lära dig att hantera Relations databaser.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 32079d6dc31cf3fcad66976737630472129b4271
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96462423"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>Konfigurera ett labb för att lära databas hantering för Relations databaser

Den här artikeln beskriver hur du konfigurerar ett labb för en grundläggande hanterings klass för databaser i Azure Lab Services. Databas koncept är en av de introduktions kurser som finns i de flesta av dator vetenskaps avdelningarna på hög skola. Structured Query Language (SQL) är en internationell standard. SQL är standard språket för Relations databas hantering, inklusive tillägg, åtkomst och hantering av innehåll i en databas.  De är mest noterade för snabb bearbetning, beprövad tillförlitlighet, enkel och flexibel användning.

I den här artikeln visar vi hur du konfigurerar en mall för virtuella datorer i ett labb med både MySQL Database Server och SQL Server 2019-Server.  [MySQL](https://www.mysql.com/) är ett program med relations databas hantering med öppen källkod som är fritt tillgängligt.  [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) är den senaste versionen av Microsofts RDBMS.

## <a name="lab-configuration"></a>Labb konfiguration

För att kunna konfigurera det här labbet behöver du ett Azure-prenumerations-och labb konto för att komma igång. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar. När du har skaffat en Azure-prenumeration kan du skapa ett nytt labb konto i Azure Lab Services. Mer information om hur du skapar ett nytt labb konto finns i [självstudier för att konfigurera ett labb konto](tutorial-setup-lab-account.md).  Du kan också använda ett befintligt labb konto.

### <a name="lab-account-settings"></a>Labb konto inställningar

Aktivera inställningarna som beskrivs i tabellen nedan för labb kontot. Mer information om hur du aktiverar Marketplace-avbildningar finns i [Ange Marketplace-avbildningar som är tillgängliga för labb skapare](./specify-marketplace-images.md).

| Inställning för labb konto | Instruktioner |
| ------------------- | ------------ |
|Marketplace-avbildning| Aktivera avbildningen "SQL Server 2019-standard på Windows Server 2019" för användning i ditt labb konto.|

### <a name="lab-settings"></a>Labb inställningar

Använd inställningarna i tabellen nedan när du konfigurerar ett klass rums labb.  Mer information om hur du skapar ett klass rums labb finns i [Konfigurera en labb Guide för klass rummet](tutorial-setup-classroom-lab.md).

| Labb inställningar | Värde/anvisningar |
| ------------ | ------------------ |
|Storlek på virtuell dator| Medium. Den här storleken passar bäst för Relations databaser, minnes intern cachelagring och analys.|
|Avbildning av virtuell dator| SQL Server 2019 standard på Windows Server 2019|

## <a name="template-machine-configuration"></a>Mall dator konfiguration

Om du vill installera MySQL på Windows Server 2019 kan du följa stegen i [Installera och köra MySQL Community Server på en virtuell dator](/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine).

SQL Server 2019 är förinstallerat i den virtuella dator avbildningen som du valde när du skapade det nya labbet.

## <a name="cost-estimate"></a>Kostnads uppskattning

Vi ska se en möjlig kostnads uppskattning för den här klassen.  Vi använder en klass av 25 studenter.  Det finns 20 timmar med den schemalagda klass tiden.  Dessutom får varje student en kvot på 10 timmar för läxor eller tilldelningar utanför schemalagda klass tider.  Storleken på den virtuella datorn som vi valde var medel, vilket är 42 lab-enheter.

Här är ett exempel på en möjlig kostnads uppskattning för den här klassen:

25 studenter \* (20 schemalagda timmar + 10 kvot timmar) \* 0,42 USD per timme = 315,00 USD

Mer detaljerad information om priser finns [Azure Lab Services prissättning](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Slutsats

Den här artikeln gick dig igenom de steg som krävs för att skapa ett labb för grundläggande koncept för databas hantering med hjälp av både MySQL och SQL Server. Du kan använda en liknande installation för andra databas klasser.

## <a name="next-steps"></a>Nästa steg

Nästa steg är vanliga för att ställa in alla labb.

- [Skapa och hantera en mall](how-to-create-manage-template.md)
- [Lägg till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-postregistrering länkar till studenter](how-to-configure-student-usage.md#send-invitations-to-users)