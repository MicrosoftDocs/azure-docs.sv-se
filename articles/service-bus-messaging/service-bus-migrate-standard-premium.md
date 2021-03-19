---
title: Migrera Azure Service Bus namnrymder – standard till Premium
description: Guide för att tillåta migrering av befintliga Azure Service Bus standard namn områden till Premium
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 1ed09a077f086390c658e6650171c552b361008d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85340742"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>Migrera befintliga Azure Service Bus standard namn områden till Premium-nivån

Tidigare erbjöds endast namn områden på standard nivån för Azure Service Bus. Namn områden är installations program för flera innehavare som är optimerade för låg data flöde och utvecklings miljöer. Premium-nivån erbjuder dedikerade resurser per namnrymd för förutsägbar svars tid och större data flöde till ett fast pris. Premium nivån är optimerad för stora data flöden och produktions miljöer som kräver ytterligare företags funktioner.

I den här artikeln beskrivs hur du migrerar befintliga namn områden på standard nivå till Premium nivån.  

>[!WARNING]
> Migrering är avsedd för Service Bus standard namn områden som ska uppgraderas till Premium nivån. Migreringsverktyget stöder inte nedgradering.

Några av punkterna att Observera:

- Den här migreringen är avsedd att ske på plats, vilket innebär att befintliga avsändare och mottagar program **inte kräver några ändringar i koden eller konfigurationen**. Den befintliga anslutnings strängen pekar automatiskt på det nya Premium-namnområdet.
- **Premium** -namnområdet får **inte ha några entiteter** för att migreringen ska lyckas.
- Alla **entiteter** i standard namn området **kopieras** till Premium-namnområdet under migreringsprocessen.
- Migrering stöder **1 000 entiteter per meddelande enhet** på Premium-nivån. För att identifiera hur många meddelande enheter du behöver börjar du med antalet entiteter som du har på din aktuella standard namn område.
- Du kan inte direkt migrera från **Basic-nivån** till Premium- **nivån**, men du kan göra detta indirekt genom att migrera från Basic till standard först och sedan från standard till Premium i nästa steg.

## <a name="migration-steps"></a>Migreringsanvisningar

Vissa villkor är kopplade till migreringsprocessen. Bekanta dig med följande steg för att minska risken för fel. De här stegen beskriver migreringsprocessen och stegvisa detaljer visas i de avsnitt som följer.

1. Skapa ett nytt Premium-namnområde.
1. Para ihop standard-och Premium-namnrymderna med varandra.
1. Synkronisera (kopiering över) entiteter från standard till Premium-namnområdet.
1. Genomför migreringen.
1. Töm entiteter i standard namn området genom att använda namnet efter migreringen för namn området.
1. Ta bort standard namn området.

>[!IMPORTANT]
> När migreringen har genomförts, kan du komma åt den gamla standard namn rymden och tömma köer och prenumerationer. När meddelandena har tömts kan de skickas till det nya Premium-namnområdet som ska bearbetas av mottagar programmen. När köerna och prenumerationerna har tömts rekommenderar vi att du tar bort den gamla standard namn rymden.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Migrera med hjälp av Azure CLI eller PowerShell

Följ dessa steg om du vill migrera Service Bus standard namn området till Premium med hjälp av Azure CLI eller PowerShell-verktyget.

1. Skapa ett nytt Service Bus Premium-namnområde. Du kan referera till [Azure Resource Manager mallar](service-bus-resource-manager-namespace.md) eller [använda Azure Portal](service-bus-create-namespace-portal.md). Se till att välja **Premium** för parametern **serviceBusSku** .

1. Ange följande miljövariabler för att förenkla migrerings kommandona.

   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > Alias/namn för efter migrering (post_migration_dns_name) kommer att användas för att få åtkomst till den gamla standard post namns flyttningen. Använd detta för att tömma köerna och prenumerationerna och sedan ta bort namn området.

1. Para ihop standard-och Premium-namnområdena och starta synkroniseringen med hjälp av följande kommando:

    ```azurecli-interactive
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```

1. Kontrol lera status för migreringen med hjälp av följande kommando:

    ```azurecli-interactive
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    Migreringen betraktas som slutförd när följande värden visas:

    * MigrationState = "Active"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "lyckades"

    Det här kommandot visar också migrerings konfigurationen. Kontrol lera att värdena är korrekt inställda. Kontrol lera också namn området Premium i portalen för att se till att alla köer och ämnen har skapats, och att de stämmer överens med det som fanns i standard namn området.

1. Genomför migreringen genom att köra följande fullständiga kommando:

   ```azurecli-interactive
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Migrera med hjälp av Azure Portal

Migrering med hjälp av Azure Portal har samma logiska flöde som migreringen med hjälp av kommandona. Följ de här stegen för att migrera med hjälp av Azure Portal.

1. I **navigerings** menyn i den vänstra rutan väljer **du migrera till Premium**. Klicka på knappen **Kom igång** för att fortsätta till nästa sida.
    ![Landnings sida för migrering][]

1. Slutför **installationen**.
   ![Installations namn område][]
   1. Skapa och tilldela Premium-namnområdet för att migrera det befintliga standard namn området till.
        ![Installations namn namnrymd – skapa Premium-namnrymd][]
   1. Välj ett **namn för migreringen**. Du kommer att använda det här namnet för att komma åt standard namn området när migreringen är klar.
        ![Installations namn namnrymd-Välj efter namn på migrering][]
   1. Fortsätt genom att välja **Nästa** .
1. Synkronisera entiteter mellan standard-och Premium-namnområden.
    ![Installations namn namnrymd-synkronisera entiteter-start][]

   1. Välj **Starta synkronisering** för att börja synkronisera entiteterna.
   1. Välj **Ja** i dialog rutan för att bekräfta och starta synkroniseringen.
   1. Vänta tills synkroniseringen är klar. Statusen är tillgänglig i statusfältet.
        ![Installations namn område – synkronisera entiteter – förlopp][]
        >[!IMPORTANT]
        > Om du behöver avbryta migreringen av någon anledning kan du granska abort-flödet i avsnittet Vanliga frågor och svar i det här dokumentet.
   1. När synkroniseringen är klar väljer du **Nästa** längst ned på sidan.

1. Granska ändringarna på sidan Sammanfattning. Välj **fullständig migrering** för att byta namn rymder och slutföra migreringen.
    ![Byt namn område – Byt meny][]  
    Sidan bekräftelse visas när migreringen är klar.
    ![Switch-namnrymd-lyckad][]

## <a name="caveats"></a>Varningar

Några av de funktioner som tillhandahålls av Azure Service Bus standard-nivån stöds inte av Azure Service Bus Premium-nivån. Detta är avsiktligt eftersom Premium-nivån erbjuder dedikerade resurser för förutsägbart data flöde och svars tider.

Här är en lista över funktioner som inte stöds av Premium och deras minskning –

### <a name="express-entities"></a>Expressenheter

   Express entiteter som inte genomför några meddelande data till lagrings enheter stöds inte i Premium. Dedikerade resurser tillhandahöll betydande data flödes förbättringar samtidigt som de säkerställer att data sparas, som förväntas från alla företags meddelande system.

   Under migreringen skapas alla dina Express-entiteter i standard namn området i Premium-namnområdet som en icke-Express-enhet.

   Om du använder Azure Resource Manager (ARM) mallar, se till att du tar bort flaggan ' enableExpress ' från distributions konfigurationen så att dina automatiserade arbets flöden körs utan fel.

### <a name="partitioned-entities"></a>Partitionerade enheter

   Partitionerade entiteter stöds i standard-nivån för att ge bättre tillgänglighet i en installation av flera innehavare. Med tillhandahållande av dedikerade resurser som är tillgängliga per namnrymd på Premium-nivån behövs inte längre detta.

   Under migreringen skapas en partitionerad entitet i standard namn området i Premium-namnområdet som en icke-partitionerad enhet.

   Om din ARM-mall anger "enablePartitioning" till "true" för en speciell kö eller ämne ignoreras den av koordinatorn.

## <a name="faqs"></a>Vanliga frågor och svar

### <a name="what-happens-when-the-migration-is-committed"></a>Vad händer när migreringen är genomförd?

När migreringen har genomförts pekar den anslutnings sträng som pekade på standard namn området på Premium-namnområdet.

Avsändaren och mottagar programmet kommer att kopplas från standard namn området och återansluta till Premium-namnområdet automatiskt.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>Vad gör jag när standard-till-Premium-migreringen har slutförts?

Med standarden för Premium-migrering ser du till att entitetens metadata, till exempel ämnen, prenumerationer och filter, kopieras från standard namn området till Premium-namnområdet. De meddelande data som har allokerats till standard namn området kopieras inte från standard namn området till Premium-namnområdet.

Standard namn området kan ha vissa meddelanden som har skickats och genomförts medan migreringen genomfördes. Töm dessa meddelanden manuellt från standard namn området och skicka dem manuellt till Premium-namnområdet. Om du vill tömma meddelandena manuellt kan du använda en-konsol eller ett skript som tömmer standard namn områdets entiteter genom att använda DNS-namnet efter migreringen som du angav i migreringen. Skicka dessa meddelanden till Premium-namnområdet så att de kan bearbetas av mottagarna.

När meddelandena har tömts tar du bort standard namn området.

>[!IMPORTANT]
> När meddelandena från standard namn området har tömts tar du bort standard namn området. Detta är viktigt eftersom anslutnings strängen som ursprungligen refereras till standard namn området nu refererar till Premium-namnområdet. Du behöver inte längre standard namn området. Om du tar bort standard namn området som du migrerade bidrar det till att minska senare förvirring.

### <a name="how-much-downtime-do-i-expect"></a>Hur mycket stillestånd förväntar jag mig?

Migreringsprocessen är avsedd att minska den förväntade stillestånds tiden för programmen. Stillestånds tiden minskas med anslutnings strängen som avsändaren och mottagar programmen använder för att peka på det nya Premium-namnområdet.

Avbrotts tiden som programmet upplever är begränsad till den tid det tar att uppdatera DNS-posten så att den pekar på Premium-namnområdet. Stillestånds tiden är cirka 5 minuter.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>Måste jag göra några konfigurations ändringar under migreringen?

Nej, det finns inga kod-eller konfigurations ändringar som krävs för att utföra migreringen. Anslutnings strängen som avsändare och mottagar program använder för att komma åt standard namn området mappas automatiskt för att fungera som ett alias för Premium-namnområdet.

### <a name="what-happens-when-i-abort-the-migration"></a>Vad händer när jag avbryter migreringen?

Migreringen kan avbrytas antingen med hjälp av `Abort` kommandot eller med hjälp av Azure Portal.

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Azure Portal

![Avbrotts flöde – Avbryt synkronisering av synkronisering av synkronisering ][]
 ![ -Avbryt slutförd][]

När migreringsprocessen avbryts avbryts processen att kopiera entiteterna (ämnen, prenumerationer och filter) från standarden till Premium-namnområdet och bryter kopplingen.

Anslutnings strängen har inte uppdaterats till att peka på Premium-namnområdet. Dina befintliga program fortsätter att fungera som de gjorde innan du påbörjade migreringen.

Däremot tar det inte bort entiteterna i Premium-namnområdet eller så tar du bort Premium-namnområdet. Ta bort entiteterna manuellt om du valde att inte flytta framåt med migreringen.

>[!IMPORTANT]
> Om du väljer att avbryta migreringen tar du bort Premium-namnområdet som du har allokerat för migreringen så att du inte debiteras för resurserna.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Jag vill inte behöva tömma meddelandena. Vad gör jag nu?

Det kan finnas meddelanden som skickas av avsändar programmen och allokeras till lagrings utrymmet i standard namn området medan migreringen sker och strax innan migreringen genomförs.

Under migreringen kopieras inte faktiska meddelande data/nytto Last från standarden till Premium-namnområdet. Meddelandena måste tömmas manuellt och sedan skickas till Premium-namnområdet.

Om du däremot kan migrera under ett planerat underhåll/underhåll-fönster och du inte vill tömma och skicka meddelandena manuellt, följer du dessa steg:

1. Stoppa avsändar programmen. Mottagar programmen bearbetar de meddelanden som finns i standard namn området och tömmer kön.
1. När köerna och prenumerationerna i standard namn området är tomma följer du proceduren som beskrivs ovan för att köra migreringen från standard till Premium-namnområdet.
1. När migreringen är klar kan du starta om avsändar programmen.
1. Avsändare och mottagare ansluter nu automatiskt till Premium-namnområdet.

    >[!NOTE]
    > Du behöver inte stoppa mottagar programmen för migreringen.
    >
    > När migreringen är klar kopplas mottagar programmen från standard namn området och ansluter automatiskt till Premium-namnområdet.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [skillnaderna mellan standard-och Premium-meddelanden](./service-bus-premium-messaging.md).
* Läs mer om [återställnings aspekterna med hög tillgänglighet och Geo-Disaster för Service Bus Premium](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium).

[Landnings sida för migrering]: ./media/service-bus-standard-premium-migration/1.png
[Installations namn område]: ./media/service-bus-standard-premium-migration/2.png
[Installations namn namnrymd – skapa Premium-namnrymd]: ./media/service-bus-standard-premium-migration/3.png
[Installations namn namnrymd-Välj efter namn på migrering]: ./media/service-bus-standard-premium-migration/4.png
[Installations namn namnrymd-synkronisera entiteter-start]: ./media/service-bus-standard-premium-migration/5.png
[Installations namn område – synkronisera entiteter – förlopp]: ./media/service-bus-standard-premium-migration/8.png
[Byt namn område – Byt meny]: ./media/service-bus-standard-premium-migration/9.png
[Switch-namnrymd-lyckad]: ./media/service-bus-standard-premium-migration/12.png

[Avbryt flöde-Avbryt synkronisering]: ./media/service-bus-standard-premium-migration/abort1.png
[Avbrotts flöde – avbrott slutfört]: ./media/service-bus-standard-premium-migration/abort3.png
