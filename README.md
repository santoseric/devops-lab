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
    sudo apt-get install git -y
    sudo curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    sudo chmod +x /usr/local/bin/docker-compose
    sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
    ```
    - clonar repositório   
    ```
    cd
    git clone https://github.com/santoseric/devopsninja.git
    ```
    
    Acessar a pasta exercicios/app/redis

    ### Buildar imagens
    - Redis
    - Node
    - Nginx


4. Instalar o Rancher Single Node no Rancher Server
     
    ```
    docker run -d --restart=unless-stopped -v /opt/rancher:/var/lib/rancher -p 80:80 -p 443:443 --privileged rancher/rancher:latest
    ```
    - Nessa altura, o Rancher já pode ser acessado através da URL http://rancher.domain
    - Criar cluster K8s através do Rancher, aguardar pois o provisionamento pode demorar um pouco. Aproveite enquanto aguarda para experiementar o [DataDog](https://www.datadoghq.com) para monitorar o seu cluster ;)
    - Instalar o kubectl no host A (Rancher Server)
