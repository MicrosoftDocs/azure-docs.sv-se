---
title: Labb koncept – Azure Lab Services | Microsoft Docs
description: Lär dig grunderna i labb tjänster och hur det kan göra det enkelt att skapa och hantera labb.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: fa3a8dad195b4b3cbf0786c8923c8b330d148898
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96435526"
---
# <a name="labs-concepts"></a>Labbkoncept

Följande lista innehåller koncept och definitioner för nyckel labb tjänster:

## <a name="quota"></a>Kvot

Kvoten är den tids gräns (i timmar) som en lärare kan ställas in för att eleven ska kunna använda en virtuell labb dator. Det kan ställas in på 0 eller ett angivet antal timmar. Om kvoten är inställd på 0, kan en student bara använda den virtuella datorn när ett schema körs eller när en lärare manuellt aktiverar den virtuella datorn för studenten.  

Kvot timmar räknas när studenten startar själva labb datorn.  Om en lärare manuellt startar den virtuella labb datorn för en student, används inte kvot timmar för den studenten.

## <a name="schedules"></a>Scheman

Scheman är de tidsintervaller som en lärare kan skapa för klassen så att de virtuella student datorerna är tillgängliga för klass tid.  Scheman kan vara engångs eller återkommande.  Kvot timmar används inte när ett schema körs.

Det finns tre typer av scheman: standard, endast start och stopp.

- **Standard.**  Det här schemat startar alla elev-VM: ar vid den angivna start tiden och stänger alla student virtuella datorer på den angivna stopp tiden.
- **Endast start**.   Det här schemat startar alla elev-VM: ar vid den angivna tiden.  Student-VM: ar stoppas inte förrän en student stoppar den virtuella datorn via Azure Lab Services portalen eller ett avbrott som endast stoppas.
- **Stoppa bara**.  Det här schemat stoppar alla elev-VM: ar vid den angivna tiden.  

## <a name="template-virtual-machine"></a>Mall för virtuell dator

En virtuell mall i ett labb är en bas avbildning av virtuella datorer från vilken alla användares virtuella datorer skapas. Utvecklare/labb skapar skapare av den virtuella dator mal len och konfigurerar den med den program vara som de vill tillhandahålla för att utbilda deltagare till att göra labb. När du publicerar en mall för virtuella Azure Lab Services datorer skapar eller uppdaterar virtuella labb virtuella datorer baserat på mallen VM.

## <a name="user-profiles"></a>Användarprofiler

Den här artikeln beskriver olika användarprofiler i Azure Lab Services.

### <a name="lab-account-owner"></a>Labbkontoägare

Normalt är en IT-administratör av organisationens moln resurser, som äger Azure-prenumerationen som en labb konto ägare och utför följande uppgifter:

- Konfigurerar ett labbkonto för organisationen.
- Hanterar och konfigurerar principer för alla labb.
- Ger behörigheter till personer i organisationen för att skapa ett labb under labbkontot.

### <a name="educator"></a>Utbildare

Vanligt vis skapar användare som lärare eller en online-utbildare labb under ett labb konto. En utbildare utför följande uppgifter:

- Skapar ett klassrumslabb.
- Skapar virtuella maskiner i labbet.
- Installerar rätt programvara på virtuella datorer.
- Anger vem som kan få åtkomst till labbet.
- Tillhandahåller registreringslänken till labbet för studenter.

### <a name="student"></a>Student

En student utför följande uppgifter:

- Använder registreringslänken som denne får från labbskaparen för att registrera sig för labbet.
- Ansluter till en virtuell dator i labbet och använder den för att göra klassarbete, uppgifter och projekt.

## <a name="next-steps"></a>Nästa steg

Kom igång med att konfigurera ett labbkonto som krävs för att skapa ett klassrumslabb med Azure Lab Services:

- [Konfigurera ett labbkonto](tutorial-setup-lab-account.md)
