# Como adicionar um grupo de instâncias a um balanceador de carga

bookmark_border



O balanceamento de carga do Google Cloud usa grupos de instâncias, gerenciadas e não gerenciadas, para veicular o tráfego. Dependendo do tipo de balanceador de carga que você estiver usando, adicione grupos de instâncias a um pool de destino ou serviço de back-end. Para saber mais, consulte [Como escolher um balanceador de carga](https://cloud.google.com/load-balancing/docs/choosing-load-balancer).

Para saber mais sobre grupos de instâncias, leia a [Visão geral de grupos de instâncias](https://cloud.google.com/compute/docs/instance-groups).

## Antes de começar

- Para usar os exemplos da API deste guia, [configure o acesso a ela](https://cloud.google.com/compute/docs/api/prereqs).
- 

- Para usar os exemplos de linha de comando deste guia, instale a [CLI do Google Cloud](https://cloud.google.com/compute/docs/gcloud-compute).

## Limitações

- Para usar o

   

  balanceamento de carga

   

  com um MIG regional, as seguintes limitações se aplicam:

  - Não é possível usar o modo de balanceamento `maxRate`.
  - Se você usa um esquema de balanceamento de carga HTTP(S) com um MIG regional, é necessário escolher o modo de balanceamento `maxRatePerInstance` ou `maxUtilization`.

## Adição de um grupo de instâncias gerenciadas a um serviço de back-end

É necessário um serviço de back-end para criar a maioria dos tipos de balanceadores de carga.

Um serviço de back-end pode conter vários back-ends. Um grupo de instâncias é um tipo de back-end. As instâncias contidas no grupo respondem ao tráfego vindo do balanceador de carga. O serviço de back-end, por sua vez, sabe quais instâncias podem ser usadas, quanto tráfego elas podem processar e a quantidade de tráfego que estão processando no momento. Além disso, a verificação de integridade é monitorada no serviço de back-end, que não envia novas conexões para instâncias não íntegras.

Use as instruções para adicionar um grupo de instâncias gerenciadas a um serviço de back-end.

[Console](https://cloud.google.com/compute/docs/instance-groups/adding-an-instance-group-to-a-load-balancer#console)[gcloud](https://cloud.google.com/compute/docs/instance-groups/adding-an-instance-group-to-a-load-balancer#gcloud)[API](https://cloud.google.com/compute/docs/instance-groups/adding-an-instance-group-to-a-load-balancer#api)

1. Acesse a página Balanceamento de carga no Console do Google Cloud.

   [Acessar a página "Balanceamento de carga"](https://console.cloud.google.com/net-services/loadbalancing/backends/list)

2. Selecione o nome do serviço de back-end ao qual você está adicionando o grupo de instâncias gerenciadas.

3. Clique em **Editar**.

4. Selecione **+Adicionar back-end**.

5. Selecione o grupo de instâncias que você quer adicionar.

6. Edite as configurações opcionais como quiser.

7. Salve as alterações.

## Adição de um grupo de instâncias gerenciadas a um pool de destino

Um pool de destino é um objeto que contém uma ou mais instâncias de máquina virtual. Ele é usado no [Balanceamento de carga de rede](https://cloud.google.com/load-balancing/docs/network), em que as solicitações do usuário são encaminhadas para o pool de destino associado ao balanceador de carga. As instâncias que fazem parte desse pool atendem essas solicitações e retornam uma resposta. Adicione um grupo de instâncias gerenciadas a um pool de destino para que, quando as instâncias forem adicionadas ou removidas do grupo, o pool também seja automaticamente atualizado com as alterações.

Para adicionar um grupo de instâncias gerenciadas a um pool de destino, é preciso que esse pool já exista. Para mais informações, consulte a documentação [Como adicionar um pool de destino](https://cloud.google.com/compute/docs/load-balancing/network/target-pools#add_a_target_pool).

Para adicionar um grupo de instâncias gerenciadas a um pool de destino, execute as etapas a seguir. Isso faz com que todas as instâncias de VM contidas no grupo sejam adicionadas ao pool de destino.

**Observação:** enquanto a instância estiver sendo adicionada, o `currentAction` dela mudará para [`REFRESHING`](https://cloud.google.com/compute/docs/reference/latest/instanceGroupManagers#currentActions.refreshing). A instância não poderá ser usada enquanto estiver no estado `REFRESHING`, mas é possível usar o comando [`wait-until-stable`](https://cloud.google.com/sdk/gcloud/reference/compute/instance-groups/managed/wait-until-stable) para pesquisar nela até que se torne estável.

[Console](https://cloud.google.com/compute/docs/instance-groups/adding-an-instance-group-to-a-load-balancer#console)[gcloud](https://cloud.google.com/compute/docs/instance-groups/adding-an-instance-group-to-a-load-balancer#gcloud)[API](https://cloud.google.com/compute/docs/instance-groups/adding-an-instance-group-to-a-load-balancer#api)

1. Acesse a página "Pools de destino" no console do Google Cloud.

   [Acessar a página "Pools de destino"](https://console.cloud.google.com/net-services/loadbalancing/advanced/targetPools/list)

2. Clique no pool de destino para adicionar o grupo de instâncias.

3. Clique no botão **Editar**.

4. Role para baixo até a seção **Instâncias da VMs** e clique em **Selecionar grupos de instâncias**.

5. Selecione um grupo de instâncias no menu suspenso.

6. Salve as alterações.

## Como atribuir portas nomeadas a grupos de instâncias gerenciadas

As portas nomeadas são pares de chave-valor que representam o nome e o número de uma porta. O nome da porta representa o nome de um serviço ou aplicativo. Esse nome representa a porta em que o serviço ou aplicativo é executado.

As portas nomeadas são utilizadas pelo Cloud Load Balancing. Balanceadores de carga que atuam como proxies se inscrevem em somente uma porta nomeada na configuração do serviço de back-end. O nome da porta é convertido em um número de porta com base no mapeamento de portas nomeadas de cada back-end do grupo de instâncias.

Por exemplo, um serviço de back-end pode se inscrever em uma porta nomeada `http-port`. O grupo de instâncias de back-end pode ter uma porta chamada `http-port:80`. O grupo de instâncias de back-end instrui o balanceador de carga a enviar tráfego para uma VM no grupo na porta 80 usando um protocolo, como TCP. O protocolo é definido no serviço de back-end do balanceador de carga.

As portas nomeadas são metadados simples usados por [balanceadores de carga do proxy](https://cloud.google.com/load-balancing/docs/backend-service#named_ports). Elas não controlam recursos de rede ou firewall no Compute Engine.

### Vários números de porta para uma porta nomeada

É possível atribuir várias portas para cada nome de serviço. Também é possível atribuir vários nomes de serviço a cada porta.

Tenha em mente os seguintes pontos:

- O tráfego é balanceado entre todas as portas com o mesmo nome.

- Um determinado serviço de back-end só pode encaminhar tráfego para uma porta nomeada de cada vez.

- Se você usar vários números de porta para uma porta nomeada, todas as portas precisarão ser do mesmo aplicativo.

  Por exemplo, `http:80,http:8080` funciona, mas `http:80,http:443` não funciona porque a porta 80 não é compatível com TLS.

É possível definir várias portas nomeadas em um grupo de instâncias. Para ilustrar, considere um serviço de back-end de exemplo que assina a porta nomeada `http-port` e que tem dois grupos de instâncias de back-end:

- `instance-group-a` com uma porta nomeada de `http-port:80`
- `instance-group-b` com uma porta nomeada de `http-port:79`

Este serviço de back-end envia tráfego para a porta 80 para VMs em `instance-group-a` e porta 79 para VMs em `instance-group-b`.

Quando você adicionar o mesmo grupo de instâncias a vários serviços de back-end (para vários balanceadores de carga), e o grupo de instâncias exibir portas diferentes em cada balanceador de carga, não adicione todos os números de porta a uma única porta nomeada. Em vez disso, crie uma porta nomeada exclusiva e mapeie-a para um conjunto de portas que cada serviço de back-end deve exibir.

Por exemplo, suponha que você tenha três aplicativos, cada um com seu próprio balanceador de carga. Cada aplicativo tem um número de porta: 81 para `app1`, 82 para `app2` e 83 para `app3`. Supondo que os três serviços de back-end estejam usando um grupo de instâncias comum, atribua as portas nomeadas desse grupo de instâncias como `[app1:81, app2:82, app3:83]`.

### Portas nomeadas e verificações de integridade

Para que uma porta nomeada com vários números de portas em um serviço de back-end funcione em um balanceador de carga HTTP(S) externo ou interno, a verificação de integridade precisa ter a sinalização `--use-serving-port` de um número de porta específico. Essa opção não está disponível no Console do Google Cloud. Você precisa usar `gcloud` ou a API para definir `--use-serving-port`.

Suponha que você tenha um aplicativo que balanceie a carga do tráfego em três portas diferentes (81, 82, 83) no mesmo serviço de back-end com um único grupo de instâncias. É possível atribuir as portas para esses grupos de instâncias como `[app:81, 82, 83]`. A verificação de integridade desse cenário precisa ter a sinalização `--use-serving-port`.

Para mais informações, consulte a [documentação de verificação de integridade do balanceamento de carga](https://cloud.google.com/load-balancing/docs/health-check-concepts#category_and_port_specification).

### Portas nomeadas e regras de firewall

Portas nomeadas **não** criam nem modificam [regras de firewall](https://cloud.google.com/vpc/docs/firewalls) do Google Cloud. Para permitir o tráfego para as VMs de back-end, você ainda precisa criar as regras de firewall necessárias.

### Portas nomeadas e serviços de back-end

Além da configuração do grupo de instâncias, configure também o serviço de back-end. Por exemplo, suponha que você definiu a porta nomeada em um grupo de instâncias com o nome `my-service-name` e a porta `8888`:

```
gcloud compute instance-groups set-named-ports my-unmanaged-ig \
    --named-ports=my-service-name:8888
```

Em seguida, faça referência à porta nomeada na configuração do serviço de back-end com o `--port-name` no serviço de back-end definido como `my-service-name`:

```
gcloud compute backend-services update my-backend-service \
    --port-name=my-service-name
```

### Como criar portas nomeadas

Permissões exigidas para a tarefa

[Console](https://cloud.google.com/compute/docs/instance-groups/adding-an-instance-group-to-a-load-balancer#console)[gcloud](https://cloud.google.com/compute/docs/instance-groups/adding-an-instance-group-to-a-load-balancer#gcloud)[API](https://cloud.google.com/compute/docs/instance-groups/adding-an-instance-group-to-a-load-balancer#api)

1. No Console do Google Cloud, acesse a página

    

   Grupos de instâncias

   .

   [Acesse grupo de instâncias](https://console.cloud.google.com/compute/instanceGroups)

2. Clique no nome do grupo de instâncias em que você quer especificar as portas nomeadas. Uma página é aberta com as propriedades do grupo de instâncias.

3. Clique em **Editar** para modificar o grupo gerenciado de instâncias.

4. Na seção **Mapeamento de portas**, clique em **Adicionar porta** e digite o nome da porta e os números de porta que você quer associar a esse nome. Se você precisar de mais entradas, clique em **Adicionar porta** novamente.

5. Clique em **Salvar** para salvar as alterações e aplicar as portas nomeadas às instâncias no grupo de instâncias gerenciadas.

## A seguir

- Teste o tutorial, [Como usar o balanceamento de carga para aplicativos altamente disponíveis](https://cloud.google.com/compute/docs/tutorials/high-availability-load-balancing).
- [Crie um modelo de instância](https://cloud.google.com/compute/docs/instance-templates) que você possa usar para um grupo de instâncias gerenciadas.
- Crie um [grupo de instâncias gerenciadas regional](https://cloud.google.com/compute/docs/instance-groups/distributing-instances-with-regional-instance-groups).
- Ative a [recuperação automática](https://cloud.google.com/compute/docs/instance-groups/autohealing-instances-in-migs) do grupo de instâncias gerenciadas.
- Ative o [escalonamento automático](https://cloud.google.com/compute/docs/autoscaler) do grupo de instâncias gerenciadas.