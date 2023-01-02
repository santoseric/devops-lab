# Cluster Kubernetes com Rancher

## Passo a passo

1. Provisionar 4 servidores (2-4 vCPU, 4-6 GB memory, 30 GB disk SSD General purpose, OS Ubuntu)
    - 1 Rancher server (host A)
    - 3 para cluster k8s (host B, C, D)

2. Registrar domínio e configurar subdomínios (neste projeto, irei trabalhar com o domínio devops-lab.click)
    - rancher.devops-lab.click
    - app1.devops-lab.click
    - app2.devops-lab.click

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
5. Criar ingress para expor a aplicação para a internet, podendo assim, acessar o dashboard do Traefik (proxy reverso)
    ```
    $ kubectl apply -f ui.yml
    ```
    Cluster >> System >> [Load Balancer](screenshots/dashboard-traefik.png) 

    [Traefik](screenshots/dashboard-traefik-2.png)

6. Volumes
    - Vamos fazer a instalação do LongHorn acessando o nome do cluster >> Default >> Apps >> [Launch](screenshots/longhorn-install.png) procure por longhorn na caixa de pesquisa e faça a instalação mantendo as opções [default](longhorn-default-parameters.png) após o deployment do LongHorn, você será capaz de acessar o [dashboard](screenshots/longhorn-dashboard.png)
    - Vamos fazer o deploy de um banco de dados MySQL com volume persistente e sua [replicação](screenshots/longhorn-deployed-volume-with-replicas.png)
    Veja o pod do MySQL através no Rancher em Workloads
    - Agora pode fazer um teste de persistência deployando um MySQL, verificando em qual servidor o pod está rodando, conectar no container mysql, criar um arquivo qualquer na pasta /var/lib/mysql, matar o pod simulnando um erro e, no próximo pod que será criado **automaticamente**, vocẽ poderá conectar e verificar.  
        ```
        $ cd /vartouch 

        ```

7. Graylog, Prometheus, Grafana
    - Vamos fazer o deploy e configuração, não esqueça de alterar o domínio devops-lab.click para o seu domínio no arquivo graylog.yml
    ```
    $ vim graylog.yml
    ```
    - No vim, utilize o comando a seguir para substituir o domínio atual pelo seu domínio. Dentro do vim, pressione ":" para chamar o comando: % s/devops-lab.click/seudominio/gc pressione enter >> confirme
    - Agora que editou o arquivo, vamos aplicar e fazer o deploy com o comando abaixo.
    ```
    $ kubectl apply -f graylog.yml
    ```
    [Print 1](screenshots/graylog-k8s.png) [Print 2](screenshots/graylog-k8s-2.png) [Print 3](screenshots/graylog-k8s-3.png)
    
    - O Prometheus já vem instalado, basta habilitar na opção Tools >> Monitoring >> Enable
    Painel de monitoramento no Rancher Server
    [Print 1](screenshots/monitoring-panel-on-rancher.png) [Print 2](screenshots/monitoring-panel-on-rancher-2.png)
    
    - Grafana
    [Print 1](screenshots/grafana-cluster.png) [Print 2](screenshots/grafana-pods-cpu.png) [Print 3](screenshots/grafana-pods-cpu-2.png)

9. Liveness probes
    Verifica de tempos em tempos se um pod está vivo ou morto e se o serviço está respondendo. Caso seja detectado algum problema, ele mata e recria o **pod.**
    Neste exemplo, vamos definir o tempo de checagem para 3 segundos. Verifique no arquivo *liveness.yml*
    