---
title: Applicazione Java a elevato utilizzo di calcolo in una macchina virtuale
description: Informazioni su come creare una macchina virtuale di Azure nella quale è in esecuzione un'applicazione Java a elevato utilizzo di calcolo che può essere monitorata da un'altra applicazione Java.
services: virtual-machines-windows
documentationcenter: java
author: rmcmurray
manager: mbaldwin
tags: azure-service-management,azure-resource-manager
ms.assetid: ae6f2737-94c7-4569-9913-d871450c2827
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: Java
ms.topic: article
ms.date: 04/11/2018
ms.author: robmcm
ms.openlocfilehash: e8da296c30f1d2be3c637e456e90d2f93da67548
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
---
# <a name="how-to-run-a-compute-intensive-task-in-java-on-a-virtual-machine"></a>Come eseguire un'attività a elevato utilizzo di calcolo in Java in una macchina virtuale
> [!IMPORTANT] 
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Con Azure è possibile usare una macchina virtuale per gestire attività a elevato utilizzo di calcolo. Ad esempio, una macchina virtuale può gestire attività e fornire risultati a computer client o ad applicazioni mobili. Dopo aver letto questo articolo, si avranno a disposizione tutte le informazioni necessarie per creare una macchina virtuale nella quale è in esecuzione un'applicazione Java a elevato utilizzo di calcolo che può essere monitorata da un'altra applicazione Java.

Questa esercitazione presuppone che l'utente sappia creare applicazioni della console Java, importare librerie nell'applicazione Java e generare un archivio Java (JAR). Non è richiesta alcuna conoscenza di Microsoft Azure.

Si acquisiranno le nozioni seguenti:

* Come creare una macchina virtuale con un Java Development Kit (JDK) già installato.
* Accedere in remoto alla macchina virtuale.
* Creare uno spazio dei nomi del bus di servizio.
* Creare un'applicazione Java che esegua un'attività a elevato utilizzo di calcolo.
* Creare un'applicazione Java che esegua un'attività che monitori l'avanzamento dell'attività a elevato utilizzo di calcolo.
* Eseguire le applicazioni Java.
* Arrestare le applicazioni Java.

In questa esercitazione verrà utilizzato il Problema del commesso viaggiatore per l'attività a elevato utilizzo di calcolo. Di seguito è riportato un esempio dell'applicazione Java che esegue l'attività a elevato utilizzo di calcolo.

![Risolutore del Problema del commesso viaggiatore][solver_output]

Di seguito è riportato un esempio dell'applicazione Java che monitora l'attività a elevato utilizzo di calcolo.

![Client del Problema del commesso viaggiatore][client_output]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Per creare una macchina virtuale
1. Accedere al [Portale di Azure](https://portal.azure.com).
2. Fare clic su **Crea una risorsa**, **Calcolo**, **Macchina virtuale** e quindi su **Da raccolta**.
3. Nella finestra di dialogo **Virtual machine image select** (Seleziona immagine macchina virtuale) selezionare **JDK 7 Windows Server 2012** (JDK 7 Windows Server 2012).
   Si noti che è disponibile anche **JDK 6 Windows Server 2012** nel caso in cui siano presenti applicazioni non ancora predisposte per l'esecuzione in JDK 7.
4. Fare clic su **Avanti**.
5. Nella finestra di dialogo **Configurazione macchina virtuale** :
   1. Specificare un nome per la macchina virtuale.
   2. Specificare la dimensione da utilizzare per la macchina virtuale.
   3. Immettere un nome per l'amministratore nel campo **User Name** . Prendere nota di questo nome e della password che verrà immessa successivamente perché verranno utilizzati per l'accesso in remoto alla macchina virtuale.
   4. Immettere una password nel campo **Nuova password** e reimmetterla nel campo **Conferma**. Si tratta della password dell'account dell'amministratore.
   5. Fare clic su **Avanti**.
6. Nella finestra di dialogo **Configurazione macchina virtuale** successiva:
   1. Per **Servizio cloud** usare l'impostazione predefinita di **Crea un nuovo servizio cloud**.
   2. Il valore di **Nome DNS del servizio cloud** deve essere univoco in cloudapp.net. Se necessario, modificarlo in modo che sia indicato come univoco in Azure.
   3. Specificare un'area, un gruppo di affinità o una rete virtuale. Ai fini di questa esercitazione, specificare come area **West US**.
   4. Nella casella **Account di archiviazione** selezionare **Usa un account di archiviazione generato automaticamente** .
   5. Nella casella **Set di disponibilità** selezionare **(Nessuno)**.
   6. Fare clic su **Avanti**.
7. Nella finestra di dialogo **Configurazione macchina virtuale** finale:
   1. Accettare le voci di endpoint predefinite.
   2. Fare clic su **Complete**.

## <a name="to-remotely-log-in-to-your-virtual-machine"></a>Per accedere in remoto alla macchina virtuale
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Virtual machines**.
3. Fare clic sul nome della macchina virtuale a cui si desidera accedere.
4. Fare clic su **Connetti**.
5. Rispondere ai prompt visualizzati per connettersi alla macchina virtuale. Quando vengono richiesti il nome e la password dell'amministratore, usare i valori specificati durante la creazione della macchina virtuale.

Si noti che la funzionalità di bus di servizio di Azure richiede l'installazione del certificato Baltimore CyberTrust Root come parte del proprio archivio **cacerts** dell'ambiente JRE. Questo certificato è incluso automaticamente nell'ambiente Java Runtime Environment (JRE) usato in questa esercitazione. Se questo certificato non è disponibile nell'archivio **cacerts** dell'ambiente JRE, vedere [Aggiunta di un certificato all'archivio certificati CA Java][add_ca_cert] per altre informazioni su come aggiungerlo e informazioni sulla visualizzazione dei certificati nell'archivio cacerts.

## <a name="how-to-create-a-service-bus-namespace"></a>Come creare uno spazio dei nomi del bus di servizio
Per iniziare a utilizzare le code del bus di servizio in Azure, è innanzitutto necessario creare uno spazio dei nomi servizio che fornisce un contenitore di ambito per fare riferimento alle risorse del bus di servizio all'interno dell'applicazione.

Per creare uno spazio dei nomi del servizio:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel riquadro di spostamento in basso a sinistra del portale di Azure fare clic su **Bus di servizio, Controllo di accesso e Cache**.
3. Nel riquadro in alto a sinistra del portale di Azure fare clic sul nodo **Bus di servizio** e quindi sul pulsante **Nuovo**.  
   ![Schermata nodo bus di servizio][svc_bus_node]
4. Nella finestra di dialogo **Crea un nuovo spazio dei nomi servizio** immettere uno spazio dei nomi servizio in **Spazio dei nomi** e quindi, per assicurarsi che sia univoco, fare clic sul pulsante **Verifica disponibilità**.  
   ![Schermata Create a New Namespace][create_namespace]
5. Dopo avere verificato che lo spazio dei nomi è disponibile, scegliere il paese o l'area geografica in cui dovrà essere ospitato e fare clic sul pulsante **Create Namespace** .  
   
   Lo spazio dei nomi creato verrà quindi visualizzato nel portale di Azure e sarà necessario attendere qualche istante affinché venga attivato. Prima di continuare, attendere che lo stato sia **Active** .

## <a name="obtain-the-default-management-credentials-for-the-namespace"></a>Recuperare le credenziali di gestione predefinite per lo spazio dei nomi
Per poter eseguire le operazioni di gestione, ad esempio creare una coda, nel nuovo spazio dei nomi, è necessario ottenere le credenziali di gestione per lo spazio dei nomi.

1. Nel riquadro di spostamento a sinistra fare clic sul nodo **Bus di servizio** per visualizzare l'elenco degli spazi dei nomi disponibili.
   ![Schermata relativa agli spazi dei nomi disponibili][avail_namespaces]
2. Selezionare lo spazio dei nomi appena creato nell'elenco visualizzato.
   ![Schermata relativa all'elenco degli spazi dei nomi][namespace_list]
3. Il riquadro **Proprietà** a destra elenca le proprietà relative al nuovo spazio dei nomi.
   ![Schermata pannello Proprietà][properties_pane]
4. Le credenziali di sicurezza in **Chiave predefinita** sono nascoste. Fare clic sul pulsante **Visualizza** per visualizzare le credenziali di sicurezza.
   ![Schermata Default Key][default_key]
5. Prendere nota dei valori indicati in **Default Issuer** e **Default Key**, in quanto dovranno essere usati per eseguire operazioni con lo spazio dei nomi.

## <a name="how-to-create-a-java-application-that-performs-a-compute-intensive-task"></a>Come creare un'applicazione Java che esegue un'attività a elevato utilizzo di calcolo
1. Nel computer di sviluppo (che non deve essere la macchina virtuale creata dall'utente) scaricare [Azure SDK for Java](https://azure.microsoft.com/develop/java/).
2. Creare un'applicazione di console Java usando il codice di esempio fornito al termine di questa sezione. In questa esercitazione verrà usato **TSPSolver.java** come nome del file Java. Modificare i segnaposto **your\_service\_bus\_namespace**, **your\_service\_bus\_owner** e **your\_service\_bus\_key** per usare rispettivamente i valori **spazio dei nomi**, **Autorità di certificazione predefinita** e **Chiave predefinita** del bus di servizio.
3. Dopo la codifica, esportare l'applicazione in un archivio Java eseguibile (JAR) e creare un pacchetto con le librerie richieste nell'archivio JAR generato. In questa esercitazione verrà usato **TSPSolver.jar** come nome dell'archivio JAR generato.

<p/>

    // TSPSolver.java

    import com.microsoft.windowsazure.services.core.Configuration;
    import com.microsoft.windowsazure.services.core.ServiceException;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import java.io.*;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import java.util.ArrayList;
    import java.util.Date;
    import java.util.List;

    public class TSPSolver {

        //  Value specifying how often to provide an update to the console.
        private static long loopCheck = 100000000;  

        private static long nTimes = 0, nLoops=0;

        private static double[][] distances;
        private static String[] cityNames;
        private static int[] bestOrder;
        private static double minDistance;
        private static ServiceBusContract service;

        private static void buildDistances(String fileLocation, int numCities) throws Exception{
            try{
                BufferedReader file = new BufferedReader(new InputStreamReader(new DataInputStream(new FileInputStream(new File(fileLocation)))));
                double[][] cityLocs = new double[numCities][2];
                for (int i = 0; i<numCities; i++){
                    String[] line = file.readLine().split(", ");
                    cityNames[i] = line[0];
                    cityLocs[i][0] = Double.parseDouble(line[1]);
                    cityLocs[i][1] = Double.parseDouble(line[2]);
                }
                for (int i = 0; i<numCities; i++){
                    for (int j = i; j<numCities; j++){
                        distances[i][j] = Math.hypot(Math.abs(cityLocs[i][0] - cityLocs[j][0]), Math.abs(cityLocs[i][1] - cityLocs[j][1]));
                        distances[j][i] = distances[i][j];
                    }
                }
            } catch (Exception e){
                throw e;
            }
        }

        private static void permutation(List<Integer> startCities, double distSoFar, List<Integer> restCities) throws Exception {

            try
            {
                nTimes++;
                if (nTimes == loopCheck)
                {
                    nLoops++;
                    nTimes = 0;
                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.print("Current time is " + dateFormat.format(date) + ". ");
                    System.out.println(  "Completed " + nLoops + " iterations of size of " + loopCheck + ".");
                }

                if ((restCities.size() == 1) && ((minDistance == -1) || (distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-1)] < minDistance))){
                    startCities.add(restCities.get(0));
                    newBestDistance(startCities, distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-2)]);
                    startCities.remove(startCities.size()-1);
                }
                else{
                    for (int i=0; i<restCities.size(); i++){
                        startCities.add(restCities.get(0));
                        restCities.remove(0);
                        permutation(startCities, distSoFar + distances[startCities.get(startCities.size()-1)][startCities.get(startCities.size()-2)],restCities);
                        restCities.add(startCities.get(startCities.size()-1));
                        startCities.remove(startCities.size()-1);
                    }
                }
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        private static void newBestDistance(List<Integer> cities, double distance) throws ServiceException, Exception {
            try
            {
                minDistance = distance;
                String cityList = "Shortest distance is "+minDistance+", with route: ";
                for (int i = 0; i<bestOrder.length; i++){
                    bestOrder[i] = cities.get(i);
                    cityList += cityNames[bestOrder[i]];
                    if (i != bestOrder.length -1)
                        cityList += ", ";
                }
                System.out.println(cityList);
                service.sendQueueMessage("TSPQueue", new BrokeredMessage(cityList));
            }
            catch (ServiceException se)
            {
                throw se;
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        public static void main(String args[]){

            try {

                Configuration config = ServiceBusConfiguration.configureWithWrapAuthentication(
                        "your_service_bus_namespace", "your_service_bus_owner",
                        "your_service_bus_key",
                        ".servicebus.windows.net",
                        "-sb.accesscontrol.windows.net/WRAPv0.9");

                service = ServiceBusService.create(config);

                int numCities = 10;  // Use as the default, if no value is specified at command line.
                if (args.length != 0)
                {
                    if (args[0].toLowerCase().compareTo("createqueue")==0)
                    {
                        // No processing to occur other than creating the queue.
                        QueueInfo queueInfo = new QueueInfo("TSPQueue");

                        service.createQueue(queueInfo);

                        System.out.println("Queue named TSPQueue was created.");

                        System.exit(0);
                    }

                    if (args[0].toLowerCase().compareTo("deletequeue")==0)
                    {
                        // No processing to occur other than deleting the queue.
                        service.deleteQueue("TSPQueue");

                        System.out.println("Queue named TSPQueue was deleted.");

                        System.exit(0);
                    }

                    // Neither creating or deleting a queue.
                    // Assume the value passed in is the number of cities to solve.
                    numCities = Integer.valueOf(args[0]);  
                }

                System.out.println("Running for " + numCities + " cities.");

                List<Integer> startCities = new ArrayList<Integer>();
                List<Integer> restCities = new ArrayList<Integer>();
                startCities.add(0);
                for(int i = 1; i<numCities; i++)
                    restCities.add(i);
                distances = new double[numCities][numCities];
                cityNames = new String[numCities];
                buildDistances("c:\\TSP\\cities.txt", numCities);
                minDistance = -1;
                bestOrder = new int[numCities];
                permutation(startCities, 0, restCities);
                System.out.println("Final solution found!");
                service.sendQueueMessage("TSPQueue", new BrokeredMessage("Complete"));
            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }
        }

    }



## <a name="how-to-create-a-java-application-that-monitors-the-progress-of-the-compute-intensive-task"></a>Come creare un'applicazione Java per monitorare lo stato dell'attività a elevato utilizzo di calcolo
1. Nel computer di sviluppo creare un'applicazione console Java usando il codice di esempio fornito al termine di questa sezione. In questa esercitazione verrà usato **TSPClient.java** come nome del file Java. Come mostrato in precedenza, modificare i segnaposto **your\_service\_bus\_namespace**, **your\_service\_bus\_owner** e **your\_service\_bus\_key** per usare rispettivamente i valori **spazio dei nomi**, **Autorità di certificazione predefinita** e **Chiave predefinita** del bus di servizio.
2. Esportare l'applicazione in un archivio Java eseguibile (JAR) e creare un pacchetto con le librerie richieste nell'archivio JAR generato. In questa esercitazione verrà usato **TSPClient.jar** come nome dell'archivio JAR generato.

<p/>

    // TSPClient.java

    import java.util.Date;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;

    public class TSPClient
    {

        public static void main(String[] args)
        {
                try
                {

                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.println("Starting at " + dateFormat.format(date) + ".");

                    String namespace = "your_service_bus_namespace";
                    String issuer = "your_service_bus_owner";
                    String key = "your_service_bus_key";

                    Configuration config;
                    config = ServiceBusConfiguration.configureWithWrapAuthentication(
                            namespace, issuer, key,
                            ".servicebus.windows.net",
                            "-sb.accesscontrol.windows.net/WRAPv0.9");

                    ServiceBusContract service = ServiceBusService.create(config);

                    BrokeredMessage message;

                    int waitMinutes = 3;  // Use as the default, if no value is specified at command line.
                    if (args.length != 0)
                    {
                        waitMinutes = Integer.valueOf(args[0]);  
                    }

                    String waitString;

                    waitString = (waitMinutes == 1) ? "minute." : waitMinutes + " minutes.";

                    // This queue must have previously been created.
                    service.getQueue("TSPQueue");

                    int numRead;

                    String s = null;

                    while (true)
                    {

                        ReceiveQueueMessageResult resultQM = service.receiveQueueMessage("TSPQueue");
                        message = resultQM.getValue();

                        if (null != message && null != message.getMessageId())
                        {

                            // Display the queue message.
                            byte[] b = new byte[200];

                            System.out.print("From queue: ");

                            s = null;
                            numRead = message.getBody().read(b);
                            while (-1 != numRead)
                            {
                                s = new String(b);
                                s = s.trim();
                                System.out.print(s);
                                numRead = message.getBody().read(b);
                            }
                            System.out.println();
                            if (s.compareTo("Complete") == 0)
                            {
                                // No more processing to occur.
                                date = new Date();
                                System.out.println("Finished at " + dateFormat.format(date) + ".");
                                break;
                            }
                        }
                        else
                        {
                            // The queue is empty.
                            System.out.println("Queue is empty. Sleeping for another " + waitString);
                            Thread.sleep(60000 * waitMinutes);
                        }
                    }

            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }

        }

    }

## <a name="how-to-run-the-java-applications"></a>Come eseguire le applicazioni Java
Eseguire l'applicazione a elevato utilizzo di calcolo innanzitutto per creare la coda, quindi per risolvere il Problema del commesso viaggiatore, che consentirà di aggiungere l'attuale route migliore alla coda del bus di servizio. Mentre l'applicazione a elevato utilizzo di calcolo è in esecuzione (oppure in seguito), eseguire il client in modo da visualizzare i risultati ottenuti dalla coda del bus di servizio.

### <a name="to-run-the-compute-intensive-application"></a>Per eseguire l'applicazione a elevato utilizzo di calcolo
1. Accedere alla macchina virtuale.
2. Creare una cartella in cui eseguire l'applicazione, ad esempio **c:\TSP**.
3. Copiare **TSPSolver.jar** in **c:\TSP**,
4. Creare un file denominato **c:\TSP\cities.txt** con il seguente contenuto.
   
        City_1, 1002.81, -1841.35
        City_2, -953.55, -229.6
        City_3, -1363.11, -1027.72
        City_4, -1884.47, -1616.16
        City_5, 1603.08, -1030.03
        City_6, -1555.58, 218.58
        City_7, 578.8, -12.87
        City_8, 1350.76, 77.79
        City_9, 293.36, -1820.01
        City_10, 1883.14, 1637.28
        City_11, -1271.41, -1670.5
        City_12, 1475.99, 225.35
        City_13, 1250.78, 379.98
        City_14, 1305.77, 569.75
        City_15, 230.77, 231.58
        City_16, -822.63, -544.68
        City_17, -817.54, -81.92
        City_18, 303.99, -1823.43
        City_19, 239.95, 1007.91
        City_20, -1302.92, 150.39
        City_21, -116.11, 1933.01
        City_22, 382.64, 835.09
        City_23, -580.28, 1040.04
        City_24, 205.55, -264.23
        City_25, -238.81, -576.48
        City_26, -1722.9, -909.65
        City_27, 445.22, 1427.28
        City_28, 513.17, 1828.72
        City_29, 1750.68, -1668.1
        City_30, 1705.09, -309.35
        City_31, -167.34, 1003.76
        City_32, -1162.85, -1674.33
        City_33, 1490.32, 821.04
        City_34, 1208.32, 1523.3
        City_35, 18.04, 1857.11
        City_36, 1852.46, 1647.75
        City_37, -167.44, -336.39
        City_38, 115.4, 0.2
        City_39, -66.96, 917.73
        City_40, 915.96, 474.1
        City_41, 140.03, 725.22
        City_42, -1582.68, 1608.88
        City_43, -567.51, 1253.83
        City_44, 1956.36, 830.92
        City_45, -233.38, 909.93
        City_46, -1750.45, 1940.76
        City_47, 405.81, 421.84
        City_48, 363.68, 768.21
        City_49, -120.3, -463.13
        City_50, 588.51, 679.33
5. Al prompt dei comandi passare alla directory c:\TSP.
6. Assicurarsi che la cartella Bin si trovi nella variabile di ambiente PATH.
7. Sarà necessario creare la coda del bus di servizio prima di eseguire le permutazioni del risolutore TSP. Eseguire il comando seguente per creare la coda del bus di servizio.
   
        java -jar TSPSolver.jar createqueue
8. Dopo aver creato la coda è possibile eseguire le permutazioni del risolutore TSP. Ad esempio, usare il comando seguente per eseguire il risolutore per 8 città.
   
        java -jar TSPSolver.jar 8
   
   Se non si specifica un numero, il risolutore verrà eseguito per 10 città. Mano a mano che rileva le route attualmente più brevi, il risolutore le aggiunge alla coda.

> [!NOTE]
> Maggiore è il numero specificato, più lunga sarà l'esecuzione del risolutore. Ad esempio, l'esecuzione per 14 città potrebbe richiedere diversi minuti, mentre l'esecuzione per 15 città potrebbe richiedere parecchie ore. Se si specificano 16 o più città potrebbero essere necessari diversi giorni di runtime (settimane, mesi e anni). Ciò è dovuto al rapido aumento del numero di permutazioni valutate dal risolutore di pari passo con l'aumento del numero di città.
> 
> 

### <a name="how-to-run-the-monitoring-client-application"></a>Come eseguire l'applicazione client di monitoraggio
1. Accedere al computer dal quale si intende eseguire l'applicazione client, che non deve essere necessariamente lo stesso computer su cui è in esecuzione l'applicazione **c:\TSP\cities.txt**.
2. Creare una cartella in cui eseguire l'applicazione, ad esempio **c:\TSP**.
3. Copiare **TSPClient.jar** in **c:\TSP**,
4. Assicurarsi che la cartella Bin si trovi nella variabile di ambiente PATH.
5. Al prompt dei comandi passare alla directory c:\TSP.
6. Eseguire il comando indicato di seguito.
   
        java -jar TSPClient.jar
   
    Facoltativamente, specificare il numero di minuti di sospensione della verifica della coda passando un argomento della riga di comando. Il periodo di sospensione predefinito per la verifica della coda corrisponde a 3 minuti. Questo intervallo viene usato se non si passa un argomento della riga di comando a **TSPClient**. Se si desidera usare un valore differente per l'intervallo di sospensione, ad esempio un minuto, eseguire il comando indicato di seguito.
   
        java -jar TSPClient.jar 1
   
    Il comando verrà eseguito sul client finché non si riceverà il messaggio di coda completa. Si noti che se si eseguono più occorrenze del risolutore senza eseguire il client, può essere necessario eseguire il client più volte per svuotare completamente la coda. In alternativa, è possibile eliminare la coda e crearla di nuovo. Per eliminare la coda, eseguire il comando **TSPSolver** (non **TSPClient**) seguente.
   
        java -jar TSPSolver.jar deletequeue
   
    Il risolutore eseguirà il comando finché non avrà terminato di esaminare tutte le route.

## <a name="how-to-stop-the-java-applications"></a>Come arrestare le applicazioni Java
Per uscire dalle applicazioni risolutore e client e terminare prima del normale completamento, premere i tasti **Ctrl+C** .

[solver_output]:media/java-run-compute-intensive-task/WA_JavaTSPSolver.png
[client_output]:media/java-run-compute-intensive-task/WA_JavaTSPClient.png
[svc_bus_node]:media/java-run-compute-intensive-task/SvcBusQueues_02_SvcBusNode.jpg
[create_namespace]:media/java-run-compute-intensive-task/SvcBusQueues_03_CreateNewSvcNamespace.jpg
[avail_namespaces]:media/java-run-compute-intensive-task/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[namespace_list]:media/java-run-compute-intensive-task/SvcBusQueues_05_NamespaceList.jpg
[properties_pane]:media/java-run-compute-intensive-task/SvcBusQueues_06_PropertiesPane.jpg
[default_key]:media/java-run-compute-intensive-task/SvcBusQueues_07_DefaultKey.jpg
[add_ca_cert]: ../../../java-add-certificate-ca-store.md
