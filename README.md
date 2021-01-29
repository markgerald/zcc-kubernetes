## ZCC - Kubernetes para Desenvolvedores - Linx

Projeto PHP(8) utilizando framework [Symfony](https://symfony.com/ "Symfony")(5). Banco de dados MySQL(8), Redis(6). Api REST gerada utilizando projeto [API-PLATFORM](https://api-platform.com/ "API-PLATFORM")

Projeto também roda com Docker compose:

`- docker-compose up -d --build`

### Necessário Instalar em ambiente local
- [Docker](https://docs.docker.com/engine/install/ "Docker")
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/ "kubectl")
- [minikube](https://minikube.sigs.k8s.io/docs/start/ "minikube")
- [Azure CLI](https://docs.microsoft.com/pt-br/cli/azure/install-azure-cli "Azure CLI")
- AKS Cli: `az aks install-cli`

### Subindo ambiente Local

Iniciar o minikube: `minikube start`
Buildar imagem Dockerfile na raiz do projeto: `docker build . -t linx/php80`
Habilitar minikube para utilizar imagens docker geradas localmente:
` eval $(minikube docker-env)`
Executar: `kubectl create -f kubernetes/`
