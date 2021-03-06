---
title: Enviar trabalhos MapReduce usando o SDK do .NET do Azure HDInsight
description: Saiba como enviar tarefas do MapReduce para o Azure HDInsight Apache Hadoop usando o SDK do .NET do HDInsight.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 1ac2dda20ba1219c9f62e834b5cd2cfba8a50086
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718963"
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Executar trabalhos MapReduce usando o SDK do .NET do HDInsight
[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Saiba como enviar trabalhos MapReduce usando o SDK do .NET do HDInsight. Os clusters HDInsight vêm com um arquivo jar com alguns exemplos de MapReduce. O arquivo jar está localizado em */example/jars/hadoop-mapreduce-examples.jar*.  Um dos exemplos é *wordcount*. Você desenvolve um aplicativo do console C# para enviar um trabalho wordcount.  O trabalho lê o arquivo */example/data/gutenberg/davinci.txt* e gera os resultados em */example/data/davinciwordcount*.  Se você desejar executar novamente o aplicativo, você deve limpar a pasta de saída.

> [!NOTE]  
> As etapas neste artigo devem ser executadas em um cliente do Windows. Para obter informações sobre como usar um cliente Linux, OS X ou Unix para trabalhar com o Hive, use o seletor de tabulação mostrado na parte superior do artigo.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, você deve ter os seguintes itens:

* **Um cluster Hadoop no HDInsight**. Confira a [Introdução ao uso do Apache Hadoop baseado em Linux no HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* **Visual Studio 2013/2015/2017**.

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Enviar trabalhos MapReduce usando o SDK do .NET do HDInsight
O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET que facilitam o trabalho com clusters HDInsight do .NET. 

**Para enviar trabalhos**

1. No Visual Studio, crie um aplicativo de console C#.
2. No Console do Gerenciador de Pacotes NuGet, execute o comando a seguir:

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```
3. Use o seguinte código:

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;

            private const string existingClusterName = "<Your HDInsight Cluster Name>";
            private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
            private const string existingClusterUsername = "<Cluster Username>";
            private const string existingClusterPassword = "<Cluster User Password>";

            private const string defaultStorageAccountName = "<Default Storage Account Name>"; //<StorageAccountName>.blob.core.windows.net
            private const string defaultStorageAccountKey = "<Default Storage Account Key>";
            private const string defaultStorageContainerName = "<Default Blob Container Name>";

            private const string sourceFile = "/example/data/gutenberg/davinci.txt";  
            private const string outputFolder = "/example/data/davinciwordcount";

            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");

                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = existingClusterUsername, Password = existingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(existingClusterUri, clusterCredentials);

                SubmitMRJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitMRJob()
            {
                List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };

                var paras = new MapReduceJobSubmissionParameters
                {
                    JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                    JarClass = "wordcount",
                    Arguments = args
                };

                System.Console.WriteLine("Submitting the MR job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(paras);
                var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                System.Console.WriteLine("JobId is " + jobId);

                System.Console.WriteLine("Waiting for the job completion ...");

                // Wait for job completion
                var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                while (!jobDetail.Status.JobComplete)
                {
                    Thread.Sleep(1000);
                    jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                }

                // Get job output
                System.Console.WriteLine("Job output is: ");
                var storageAccess = new AzureStorageAccess(defaultStorageAccountName, defaultStorageAccountKey,
                    defaultStorageContainerName);
    
                if (jobDetail.ExitValue == 0)
                {
                    // Create the storage account object
                    CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" + 
                        defaultStorageAccountName + 
                        ";AccountKey=" + defaultStorageAccountKey);
    
                    // Create the blob client.
                    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
                    // Retrieve reference to a previously created container.
                    CloudBlobContainer container = blobClient.GetContainerReference(defaultStorageContainerName);
    
                    CloudBlockBlob blockBlob = container.GetBlockBlobReference(outputFolder.Substring(1) + "/part-r-00000");
    
                    using (var stream = blockBlob.OpenRead())
                    {
                        using (StreamReader reader = new StreamReader(stream))
                        {
                            while (!reader.EndOfStream)
                            {
                                System.Console.WriteLine(reader.ReadLine());
                            }
                        }
                    }
                }
                else
                {
                    // fetch stderr output in case of failure
                    var output = _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); 
    
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
    
                }
            }
        }
    }
    ```

4. Pressione **F5** para executar o aplicativo.

Para executar o trabalho novamente, é necessário alterar o nome da pasta de saída do trabalho, no exemplo, "/example/data/davinciwordcount".

Quando o trabalho for concluído com êxito, o aplicativo imprimirá o conteúdo do arquivo de saída "part-r-00000".

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu várias maneiras de criar um cluster HDInsight. Para saber mais, consulte os seguintes artigos:

* Para enviar um trabalho de Hive, consulte [Executar consultas Apache Hive usando o SDK do .NET HDInsight](apache-hadoop-use-hive-dotnet-sdk.md).
* Para criar clusters HDInsight, confira [Criar Clusters Apache Hadoop baseados em Linux no HDInsight.](../hdinsight-hadoop-provision-linux-clusters.md).
* Para gerenciar clusters HDInsight, consulte [Gerenciar clusters Apache Hadoop no HDInsight](../hdinsight-administer-use-portal-linux.md).
* Para aprender sobre o SDK do .NET do HDInsight, consulte [referência do SDK do .NET do HDInsight](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight).
* Para autenticação não interativa no Azure, confira [Criar aplicativos .NET HDInsight de autenticação não interativa](../hdinsight-create-non-interactive-authentication-dotnet-applications.md).

