# Digital Innovation One

## Criando um sistema de orçamento, utilizando CQRS, Quarkus, Kafka e deploy no EKS

Neste projeto foi implantada uma aplicação escrita em Java/Kotlin no serviço Elastic Kubernetes Service da Amazon.
A aplicação é um exemplo do padrão CQRS que contempla dois serviços Quarkus que se comunicam através de um barramento assíncrono usando o Kafka.
Foram criados manifestos do Kubernetes para implantação no EKS com as configurações necessárias para ter o ambiente rodando em produção.

![Diagrama](docs/diagram.png)

---

### ✅ Requisitos

- [Java 11 JDK](https://www.oracle.com/br/java/technologies/javase-jdk11-downloads.html)
- [Maven](https://maven.apache.org/)
- [Docker](https://www.docker.com/)

---

### 📑 Instruções

Devemos criar um container para rodar a aplicação Quarkus de modo nativo na JVM:
   
> Obs: O **Docker** precisa estar em execução.
   
1. Navegue até a pasta do projeto `transaction-service` e execute a sequência de comandos abaixo:
   
    1.1. Digite o comando abaixo para construir o container:

    ```shell
    mvn package -Pnative -Dquarkus.native.container-build=true
    ```
   
   1.2. Digite o comando abaixo para fazer o *build* da imagem:
   
   ```shell
   docker build -f src/main/docker/Dockerfile.native -t moduscreate/quarkus-transaction-service:v1 .
   ```

   1.3. Digite o comando abaixo para executar o container:
   
   ```shell
   docker run -i --rm -p 8080:8080 moduscreate/quarkus-transaction-service:v1
   ```
   
   1.4. Fazer o *push* da imagem para o registro de containers da AWS:

   ```shell
   docker push moduscreate/quarkus-transaction-service:v1
   ```

   1.5. Digite o comando abaixo para fazer o deploy da aplicação no *cluster* ***kubernetes***:

   ```shell
   kubectl apply -f src/main/kubefiles/deployment.yml
   ```

2. Navegue até a pasta do projeto `balance-service` e execute a sequência de comandos abaixo:

   1.1. Digite o comando abaixo para construir o container:

    ```shell
    mvn package -Pnative -Dquarkus.native.container-build=true
    ```

   1.2. Digite o comando abaixo para fazer o *build* da imagem:

   ```shell
   docker build -f src/main/docker/Dockerfile.native -t moduscreate/quarkus-balance-service:v1 .
   ```

   1.3. Digite o comando abaixo para executar o container:

   ```shell
   docker run -i --rm -p 8080:8080 moduscreate/quarkus-balance-service:v1
   ```

   1.4. Fazer o *push* da imagem para o registro de containers da AWS:

   ```shell
   docker push moduscreate/quarkus-balance-service:v1
   ```

   1.5. Fazer o deploy da aplicação no *cluster* ***kubernetes***:

   ```shell
   kubectl apply -f src/main/kubefiles/deployment.yml
   ```

3. Configurar o *Ingress* para tornar os aplicativos do *cluster* acessíveis na Internet, executando o seguinte comando na pasta raiz do repositório:

```shell
kubectl apply -f kubefiles/ingress.yml
```

---

### 📚 Referências

- [About CQRS - Command Query Responsibility Segregation](https://github.com/wesleyfuchter/cqrs-quarkus-kafka)