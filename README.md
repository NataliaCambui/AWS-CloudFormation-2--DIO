# AWS-CloudFormation-2--DIO

# AWS CloudFormation — Guia Prático e Didático

## 1. Introdução

Neste material, vou explicar o que é o AWS CloudFormation, como ele funciona e como posso utilizá-lo para criar e gerenciar recursos na AWS.

O objetivo é entender como automatizar a criação de infraestrutura utilizando arquivos de configuração, em vez de configurar cada recurso manualmente pelo console da AWS.

O CloudFormation é um serviço de Infraestrutura como Código (Infrastructure as Code — IaC). Com ele, posso descrever os recursos que desejo criar utilizando arquivos nos formatos YAML ou JSON. A AWS interpreta essas definições e realiza as operações necessárias para provisionar os recursos.

## 2. O que é o AWS CloudFormation?

O AWS CloudFormation é um serviço que permite definir, provisionar, atualizar e gerenciar recursos da AWS por meio de templates.

Por exemplo, se eu precisar criar um bucket S3 para armazenar arquivos, uma tabela DynamoDB para guardar informações e uma função Lambda para executar um processamento, posso descrever todos esses recursos em um template.

Em vez de criar cada recurso manualmente, utilizo o CloudFormation para executar as configurações definidas no arquivo.

### Principais vantagens

- **Automação:** reduz a necessidade de configurar recursos manualmente.
- **Padronização:** permite utilizar as mesmas definições em diferentes ambientes.
- **Reutilização:** possibilita reaproveitar templates para criar infraestruturas semelhantes.
- **Versionamento:** os arquivos podem ser armazenados no GitHub e acompanhados ao longo do tempo.
- **Rastreabilidade:** permite acompanhar as operações realizadas nas stacks.
- **Manutenção:** facilita a atualização da infraestrutura por meio de alterações no template.

## 3. Conceitos fundamentais

Antes de utilizar o CloudFormation, preciso entender alguns conceitos importantes.

| Conceito | Explicação |
|---|---|
| Template | Arquivo YAML ou JSON que descreve a infraestrutura desejada. |
| Stack | Conjunto de recursos que o CloudFormation gerencia como uma unidade. |
| Resources | Seção do template em que declaro os recursos que desejo criar. |
| Parameters | Valores que posso informar ao executar o template. |
| Properties | Configurações específicas de cada recurso. |
| Outputs | Informações que o CloudFormation disponibiliza após a execução. |
| Change Set | Prévia das alterações que serão realizadas em uma stack. |
| Drift Detection | Recurso que verifica diferenças entre a configuração esperada e a configuração encontrada na AWS. |

### Como esses conceitos se relacionam?

O template contém as definições da infraestrutura. Quando envio esse arquivo para o CloudFormation, posso criar uma stack. Essa stack passa a gerenciar os recursos descritos no template.

Quando preciso modificar a infraestrutura, altero o template e solicito uma atualização da stack. O CloudFormation avalia as diferenças e executa as alterações necessárias, respeitando as regras e dependências dos recursos.

## 4. Como funciona um template?

Um template CloudFormation é um arquivo que descreve a infraestrutura desejada.

Ele pode conter diferentes seções, mas as principais para começar são:

- `AWSTemplateFormatVersion`: identifica a versão do formato do template.
- `Description`: descreve a finalidade do arquivo.
- `Parameters`: define valores que podem ser informados durante a execução.
- `Resources`: declara os recursos que serão gerenciados.
- `Outputs`: disponibiliza informações importantes após a execução.

Nem todas as seções são obrigatórias. Em um template simples, posso declarar apenas os recursos que desejo criar.

## 5. Exemplo prático: criando um bucket S3

Neste exemplo, vou criar um bucket Amazon S3 utilizando o CloudFormation.

Um bucket S3 é utilizado para armazenar objetos, como documentos, imagens e arquivos de processamento.

Vou configurar o bucket com criptografia dos objetos e bloqueio de acesso público.

Crio um arquivo chamado `bucket.yaml` com o seguinte conteúdo:

```yaml
AWSTemplateFormatVersion: '2010-09-09'

Description: Exemplo de criacao de um bucket S3

Resources:
  MeuBucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketEncryption:
        ServerSideEncryptionConfiguration:
          - ServerSideEncryptionByDefault:
              SSEAlgorithm: AES256

      PublicAccessBlockConfiguration:
        BlockPublicAcls: true
        IgnorePublicAcls: true
        BlockPublicPolicy: true
        RestrictPublicBuckets: true
```

### Entendendo o código

**`AWSTemplateFormatVersion`**

Identifica a versão do formato utilizado pelo template.

**`Description`**

Apresenta uma descrição para facilitar o entendimento da finalidade do arquivo.

**`Resources`**

É a seção em que declaro os recursos que o CloudFormation deverá criar ou gerenciar.

**`MeuBucket`**

É o identificador lógico do recurso dentro do template. Não corresponde necessariamente ao nome real do bucket na AWS.

**`Type: AWS::S3::Bucket`**

Informa ao CloudFormation que desejo criar um bucket do Amazon S3.

**`Properties`**

Contém as propriedades utilizadas para configurar o recurso.

**`BucketEncryption`**

Configura a criptografia dos objetos armazenados no bucket. Neste exemplo, utilizo o algoritmo AES256.

**`PublicAccessBlockConfiguration`**

Configura bloqueios para impedir diferentes formas de acesso público ao bucket.

Como não defini explicitamente a propriedade `BucketName`, a AWS gera um nome para o bucket. Isso evita a necessidade de escolher manualmente um nome que seja exclusivo globalmente.

Esse exemplo demonstra como posso declarar um recurso e suas configurações de segurança em um arquivo de infraestrutura como código.

## 6. Como configurar e executar pelo console AWS

Para executar o template e criar um recurso real, preciso de uma conta AWS com as permissões necessárias.

O procedimento é o seguinte:

1. Acesso o [console do AWS CloudFormation](https://console.aws.amazon.com/cloudformation/).
2. Seleciono a região em que desejo criar os recursos, como São Paulo (`sa-east-1`).
3. Escolho a opção **Create stack**.
4. Seleciono a opção para utilizar um template existente.
5. Envio o arquivo `bucket.yaml`.
6. Informo um nome para a stack, como `meu-primeiro-bucket`.
7. Avanço pelas etapas de configuração e reviso os dados.
8. Confirmo a criação da stack.
9. Acompanho a execução pela aba **Events**.
10. Verifico os recursos criados na aba **Resources**.

Quando a criação é concluída com o status `CREATE_COMPLETE`, significa que o CloudFormation terminou a operação de criação da stack com sucesso.

Posso então acessar o Amazon S3 e verificar se o bucket foi criado.

## 7. Utilizando parâmetros no template

Uma das funcionalidades importantes do CloudFormation é a possibilidade de parametrizar os templates.

Isso significa que posso definir valores que serão informados durante a criação da stack, sem precisar alterar o código a cada execução.

Por exemplo, posso utilizar um parâmetro para identificar o ambiente:

```yaml
AWSTemplateFormatVersion: '2010-09-09'

Description: Bucket S3 com ambiente configuravel

Parameters:
  Environment:
    Type: String
    Default: dev
    AllowedValues:
      - dev
      - test
      - prod

Resources:
  MeuBucket:
    Type: AWS::S3::Bucket
    Properties:
      Tags:
        - Key: Environment
          Value: !Ref Environment
```

### Entendendo os parâmetros

- `Parameters`: define os valores que poderão ser informados na criação da stack.
- `Environment`: é o nome do parâmetro.
- `Type: String`: indica que o valor será textual.
- `Default: dev`: define o valor padrão.
- `AllowedValues`: limita os valores aceitos.
- `!Ref Environment`: utiliza o valor selecionado para preencher a tag do bucket.

Por exemplo, se eu selecionar `test` durante a criação da stack, o bucket receberá a tag `Environment=test`.

É importante observar que essa tag apenas identifica o ambiente. Ela não configura automaticamente permissões, políticas ou regras específicas de produção.

## 8. Executando o CloudFormation pela AWS CLI

Além do console, posso utilizar a AWS Command Line Interface (AWS CLI) para executar operações do CloudFormation pelo terminal.

Essa alternativa é útil para automações e pipelines de implantação.

### 8.1. Instalar a AWS CLI

Primeiro, instalo a AWS CLI seguindo a documentação oficial:

https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html

Depois, verifico a instalação:

```bash
aws --version
```

### 8.2. Configurar o acesso à AWS

Preciso configurar um método de autenticação autorizado e definir a região que utilizarei.

Uma opção tradicional é executar:

```bash
aws configure
```

Esse comando solicita as credenciais, a região padrão e o formato de saída.

Em ambientes corporativos, posso utilizar perfis, AWS IAM Identity Center ou outros métodos de autenticação recomendados pela organização.

Não devo armazenar chaves de acesso diretamente no código ou em arquivos enviados ao GitHub.

### 8.3. Criar uma stack

Depois de salvar o arquivo `bucket.yaml`, posso executar:

```bash
aws cloudformation deploy \
  --template-file bucket.yaml \
  --stack-name meu-primeiro-bucket \
  --region sa-east-1
```

O comando envia o template e cria a stack. Se a stack já existir, ele poderá executar uma atualização, conforme as diferenças encontradas.

### 8.4. Consultar a stack

Para consultar o estado da stack, utilizo:

```bash
aws cloudformation describe-stacks \
  --stack-name meu-primeiro-bucket \
  --region sa-east-1
```

Esse comando retorna informações como o nome da stack e seu status atual.

### 8.5. Excluir uma stack

Quando não precisar mais dos recursos, posso solicitar a exclusão:

```bash
aws cloudformation delete-stack \
  --stack-name meu-primeiro-bucket \
  --region sa-east-1
```

Antes de executar esse comando, preciso verificar quais recursos poderão ser excluídos e se existem dados importantes.

No caso de um bucket S3 que contenha objetos, a exclusão pode falhar se ele não estiver vazio. A exclusão da stack também não significa que todos os dados serão necessariamente removidos: o comportamento depende das configurações e políticas de retenção dos recursos.

## 9. Como atualizar a infraestrutura

Uma das vantagens do CloudFormation é que posso atualizar a infraestrutura a partir do template.

Por exemplo, se eu precisar adicionar uma configuração ao bucket, posso seguir este fluxo:

1. Altero o template YAML.
2. Valido o arquivo antes da implantação.
3. Envio o template atualizado.
4. Analiso as alterações propostas.
5. Executo a atualização após revisar o impacto.
6. Acompanho os eventos para verificar o resultado.

Para mudanças importantes, posso utilizar um **Change Set**. Ele apresenta as alterações previstas, como recursos que serão adicionados, modificados ou substituídos.

O Change Set ajuda a identificar impactos antes da execução, mas não garante que a atualização terminará com sucesso.

Também devo considerar que algumas alterações podem substituir recursos e que nem todos os tipos de recurso aceitam todas as modificações sem impacto.

## 10. Validação dos templates

Antes de executar um template, é importante verificar se ele está corretamente estruturado.

Uma ferramenta que posso utilizar é o `cfn-lint`, que identifica diversos problemas comuns nos templates CloudFormation.

Instalo a ferramenta com:

```bash
pip install cfn-lint
```

Depois, valido o arquivo:

```bash
cfn-lint bucket.yaml
```

Se a ferramenta não apresentar erros, significa que não identificou problemas que precisem ser reportados naquela execução.

Essa validação não garante que a implantação real será bem-sucedida. Ainda preciso considerar permissões, limites da conta, região, dependências e outras condições da AWS.

## 11. Cuidados importantes

Ao utilizar o CloudFormation, preciso considerar alguns pontos:

- **Segurança:** devo configurar permissões e políticas de acesso adequadas para cada recurso.
- **Custos:** recursos criados na AWS podem gerar cobranças.
- **Credenciais:** não devo publicar chaves de acesso no GitHub.
- **Região:** preciso verificar a região em que os recursos serão criados.
- **Atualizações:** algumas alterações podem substituir recursos existentes.
- **Exclusão:** excluir uma stack pode remover recursos e afetar dados.
- **Versionamento:** manter os templates no GitHub facilita acompanhar alterações e recuperar versões anteriores do código.

## 12. Conclusão

Com este estudo, compreendi como o AWS CloudFormation permite automatizar a criação e o gerenciamento da infraestrutura por meio de templates.

Aprendi os principais conceitos, a estrutura básica de um arquivo YAML, a criação de um bucket S3, a utilização de parâmetros e as formas de executar operações pelo console e pela AWS CLI.

Também entendi a importância de validar os templates, revisar alterações antes de executá-las e considerar os aspectos de segurança e custos.

O CloudFormation é uma ferramenta importante para aplicar Infraestrutura como Código, pois permite definir configurações de maneira padronizada, reutilizável e versionável, facilitando a manutenção e a automação dos ambientes na AWS.

## Referências

- [Documentação oficial do AWS CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html)
- [Anatomia de um template CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html)
- [Documentação da AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/)
- [AWS CloudFormation Linter — cfn-lint](https://github.com/aws-cloudformation/cfn-lint)
