# Criar modelos de instância

bookmark_border



Nesta página, você aprenderá a criar e gerenciar [modelos de instância](https://cloud.google.com/compute/docs/instance-templates). Com esses modelos, é possível especificar o tipo de máquina, a imagem do disco de inicialização, a rede e outras propriedades de VM que você quer usar ao criar instâncias de máquina virtual (VM).

Use os modelos para criar VMs em um [grupo de instâncias gerenciadas (MIG, na sigla em inglês)](https://cloud.google.com/compute/docs/instance-groups) ou para [criar VMs individuais](https://cloud.google.com/compute/docs/instances/create-vm-from-instance-template).

## Antes de começar

- Para usar os exemplos de linha de comando deste guia, faça o seguinte:
  1. Instale ou atualize para a versão mais recente da [Google Cloud CLI](https://cloud.google.com/compute/docs/gcloud-compute).
  2. [Defina uma região e uma zona padrão](https://cloud.google.com/compute/docs/gcloud-compute#set_default_zone_and_region_in_your_local_client).
- Para usar os exemplos da API deste guia, [configure o acesso a ela](https://cloud.google.com/compute/docs/api/prereqs).
- Leia quando e por que criar [modelos deterministas de instância](https://cloud.google.com/compute/docs/instance-templates/deterministic-instance-templates).

## Limitações

- A VPC compartilhada em interfaces diferentes de `nic0` para modelos de instância é compatível com a CLI gcloud e a API, mas não no console do Google Cloud.
- Não é possível atualizar ou alterar um modelo de instância depois de criá-lo. Caso tenha um modelo desatualizado ou precise fazer alterações na configuração, crie outro modelo.

## Criar um novo modelo de instância

A maioria das propriedades de VM que você pode especificar em uma solicitação para criar uma instância de VM individual também pode ser especificada para um modelo de instância, incluindo metadados de VM, scripts de inicialização, discos permanentes, contas de serviço e assim por diante. Especifique o tipo de máquina, o disco de inicialização e a rede.

Crie um modelo de instância através do [Console do Google Cloud](https://console.cloud.google.com/), [CLI do Google Cloud](https://cloud.google.com/compute/docs/gcloud-compute), [API](https://cloud.google.com/compute/docs/reference/latest/instanceTemplates), Terraform ou a [Bibliotecas de cliente do Cloud](https://cloud.google.com/compute/docs/api/libraries).

Permissões exigidas para a tarefa

[Console](https://cloud.google.com/compute/docs/instance-templates/create-instance-templates#console)[gcloud](https://cloud.google.com/compute/docs/instance-templates/create-instance-templates#gcloud)[API](https://cloud.google.com/compute/docs/instance-templates/create-instance-templates#api)[Terraform](https://cloud.google.com/compute/docs/instance-templates/create-instance-templates#terraform)[Go](https://cloud.google.com/compute/docs/instance-templates/create-instance-templates#go)[Java](https://cloud.google.com/compute/docs/instance-templates/create-instance-templates#java)[Node.js](https://cloud.google.com/compute/docs/instance-templates/create-instance-templates#node.js)[Python](https://cloud.google.com/compute/docs/instance-templates/create-instance-templates#python)

1. No Console do Google Cloud, acesse a página **Modelos de instância**.

   [Acesse "Modelos de instância"](https://console.cloud.google.com/compute/instanceTemplates/list)

2. Clique em **Criar modelo de instância**.

3. Digite valores para os campos a seguir ou aceite os valores padrão. Os valores padrão mudam com base na família de máquinas que você seleciona.

   - Tipo de máquina
   - Image
   - Disco de inicialização
   - Rede VPC
   - Endereço IP

4. Opcional: se você escolher uma imagem compatível com a VM protegida, altere as configurações de [VM protegida](https://cloud.google.com/security/shielded-cloud/shielded-vm) da VM:

   1. Em **Gerenciamento, segurança, discos, rede, locatário único**, clique na guia **Rede**.

   2. Se você quiser desativar a Inicialização segura, desmarque a caixa de seleção **Ativar a Inicialização segura**. A Inicialização segura ajuda a proteger as instâncias de VM de malware e rootkits nos níveis da inicialização e do kernel. Para mais informações, consulte [Inicialização segura](https://cloud.google.com/security/shielded-cloud/shielded-vm#secure-boot).

   3. Se quiser desativar o módulo da plataforma virtual confiável (vTPM, na sigla em inglês), desmarque a caixa de seleção **Ativar vTPM**. O vTPM permite a Inicialização medida, que valida a integridade da VM antes e durante a inicialização. Para mais informações, consulte [Módulo da plataforma virtual confiável (vTPM)](https://cloud.google.com/security/shielded-cloud/shielded-vm#vtpm).

      **Importante:** ao desativar o vTPM, você desabilita também o monitoramento de integridade, que depende dos dados coletados pela Inicialização medida.

   4. Se você quiser desativar o monitoramento de integridade, desmarque a caixa de seleção **Ativar monitoramento de integridade**. Com o monitoramento de integridade, você consegue monitorar a integridade da inicialização das instâncias de VM protegidas usando o Cloud Monitoring. Para mais informações, consulte [Monitoramento de integridade](https://cloud.google.com/security/shielded-cloud/shielded-vm#integrity-monitoring).

5. Opcional: em **Gerenciamento, segurança, discos, rede, locatário único**, clique nas guias para personalizar ainda mais o modelo. Por exemplo, adicione até 15 discos secundários que não sejam de inicialização.

6. Opcional: clique em **REST equivalente** para visualizar o corpo da solicitação REST, que inclui a [representação JSON](https://cloud.google.com/compute/docs/reference/rest/v1/instanceTemplates) do modelo de instância.

7. Clique em **Criar** para criar o modelo.

## Como criar um modelo com base em uma instância atual

Use a API Compute Engine ou a CLI gcloud para salvar a configuração de uma instância de VM atual como um modelo de instância. Como opção, modifique a maneira como os discos de origem são definidos no modelo.

Se você precisar modificar outras propriedades, primeiro crie um modelo com base em uma instância atual e, em seguida, [crie um modelo semelhante](https://cloud.google.com/compute/docs/instance-templates/create-instance-templates#based-on-existing-template) com outras modificações.

**Aviso:** se a instância atual contiver um endereço IP externo estático, esse endereço será copiado para o modelo de instância e poderá limitar o uso dele.

Permissões exigidas para a tarefa

[gcloud](https://cloud.google.com/compute/docs/instance-templates/create-instance-templates#gcloud)[API](https://cloud.google.com/compute/docs/instance-templates/create-instance-templates#api)[Go](https://cloud.google.com/compute/docs/instance-templates/create-instance-templates#go)[Java](https://cloud.google.com/compute/docs/instance-templates/create-instance-templates#java)[Node.js](https://cloud.google.com/compute/docs/instance-templates/create-instance-templates#node.js)[Python](https://cloud.google.com/compute/docs/instance-templates/create-instance-templates#python)

Use o [comando `gcloud compute instance-templates create`](https://cloud.google.com/sdk/gcloud/reference/compute/instance-templates/create) com as sinalizações `--source-instance` e `--source-instance-zone`.

```
gcloud compute instance-templates create INSTANCE_TEMPLATE_NAME \
    --source-instance=SOURCE_INSTANCE \
    --source-instance-zone=SOURCE_INSTANCE_ZONE
```

Para modificar como os discos da instância de origem são definidos, adicione uma ou mais sinalizações `--configure-disk`:

```
gcloud compute instance-templates create INSTANCE_TEMPLATE_NAME \
    --source-instance=SOURCE_INSTANCE \
    --source-instance-zone=SOURCE_INSTANCE_ZONE \
    --configure-disk= \
        device-name=SOURCE_DISK, \
        instantiate-from=INSTANTIATE_OPTIONS, \
        auto-delete=AUTO_DELETE
```

Substitua:

- `INSTANCE_TEMPLATE_NAME` é o nome do modelo a ser criado;

- `SOURCE_INSTANCE` é o nome da instância a ser usada como modelo para o novo modelo;

- `SOURCE_INSTANCE_ZONE` é a zona que contém a instância de origem;

- `SOURCE_DISK` é o nome de um disco de instância de origem que você quer substituir no modelo;

- `INSTANTIATE_OPTIONS` especifica se precisa incluir o disco e qual imagem usar. Os valores válidos dependem do tipo de disco:

  - `source-image` ou `source-image-family`: válido somente para discos de inicialização e outros discos de leitura/gravação permanentes. Especifique esta opção se você quiser usar a mesma imagem de origem ou família de imagens de origem que foi usada para criar o disco na instância de VM de origem.

  - `custom-image`: válido somente para discos de inicialização e outros discos de leitura/gravação permanentes. Se quiser manter aplicativos e configurações das VMs de origem no modelo de instância, [crie uma imagem personalizada](https://cloud.google.com/compute/docs/images/create-delete-deprecate-private-images) e especifique-a ao criar o modelo. Se especificado, forneça o caminho ou URL para a imagem personalizada, conforme mostrado no exemplo a seguir. Como alternativa, também é possível especificar uma família de imagens usando o seguinte formato:

    `--configure-disk=device-name=DATA_DISK_NAME,instantiate-from=custom-image,custom-image=projects/PROJECT_ID/global/images/family/IMAGE_FAMILY_NAME`

  - `attach-read-only`: válido apenas para discos somente leitura.

  - `blank`: válido apenas para discos permanentes que não sejam de inicialização e SSDs locais. Se especificado, quando o modelo for usado para criar uma nova instância, o disco será criado sem formatação. [Formate e ative](https://cloud.google.com/compute/docs/disks/add-persistent-disk#formatting) o disco em um script de inicialização para poder usá-lo em uma configuração escalonável.

  - `do-not-include`: válido apenas para discos permanentes que não sejam de inicialização e discos somente leitura.

- `AUTO_DELETE` especifica se o disco será excluído automaticamente quando a instância for excluída. Os valores válidos são: `false`, `no`, `true` e `yes`.

Por exemplo, o comando a seguir cria um modelo de instância com base em `my-source-instance`, com a opção de usar a imagem original de `data-disk-a`, mas definindo a exclusão automática como `true` e substituindo `data-disk-b` por uma imagem personalizada.

```
gcloud compute instance-templates create my-instance-template  \
    --source-instance=my-source-instance \
    --configure-disk=device-name=data-disk-a,instantiate-from=source-image, \
      auto-delete=true
    --configure-disk=device-name=data-disk-b,instantiate-from=custom-image, \
      custom-image=projects/cps-cloud/global/images/cos-89-16108-403-15
```

A tabela a seguir mostra como as opções de substituição de discos são definidas no modelo.

| Tipo de disco                                 | Opções                                                       |
| :-------------------------------------------- | :----------------------------------------------------------- |
| Disco de inicialização                        | **[Padrão]** Use a mesma imagem de origem ou família de imagens que foi usada para criar o disco de inicialização na instância de origem.Use o URL de qualquer imagem (personalizada ou pública), conforme descrito no exemplo anterior, ou especifique uma família de imagens usando o seguinte formato:`projects/exampleproject/global/images/family/IMAGE_FAMILY_NAME` |
| Outros discos permanentes de leitura/gravação | **[Padrão]** Use a mesma imagem/família de imagens de origem que foi usada para criar o disco na instância de origem. Observação: se o disco da instância de origem não tiver uma imagem de origem ou uma propriedade de família de imagem desse tipo, ele será incluído no modelo como um disco vazio.Use o URL de qualquer imagem (personalizada ou pública), conforme descrito no exemplo anterior, ou especifique uma família de imagens usando o seguinte formato:`projects/exampleproject/global/images/family/IMAGE_FAMILY_NAME`Use um disco vazio no modelo. Quando o modelo é usado para criar uma nova instância, esse disco é criado sem formatação. [Formate e ative](https://cloud.google.com/compute/docs/disks/add-persistent-disk#formatting) o disco em um script de inicialização para poder usá-lo em uma configuração escalonável.Não inclua o disco. |
| Discos somente de leitura                     | **[Padrão]** Inclua o disco no modo somente leitura.Não inclua o disco. |
| SSDs locais                                   | **[Padrão]** Inclua um SSD local em branco. Quando o modelo é usado para criar uma nova instância, esse disco é criado sem formatação. [Formate e ative](https://cloud.google.com/compute/docs/disks/add-local-ssd#formatandmount) o disco em um script de inicialização para poder usá-lo em uma configuração escalonável. |

Também é possível modificar o atributo `auto-delete` para cada disco, especificando se ele precisa ser excluído ou não quando sua instância associada for excluída.

Por padrão, se nenhuma opção de modificação for especificada, a configuração do disco no modelo corresponderá à instância de origem.

## Como criar um modelo de instância com base em um modelo atual

Não é possível atualizar um modelo de instância atual. No entanto, se um modelo de instância ficar desatualizado ou se você precisar fazer alterações, use o console para criar outro com propriedades semelhantes.

1. Acesse a página **Modelos de instância**.

   [Acesse "Modelos de instância"](https://console.cloud.google.com/compute/instanceTemplates)

2. Clique no modelo da instância que você quer copiar e atualizar.

3. Clique em **Criar semelhante**.

4. Atualize a configuração no novo modelo.

5. Clique em **Criar**.

## Como criar um modelo de instância com uma imagem de contêiner

Especifique uma imagem de contêiner em um modelo de instância. Por padrão, o Compute Engine também inclui no modelo uma [imagem do sistema operacional Container-Optimized](https://cloud.google.com/container-optimized-os/docs) com o Docker instalado. Quando você usa o modelo para criar uma nova instância, o contêiner é iniciado automaticamente à medida que a instância é iniciada.

[Console](https://cloud.google.com/compute/docs/instance-templates/create-instance-templates#console)[gcloud](https://cloud.google.com/compute/docs/instance-templates/create-instance-templates#gcloud)

1. Acesse a página **Modelos de instância**.

   [Acesse "Modelos de instância"](https://console.cloud.google.com/compute/instanceTemplates)

2. Clique em **Criar modelo de instância**.

3. Na seção **Contêiner**, clique em **Implantar contêiner**.

4. Na caixa de diálogo **Configurar contêiner**, especifique a **Imagem de contêiner** a ser usada.

   - É possível especificar uma imagem do

      

     Container Registry

      

     ou do

      

     Artifact Registry

     . Exemplo:

     - `gcr.io/cloud-marketplace/google/nginx1:TAG`, em que TAG é a tag definida para uma versão específica da imagem do contêiner NGINX disponível no Google Cloud Marketplace.
     - `us-docker.pkg.dev/google-samples/containers/gke/hello-app:1.0` seleciona uma amostra de imagem `hello-app` armazenada no Artifact Registry.

   - Se você usar uma imagem de contêiner do Docker Hub, sempre especifique o nome completo da imagem do Docker. Por exemplo, forneça o seguinte nome para implantar uma imagem do contêiner Apache: `docker.io/httpd:2.4`

5. Se quiser, clique em **Opções de contêiner avançadas**. Para mais informações, consulte [Como configurar opções para executar seu contêiner](https://cloud.google.com/compute/docs/containers/configuring-options-to-run-containers).

6. Clique em **Criar**.

## Como criar um modelo de instância que especifica uma sub-rede

[gcloud](https://cloud.google.com/compute/docs/instance-templates/create-instance-templates#gcloud)[Go](https://cloud.google.com/compute/docs/instance-templates/create-instance-templates#go)[Java](https://cloud.google.com/compute/docs/instance-templates/create-instance-templates#java)[Node.js](https://cloud.google.com/compute/docs/instance-templates/create-instance-templates#node.js)[Python](https://cloud.google.com/compute/docs/instance-templates/create-instance-templates#python)

Use a sinalização `--subnet` para colocar instâncias criadas a partir do modelo na sub-rede escolhida. A sinalização `--subnet` exige a sinalização `--region`.

```
gcloud compute instance-templates create INSTANCE_TEMPLATE_NAME \
  --region=REGION \
  --subnet=SUBNET_NAME_OR_URL
```

Substitua:

- `INSTANCE_TEMPLATE_NAME`: o nome do modelo da instância

- `REGION`: a região da sub-rede

- `SUBNET_NAME_OR_URL`: o nome da sub-rede ou o URL dela

  **Observação:** se você usar o nome da sub-rede, o Google a encontrará na região de destino, desde que ela exista nesse local. Porém, se usar o URL da sub-rede, o modelo de instância só poderá ser usado para criar instâncias na região específica associada ao URL da sub-rede.

No exemplo a seguir, criamos um modelo chamado `template-qa` que gera instâncias apenas na sub-rede `subnet-us-qa`.

```
gcloud compute instance-templates create template-qa \
  --region=us-central1 \
  --subnet=subnet-us-qa

Created [https://compute.googleapis.com/compute/latest/projects/PROJECT_ID/global/instanceTemplates/template-qa].
NAME        MACHINE_TYPE        PREEMPTIBLE CREATION_TIMESTAMP
template-qa e2-standard-2       2019-12-23T20:34:00.791-07:00
```

O uso desse modelo para criar instâncias de um [MIG](https://cloud.google.com/compute/docs/instance-groups) com ou sem [escalonamento automático](https://cloud.google.com/compute/docs/autoscaler), gera automaticamente a instância na região e na sub-rede especificadas. Isso permite controlar a sub-rede de novas instâncias criadas para balanceamento de carga.

## Como usar imagens personalizadas ou públicas nos modelos de instância

É possível usar uma imagem personalizada ou uma imagem pública para os modelos de instância:

- **Imagens personalizadas.** Como os MIGs foram projetados para adicionar e remover instâncias com frequência, vale a pena [criar uma imagem personalizada](https://cloud.google.com/compute/docs/images/create-delete-deprecate-private-images) e especificá-la no modelo de instância. Prepare a imagem com os aplicativos e as configurações necessários para que as VMs não precisem configurar manualmente esses itens em VMs individuais no MIG.
- **Imagens públicas.** É possível criar um modelo de instância que use uma [imagem pública](https://cloud.google.com/compute/docs/images) e um [script de inicialização](https://cloud.google.com/compute/docs/startupscript) para preparar essa instância assim que ela começar a ser executada.

As imagens personalizadas têm caráter mais [determinista](https://cloud.google.com/compute/docs/instance-templates/deterministic-instance-templates) e são inicializadas mais rapidamente do que as VMs com scripts de inicialização. No entanto, esses scripts são mais flexíveis e permitem que você atualize os apps e as configurações nas suas instâncias com mais facilidade.

Se você estiver gerenciando imagens usando [famílias de imagens](https://cloud.google.com/compute/docs/images#image_families), especifique o nome da sua família de imagens personalizada ou pública no modelo de instância. Para mais informações sobre famílias de imagens, consulte [práticas recomendadas ao usar famílias de imagens](https://cloud.google.com/compute/docs/images/image-families-best-practices#how-to-use) no Compute Engine.

**Observação:** se a imagem que você quer usar pertencer a um projeto diferente, ainda será possível usá-la no modelo de instância, desde que o proprietário desse projeto conceda ao MIG acesso às imagens. Para mais informações, consulte [Como conceder ao MIG acesso às imagens](https://cloud.google.com/compute/docs/images/sharing-images-across-projects#granting_a_managed_instance_group_access_to_images).

## A seguir

- Crie uma [instância de VM a partir de um modelo de instância](https://cloud.google.com/compute/docs/instances/create-vm-from-instance-template).
- Crie um [grupo de instâncias gerenciadas por zona (MIG)](https://cloud.google.com/compute/docs/instance-groups/creating-groups-of-managed-instances).
- Crie um [MIG regional](https://cloud.google.com/compute/docs/instance-groups/distributing-instances-with-regional-instance-groups).
- [Atualize as VMs em um MIG atual](https://cloud.google.com/compute/docs/instance-groups/updating-migs) para usar a configuração de um novo modelo.