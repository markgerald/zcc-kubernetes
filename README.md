## ZCC - Kubernetes para Desenvolvedores - Linx

Projeto PHP(8) utilizando framework [Symfony](https://symfony.com/ "Symfony")(5). Banco de dados MySQL(8), Redis(6). Api REST gerada utilizando projeto [API-PLATFORM](https://api-platform.com/ "API-PLATFORM")

Projeto também roda com Docker compose:

`- docker-compose up -d --build`

### Necessário Instalar em ambiente local
- [Docker](https://docs.docker.com/engine/install/ "Docker")
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/ "kubectl")
- [minikube](https://minikube.sigs.k8s.io/docs/start/ "minikube")
- [Azure CLI](https://docs.microsoft.com/pt-br/cli/azure/install-azure-cli "Azure CLI")

### Subindo ambiente Local

Iniciar o minikube: `minikube start`

Buildar imagem Dockerfile na raiz do projeto: `docker build . -t linx/php80`

Habilitar minikube para utilizar imagens docker geradas localmente:
` eval $(minikube docker-env)`

Executar: `kubectl create -f kubernetes/`

Verificar se está tudo ok: `minikube dashboard`

Subir endpoint para acesso: `minikube service nginx-webserver-service --url`

**Criar database e esquema via container/pop do PHP:**
- pegar nome do pod php através do comando: `kubectl get pods`
- Acessar: `kubectl exec -it NOME-DO-POD bash`
- Criar database: `php bin/console doctrine:database:create`
- Criar Schema: `php bin/console doctrine:schema:create`

## Azure
- Instalar AKS Cli: `az aks install-cli`
- Criar um cluster: https://docs.microsoft.com/pt-br/azure/aks/kubernetes-walkthrough-portal
- Criar um Container Repository: https://docs.microsoft.com/pt-br/azure/container-registry/container-registry-get-started-portal

Certifiquese de ter a *Azure Cli* instalada e configurada, para instalar assim como a AKS CLi

#### Enviando imagem para ACR
Logar no respositorio(pode alterar o nome *zcclinx*, caso crie o repositório com outro nome):
`az acr login --name zcclinx`

Criar tag para sua imagem gerada localmente: `docker tag linx/php80 SEUREGISTROAQUI.azurecr.iolinx/php80`

Enviar imagem para ACR: `docker push SEUREGISTRO.azurecr.io/linx/php80`

#### Editando deployment PHP para utilizar imagem docker a partir do ACR

- Editar arquivo kubernetes/php-deployment, alterando linha 16 para: `image: SEUREGISTROAQUI.azurecr.io/linx/php80`
- Comentar linha 17

#### Integrar ACR com AKS
> (alterar myAKSCluster para o nome de seu cluster e myResourceGroup para seu grupo de recursos, assim como <acr-name> para o nome de seu repositório)

`az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-name>`

#### Linkar Kubectl ao cluster criado na AKS
> (alterar myAKSCluster para o nome de seu cluster e myResourceGroup para seu grupo de recursos, assim como <acr-name> para o nome de seu repositório)
  
`az aks get-credentials --resource-group myResourceGroup --name myAKSCluster`

#### Subir projeto para cluster criado na Azure e configurá-lo
`kubectl create -f kubernetes/`
(Acompanhar status no painel da Azure)

**Criar database e esquema via container/pop do PHP:**
- pegar nome do pod php através do comando: `kubectl get pods`
- Acessar: `kubectl exec -it NOME-DO-POD bash`
- Criar database: `php bin/console doctrine:database:create`
- Criar Schema: `php bin/console doctrine:schema:create`

> Para verificar projeto rodando, acessar aba "serviços no painel Kubernetes da Azure, o IP para acesso a aplicação. O Swagger da api, roda na rota /api.

