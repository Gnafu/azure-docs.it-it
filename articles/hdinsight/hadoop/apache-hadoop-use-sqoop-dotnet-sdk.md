---
title: Eseguire processi Sqoop usando .NET e HDInsight - Azure
description: Informazioni su come usare HDInsight .NET SDK per eseguire attività di importazione ed esportazione Sqoop tra un cluster Hadoop e un database SQL di Azure.
keywords: processo sqoop
ms.reviewer: jasonh
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 423934dee0b490bb12522cec6d37393a1ec5e2f4
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2018
ms.locfileid: "51014874"
---
# <a name="run-sqoop-jobs-by-using-net-sdk-for-hadoop-in-hdinsight"></a>Eseguire processi Sqoop con .NET SDK per Hadoop in HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Informazioni su come usare Azure HDInsight .NET SDK per eseguire processi Sqoop in HDInsight per attività di importazione ed esportazione tra un cluster HDInsight e un database SQL di Azure o un database di SQL Server.

> [!NOTE]
> Anche se le procedure illustrate in questo articolo possono essere usate sia con un cluster HDInsight basato su Windows che con uno basato su Linux, funzionano solo se eseguite da un client Windows. Per scegliere altri metodi, usare il selettore di schede all'inizio di questo articolo.
> 

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

* Un cluster Hadoop in HDInsight. Per altre informazioni, vedere [Creare un cluster e un database SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Usare Sqoop nei cluster HDInsight con .NET SDK
HDInsight .NET SDK fornisce librerie client .NET che semplificano l'uso dei cluster HDInsight da .NET. In questa sezione si crea un'applicazione console C# per esportare la tabella hivesampletable nella tabella del database SQL di Azure creata in precedenza in questa esercitazione.

## <a name="submit-a-sqoop-job"></a>Inviare un processo Sqoop

1. Creare un'applicazione console C# in Visual Studio.

2. Dalla console di Gestione pacchetti di Visual Studio importare il pacchetto eseguendo il comando NuGet seguente:
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job

3. Nel file Program.cs usare il codice seguente:
   
        using System.Collections.Generic;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;
   
        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;
   
                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitSqoopJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitSqoopJob()
                {
                    var sqlDatabaseServerName = "<SQLDatabaseServerName>";
                    var sqlDatabaseLogin = "<SQLDatabaseLogin>";
                    var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                    var sqlDatabaseDatabaseName = "<DatabaseName>";
   
                    var tableName = "<TableName>";
                    var exportDir = "/tutorials/usesqoop/data";
   
                    // Connection string for using Azure SQL Database.
                    // Comment if using SQL Server
                    var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                    // Connection string for using SQL Server.
                    // Uncomment if using SQL Server
                    //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
   
                    var parameters = new SqoopJobSubmissionParameters
                    {
                        Files = new List<string> { "/user/oozie/share/lib/sqoop/sqljdbc41.jar" }, // This line is required for Linux-based cluster.
                        Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
                    };
   
                    System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }

4. Per eseguire il programma, premere **F5**. 

## <a name="limitations"></a>Limitazioni
HDInsight basato su Linux prevede le limitazioni seguenti:

* Esportazione in blocco: il connettore Sqoop usato per esportare dati in Microsoft SQL Server o nel database SQL di Azure non supporta al momento gli inserimenti in blocco.

* Invio in batch: usando l'opzione `-batch` durante gli inserimenti, Sqoop esegue più inserimenti invece di inviare in batch le operazioni di inserimento.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come usare Sqoop. Per altre informazioni, vedere:

* [Usare Oozie con HDInsight](../hdinsight-use-oozie.md): usare un'azione di Sqoop nel flusso di lavoro di Oozie.
* [Analizzare i dati sui ritardi dei voli usando HDInsight](../hdinsight-analyze-flight-delay-data.md): usare Hive nell'analisi dei dati sui ritardi dei voli e quindi usare Sqoop per esportare dati nel database SQL di Azure.
* [Caricare i dati in HDInsight](../hdinsight-upload-data.md): trovare altri metodi per caricare i dati in HDInsight o in Archiviazione BLOB di Azure.

