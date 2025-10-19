# dio-aws-cloudformation-projeto
Este laboratório tem como objetivo de implementar sua primeira Stack com AWS CloudFormation. O entregável é um repositório organizado contendo anotações e insights adquiridos durante a prática, servindo como material de apoio para os seus estudos e futuras implementações.

## O que é AWS CloudFormation?

É um serviço que auxilia no processo de automação da criação de recursos na AWS por meio de templates JSON ou YAML. Podemos utilizar os templates quantas vezes quisermos e pagamos apenas pelas stacks criadas (conjunto de recursos, ex: EC2, RDS, S3, etc).

Além de ser um processo automatizado, conseguimos versionar esses templates. Com ele podemos criar recursos simples como um EC2 até uma arquitetura robusta com vários recursos.

## Exemplo Completo de Template CloudFormation

- Abaixo temos um exemplo mais avançado de um template CloudFormation em .json, com ele podemos criar uma instância EC2 com um Security Group.
- Esses recursos (EC2 e Security Group) são inseridos na seção de Resources

~~~json
{
  "AWSTemplateFormatVersion": "2010-09-09",
  "Description": "Template de exemplo para criar uma instância EC2 com um Security Group.",
  "Parameters": {
    "InstanceType": {
      "Description": "Tipo de instância EC2",
      "Type": "String",
      "Default": "t2.micro",
      "AllowedValues": [
        "t2.micro",
        "t2.small",
        "t2.medium"
      ],
      "ConstraintDescription": "Deve ser um tipo de instância EC2 válido."
    },
    "LatestAmiId": {
      "Description": "ID da AMI para a instância EC2",
      "Type": "AWS::EC2::Image::Id",
      "Default": "ami-0abcdef1234567890"
    },
    "SSHLocation": {
      "Description": "O intervalo de endereços IP que podem usar SSH para acessar a instância",
      "Type": "String",
      "MinLength": "9",
      "MaxLength": "18",
      "Default": "0.0.0.0/0",
      "AllowedPattern": "(\\d{1,3})\\.(\\d{1,3})\\.(\\d{1,3})\\.(\\d{1,3})/(\\d{1,2})",
      "ConstraintDescription": "Deve ser um bloco CIDR válido (ex: 10.0.0.0/16)."
    }
  },
  "Resources": {
    "MyEC2Instance": {
      "Type": "AWS::EC2::Instance",
      "Properties": {
        "ImageId": { "Ref": "LatestAmiId" },
        "InstanceType": { "Ref": "InstanceType" },
        "SecurityGroupIds": [
          { "Fn::GetAtt": ["MySecurityGroup", "GroupId"] }
        ],
        "Tags": [
          { "Key": "Name", "Value": "MyCloudFormationInstance" }
        ]
      }
    },
    "MySecurityGroup": {
      "Type": "AWS::EC2::SecurityGroup",
      "Properties": {
        "GroupDescription": "Habilita acesso SSH",
        "SecurityGroupIngress": [
          {
            "IpProtocol": "tcp",
            "FromPort": "22",
            "ToPort": "22",
            "CidrIp": { "Ref": "SSHLocation" }
          }
        ],
        "Tags": [
          { "Key": "Name", "Value": "MyCloudFormationSecurityGroup" }
        ]
      }
    }
  },
  "Outputs": {
    "InstanceId": {
      "Description": "ID da instância EC2 criada",
      "Value": { "Ref": "MyEC2Instance" }
    },
    "PublicIp": {
      "Description": "Endereço IP público da instância EC2",
      "Value": { "Fn::GetAtt": ["MyEC2Instance", "PublicIp"] }
    }
  }
}
~~~

## Como criar sua primeira Stack CloudFormation - Passo a Passo

### 1. Preparação do Ambiente
- Acesse o Console AWS
- Navegue até o serviço CloudFormation
- Certifique-se de estar na região desejada (ex: us-east-1)

### 2. Criando o Template
Crie um arquivo com extensão `.json` ou `.yaml`. Vamos usar JSON neste exemplo:

```json
{
  "AWSTemplateFormatVersion": "2010-09-09",
  "Description": "Minha primeira Stack CloudFormation"
}
```

### 3. Adicionando Recursos
Insira os recursos na seção `Resources`. Exemplo básico de um bucket S3:

```json
{
  "AWSTemplateFormatVersion": "2010-09-09",
  "Description": "Stack com bucket S3",
  "Resources": {
    "MeuBucketS3": {
      "Type": "AWS::S3::Bucket",
      "Properties": {
        "BucketName": "meu-primeiro-bucket-cf"
      }
    }
  }
}
```

### 4. Criando a Stack no Console
1. No CloudFormation, clique em "Create stack"
2. Escolha "Upload a template file"
3. Faça upload do seu arquivo JSON
4. Clique em "Next"
5. Digite um nome para sua stack (ex: "minha-primeira-stack")
6. Configure parâmetros se necessário
7. Clique em "Next" até chegar em "Create stack"

### 5. Monitorando a Criação
- Acompanhe o status na aba "Events"
- Aguarde o status mudar para "CREATE_COMPLETE"
- Verifique os recursos criados na aba "Resources"

### 6. Limpeza (Opcional)
Para deletar todos os recursos:
1. Selecione sua stack
2. Clique em "Delete"
3. Confirme a exclusão