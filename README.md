# Cluster Kubernetes com Rancher

## Passo a passo

1. Provisionar 4 servidores (2-4 vCPU, 4-6 GB memory, 30 GB disk SSD General purpose, OS Ubuntu)
    - 1 Rancher server
    - 3 para cluster k8s

2. Registrar domínio e configurar subdomínios
    - rancher.domain
    - app1.domain
    - app2.domain

3. Instalar o Docker em todas as máquinas
    - Docker versão 18.09
    ```
     curl https://releases.rancher.com/install-docker/18.09.sh | sh    
    ```
    - Conectar no Rancher Server e instalar Docker Compose
    ```
    $ sudo apt-get install git -y
    $ sudo curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    $ sudo chmod +x /usr/local/bin/docker-compose
    $ sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
    ```
    - clonar repositório   
    ```
    $ cd ~
    $ git clone https://github.com/santoseric/devopsninja.git
    ```
    
    Acessar a pasta exercicios/app

    #### Buildar imagens
    - Redis
    ```
    $ cd redis
    $ docker build -t xcirel/redis:devops .
    $ docker run -d --name redis -p 6379:6379 xcirel/redis:devops
    $ docker container ls
    $ docker logs redis
    ```
    - Node
    ```
    $ cd ../node
    $ docker build -t xcirel/node:devops .
    $ docker run -d --name node -p 8080:8080 --link redis xcirel/node:devops
    $ docker container ls
    $ docker logs node
    ```
    - Nginx
    ```
    $ cd ../nginx
    $ docker build -t xcirel/nginx:devops .
    $ docker run -d --name nginx -p 80:80 --link node xcirel/nginx:devops
    $ docker container ls
    ```

4. Instalar o Rancher Single Node no Rancher Server
     
    ```
    $ docker run -d --restart=unless-stopped -v /opt/rancher:/var/lib/rancher -p 80:80 -p 443:443 --privileged rancher/rancher:latest
    ```
    - Nessa altura, o Rancher já pode ser acessado através da URL http://rancher.domain
    - Criar cluster K8s através do Rancher, aguardar pois o provisionamento pode demorar um pouco.
    - Instalar o kubectl no host A (Rancher Server)
    ```
    $ sudo apt-get update && sudo apt-get install -y apt-transport-https gnupg2
    $ curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
    $ echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
    $ sudo apt-get update
    $ sudo apt-get install -y kubectl
    $ kubectl version --output=yaml
    ```
    - Criar diretório, lançar configurações do cluster no Rancher Server para poder disparar comando para o cluster através do bash
        - Acessar o cluster e obter informações [Kubeconfig File](screenshots/kubeconfig-file-button.png), criar arquivo config em ~/.kube/config com o [conteúdo yaml](screenshots/kubeconfig-file-yaml.png) do cluster
    ```
    $ mkdir ~/.kube
    $ vi ~/.kube/config
    $ kubectl get nodes    
    ```
    -  [Get nodes](screenshots/kubectl-get-nodes.png),
    
