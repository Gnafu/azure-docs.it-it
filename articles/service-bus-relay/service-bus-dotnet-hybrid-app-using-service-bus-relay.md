---
title: Applicazione ibrida cloud/locale di inoltro WFC di Azure (.NET) | Microsoft Docs
description: Informazioni su come esporre un servizio WCF locale a un'applicazione Web nel cloud usando Inoltro di Azure
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 9ed02f7c-ebfb-4f39-9c97-b7dc15bcb4c1
ms.service: service-bus-relay
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: 145960db27247a8535eb96640000b86d810619c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60419910"
---
# <a name="expose-an-on-premises-wcf-service-to-a-web-application-in-the-cloud-by-using-azure-relay"></a>Esporre un servizio WCF locale a un'applicazione Web nel cloud usando Inoltro di Azure 
Questo articolo illustra come compilare un'applicazione cloud ibrida con Microsoft Azure e Visual Studio. Si creerà un'applicazione in esecuzione nel cloud e che usa più risorse di Azure.

* Creare o adattare un servizio Web esistente utilizzabile in una soluzione Web.
* Usare il servizio d'inoltro WCF di Azure per condividere dati tra un'applicazione Azure e un servizio Web ospitato altrove.

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Esaminare lo scenario
> * Creare uno spazio dei nomi
> * Creare un server locale
> * Creare un'applicazione ASP.NET
> * Eseguire l'app in locale.
> * Distribuire l'app Web in Azure
> * Eseguire l'app in Azure

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

- Una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- [Visual Studio 2015 o versione successiva](https://www.visualstudio.com). Negli esempi di questa esercitazione viene usato Visual Studio 2017.
- Azure SDK per .NET. Installare l'SDK dalla [pagina Download per gli SDK](https://azure.microsoft.com/downloads/).

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Vantaggi del servizio d'inoltro di Azure con soluzioni ibride
Le soluzioni aziendali sono in genere costituite da una combinazione di codice personalizzato appositamente scritto in risposta a specifici requisiti aziendali e da funzionalità esistenti fornite da soluzioni e sistemi già implementati.

Da qualche tempo gli architetti di soluzioni hanno iniziato a usare il cloud per semplificare la gestione dei requisiti di scalabilità e ridurre i costi operativi. Nell'adottare un tale approccio hanno quindi scoperto che le risorse di servizi esistenti che desiderano sfruttare come blocchi predefiniti delle soluzioni si trovano all'interno del firewall aziendale e quindi non sono facilmente raggiungibili dalla soluzione cloud. Molti servizi interni non sono creati né vengono ospitati in modo tale da consentirne una agevole esposizione al perimetro della rete aziendale.

Il [servizio d'inoltro di Azure](https://azure.microsoft.com/services/service-bus/) è progettato per i casi d'uso in cui i servizi Web WCF (Windows Communication Foundation) vengono resi accessibili in modo protetto a soluzioni che risiedono all'esterno del perimetro aziendale senza richiedere modifiche di notevole impatto all'infrastruttura di rete aziendale. Tali servizi d'inoltro sono comunque ospitati all'interno dell'ambiente esistente, ma delegano l'ascolto delle sessioni e delle richieste in ingresso al servizio d'inoltro ospitato nel cloud. Il servizio d'inoltro di Azure protegge anche quei servizi dall'accesso non autorizzato tramite l'autenticazione con [firma di accesso condiviso](../service-bus-messaging/service-bus-sas.md).

## <a name="review-the-scenario"></a>Esaminare lo scenario
In questa esercitazione viene creato un sito Web ASP.NET che consente di visualizzare un elenco di prodotti nella pagina relativa all'inventario dei prodotti.

![Scenario][0]

In questa esercitazione si presuppone che le informazioni sui prodotti siano già disponibili in un sistema locale esistente e che per accedere a tale sistema venga usato il servizio d'inoltro di Azure. Tale operazione viene simulata da un servizio Web eseguito in una semplice applicazione console ed è supportata da un insieme di prodotti in memoria. Sarà quindi possibile eseguire questa applicazione console nel computer in uso e distribuire il ruolo Web in Azure. In tal modo sarà possibile osservare che il ruolo Web in esecuzione nel data center di Azure verrà effettivamente chiamato nel computer in uso, anche se quest'ultimo sarà quasi certamente protetto da almeno un firewall e un livello NAT (Network Address Translation).

## <a name="set-up-the-development-environment"></a>Configurare l'ambiente di sviluppo

Prima di iniziare a sviluppare applicazioni Azure, è necessario scaricare gli strumenti e configurare l'ambiente di sviluppo:

1. Installare Azure SDK per .NET dalla [pagina di download](https://azure.microsoft.com/downloads/) di SDK.
2. Nella colonna **.NET** fare clic sulla versione di [Visual Studio](https://www.visualstudio.com) in uso. Nelle procedure di questa esercitazione viene usato Visual Studio 2017.
3. Quando viene richiesto se eseguire o salvare il file di installazione, fare clic su **Esegui**.
4. Nell'**Installazione guidata piattaforma Web** fare clic su **Installa** e procedere con l'installazione.
5. Al termine dell'installazione, saranno disponibili tutti gli strumenti necessari per avviare lo sviluppo dell’app. Nell'SDK sono disponibili gli strumenti che consentono di sviluppare con facilità applicazioni per Azure in Visual Studio.

## <a name="create-a-namespace"></a>Creare uno spazio dei nomi
Il primo passaggio consiste nel creare uno spazio dei nomi e nell'ottenere una chiave di [firma di accesso condiviso](../service-bus-messaging/service-bus-sas.md). Uno spazio dei nomi fornisce un limite per ogni applicazione esposta tramite il servizio di inoltro. Una chiave di firma di accesso condiviso viene generata dal sistema quando viene creato uno spazio dei nomi del servizio. La combinazione di spazio dei nomi servizio e chiave di firma di accesso condiviso fornisce le credenziali che consentono ad Azure di autenticare l'accesso a un'applicazione.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Creare un server locale
In primo luogo, viene creato un sistema di catalogo prodotti locale fittizio.  Il progetto è un'applicazione console di Visual Studio e usa il [pacchetto NuGet del bus di servizio di Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) per includere le librerie e le impostazioni di configurazione del bus di servizio.

### <a name="create-the-project"></a>Creare il progetto
1. Usando privilegi di amministratore, avviare Microsoft Visual. A tale scopo, fare clic con il tasto destro del mouse sull'icona del programma Visual Studio e quindi fare clic su **Esegui come amministratore**.
2. In Visual Studio scegliere **Nuovo** dal menu **File**, quindi fare clic su **Progetto**.
3. Da **Modelli installati** in **Visual C#** fare clic su **App console (.NET Framework)**. Nella casella **Nome** digitare il nome **ProductsServer**:

   ![Finestra di dialogo Nuovo progetto][11]
4. Fare clic su **OK** per creare il progetto **ProductsServer**.
5. Se Gestione pacchetti NuGet per Visual Studio è già stato installato, continuare con il passaggio successivo. In caso contrario, visitare il sito [NuGet][NuGet] e fare clic su [Install NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) (Installa NuGet). Seguire le istruzioni visualizzate per installare Gestione pacchetti NuGet e quindi riavviare Visual Studio.
6. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ProductsServer** e quindi scegliere **Gestisci pacchetti NuGet**.
7. Fare clic sulla scheda **Sfoglia** e quindi cercare **WindowsAzure.ServiceBus**. Selezionare il pacchetto **WindowsAzure.ServiceBus**.
8. Fare clic su **Installa**e accettare le condizioni per l'utilizzo.

   ![Selezionare il pacchetto NuGet][13]

   I riferimenti agli assembly client necessari sono ora disponibili.
8. Aggiungere una nuova classe per il contratto dei prodotti. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ProductsServer**, scegliere **Aggiungi** e quindi fare clic su **Classe**.
9. Nella casella **Nome** digitare il nome **ProductsContract.cs**. Fare quindi clic su **Aggiungi**.
10. In **ProductsContract.cs** sostituire la definizione dello spazio dei nomi con il codice seguente, che consente di definire il contratto per il servizio.

    ```csharp
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;

        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }

        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();

        }

        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```
11. In Program.cs sostituire la definizione dello spazio dei nomi con il codice seguente, che consente di aggiungere il servizio profili e l'host relativo.

    ```csharp
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;

        // Implement the IProducts interface.
        class ProductsService : IProducts
        {

            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };

            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }

        }

        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();

                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();

                sh.Close();
            }
        }
    }
    ```
12. In Esplora soluzioni fare doppio clic sul file **App.config** per aprirlo nell'editor di Visual Studio. In fondo il `<system.ServiceModel>` elemento (ma sempre in `<system.ServiceModel>`), aggiungere il codice XML seguente: Assicurarsi di sostituire yourServiceNamespace con il nome del proprio spazio dei nomi e *yourKey* con la chiave di firma di accesso condiviso recuperata in precedenza dal portale:

    ```xml
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```
    L'errore causato da "transportClientEndpointBehavior" è solo un avviso e non un problema relativo a un blocco per questo esempio.
    
13. Nell'elemento `<appSettings>`, sempre nel file App.config, sostituire il valore della stringa di connessione con la stringa di connessione ottenuta prima dal portale.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```
14. Premere **CTRL+MAIUSC+B** oppure scegliere **Compila soluzione** dal menu **Compila** per compilare l'applicazione e verificare la correttezza del lavoro svolto finora.

## <a name="create-an-aspnet-application"></a>Creare un'applicazione ASP.NET

In questa sezione viene creata una semplice applicazione ASP.NET per visualizzare i dati recuperati dal servizio dei prodotti.

### <a name="create-the-project"></a>Creare il progetto

1. Assicurarsi che Visual Studio sia in esecuzione con privilegi di amministratore.
2. In Visual Studio scegliere **Nuovo** dal menu **File**, quindi fare clic su **Progetto**.
3. Da **Modelli installati** in **Visual C#** fare clic su **Applicazione Web ASP.NET (.NET Framework)**. Assegnare al progetto il nome **ProductsPortal**. Fare quindi clic su **OK**.

   ![Finestra di dialogo Nuovo progetto][15]

4. Nell'elenco dei **modelli ASP.NET** nella finestra di dialogo **Nuova applicazione Web ASP.NET** fare clic su **MVC**.

   ![Selezionare l'applicazione Web ASP.NET][16]

6. Fare clic sul pulsante **Modifica autenticazione**. Nella finestra di dialogo **Modifica autenticazione** assicurarsi che sia selezionato **Nessuna autenticazione** e quindi fare clic su **OK**. Per questa esercitazione si distribuisce un'app che non richiede l'accesso utente.

    ![Specificare l'autenticazione][18]

7. Nella finestra di dialogo **Nuova applicazione Web ASP.NET** fare clic su **OK** per creare l'app MVC.
8. A questo punto è necessario configurare le risorse di Azure per una nuova app Web. Seguire i passaggi nella sezione [Pubblicazione in Azure di questo articolo](../app-service/app-service-web-get-started-dotnet-framework.md#launch-the-publish-wizard). Tornare quindi a questa esercitazione e procedere al passaggio successivo.
10. In Esplora soluzioni fare clic con il pulsante destro del mouse su **Modelli**, scegliere **Aggiungi** e infine fare clic su **Classe**. Nella casella **Nome** digitare il nome **Product.cs**. Fare quindi clic su **Aggiungi**.

    ![Creare il modello Product][17]

### <a name="modify-the-web-application"></a>Modificare l'applicazione web

1. Nel file Product.cs in Visual Studio sostituire la definizione dello spazio dei nomi esistente con il codice seguente:

   ```csharp
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
       public class Product
       {
           public string Id { get; set; }
           public string Name { get; set; }
           public string Quantity { get; set; }
       }
    }
    ```
2. In Esplora soluzioni espandere la cartella **Controller** e quindi fare doppio clic sul file **HomeController.cs** per aprirlo in Visual Studio.
3. In **HomeController.cs** sostituire la definizione dello spazio dei nomi esistente con il codice seguente:

    ```csharp
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;

        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```
4. In Esplora soluzioni espandere la cartella Views\Shared e quindi fare doppio clic su **_Layout.cshtml** per aprirlo nell'editor di Visual Studio.
5. Sostituire tutte le occorrenze di **My ASP.NET Application** con **Northwind Traders Products**.
6. Rimuovere i collegamenti **Home**, **About** e **Contact**. Nell'esempio seguente, eliminare il codice evidenziato.

    ![Eliminare gli elementi elenco generati][41]

7. In Esplora soluzioni espandere la cartella Views\Home e quindi fare doppio clic su **Index.cshtml** per aprirlo nell'editor di Visual Studio. Sostituire l'intero contenuto del file con il codice seguente:

   ```html
   @model IEnumerable<ProductsWeb.Models.Product>

   @{
            ViewBag.Title = "Index";
   }

   <h2>Prod Inventory</h2>

   <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.Name)
                 </th>
                 <th></th>
                 <th>
                     @Html.DisplayNameFor(model => model.Quantity)
                 </th>
             </tr>

   @foreach (var item in Model) {
             <tr>
                 <td>
                     @Html.DisplayFor(modelItem => item.Name)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.Quantity)
                 </td>
             </tr>
   }

   </table>
   ```
8. Per verificare la correttezza del lavoro svolto finora, premere **CTRL+MAIUSC+B** per compilare il progetto.

### <a name="run-the-app-locally"></a>Eseguire l'app in locale

Eseguire l'applicazione per verificarne il funzionamento.

1. Assicurarsi che il progetto attivo sia **ProductsPortal**. Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni, quindi scegliere **Imposta come progetto di avvio**.
2. In Visual Studio premere **F5**.
3. L'applicazione dovrebbe risultare in esecuzione in un browser.

   ![Applicazione Web][21]

## <a name="put-the-pieces-together"></a>Combinare i diversi componenti

Nel passaggio successivo si collegherà il server dei prodotti locale all'applicazione ASP.NET.

1. Se non è già aperto, in Visual Studio riaprire il progetto **ProductsPortal** creato nella sezione [Creare un'applicazione ASP.NET](#create-an-aspnet-application).
2. Analogamente a quanto descritto nella sezione "Creare un server locale", aggiungere il pacchetto NuGet ai riferimenti del progetto. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ProductsPortal** e quindi scegliere **Gestisci pacchetti NuGet**.
3. Cercare **WindowsAzure.ServiceBus** e selezionare l'elemento **WindowsAzure.ServiceBus**. Completare quindi l'installazione e chiudere la finestra di dialogo.
4. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ProductsPortal**, quindi scegliere **Aggiungi** e infine **Elemento esistente**.
5. Individuare il file **ProductsContract.cs** nel progetto console **ProductsServer**. Fare clic per evidenziare ProductsContract.cs. Fare clic sulla freccia rivolta verso il basso accanto ad **Aggiungi**, quindi fare clic su **Aggiungi come collegamento**.

   ![Aggiungi come collegamento][24]

6. A questo punto aprire il **HomeController.cs** file nell'editor di Visual Studio e sostituire la definizione dello spazio dei nomi con il codice seguente: Assicurarsi di sostituire yourServiceNamespace con il nome dello spazio dei nomi servizio e *yourKey* con la chiave di firma di accesso condiviso. In questo modo il client potrà chiamare il servizio locale e restituire il risultato della chiamata.

   ```csharp
   namespace ProductsWeb.Controllers
   {
       using System.Linq;
       using System.ServiceModel;
       using System.Web.Mvc;
       using Microsoft.ServiceBus;
       using Models;
       using ProductsServer;

       public class HomeController : Controller
       {
           // Declare the channel factory.
           static ChannelFactory<IProductsChannel> channelFactory;

           static HomeController()
           {
               // Create shared access signature token credentials for authentication.
               channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                   "sb://yourServiceNamespace.servicebus.windows.net/products");
               channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                   TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                       "RootManageSharedAccessKey", "yourKey") });
           }

           public ActionResult Index()
           {
               using (IProductsChannel channel = channelFactory.CreateChannel())
               {
                   // Return a view of the products inventory.
                   return this.View(from prod in channel.GetProducts()
                                    select
                                        new Product { Id = prod.Id, Name = prod.Name,
                                            Quantity = prod.Quantity });
               }
           }
       }
   }
   ```
7. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione **ProductsPortal**. Verificare di fare clic con il pulsante destro del mouse sulla soluzione, non sul progetto. Scegliere **Aggiungi**, quindi fare clic su **Progetto esistente**.
8. Individuare il progetto **ProductsServer**, quindi fare doppio clic sulla soluzione **ProductsServer.csproj** per aggiungerla.
9. **ProductsServer** deve essere in esecuzione per visualizzare i dati in **ProductsPortal**. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione **ProductsPortal** e scegliere **Proprietà**. Viene visualizzata la finestra di dialogo **Pagine delle proprietà**.
10. Sul lato sinistro fare clic su **Progetto di avvio**. Sul lato destro fare clic su **Progetti di avvio multipli**. Assicurarsi che **ProductsServer** e **ProductsPortal** siano visualizzati in questo ordine e che l'azione **Avvia** sia impostata per entrambi.

      ![Progetti di avvio multipli][25]

11. Sempre nella finestra di dialogo **Proprietà** fare clic su **Dipendenze progetto** sul lato sinistro.
12. Nell'elenco **Progetti** fare clic su **ProductsServer**. Verificare che **ProductsPortal** non sia selezionato.
13. Nell'elenco **Progetti** fare clic su **ProductsPortal**. Assicurarsi che **ProductsServer** sia selezionato.

    ![Dipendenze progetto][26]

14. Fare clic su **OK** nella finestra di dialogo **Pagine delle proprietà**.

## <a name="run-the-project-locally"></a>Eseguire il progetto in locale

Per testare l'applicazione in locale, in Visual Studio premere **F5**. Il server locale, **ProductsServer**, verrà avviato per primo, quindi verrà avviata l'applicazione **ProductsPortal** in una finestra del browser. Questa volta è possibile notare che nell'inventario dei prodotti sono elencati i dati recuperati dal sistema locale del servizio dei prodotti.

![Applicazione Web][10]

Fare clic su **Aggiorna** nella pagina **ProductsPortal**. Ogni volta che si aggiorna la pagina, nell'app server viene visualizzato un messaggio quando si chiama `GetProducts()` da **ProductsServer**.

Chiudere entrambe le applicazioni prima di procedere al passaggio successivo.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Distribuire il progetto ProductsPortal in un'app Web di Azure

Nel passaggio successivo si ripubblicherà il front-end **ProductsPortal** dell'app Web di Azure. Eseguire le operazioni seguenti:

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **ProductsPortal** e quindi fare clic su **Pubblica**. Fare quindi clic su **Pubblica** nella pagina di **pubblicazione**.

   > [!NOTE]
   > È possibile che nella finestra del browser venga visualizzato un messaggio di errore quando il progetto Web **ProductsPortal** viene avviato automaticamente dopo la distribuzione. Si tratta di un comportamento previsto che si verifica perché l'applicazione **ProductsServer** non è ancora in esecuzione.
   >
   >

2. Copiare l'URL dell'app Web distribuita, perché sarà necessario nel passaggio successivo. È anche possibile ottenere l'URL dalla finestra Attività del servizio app di Azure in Visual Studio:

   ![URL dell'app distribuita][9]

3. Chiudere la finestra del browser per arrestare l'applicazione in esecuzione.

### <a name="set-productsportal-as-web-app"></a>Impostare ProductsPortal come app Web

Prima di eseguire l'applicazione nel cloud è necessario assicurarsi che **ProductsPortal** venga avviato dall'interno di Visual Studio come app Web.

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto **ProductsPortal** e quindi scegliere **Proprietà**.
2. Nella colonna sinistra fare clic su **Web**.
3. Nella sezione **Azione di avvio** fare clic sul pulsante **URL di avvio** e nella casella di testo immettere l'URL dell'app Web distribuita in precedenza, ad esempio, `http://productsportal1234567890.azurewebsites.net/`.

    ![URL di avvio][27]

4. Scegliere **Salva tutto** dal menu **File** in Visual Studio.
5. In Visual Studio scegliere **Ricompila soluzione** dal menu Compila.

## <a name="run-the-application"></a>Eseguire l'applicazione

1. Premere F5 per compilare ed eseguire l'applicazione. Il server in locale (il **ProductsServer** applicazione console) verrà avviato per primo, il **ProductsPortal** dell'applicazione deve essere avviato in una finestra del browser, come illustrato nello screenshot seguente: Si noterà di nuovo che nell'inventario dei prodotti sono elencati i dati recuperati dal sistema locale del servizio dei prodotti, che vengono visualizzati nell'app Web. Verificare l'URL per assicurarsi che **ProductsPortal** sia in esecuzione nel cloud come app Web di Azure.

   ![Eseguire l'app Web in Azure][1]

   > [!IMPORTANT]
   > L'applicazione console **ProductsServer** deve essere in esecuzione e in grado di passare dati all'applicazione **ProductsPortal**. Se nel browser viene visualizzato un errore, attendere alcuni secondi che **ProductsServer** venga caricato e visualizzi il messaggio seguente. Fare quindi clic su **Aggiorna** nel browser.
   >
   >

   ![Output del server][37]
2. Nel browser fare clic su **Aggiorna** nella pagina **ProductsPortal**. Ogni volta che si aggiorna la pagina, nell'app server viene visualizzato un messaggio quando si chiama `GetProducts()` da **ProductsServer**.

    ![Output aggiornato][38]

## <a name="next-steps"></a>Passaggi successivi
Passare all'esercitazione seguente: 

> [!div class="nextstepaction"]
>[Esporre un servizio WCF locale a un client WCF esterno alla rete](service-bus-relay-tutorial.md)

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[NuGet]: https://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-5.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-9.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App3.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-8.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
