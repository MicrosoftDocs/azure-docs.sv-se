---
title: 'Snabb start: skapa en Android-app med Azure Maps | Microsoft Azure '
description: 'Snabb start: Lär dig hur du skapar en Android-app med hjälp av Azure Maps Android SDK.'
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2020
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: mvc
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 3c0f95c1252b6895b4604d14e5565395beab8952
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039656"
---
# <a name="quickstart-create-an-android-app-with-azure-maps"></a>Snabb start: skapa en Android-app med Azure Maps

Den här artikeln visar hur du lägger till Azure Maps i en Android-app. Vi går igenom de grundläggande stegen:

* Konfigurera utvecklings miljön.
* Skapa ditt eget Azure Maps-konto.
* Hämta din primära Azure Maps nyckel som ska användas i appen.
* Referera till Azure Maps biblioteken från projektet.
* Lägg till en Azure Maps-kontroll i appen.

## <a name="prerequisites"></a>Förutsättningar

1. Skapa ett Azure Maps-konto genom att logga in på [Azure Portal](https://portal.azure.com). Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
2. [Skapa ett Azure Maps konto](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel. Mer information om autentisering i Azure Maps finns i [hantera autentisering i Azure Maps](how-to-manage-authentication.md).
4. [Ladda ned Android Studio](https://developer.android.com/studio/) kostnads fritt från Google.

## <a name="create-an-azure-maps-account"></a>Skapa ett Azure Maps-konto

Skapa ett nytt Azure Maps-konto med följande steg:

1. Klicka på **Skapa en resurs** längst upp till vänster i [Azure Portal](https://portal.azure.com).
2. I rutan *Sök på Marketplace skriver du* **Azure Maps**.
3. Välj **Azure Maps** i *resultatet*. Klicka på knappen **Skapa** som visas nedanför kartan.
4. Ange följande värden på sidan **Skapa Maps-konto**:
    * Den *Prenumeration* som ska användas för det här kontot.
    * Namnet på *Resursgrupp* för kontot. Du kan välja att *skapa ny* eller *använda befintlig* resursgrupp.
    * *Namn* för ditt nya konto.
    * *Pris nivån* för det här kontot.
    * Läs *licensen* och *sekretesspolicy* och markera kryssrutan för att godkänna villkoren.
    * Klicka på knappen **Skapa**.

    ![Skapa Maps-konto i portalen](media/quick-android-map/create-account.png)

## <a name="get-the-primary-key-for-your-account"></a>Hitta primärnyckeln för ditt konto

När ditt Maps-konto har skapats hämtar du den primära nyckeln som gör att du kan fråga Kartornas API: er.

1. Öppna ditt Maps-konto i portalen.
2. I avsnittet Inställningar väljer du **autentisering**.
3. Kopiera **Primärnyckel** till Urklipp. Spara den lokalt för senare användning i den här självstudien.

>[!NOTE]
> Om du använder Azures prenumerations nyckel i stället för Azure Maps primär nyckel, återges inte kartan på rätt sätt. Av säkerhets skäl rekommenderar vi också att du roterar mellan dina primära och sekundära nycklar. Om du vill rotera nycklar uppdaterar du appen så att den använder den sekundära nyckeln, distribuerar och trycker sedan på knappen cykel/uppdatera bredvid den primära nyckeln för att generera en ny primär nyckel. Den gamla primär nyckeln kommer att inaktive ras. Mer information om nyckel rotation finns i [konfigurera Azure Key Vault med nyckel rotation och granskning](../key-vault/secrets/tutorial-rotation-dual.md)

![Hämta primär nyckel Azure Maps nyckel i Azure Portal](media/quick-android-map/get-key.png)

## <a name="create-a-project-in-android-studio"></a>Skapa ett projekt i Android Studio

Börja med att skapa ett nytt projekt med en tom aktivitet. Utför de här stegen för att skapa ett Android Studio-projekt:

1. Välj **telefon och surfplatta** under **Välj ditt projekt**. Programmet kommer att köras på den här formulär faktorn.
2. På fliken **telefon och surfplatta** väljer du **Tom aktivitet** och väljer sedan **Nästa**.
3. Under **Konfigurera ditt projekt** väljer `API 21: Android 5.0.0 (Lollipop)` du som lägsta SDK. Detta är den tidigaste versionen som stöds av Azure Maps Android SDK.
4. Godkänn standard `Activity Name` och `Layout Name` Välj **Slutför**.

Se [Android Studio-dokumentationen](https://developer.android.com/studio/intro/) för att få hjälp med att installera Android Studio och skapa ett nytt projekt.

![Skapa ett projekt i Android Studio](media/quick-android-map/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Konfigurera en virtuell enhet

Med Android Studio kan du konfigurera en virtuell Android-enhet på datorn. På så sätt kan du testa ditt program under utveckling. Om du vill konfigurera en virtuell enhet väljer du ikonen för AVD-hanteraren (Android Virtual Device) i det övre högra hörnet på projekt skärmen och väljer sedan **Skapa virtuell enhet**. Du kan också gå till avd Manager genom att välja **verktyg**  >  **Android**  >  **avd Manager** i verktygsfältet. I kategorin **telefoner** väljer du **Nexus 5x**, och väljer sedan **Nästa**.

Du kan lära dig mer om hur du konfigurerar en AVD i [Android Studio-dokumentationen](https://developer.android.com/studio/run/managing-avds).

![Android-emulator](media/quick-android-map/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Installera Azure Maps Android SDK

Nästa steg i att skapa ditt program är att installera Azure Maps Android SDK. Slutför de här stegen för att installera SDK:

1. Öppna filen **build. gradle** på den översta nivån och Lägg till följande kod i avsnittet **alla projekt**, **databaser** :

    ```gradle
    maven {
        url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Uppdatera **appen/build. gradle** och Lägg till följande kod i den:

    1. Kontrol lera att projektets **minSdkVersion** är i API 21 eller högre.

    2. Lägg till följande kod i Android-avsnittet:

        ```gradle
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```

    3. Uppdatera beroende blocket och Lägg till en ny implementerings beroende linje för den senaste Azure Maps Android SDK:

        ```gradle
        implementation "com.microsoft.azure.maps:mapcontrol:0.7"
        ```

        > [!Note]
        > Du kan ange versions numret till "0 +" för att koden alltid ska peka på den senaste versionen.

    4. Gå till **filen** i verktygsfältet och klicka sedan på **Synkronisera projekt med Gradle-filer**.
3. Lägg till ett kart fragment i huvud aktiviteten (aktivitet för res \> layout \> \_main.xml):

    ```xml
    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/mapcontrol"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        />
    ```

::: zone pivot="programming-language-java-android"

4. I filen **MainActivity. java** måste du:

    * lägga till importer för Azure Maps SDK
    * Ange information om Azure Maps-autentisering
    * Hämta kart kontroll instansen i **onCreate** -metoden

    Att ange autentiseringsinformation för `AzureMaps` klassen globalt med hjälp av `setSubscriptionKey` metoderna eller `setAadProperties` gör det så att du inte behöver lägga till autentiseringsinformation i alla vyer.

    Kart kontrollen innehåller sina egna livs cykel metoder för att hantera Android: s OpenGL-livscykel. Dessa livs cykel metoder måste anropas direkt från den som innehåller aktiviteten. För att appen ska kunna anropa kart kontrollens livs cykel metoder måste du åsidosätta följande livs cykel metoder i den aktivitet som innehåller kart kontrollen. Och du måste anropa respektive kart kontroll metod.

    * `onCreate(Bundle)`
    * `onDestroy()`
    * `onLowMemory()`
    * `onPause()`
    * `onResume()`
    * `onSaveInstanceState(Bundle)`
    * `onStart()`
    * `onStop()`

    Redigera filen **MainActivity. java** enligt följande:

    ```java
    package com.example.myapplication;
    
    import androidx.appcompat.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    
    public class MainActivity extends AppCompatActivity {
        
    static {
        AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");

        //Alternatively use Azure Active Directory authenticate.
        //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
    }

    MapControl mapControl;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mapControl = findViewById(R.id.mapcontrol);

        mapControl.onCreate(savedInstanceState);

        //Wait until the map resources are ready.
        mapControl.onReady(map -> {
            //Add your post map load code here.

        });
    }

    @Override
    public void onResume() {
        super.onResume();
        mapControl.onResume();
    }

    @Override
    protected void onStart(){
        super.onStart();
        mapControl.onStart();
    }

    @Override
    public void onPause() {
        super.onPause();
        mapControl.onPause();
    }

    @Override
    public void onStop() {
        super.onStop();
        mapControl.onStop();
    }

    @Override
    public void onLowMemory() {
        super.onLowMemory();
        mapControl.onLowMemory();
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        mapControl.onDestroy();
    }

    @Override
    protected void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
        mapControl.onSaveInstanceState(outState);
    }}
    ```

    > [!NOTE]
    > När du har slutfört föregående steg kan du få varningar från Android Studio om en del av koden. Om du vill lösa de här varningarna importerar du de klasser som refereras till i `MainActivity.java` .
    > Du kan importera dessa klasser automatiskt genom att välja `Alt`  +  `Enter` ( `Option`  +  `Return` på en Mac).

::: zone-end

::: zone pivot="programming-language-kotlin"

4. I filen **MainActivity. kt** måste du:

    * lägga till importer för Azure Maps SDK
    * Ange information om Azure Maps-autentisering
    * Hämta kart kontroll instansen i **onCreate** -metoden

    Att ange autentiseringsinformation för `AzureMaps` klassen globalt med hjälp av `setSubscriptionKey` metoderna eller `setAadProperties` gör det så att du inte behöver lägga till autentiseringsinformation i alla vyer.

    Kart kontrollen innehåller sina egna livs cykel metoder för att hantera Android: s OpenGL-livscykel. Dessa livs cykel metoder måste anropas direkt från den som innehåller aktiviteten. För att appen ska kunna anropa kart kontrollens livs cykel metoder måste du åsidosätta följande livs cykel metoder i den aktivitet som innehåller kart kontrollen. Och du måste anropa respektive kart kontroll metod.

    * `onCreate(Bundle)`
    * `onDestroy()`
    * `onLowMemory()`
    * `onPause()`
    * `onResume()`
    * `onSaveInstanceState(Bundle)`
    * `onStart()`
    * `onStop()`

    Redigera filen **MainActivity. kt** på följande sätt:

    ```kotlin
    package com.example.myapplication;

    import androidx.appcompat.app.AppCompatActivity
    import android.os.Bundle
    import com.microsoft.azure.maps.mapcontrol.AzureMap
    import com.microsoft.azure.maps.mapcontrol.AzureMaps
    import com.microsoft.azure.maps.mapcontrol.MapControl
    import com.microsoft.azure.maps.mapcontrol.events.OnReady
    
    class MainActivity : AppCompatActivity() {
    
        companion object {
            init {
                AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
    
                //Alternatively use Azure Active Directory authenticate.
                //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
            }
        }
    
        var mapControl: MapControl? = null
    
        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            setContentView(R.layout.activity_main)
    
            mapControl = findViewById(R.id.mapcontrol)
    
            mapControl?.onCreate(savedInstanceState)
    
            //Wait until the map resources are ready.
            mapControl?.onReady(OnReady { map: AzureMap -> })
        }
    
        public override fun onStart() {
            super.onStart()
            mapControl?.onStart()
        }
    
        public override fun onResume() {
            super.onResume()
            mapControl?.onResume()
        }
    
        public override fun onPause() {
            mapControl?.onPause()
            super.onPause()
        }
    
        public override fun onStop() {
            mapControl?.onStop()
            super.onStop()
        }
    
        override fun onLowMemory() {
            mapControl?.onLowMemory()
            super.onLowMemory()
        }
    
        override fun onDestroy() {
            mapControl?.onDestroy()
            super.onDestroy()
        }
    
        override fun onSaveInstanceState(outState: Bundle) {
            super.onSaveInstanceState(outState)
            mapControl?.onSaveInstanceState(outState)
        }
    }
    ```

    > [!NOTE]
    > När du har slutfört föregående steg kan du få varningar från Android Studio om en del av koden. Om du vill lösa de här varningarna importerar du de klasser som refereras till i `MainActivity.kt` .
    > Du kan importera dessa klasser automatiskt genom att välja `Alt`  +  `Enter` ( `Option`  +  `Return` på en Mac).

::: zone-end

5. Välj knappen Kör, som du ser i följande bild (eller tryck `Control`  +  `R` på en Mac) för att skapa programmet.

    ![Klicka på Kör](media/quick-android-map/run-app.png)

Android Studio tar några sekunder att skapa programmet. När versionen har slutförts kan du testa programmet i den emulerade Android-enheten. Du bör se en karta som den här:

![Azure Maps i Android-program](media/quick-android-map/quickstart-android-map.png)

## <a name="clean-up-resources"></a>Rensa resurser

>[!WARNING]
> Självstudierna som anges i avsnittet [Nästa steg](#next-steps) beskriver hur du använder och konfigurerar Azure Maps med ditt konto. Rensa inte resurserna som du skapar i den här snabbstarten om du planerar att fortsätta med självstudierna.

Om du inte planerar att fortsätta med självstudierna gör du följande för att rensa resurserna:

1. Stäng Android Studio och ta bort det program som du har skapat.
2. Om du har testat programmet på en extern enhet avinstallerar du programmet från den enheten.

Om du inte planerar att fortsätta utveckla med Azure Maps Android SDK:

1. Gå till sidan Azure Portal. Välj **alla resurser** på huvud Portal sidan. Du kan också klicka på Meny ikonen i det övre vänstra hörnet. Välj **Alla resurser**.
2. Klicka på ditt Azure Maps-konto. Klicka på **ta bort** längst upp på sidan.
3. Om du inte planerar att fortsätta utveckla Android-appar avinstallerar du Android Studio.

Fler kod exempel finns i följande guider:

* [Hantera autentisering i Azure Maps](how-to-manage-authentication.md)
* [Ändra kart format i Android Maps](set-android-map-styles.md)
* [Lägga till ett symbolskikt](how-to-add-symbol-to-android-map.md)
* [Lägg till ett linjeskikt](android-map-add-line-layer.md)
* [Lägg till ett polygonskikt](how-to-add-shapes-to-android-map.md)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ditt Azure Maps-konto och skapade ett demo program. Ta en titt på följande självstudier om du vill lära dig mer om Azure Maps:

> [!div class="nextstepaction"]
> [Läs in data i interjson i Azure Maps](tutorial-load-geojson-file-android.md)