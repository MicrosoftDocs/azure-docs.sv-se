---
title: Livs cykel och tillstånd för en virtuell dator i Azure
description: Översikt över livs cykeln för en virtuell dator i Azure, inklusive beskrivningar av de olika tillstånd som en virtuell dator kan ha i taget.
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 08/09/2018
ms.author: vashan
ms.openlocfilehash: cd104a31fdba932481889db198ae3cc4998a4466
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88261896"
---
# <a name="virtual-machines-lifecycle-and-states"></a>Livscykler och tillstånd för Virtual Machines

Azure Virtual Machines (VM) går igenom olika tillstånd som kan kategoriseras i *etablerings* -och *energi* tillstånd. Syftet med den här artikeln är att beskriva dessa tillstånd och särskilt Markera när kunder faktureras för användning av instanser. 

## <a name="power-states"></a>Energinivåer

Energi läget motsvarar det senaste kända läget för den virtuella datorn.

![Diagram över virtuellt dator energi tillstånd](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
Följande tabell innehåller en beskrivning av varje instans tillstånd och indikerar om det debiteras för användning av instanser eller inte.

:::row:::
   :::column span="":::

   **Stat**
   
   :::column-end:::
   :::column span="":::

   **Beskrivning**

   :::column-end:::
   :::column span="":::

   **Användning av instans fakturerad**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Startar**

   :::column-end:::
   :::column span="":::

   Den virtuella datorn startas.

   ```json
   "statuses": [
    {
    "code": "PowerState/starting",
    "level": "Info",
    "displayStatus": "VM starting"
    }
   ]
   ```
   :::column-end:::
   :::column span="":::

   **Ej fakturerat**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Körs**

   :::column-end:::
   :::column span="":::

   Normalt arbets läge för en virtuell dator

   ```json
   "statuses": [
    {
    "code": "PowerState/running",
    "level": "Info",
    "displayStatus": "VM running"
    }
  ]
  ```
   :::column-end:::
   :::column span="":::

   **Debiteras**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Stoppas**

   :::column-end:::
   :::column span="":::

   Detta är ett över gångs tillstånd. När det är slutfört visas det som **stoppad**.

   ```json
   "statuses": [
    {
    "code": "PowerState/stopping",
    "level": "Info",
    "displayStatus": "VM stopping"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Debiteras**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Stoppad**

   :::column-end:::
   :::column span="":::

   Den virtuella datorn har stängts av från gäst operativ systemet eller använder avstängnings läge-API: er.

   Maskin varan är fortfarande allokerad till den virtuella datorn och den finns kvar på värden.

   ```json
   "statuses": [
    {
    "code": "PowerState/stopped",
    "level": "Info",
    "displayStatus": "VM stopped"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Debiteras***

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Frigör**

   :::column-end:::
   :::column span="":::

   Över gångs tillstånd. När den är klar visas den virtuella datorn som **frigjord**.

   ```json
   "statuses": [
    {
    "code": "PowerState/deallocating",
    "level": "Info",
    "displayStatus": "VM deallocating"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Ej fakturerat***

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Frigjord**

   :::column-end:::
   :::column span="":::

   Den virtuella datorn har stoppats och tagits bort från värden.

   ```json
   "statuses": [
    {
    "code": "PowerState/deallocated",
    "level": "Info",
    "displayStatus": "VM deallocated"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Ej fakturerat**

   :::column-end:::
:::row-end:::


&#42; vissa Azure-resurser, till exempel diskar och nätverk, debiteras. Program varu licenser på instansen debiteras inte.

## <a name="provisioning-states"></a>Etablerings tillstånd

Ett etablerings tillstånd är status för en användarinitierad, kontroll Plans åtgärd på den virtuella datorn. Dessa tillstånd är skilda från den virtuella datorns energi tillstånd.

- **Skapa** – Skapa virtuell dator.

- **Update** – uppdaterar modellen för en befintlig virtuell dator. Vissa ändringar av den virtuella datorn som inte är modeller, till exempel starta/starta om, omfattas också av Update.

- **Ta bort** – borttagning av virtuell dator.

- **Frigör** – är där en virtuell dator stoppas och tas bort från värden. Om du avallokerar en virtuell dator betraktas den som en uppdatering, så den visar etablerings tillstånd som är relaterade till uppdatering.



Här följer över gångs åtgärds tillstånd efter att plattformen har accepterat en åtgärd som initieras av användaren:

:::row:::
   :::column span="":::

   **Stat**
   
   :::column-end:::
   :::column span="2":::

   **Beskrivning**

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Skapar**

   :::column-end:::
   :::column span="2":::

  ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating",
    "level": "Info",
    "displayStatus": "Creating"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Uppdatera**

   :::column-end:::
   :::column span="2":::

   ```json
   "statuses": [
    {
    "code": "ProvisioningState/updating",
    "level": "Info",
    "displayStatus": "Updating"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Tas bort**

   :::column-end:::
   :::column span="2":::

   ```json
   "statuses": [
    {
    "code": "ProvisioningState/deleting",
    "level": "Info",
    "displayStatus": "Deleting"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Etablerings tillstånd för operativ system**
   
   :::column-end:::
   :::column span="2":::

   **Beskrivning**

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::



   :::column-end:::
   :::column span="2":::

   Om en virtuell dator skapas med en operativ system avbildning och inte med en specialiserad avbildning kan följande under tillstånd observeras:

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **OSProvisioningInprogress**

   :::column-end:::
   :::column span="2":::

   Den virtuella datorn körs och installationen av gäst operativ systemet pågår.
 
   ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating/OSProvisioningInprogress",
    "level": "Info",
    "displayStatus": "OS Provisioning In progress"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **OSProvisioningComplete**

   :::column-end:::
   :::column span="2":::
   
   Status för kort livs längd. Den virtuella datorn övergår snabbt till **lyckad** om inga tillägg behöver installeras. Det kan ta tid att installera tillägg.
   
   ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating/OSProvisioningComplete",
    "level": "Info",
    "displayStatus": "OS Provisioning Complete"
    }
   [
   ```
   
   **Obs!** OS-etableringen kan övergå till **fel** om ett operativ system fel uppstår eller om operativ systemet inte installeras i tid. Kunderna debiteras för den distribuerade virtuella datorn i infrastrukturen.

   :::column-end:::

:::row-end:::

När åtgärden har slutförts övergår den virtuella datorn till något av följande tillstånd:

- **Lyckades** – åtgärderna som initierades av användaren har slutförts.

    ```
  "statuses": [ 
  {
     "code": "ProvisioningState/succeeded",
     "level": "Info",
     "displayStatus": "Provisioning succeeded",
     "time": "time"
  }
  ]
    ```

 

- **Misslyckades** – representerar en misslyckad åtgärd. Läs fel koderna för att få mer information och möjliga lösningar.

    ```
  "statuses": [
    {
      "code": "ProvisioningState/failed/InternalOperationError",
      "level": "Error",
      "displayStatus": "Provisioning failed",
      "message": "Operation abandoned due to internal error. Please try again later.",
      "time": "time"
    }
    ]
    ```



## <a name="vm-instance-view"></a>Vy för VM-instans

Instans visnings-API: et tillhandahåller information om den virtuella datorns körnings tillstånd. Mer information finns i dokumentationen för [Virtual Machines-instans Visa](https://docs.microsoft.com/rest/api/compute/virtualmachines/instanceview) API.

Azures resurs Utforskaren innehåller ett enkelt användar gränssnitt för att visa den virtuella datorn som kör tillstånd: [Resursläsaren](https://resources.azure.com/).

Etablerings tillstånd visas på VM-egenskaper och instans visning. Energi spar lägen är tillgängliga i instans visning av virtuell dator.

Om du vill hämta energi läget för alla virtuella datorer i din prenumeration använder du [Virtual Machines-List all API](https://docs.microsoft.com/rest/api/compute/virtualmachines/listall) med parametern **statusOnly** inställd på *True*.

## <a name="next-steps"></a>Nästa steg

Mer information om övervakning av den virtuella datorn finns i [övervaka virtuella datorer i Azure](../azure-monitor/insights/monitor-vm-azure.md).