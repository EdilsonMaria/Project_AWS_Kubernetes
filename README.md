# ☁️ Projeto Final do Programa de Bolsas da Compass UOL | AWS e DevSecOps - Infraestrutura AWS para um eCommerce da Fast Engineering

## 📋 Integrantes do Projeto
- Bruno Silveira: https://github.com/brunohsilv
- Edilson Maria: https://github.com/EdilsonMaria
- Felipe Santos: https://github.com/Felipe53650
- Laura Capssa: https://github.com/laura-capssa
- Juan paulo: https://github.com/juanfricke

---

## 🔎 **CASE:**
A Fast Engineering S/A é uma empresa de e-commerce que está crescendo rapidamente. Desde o início do ano, os acessos e compras estão aumentando em 20% a cada mês. A arquitetura on-premise atual, composta por servidores dedicados para aplicação, web server e banco de dados, não suporta mais a alta demanda de acessos e compras. Diante disso, a solução encontrada foi migrar o ambiente para a nuvem AWS, garantindo escalabilidade, segurança e alta disponibilidade para o e-commerce.

### **Arquitetura Atual**
Atualmente, o ambiente on-premise consiste em:
- **1 servidor para Banco de Dados MySQL**
- **1 servidor para aplicação (React)**
- **1 servidor de Web Server**, que também armazena arquivos estáticos como fotos e links.

<img src="/imgs/arq_atual.png">
Arquitetura atual da Fast Engineering

---

## 🆕 **Nova Arquitetura em Cloud (AWS)**

A solução de migração envolve a implementação de uma arquitetura na AWS, projetada para garantir alta disponibilidade, escalabilidade e segurança.

### **Descrição da Nova Arquitetura:**

1. **Usuário acessa o site do e-commerce da Fast Engineering** através do serviço **AWS Route 53**, que faz o roteamento do tráfego DNS.
   
2. **AWS WAF** é utilizado para garantir a segurança, protegendo contra ataques e acessos maliciosos.
   
3. O conteúdo estático (como fotos e links) será distribuído globalmente utilizando **Amazon CloudFront** para reduzir a latência e melhorar a experiência do usuário.
   
4. O tráfego chega a um **Load Balancer (Aplication Load Balancer - ALB)**, que distribui as solicitações para os containers e microsserviços.

5. **Aplicação em Containers gerenciados pelo EKS (Elastic Kubernetes Service)** utilizando **AWS Fargate**. 
    - **Pods rodando o front-end (React)**.
    - **Pods rodando um Web Server (Nginx)**
    - **Pods rodando a conexão com DB conection**
    - **Pods rodando a conexão com S3 e EFS conection**
    - **Pods rodando coleta dos logs**

6. O banco de dados será migrado para **Amazon RDS (MySQL)** com configuração de Multi-AZ, garantindo alta disponibilidade e backups automáticos.

7. **AWS CodePipeline** e **AWS CodeDeploy** serão usados para a entrega contínua (CI/CD) da aplicação, garantindo automatização e eficiência nas atualizações.

8. **AWS CloudWatch** será configurado para monitorar a saúde dos serviços e enviar alertas sobre qualquer anomalia.

9. O gerenciamento de permissões será feito com **AWS IAM** para garantir segurança e controle de acessos.

---

<img src="/imgs/arq_aws.png">
- Arquitetura da Fast Engineering na AWS

---

<img src="/imgs/arq_kubernetes.png">
- Arquitetura interna do kubernetes

---

## 🧰 **Serviços e Recursos Usados na Arquitetura**

1. **AWS Route 53**: Serviço de gerenciamento de DNS que permite o roteamento de usuários para diferentes recursos da AWS com base em regras definidas, como a proximidade geográfica ou saúde do serviço.
   
2. **AWS WAF (Web Application Firewall)**: de aplicação da web que protege contra ameaças comuns da internet, como injeção de SQL, cross-site scripting (XSS), e ataques DDoS.

3. **Amazon CloudFront**:  Serviço de distribuição de conteúdo (CDN) que acelera a entrega de conteúdo estático ou dinâmico em todo o mundo, reduzindo a latência para os usuários finais.

4. **Aplication Load Balancer (ALB)**: Balanceador de carga que distribui o tráfego entre diferentes instâncias ou containers, garantindo que o tráfego seja equilibrado e as falhas sejam redirecionadas para instâncias saudáveis.

5. **Amazon EKS (Elastic Kubernetes Service)**: Serviço gerenciado de Kubernetes que orquestra containers, permitindo escalabilidade, automação e implantação rápida de aplicações em microsserviços.

6. **AWS Fargate**: Serviço de execução de containers serverless que elimina a necessidade de provisionar e gerenciar servidores. Com Fargate, os containers são executados sob demanda.

7. **Amazon S3 (Simple Storage Service)**: Serviço de armazenamento escalável e seguro que permite armazenar arquivos estáticos, como imagens e vídeos, com alta durabilidade e acessibilidade.

8. **Amazon EFS (Elastic File System)**: Sistema de arquivos compartilhado que permite que várias instâncias ou containers acessem o mesmo sistema de arquivos simultaneamente, ideal para cenários onde há compartilhamento de dados.

9. **Amazon RDS (MySQL)**: Banco de dados relacional gerenciado que permite a configuração de alta disponibilidade (Multi-AZ) e backups automáticos para garantir redundância e recuperação rápida em caso de falhas.

10. **AWS CodePipeline e AWS CodeDeploy**: Serviços de integração contínua (CI) e entrega contínua (CD) que automatizam os processos de build, teste e deploy da aplicação.

11. **AWS CloudWatch**: Serviço de monitoramento que coleta e rastreia métricas, registros e eventos da AWS, enviando alertas e permitindo o acompanhamento da saúde e performance da infraestrutura.

12. **AWS IAM (Identity and Access Management)**: Serviço de gerenciamento de identidades que permite definir permissões granulares para acessar e gerenciar recursos da AWS de forma segura.

---

## 🔧 **Implantação**

### Pré-requisitos

1. **Conta AWS configurada**
2. **AWS CLI** instalado e configurado
3. **kubectl** configurado para acessar seu cluster EKS
4. **IAM roles** com permissões adequadas
5. **Docker** instalado para construir as imagens

### 1. Configuração do Repositório e CI/CD

#### 1.1. Criar Repositório no AWS CodeCommit
- Acesse o AWS Console, navegue até o **CodeCommit** e crie um novo repositório.
- Clone o repositório em sua máquina local:
  ```bash
  git clone https://git-codecommit.REGION.amazonaws.com/v1/repos/NOME_DO_REPOSITORIO
  ```

#### 1.2. Definir Pipeline no AWS CodePipeline
- Navegue para o **CodePipeline** e crie um novo pipeline.
- Escolha o repositório CodeCommit criado anteriormente como fonte.
- Configure as fases de **CodeBuild** para construir o frontend e o backend e **CodeDeploy** para implantar as imagens no cluster EKS.

#### 1.3. Configurar CodeBuild
- Crie um **buildspec.yml** para definir as etapas de construção de cada componente.

### 2. Configuração do EKS e Fargate

#### 2.1. Criar Cluster EKS
- Use o **eksctl** para criar o cluster EKS com suporte a Fargate.
  ```bash
  eksctl create cluster     --name ecommerce-cluster     --region us-east-1     --fargate
  ```

#### 2.2. Configurar Fargate Profiles
- Crie um profile Fargate para associar seus pods ao serviço Fargate.
  ```bash
  eksctl create fargateprofile     --cluster ecommerce-cluster     --name ecommerce-app     --namespace default
  ```

#### 2.3. Deploy dos Serviços no EKS
- Crie arquivos **YAML** para definir os serviços do Kubernetes (Deployment, Service, ConfigMap, etc.).
- Arquivo `deployment-backend.yaml` para o servidor web:
- Para aplicar o deployment:
  ```bash
  kubectl apply -f deployment-backend.yaml
  ```

### 3. Configuração do Banco de Dados RDS MySQL

#### 3.1. Criar Instância RDS
- Acesse o console RDS e crie uma nova instância MySQL.
  - Escolha a versão MySQL adequada.
  - Configure as VPCs, subnets e segurança para permitir a comunicação com o EKS.
- Configure o endpoint do banco de dados no arquivo de configuração da aplicação.

#### 3.2. Configurar Variáveis de Ambiente
- No seu arquivo de configuração **ConfigMap** ou diretamente no deployment:

### 4. Configuração do S3 e EFS

#### 4.1. Criar Bucket no S3
- Crie um bucket no **S3** para armazenar arquivos estáticos e imagens do e-commerce.
- No código da aplicação, use o SDK da AWS para interagir com o S3 e fazer upload/download dos arquivos.

#### 4.2. Montar EFS no EKS
- Crie um sistema de arquivos **EFS** no console da AWS.
- Configure um **PersistentVolume** e um **PersistentVolumeClaim** no Kubernetes:

### 5. Configuração de Logs e Monitoramento

#### 5.1. Configurar CloudWatch Logs
- Habilite o envio de logs dos containers no EKS para o **CloudWatch**.
- Adicione a seguinte configuração nos deployments:

### 6. Finalizando a Implantação

Após configurar todos os recursos acima, o pipeline de CI/CD garantirá que o código seja automaticamente construído, testado e implantado no cluster EKS. Cada alteração no repositório CodeCommit acionará o pipeline.

---

## 💰 **Orçamento**

### Estimativa de custo da nova arquitetura

- [Link da Calculadora AWS](https://calculator.aws/#/estimate?id=eddd7d07382facf5f41f637a4d8dfc3b31f629a5)

<img src="/imgs/princing_calculator.png">
Estimativa de custo da nova arquitetura

- Custo mensal: 2.016,31 USD

- Custo total de 12 meses: 24.195,72 USD

---

## 📬 **Cronograma**
<!-- Dividir o cronograma da migração por etapas -->
A ser anexado.

---

## 📑 **Referências**
- [AWS Route 53](https://aws.amazon.com/route53/)
- [AWS WAF](https://aws.amazon.com/waf/)
- [Amazon CloudFront](https://aws.amazon.com/cloudfront/)
- [Elastic Load Balancer](https://aws.amazon.com/elasticloadbalancing/)
- [Amazon EKS](https://aws.amazon.com/eks/)
- [AWS Fargate](https://aws.amazon.com/fargate/)
- [Amazon S3](https://aws.amazon.com/s3/)
- [Amazon RDS](https://aws.amazon.com/rds/)
- [AWS CodePipeline](https://aws.amazon.com/codepipeline/)
- [AWS CloudWatch](https://aws.amazon.com/cloudwatch/)
- [AWS IAM](https://aws.amazon.com/iam/)
