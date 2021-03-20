---
title: Köra start åtgärder i Azure Cloud Services (klassisk) | Microsoft Docs
description: Start aktiviteter hjälper dig att förbereda din moln tjänst miljö för din app. Detta lär dig hur start aktiviteter fungerar och hur du gör dem
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 25190075bdd13bd4b75dd82c97ee06ee60f4c26c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743193"
---
# <a name="how-to-configure-and-run-startup-tasks-for-an-azure-cloud-service-classic"></a>Konfigurera och köra start åtgärder för en Azure Cloud Service (klassisk)

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Du kan använda Start åtgärder för att utföra åtgärder innan en roll startar. Åtgärder som du kanske vill utföra är att installera en komponent, registrera COM-komponenter, ange register nycklar eller starta en tids krävande process.

> [!NOTE]
> Start aktiviteter kan inte tillämpas på Virtual Machines, endast för webb-och arbets roller i moln tjänster.
> 
> 

## <a name="how-startup-tasks-work"></a>Så här fungerar start aktiviteter
Start åtgärder är åtgärder som vidtas innan dina roller börjar och definieras i filen [service definition. csdef] med hjälp av [uppgifts] elementet i [Start] elementet. Vanliga start uppgifter är kommandofiler, men de kan också vara konsol program eller batch-filer som startar PowerShell-skript.

Miljövariabler skickar information till en start uppgift och lokal lagring kan användas för att skicka information från en start aktivitet. En miljö variabel kan till exempel ange sökvägen till ett program som du vill installera, och filer kan skrivas till lokal lagring som sedan kan läsas senare av dina roller.

Din start åtgärd kan logga information och fel i katalogen som anges av miljövariabeln **Temp** . Under start aktiviteten matchas miljövariabeln för den **temporära** miljön på *C: \\ resurserna \\ Temp \\ [GUID]. [ rolename] \\ RoleTemp* Directory när den körs i molnet.

Startåtgärder kan även köras flera gånger mellan omstarter. Startåtgärden körs till exempel varje gång rollen återanvänds och rollåteranvändningar kanske inte alltid innefattar en omstart. Start aktiviteter ska skrivas på ett sätt som gör att de kan köras flera gånger utan problem.

Start aktiviteter måste avslutas med en **errorlevel** (eller avslutnings kod) på noll för att start processen ska slutföras. Om en start aktivitet slutar med en **errorlevel** som inte är noll startar inte rollen.

## <a name="role-startup-order"></a>Start ordning för roll
Nedan visas en lista över roll start proceduren i Azure:

1. Instansen markeras som **startar** och tar inte emot trafik.
2. Alla Start aktiviteter körs enligt deras **taskType** -attribut.
   
   * **Enkla** uppgifter körs synkront, en i taget.
   * **Bakgrunden** och **förgrunds** aktiviteterna startas asynkront, parallellt med start uppgiften.  
     
     > [!WARNING]
     > IIS kanske inte är fullständigt konfigurerat under start aktivitets steget i Start processen, så det är inte säkert att roll-/regionsspecifika data är tillgängliga. Start aktiviteter som kräver rollspecifika data bör använda [Microsoft. windowsazure. ServiceRuntime. RoleEntryPoint. OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)).
     > 
     > 
3. Roll värd processen har startats och platsen skapas i IIS.
4. Metoden [Microsoft. windowsazure. ServiceRuntime. RoleEntryPoint. OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) anropas.
5. Instansen är markerad som **klar** och trafik dirigeras till instansen.
6. Metoden [Microsoft. windowsazure. ServiceRuntime. RoleEntryPoint. Run](/previous-versions/azure/reference/ee772746(v=azure.100)) anropas.

## <a name="example-of-a-startup-task"></a>Exempel på en start åtgärd
Start aktiviteter definieras i filen [service definition. csdef] i **aktivitets** elementet. Kommando **rads** attributet anger namn och parametrar för start kommando filen eller konsol kommandot, **ExecutionContext** -attributet anger behörighets nivån för start aktiviteten och attributet **taskType** anger hur aktiviteten ska köras.

I det här exemplet skapas en miljö variabel, **MyVersionNumber**, för start aktiviteten och anges till värdet "**1.0.0.0**".

**Service definition. csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

I följande exempel skriver **Start. cmd** -batchfilen raden "den aktuella versionen är 1.0.0.0" till den StartupLog.txt filen i katalogen som anges av variabeln temp. `EXIT /B 0`Raden ser till att start aktiviteten slutar med en **errorlevel** noll.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> I Visual Studio bör egenskapen **Kopiera till utdata-katalog** för din start kommando fil vara inställd på **Kopiera alltid** för att vara säker på att din start kommando fil är korrekt distribuerad till projektet på Azure (**appens rot \\ bin** för webb roller och **appens rot** för arbets roller).
> 
> 

## <a name="description-of-task-attributes"></a>Beskrivning av uppgiftsattribut
I följande avsnitt beskrivs attributen för **aktivitets** elementet i filen [service definition. csdef] :

**kommando rad – anger** kommando raden för start åtgärden:

* Kommandot med valfria kommando rads parametrar som startar start aktiviteten.
* Detta är ofta fil namnet för en. cmd-eller. bat-kommando fil.
* Aktiviteten är relativ i förhållande till appens rot \\ bin-mappen för distributionen. Miljövariabler utökas inte vid fast ställande av uppgiftens sökväg och fil. Om miljö expansion krävs kan du skapa ett litet. cmd-skript som anropar start aktiviteten.
* Kan vara ett konsol program eller en kommando fil som startar ett [PowerShell-skript](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**ExecutionContext** -anger behörighets nivån för start aktiviteten. Behörighets nivån kan vara begränsad eller utökad:

* **bara**  
  Start aktiviteten körs med samma privilegier som rollen. När attributet **ExecutionContext** för [runtime] -elementet också är **begränsat** används användar behörigheter.
* **med**  
  Start aktiviteten körs med administratörs behörighet. Detta gör att start aktiviteter kan installera program, göra ändringar i IIS-konfigurationen, utföra ändringar i registret och andra åtgärder på administratörs nivå, utan att öka behörighets nivån för själva rollen.  

> [!NOTE]
> Behörighets nivån för en start åtgärd behöver inte vara samma som själva rollen.
> 
> 

**taskType** – anger hur en start aktivitet körs.

* **gång**  
  Aktiviteter körs synkront, en i taget, i den ordning som anges i filen [service definition. csdef] . När en **enkel** start åtgärd slutar med en **errorlevel** på noll körs nästa **enkla** start aktivitet. Om det inte finns några **enkla** start uppgifter att köra, kommer själva rollen att startas.   
  
  > [!NOTE]
  > Om den **enkla** aktiviteten slutar med en **errorlevel** som inte är noll blockeras instansen. Efterföljande **enkla** start aktiviteter, och själva rollen, kommer inte att starta.
  > 
  > 
  
    Kör kommandot  `EXIT /B 0` i slutet av batch-filprocessen för att säkerställa att kommando filen slutar med en ERRORLEVEL på noll.
* **background**  
  Aktiviteter körs asynkront, parallellt med rollens start.
* **överst**  
  Aktiviteter körs asynkront, parallellt med rollens start. Den största skillnaden mellan en **förgrunds** aktivitet och en **bakgrunds** aktivitet är att en **förgrunds** aktivitet förhindrar att rollen åter användning eller stängs av förrän uppgiften har avslut ATS. **Bakgrunds** aktiviteterna har inte den här begränsningen.

## <a name="environment-variables"></a>Miljövariabler
Miljövariabler är ett sätt att skicka information till en start aktivitet. Du kan till exempel ange sökvägen till en blob som innehåller ett program som ska installeras, eller port nummer som din roll använder, eller inställningar för att styra funktionerna i Start aktiviteten.

Det finns två typer av miljövariabler för start åtgärder. statiska miljövariabler och miljövariabler baserade på medlemmar i klassen [RoleEnvironment] . Båda finns i avsnittet [miljö] i filen [service definition. csdef] och båda använder attributet [variabel] element och **Name** .

Variabler för statisk miljö använder attributet **Value** för [variabeln] element. Exemplet ovan skapar miljövariabeln **MyVersionNumber** som har ett statiskt värde för "**1.0.0.0**". Ett annat exempel är att skapa en **StagingOrProduction** -miljövariabel som du manuellt kan ange till värdena för "**mellanlagring**" eller "**produktion**" för att utföra olika start åtgärder baserat på värdet för miljövariabeln **StagingOrProduction** .

Miljövariabler baserade på medlemmar i klassen RoleEnvironment använder inte attributet **Value** i [variabeln] -elementet. I stället används det underordnade elementet [RoleInstanceValue] med lämpligt värde för **XPath** -attribut för att skapa en miljö variabel baserat på en speciell medlem i [RoleEnvironment] -klassen. Värdena för **XPath** -attributet för att komma åt olika [RoleEnvironment] -värden finns [här](cloud-services-role-config-xpath.md).

Om du till exempel vill skapa en miljö variabel som är "**Sant**" när instansen körs i Compute-emulatorn och "**falskt**" när den körs i molnet, använder du följande [variabel] -och [RoleInstanceValue] -element:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>

            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->

            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>

        </Environment>
    </Task>
</Startup>
```

## <a name="next-steps"></a>Nästa steg
Lär dig hur du utför några [vanliga start uppgifter](cloud-services-startup-tasks-common.md) med din moln tjänst.

[Paketera](cloud-services-model-and-package.md) din moln tjänst.  

[Service definition. csdef]: cloud-services-model-and-package.md#csdef
[Uppgift]: /previous-versions/azure/reference/gg557552(v=azure.100)#Task
[Start]: /previous-versions/azure/reference/gg557552(v=azure.100)#Startup
[Körning]: /previous-versions/azure/reference/gg557552(v=azure.100)#Runtime
[Miljö]: /previous-versions/azure/reference/gg557552(v=azure.100)#Environment
[Variabel]: /previous-versions/azure/reference/gg557552(v=azure.100)#Variable
[RoleInstanceValue]: /previous-versions/azure/reference/gg557552(v=azure.100)#RoleInstanceValue
[RoleEnvironment]: /previous-versions/azure/reference/ee773173(v=azure.100)