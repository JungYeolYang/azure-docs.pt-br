---
title: Destinos de computação remotos do ML automatizado
titleSuffix: Azure Machine Learning service
description: Saiba como criar modelos usando o aprendizado de máquina automatizados em um destino de computação remota do Azure Machine Learning com o serviço de Azure Machine Learning
services: machine-learning
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 6a18bdf3a2a1ccd60ff20d21ebd99f4f6e15e38f
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/13/2019
ms.locfileid: "65551347"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Treinar modelos com o aprendizado de máquina automatizado na nuvem

No Azure Machine Learning, você treina seu modelo em diferentes tipos de recursos de computação que gerencia. O destino de computação pode ser um computador local ou um computador na nuvem.

Você pode facilmente escalar verticalmente ou escalar horizontalmente sua experiência de aprendizado de máquina adicionando destinos de computação adicionais, como a computação do Azure Machine Learning (AmlCompute). AmlCompute é uma infraestrutura de computação gerenciada que permite que você crie facilmente uma computação única ou vários nó.

Neste artigo, você aprenderá como criar um modelo usando ML automatizado com AmlCompute.

## <a name="how-does-remote-differ-from-local"></a>Como remoto difere do local?

O tutorial "[Treinar um modelo de classificação com aprendizado de máquina automatizado](tutorial-auto-train-models.md)" ensina a usar um computador local para treinar o modelo com ML automatizado.  O fluxo de trabalho durante o treinamento local também se aplica aos destinos remotos. No entanto, com a computação remota, as iterações de experimento de ML automatizado são executadas de maneira Assíncrona. Essa funcionalidade permite que você cancele uma iteração específica, observe o status da execução ou continue trabalhando em outras células no Jupyter Notebook. Para treinar remotamente, você primeiro crie um destino de computação remota, como AmlCompute. Em seguida, configura o recurso remoto e envia seu código para ele.

Este artigo mostra as etapas adicionais necessárias para executar um experimento AM automatizado em um destino AmlCompute remoto. O objeto de workspace, `ws`, do tutorial, é usado em todo o código aqui.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Criar recurso

Criar o destino AmlCompute no seu espaço de trabalho (`ws`) se ele ainda não existir.  

**Tempo estimado**: A criação do destino AmlCompute leva aproximadamente 5 minutos.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget

amlcompute_cluster_name = "automlcl" #Name your cluster
provisioning_config = AmlCompute.provisioning_configuration(vm_size = "STANDARD_D2_V2", 
                                                            # for GPU, use "STANDARD_NC6"
                                                            #vm_priority = 'lowpriority', # optional
                                                            max_nodes = 6)

compute_target = ComputeTarget.create(ws, amlcompute_cluster_name, provisioning_config)
    
# Can poll for a minimum number of nodes and for a specific timeout.
# If no min_node_count is provided, it will use the scale settings for the cluster.
compute_target.wait_for_completion(show_output = True, min_node_count = None, timeout_in_minutes = 20)
```

Agora você pode usar o objeto `compute_target` como o destino de computação remota.

Restrições de nome de cluster incluem:
+ Deve ser menor do que 64 caracteres.  
+ Não pode incluir nenhum dos seguintes caracteres: `\` ~! @ # $ % ^ & * ( ) = + _ [ ] { } \\\\ | ; : \' \\" , < > / ?.`

## <a name="access-data-using-getdata-file"></a>Acessar dados usando o arquivo get_data

Forneça ao recurso remoto acesso aos seus dados de treinamento. Para experimentos de aprendizado de máquina automatizados em execução em computação remota, os dados precisam ser buscados usando uma função `get_data()`.  

Para fornecer acesso, você deve:
+ Criar um arquivo get_data.py contendo uma função `get_data()` 
+ Coloque esse arquivo em um diretório acessível como um caminho absoluto 

Você pode encapsular o código para ler dados de um armazenamento de blobs ou de um disco local no arquivo get_data.py. No exemplo de código a seguir, os dados vêm do pacote sklearn.

>[!Warning]
>Se você está usando computação remota, deve usar `get_data()` no local em que suas transformações de dados são realizadas. Se você precisa instalar bibliotecas adicionais para transformações de dados como parte do get_data(), há etapas adicionais a serem seguidas. Consulte o [notebook de exemplo auto-ml-dataprep](https://aka.ms/aml-auto-ml-data-prep ) para encontrar detalhes.


```python
# Create a project_folder if it doesn't exist
if not os.path.exists(project_folder):
    os.makedirs(project_folder)

#Write the get_data file.
%%writefile $project_folder/get_data.py

from sklearn import datasets
from scipy import sparse
import numpy as np

def get_data():
    
    digits = datasets.load_digits()
    X_digits = digits.data[10:,:]
    y_digits = digits.target[10:]

    return { "X" : X_digits, "y" : y_digits }
```

## <a name="configure-experiment"></a>Configurar o experimento

Especifique as configurações para `AutoMLConfig`.  (Veja uma [lista completa de parâmetros](how-to-configure-auto-train.md#configure-experiment) e seus valores possíveis.)

Nas configurações, `run_configuration` é definido como o objeto `run_config`, que contém as definições e a configuração para a DSVM.  

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "iteration_timeout_minutes": 10,
    "iterations": 20,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "preprocess": False,
    "max_concurrent_iterations": 10,
    "verbosity": logging.INFO
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target = compute_target,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings,
                            )
```

### <a name="enable-model-explanations"></a>Habilitar explicações do modelo

Configure o parâmetro `model_explainability` opcional no construtor `AutoMLConfig`. Além disso, um objeto de dataframe de validação deve ser passado como um parâmetro `X_valid` para usar o recurso de explicabilidade do modelo.

```python
automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target = compute_target,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings,
                             model_explainability=True,
                             X_valid = X_test
                            )
```

## <a name="submit-training-experiment"></a>Enviar o teste de treinamento

Agora, envie a configuração para selecionar automaticamente o algoritmo, os hiperparâmetros e treinar o modelo.

```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

Você verá uma saída semelhante ao exemplo a seguir:

    Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
    ***********************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE:  A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ***********************************************************************************************
    
     ITERATION     PIPELINE                               DURATION                METRIC      BEST
             2      Standardize SGD classifier            0:02:36                  0.954     0.954
             7      Normalizer DT                         0:02:22                  0.161     0.954
             0      Scale MaxAbs 1 extra trees            0:02:45                  0.936     0.954
             4      Robust Scaler SGD classifier          0:02:24                  0.867     0.954
             1      Normalizer kNN                        0:02:44                  0.984     0.984
             9      Normalizer extra trees                0:03:15                  0.834     0.984
             5      Robust Scaler DT                      0:02:18                  0.736     0.984
             8      Standardize kNN                       0:02:05                  0.981     0.984
             6      Standardize SVM                       0:02:18                  0.984     0.984
            10      Scale MaxAbs 1 DT                     0:02:18                  0.077     0.984
            11      Standardize SGD classifier            0:02:24                  0.863     0.984
             3      Standardize gradient boosting         0:03:03                  0.971     0.984
            12      Robust Scaler logistic regression     0:02:32                  0.955     0.984
            14      Scale MaxAbs 1 SVM                    0:02:15                  0.989     0.989
            13      Scale MaxAbs 1 gradient boosting      0:02:15                  0.971     0.989
            15      Robust Scaler kNN                     0:02:28                  0.904     0.989
            17      Standardize kNN                       0:02:22                  0.974     0.989
            16      Scale 0/1 gradient boosting           0:02:18                  0.968     0.989
            18      Scale 0/1 extra trees                 0:02:18                  0.828     0.989
            19      Robust Scaler kNN                     0:02:32                  0.983     0.989


## <a name="explore-results"></a>Explorar os resultados

Você pode usar o mesmo widget do Jupyter que o mostrado no [tutorial de treinamento](tutorial-auto-train-models.md#explore-the-results) para ver um gráfico e uma tabela de resultados.

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```
Aqui está uma imagem estática do widget.  No notebook, você pode clicar em qualquer linha na tabela para ver as propriedades de execução e os logs de saída para a execução.   Você também pode usar a lista suspensa acima do gráfico para exibir um gráfico de cada métrica disponível para cada iteração.

![tabela de widget](./media/how-to-auto-train-remote/table.png)
![gráfico de widget](./media/how-to-auto-train-remote/plot.png)

O widget exibirá uma URL que você pode usar para ver e explorar os detalhes individuais da execução.
 
### <a name="view-logs"></a>Exibir logs

Localize os logs na DSVM em `/tmp/azureml_run/{iterationid}/azureml-logs`.

## <a name="best-model-explanation"></a>Melhor explicação do modelo

A recuperação dos dados de explicação do modelo permite que você visualize informações detalhadas sobre os modelos para aumentar a transparência sobre o que está sendo executado no back-end. Neste exemplo, você executa explicações de modelo apenas para o modelo de melhor ajuste. Se você executar todos os modelos no pipeline, isso resultará em um tempo de execução significativo. As informações da explicação do modelo incluem:

* shap_values: As informações de explicação geradas pelo lib da forma.
* expected_values: O valor esperado do modelo aplicado ao conjunto de dados X_train.
* overall_summary: Os valores de importância do recurso no nível do modelo classificados em ordem decrescente.
* overall_imp: Os nomes de recurso classificados na mesma ordem como overall_summary.
* per_class_summary: Os valores de importância do recurso de nível de classe são classificados em ordem decrescente. Disponível somente para o caso de classificação.
* per_class_imp: Os nomes dos recursos são classificados na mesma ordem em per_class_summary. Disponível somente para o caso de classificação.

Use o código a seguir para selecionar o melhor pipeline das iterações. O método `get_output` retorna a melhor execução e o modelo ajustado para a última invocação de ajuste.

```python
best_run, fitted_model = remote_run.get_output()
```

Importe a função `retrieve_model_explanation` e execute no melhor modelo.

```python
from azureml.train.automl.automlexplainer import retrieve_model_explanation

shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
    retrieve_model_explanation(best_run)
```

Imprima os resultados das variáveis de explicação `best_run` que você quer exibir.

```python
print(overall_summary)
print(overall_imp)
print(per_class_summary)
print(per_class_imp)
```

A impressão das variáveis de resumo da explicação `best_run` resulta na seguinte saída.

![Saída do console de explicabilidade do modelo](./media/how-to-auto-train-remote/expl-print.png)

Também é possível visualizar a importância do recurso por meio da interface do usuário do widget, bem como da interface do usuário da Web no portal do Azure dentro do workspace.

![UI de explicabilidade do modelo](./media/how-to-auto-train-remote/model-exp.png)

## <a name="example"></a>Exemplo

O [how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb) notebook demonstra conceitos neste artigo. 

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas

Saiba [como definir as configurações para treinamento automático](how-to-configure-auto-train.md).
