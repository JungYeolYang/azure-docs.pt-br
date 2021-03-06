---
title: Configurar políticas do Apache Kafka no HDInsight com o Enterprise Security Package - Azure
description: Aprenda a configurar as políticas do Apache Ranger para o Kafka no Azure HDInsight com o Enterprise Security Package.
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: tutorial
ms.date: 01/14/2019
ms.openlocfilehash: 6434f7cae3c3fa402efad00b2f6bfb0bc405f9e3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64730241"
---
# <a name="tutorial-configure-apache-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Tutorial: Configurar políticas do Apache Kafka no HDInsight com o Enterprise Security Package (Versão Prévia)

Saiba como configurar políticas do Apache Ranger para clusters Apache Kafka do Enterprise Security Package (ESP). Os clusters ESP estão conectados a um domínio, permitindo que os usuários se autentiquem com credenciais de domínio. Neste tutorial, você cria duas políticas do Ranger para restringir o acesso aos tópicos `sales*` e `marketingspend`.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Crie usuários de domínio
> * Criar políticas do Ranger
> * Criar tópicos em um cluster do Kafka
> * Testar as políticas do Ranger

## <a name="before-you-begin"></a>Antes de começar

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

* Entre no [Portal do Azure](https://portal.azure.com/).

* Criar uma [HDInsight Kafka cluster com o pacote de segurança empresarial](apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Conectar-se à interface do usuário de Administração do Apache Ranger

1. Em um navegador, conecte-se à interface de usuário do Ranger Admin usando o URL `https://<ClusterName>.azurehdinsight.net/Ranger/`. Lembre-se de alterar `<ClusterName>` para o nome do seu cluster Kafka.

    > [!NOTE]  
    > Credenciais do Ranger não são as mesmas credenciais do cluster do Hadoop. Para impedir que os navegadores usem credenciais do Hadoop em cache, use uma nova janela do navegador InPrivate para se conectar à interface de administração do Ranger Admin.

2. Entre usando suas credenciais de administrador do Azure Active Directory (AD). As credenciais de administrador do Azure AD não são iguais às credenciais de cluster do HDInsight nem às credenciais SSH do nó do Linux HDInsight.

   ![Interface de usuário de administração do Apache Ranger](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>Crie usuários de domínio

Visite [Crie um cluster do HDInsight com o Enterprise Security Package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds), para aprender como criar os usuários de domínio **sales_user** e **marketing_user**. Em um cenário de produção, os usuários do domínio vêm do seu locatário do Active Directory.

## <a name="create-ranger-policy"></a>Criar política do Ranger

Crie uma política de Ranger para **sales_user** e **marketing_user**.

1. Abra o **interface do usuário administrador do Ranger**.

2. Clique em  **\<ClusterName > _kafka** sob **Kafka**. Uma política previamente configurada pode estar listada.

3. Clique em **adicionar nova política** e insira os seguintes valores:

   |**Configuração**  |**Valor sugerido**  |
   |---------|---------|
   |Nome da política  |  política de vendas * hdi   |
   |Tópico   |  vendas * |
   |Selecionar usuário  |  sales_user1 |
   |Permissões  | publicar, consumir, criar |

   Os curingas a seguir podem ser incluídos no nome do tópico:

   * '*' Indica zero ou mais ocorrências de caracteres.
   * '?' Indica um caractere único.

   ![Política de criação de UI do administrador do Apache Ranger](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png)   

   >[!NOTE]
   >Aguarde alguns instantes para que o Ranger sincronize com o Azure AD se um usuário do domínio não for preenchido automaticamente para **Selecionar usuário**.

4. Clique em **Adicionar** para salvar a política.

5. Clique em **adicionar nova política** e, em seguida, insira os seguintes valores:

   |**Configuração**  |**Valor sugerido**  |
   |---------|---------|
   |Nome da política  |  política de marketing do hdi   |
   |Tópico   |  marketingspend |
   |Selecionar usuário  |  marketing_user1 |
   |Permissões  | publicar, consumir, criar |

   ![Política de criação de UI do administrador do Apache Ranger](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png)  

6. Clique em **Adicionar** para salvar a política.

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>Criar tópicos em um cluster do Kafka com ESP

Para criar dois tópicos, `salesevents` e `marketingspend`:

1. Use o seguinte comando para abrir uma conexão SSH para o cluster:

   ```bash
   ssh DOMAINADMIN@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   Substitua `DOMAINADMIN` pelo usuário administrador do cluster configurado durante a [criação do cluster](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds#create-a-hdinsight-cluster-with-esp) e substitua `CLUSTERNAME` pelo nome do cluster. Se solicitado, insira a senha da conta de usuário administrador. Para saber mais sobre como usar o `SSH` com HDInsight, confira [Usar SSH com HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix).

2. Use os seguintes comandos para salvar o nome do cluster em uma variável e instalar um utilitário de análise JSON `jq`. Quando solicitado, insira o nome do cluster Kafka.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. Use os comandos a seguir para obter os hosts do broker do Kafka. Quando solicitado, digite a senha da conta de administrador do cluster.

   ```bash
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

   > [!Note]  
   > Antes de prosseguir, você precisará configurar seu ambiente de desenvolvimento se ainda não tiver feito isso. Você precisará de componentes, como o Java JDK, Apache Maven e um cliente SSH com o scp. Para obter mais detalhes, confira as [instruções de instalação](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).
   
1. Baixe os [exemplos de consumidor do produtor ingressado no domínio do Apache Kafka](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Siga as etapas 2 e 3 em **Compilar e implantar o exemplo** em [Tutorial: Usar as APIs de produtor e consumidor do Apache Kafka](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-producer-consumer-api#build-and-deploy-the-example)

1. Execute os seguintes comandos:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

## <a name="test-the-ranger-policies"></a>Testar as políticas do Ranger

Com base nas políticas do Ranger configuradas, **sales_user** pode produzir/consumir o tópico `salesevents`, mas não o tópico `marketingspend`. Por outro lado, **marketing_user** pode produzir/consumir o tópico `marketingspend`, mas não o tópico `salesevents`.

1. Abra uma nova conexão de SSH ao cluster. Use o seguinte comando para entrar como **sales_user1**:

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. Execute o seguinte comando:

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf"
   ```

3. Use os nomes de broker da seção anterior para definir a seguinte variável de ambiente:

   ```bash
   export KAFKABROKERS=<brokerlist>:9092
   ```

   Exemplo: `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

4. Siga a Etapa 3 de **Compilar e implantar o exemplo** em [Tutorial: Usar as APIs de Produtor e Consumidor do Apache Kafka](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-producer-consumer-api#build-and-deploy-the-example) para garantir que o `kafka-producer-consumer.jar` também esteja disponível para **sales_user**.

5. Verifique se **sales_user1** pode produzir para o tópico `salesevents` executando o seguinte comando:

   ```bash
   java -jar kafka-producer-consumer.jar producer salesevents $KAFKABROKERS
   ```

6. Execute o seguinte comando para consumir do tópico `salesevents`:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Verifique se você consegue ler as mensagens.

7. Verifique se **sales_user1** não pode produzir para o tópico `marketingspend` executando o seguinte na mesma janela do SSH:

   ```bash
   java -jar kafka-producer-consumer.jar producer marketingspend $KAFKABROKERS
   ```

   Ocorre um erro de autorização e ele pode ser ignorado.

8. Observe que **marketing_user1** não pode consumir do tópico `salesevents`.

   Repita as etapas 1 a 4 acima, mas desta vez como **marketing_user1**.

   Execute o seguinte comando para consumir do tópico `salesevents`:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   As mensagens anteriores não podem ser vistas.

9. Exiba os eventos de auditoria de acesso da interface do usuário do Ranger.

   ![Auditoria de políticas do Ranger da interface do usuário](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)

## <a name="next-steps"></a>Próximas etapas

* [Escolha a opção Bring Your Own Key para o Apache Kafka](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok)
* [Uma introdução à segurança do Apache Hadoop com Enterprise Security Package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-introduction)
