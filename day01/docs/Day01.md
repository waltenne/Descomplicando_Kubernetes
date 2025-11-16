# O que é Container?

Um container é uma unidade de software leve que empacota um aplicativo junto com todas as suas dependências, isolando-o do sistema operacional e de outros processos.

Ele não isola apenas “recursos como memória e CPU”, mas isola o ambiente de execução (bibliotecas, binários, filesystem e processos) usando recursos do próprio kernel, como:

- Namespaces → isolamento de processos, rede, PID, filesystem, usuários
- cgroups → controle de recursos (CPU, memória, IO)

*“Imagine que o computador físico é uma casa. Cada quarto é um espaço isolado onde uma pessoa pode viver com seus próprios móveis e regras, mas todos os quartos compartilham a mesma estrutura da casa. Assim funcionam os containers: ambientes isolados que compartilham o mesmo kernel."

# O que é Container Engine?


O Container Engine é o componente que gerencia e organiza os containers, oferecendo comandos, APIs e funcionalidades práticas para o usuário.

Ele não cria containers diretamente  ele orquestra.  
Quando você executa `docker run`, é o engine (ex: Docker Engine ou Podman Engine) que recebe o comando e solicita ao Container Runtime que execute o container.

Funções principais:

- Gerenciar imagens    
- Criar e remover container=s (via runtime)    
- Criar redes e volumes    
- Gerenciar logs e eventos    
- Executar health checks    
- Fornecer CLI e API REST    
- Controlar o ciclo de vida dos containers

*"Se o container é um quarto e o runtime é o pedreiro que o constrói, o Container Engine é o “arquiteto/gerente” que organiza tudo: ele decide onde o quarto será feito, define as regras, registra quem está usando qual quarto e chama o pedreiro para construir quando necessário."*

# O que é Container Runtime?


O Container Runtime é o componente responsável por realmente _executar_ o container, interagindo diretamente com o kernel do sistema operacional.

Ele usa mecanismos do kernel  namespaces, cgroups, montagem de filesystem, isolamento de rede  para criar o ambiente isolado onde o processo vai rodar.  
Ou seja: ele é quem transforma a definição do container (imagem + configs) em um processo real isolado.

Existem dois níveis:

- Low-level runtimes (ex: runc, crun) → falam diretamente com o kernel para criar o container    
- High-level runtimes (ex: containerd, cri-o) → gerenciam múltiplos containers e chamam o runtime low-level    

Funções principais:

- Criar o processo isolado do container    
- Configurar namespaces e cgroups    
- Montar o filesystem    
- Lidar com syscalls do kernel    
- Iniciar, parar e remover containers em nível de sistema operacional
    
*"Se o computador é uma casa e o container é um quarto, o Container Runtime é o “pedreiro” que realmente constrói o quarto  levantando as paredes, instalando a porta, conectando energia e deixando tudo isolado.  
Ele é quem faz o trabalho físico usando a estrutura da casa (kernel)."*

# O que é OCI (Open Container Iniciative)?

A OCI  Open Container Initiative  é uma organização que define padrões abertos para containers.  
Ela não executa containers, não cria imagens e não roda nada.

A função da OCI é garantir que todos os containers e runtimes sigam o mesmo conjunto de regras, permitindo interoperabilidade entre ferramentas diferentes (Docker, Podman, containerd, CRI-O, Kubernetes, etc.).

A OCI define dois padrões principais:

1. OCI Image Specification  
    → Define como uma imagem de container deve ser estruturada (camadas, manifestos, configuração).  
    Isso garante que uma imagem criada no Docker pode ser usada no Podman, containerd ou Kubernetes sem problemas.
    
2. OCI Runtime Specification  
    → Define como um container deve ser executado:
    
    - namespaces        
    - cgroups        
    - montagem de filesystem        
    - formato do `config.json`  
        Isso garante que runtimes como runc e crun executem containers de forma compatível.
        

A OCI traz padronização, evitando que cada fornecedor invente seu próprio formato de imagem.

## Por que a OCI existe?

Antes da OCI, cada ferramenta fazia containers “do seu jeito”.  
Isso criava incompatibilidade entre imagens e runtimes.

Criar um container no Docker e rodar no Kubernetes, por exemplo, era um desafio.

Hoje:

- Docker cria imagens compatíveis com OCI    
- Podman cria imagens compatíveis com OCI    
- containerd, CRI-O, Kubernetes e Cloud Providers usam runtimes compatíveis com OCI

_Se o computador é uma casa e containers são quartos:_

- A OCI é a empresa que escreveu as normas de construção.    
- Ela diz:
    
    - como as portas devem ser,        
    - como as paredes devem ser montadas,        
    - quais medidas mínimas são necessárias,        
    - como os quartos devem ser organizados.

_O objetivo é garantir que qualquer pedreiro (runtime) possa construir um quarto e que qualquer gerente/arquiteto (engine) consiga trabalhar com esse padrão sem precisar reinventar tudo._

> A OCI é responsável por definir os padrões oficiais que garantem que containers e imagens funcionem da mesma forma em qualquer ferramenta.  
> Ela padroniza formatos e comportamentos, permitindo compatibilidade entre Docker, Podman, containerd, CRI-O e Kubernetes.

# O que é Kubernets?


Kubernetes é uma plataforma de orquestração de containers responsável por gerenciar centenas ou milhares de containers de forma automática, eficiente e resiliente.

Ele resolve problemas que aparecem quando você executa containers em larga escala, como:

- Onde cada container deve rodar?    
- O que fazer se um container ou máquina falhar?    
- Como distribuir carga entre containers?    
- Como atualizar containers sem derrubar o sistema?    
- Como garantir que sempre existam “X” réplicas rodando?    

Kubernetes automatiza tudo isso.

## O que exatamente o Kubernetes faz?

1. Orquestra containers

Decide onde containers vão rodar dentro de um cluster.

2. Mantém aplicações sempre funcionando

Se um container morre, ele cria outro automaticamente.

3. Escala aplicações

- para cima (mais réplicas)    
- para baixo (menos réplicas)    

de acordo com demanda, métricas ou regras.

 4. Faz atualizações sem downtime

Implementa:

- rolling updates    
- rollbacks    
- versionamento de deployments

5. Gerencia rede e comunicação

- Service Discovery    
- Load Balancing    
- IP fixo para serviços    

6. Isola e organiza aplicações

Usa objetos como:

- Pods    
- Deployments    
- Services    
- Namespaces- 

Kubernetes NÃO é…

- não executa containers    
- não constrói imagens    
- não substitui o Container Engine    
- não substitui o Container Runtime    

Ele apenas orquestra os componentes abaixo dele.---

_Se o computador é uma casa, containers são quartos, o runtime é o pedreiro e o engine é o arquiteto/gerente… então:_

Kubernetes é o síndico de um condomínio gigantesco.

- Ele decide onde cada quarto (container) será construído    
- Garante que sempre haja o número certo de quartos funcionando    
- Se um quarto pega fogo, ele manda construir outro imediatamente    
- Distribui moradores entre quartos conforme a demanda    
- Organiza redes, regras, acessos, manutenção e atualizações    
- Garante que o condomínio continue funcionando mesmo se um bloco inteiro cair    


> Kubernetes é uma plataforma que automatiza o deployment, escalonamento, atualização e recuperação de containers em um cluster de servidores.  
> Ele garante que aplicações rodem de forma estável, distribuída e resiliente, mesmo em larga escala.

# O que sao Workers e o Control Plane do Kubernets?

Dentro de um cluster Kubernetes, existem dois grandes grupos de máquinas (nós):

- Control Plane → o “cérebro” do cluster    
- Worker Nodes → os “trabalhadores” onde os containers realmente rodam    

Eles têm papéis totalmente diferentes, mas funcionam juntos para manter o cluster estável.

## O que é o Control Plane?

O Control Plane é o conjunto de componentes responsáveis por controlar, gerenciar e decidir tudo no Kubernetes.  
Ele é literalmente o cérebro e a camada de orquestração.

É formado por vários serviços críticos:

### Componentes do Control Plane

- etcd → banco de dados distribuído que armazena o estado desejado do cluster   
- kube-apiserver → a “porta de entrada” do cluster; recebe todos os comandos
- kube-scheduler → decide em qual Worker cada Pod deve rodar    
- kube-controller-manager → cria, replica e mantém tudo funcionando    
- cloud-controller-manager → integra com provedores de nuvem (quando existe)    

### Funções principais

- Decide onde Pods serão criados    
- Monitora o cluster    
- Reage a falhas e recria recursos    
- Mantém o “estado desejado”    
- Atua como cérebro, juiz e gerente do cluster    

> O Control Plane não roda aplicações do usuário ele só gerencia.

## O que são os Worker Nodes?

Workers são as máquinas onde os containers realmente rodam.  
Eles fazem o trabalho pesado.

### Componentes principais do Worker

- kubelet → agente que recebe ordens do Control Plane    
- kube-proxy → controla a rede e load balancing interno    
- Container Runtime (ex: containerd, CRI-O) → executa os containers    

### Funções principais

- Rodar Pods (containers)    
- Criar e parar containers sob ordem do controlar plane    
- Gerenciar recursos locais (CPU, memória, storage)    
- Enviar status para o Control Plane    
- Implementar networking interno    

> Workers são as máquinas onde as aplicações realmente vivem.

- O cluster Kubernetes é um condomínio inteiro    
- Cada container é um quarto dentro de um apartamento    
- Os Workers são os blocos de apartamentos onde os moradores vivem  
    → São eles que possuem os quartos (containers) de verdade    
- O Control Plane é a administradora/síndico do condomínio, que:    
    - decide onde cada morador vai ficar        
    - fiscaliza tudo        
    - distribui tarefas        
    - lida com problemas e falhas        
    - garante que o condomínio está funcionando       
    
- Control Plane = síndico/administrador do condomínio (orquestra, decide, controla)*
- Workers = blocos de apartamentos com os quartos onde as pessoas realmente moram (executam containers)

> O Control Plane decide, gerencia e orquestra.  
> Os Workers executam os containers.  
> O Control Plane é o cérebro.  
> Os Workers são os músculos.

# Quais os componentes do Workers do Kubernets?

O Worker Node é a máquina dentro do cluster Kubernetes onde os Pods e containers realmente rodam.  
Para que isso aconteça, ele possui três componentes essenciais:

## 🔧 1. Kubelet (agente do Kubernetes no nó)

É o principal agente do Worker Node.  
Ele se comunica com o Control Plane e garante que tudo o que foi solicitado realmente está sendo executado no nó.

### Funções do kubelet:

- Recebe ordens do Control Plane (API Server)    
- Cria, inicia e monitora Pods    
- Envia status do nó e dos Pods para o Control Plane    
- Garante que o “estado desejado” esteja sempre sendo seguido    
- Interage com o Container Runtime para criar containers    
- Realiza health checks   

Sem o kubelet, o Worker não participa do cluster.

## 🔌 2. Kube-proxy (gerencia rede dentro do nó)

O kube-proxy cuida do tráfego de rede dentro e fora do nó.

### Funções do kube-proxy:

- Implementa regras de roteamento de rede    
- Garante comunicação entre Pods    
- Implementa o load balancing interno dos Services    
- Configura regras de iptables / eBPF    
- Gerencia portas e acesso aos serviços dentro do cluster

## 🎬 3. Container Runtime (executa os containers)

É o componente que roda os containers de verdade.

Exemplos mais comuns:

- containerd (o mais usado hoje)    
- CRI-O (usado muito em clusters Kubernetes puros)    
- Docker Engine (cada vez menos usado — desde 1.20 não é nativo)    
- runc / crun (low-level runtimes usados por containerd/CRI-O)    

### Funções do Container Runtime:

- Criar containers    
- Isolar processos (namespaces, cgroups)    
- Configurar filesystem    
- Baixar e armazenar imagens    
- Gerenciar ciclo de vida dos containers    

Sem um runtime, nenhum container seria executado.

Dependendo da instalação, o Worker pode ter:

- CNI Plugins (Container Network Interface)  
    → Usados para redes de Pods (Calico, Flannel, Cilium etc.)    
- CSI Plugins (Container Storage Interface)  
    → Gerenciam volumes e storage (EBS, Ceph, Longhorn, NFS etc.)    

Esses não são “componentes nativos”, mas fazem parte do funcionamento do cluster.

Em um condomínio (cluster):

- Worker Node = bloco de apartamentos    
- Kubelet = zelador do bloco    
    - garante que os quartos (Pods) estejam corretos        
    - reporta status ao síndico (Control Plane)        
- Kube-proxy = porteiro do bloco*    
    - controla o tráfego de entrada/saída        
    - direciona visitantes e moradores (rede e regras de acesso)        
- Container Runtime = construtor    
    - cria de verdade os quartos (containers) dentro dos apartamentos (Pods)        


Componentes essenciais do Worker Node:

1. kubelet → agente que recebe ordens e gerencia Pods    
2. kube-proxy → gerencia rede e load balancing    
3. container runtime → executa containers    

Com isso, o Worker Node pode executar aplicações dentro do cluster.

# Quais as portas TCP e UDP dos componentes do Kubernets?

O Kubernetes usa várias portas para permitir comunicação entre o Control Plane, os Workers, e os componentes internos como kubelet, kube-apiserver, etcd, kube-proxy, runtime, etc.

Abaixo estão todas as portas oficiais, divididas por grupo.
## 🧠 1. Portas do Control Plane

### 📌 kube-apiserver (API Server) — TCP

Portas obrigatórias:

|Porta|Protocolo|Função|
|---|---|---|
|6443|TCP|Porta principal da API Kubernetes|
|8080|TCP|API HTTP sem TLS (desativada na maioria das instalações modernas)|

### 📌 etcd — banco de dados do Kubernetes — TCP

| Porta    | Protocolo | Função                                       |
| -------- | --------- | -------------------------------------------- |
| 2379 | TCP       | Comunicação dos clientes (API Server → etcd) |
| 2380 | TCP       | Comunicação entre membros do cluster etcd    |
|          |           |                                              |

### 📌 kube-scheduler — TCP

|Porta|Protocolo|Função|
|---|---|---|
|10259|TCP|Porta segura do kube-scheduler (TLS)|

### 📌 kube-controller-manager — TCP

|Porta|Protocolo|Função|
|---|---|---|
|10257|TCP|Porta segura do controller-manager (TLS)|

## ⚙️ 2. Portas dos Worker Nodes

### 📌 kubelet — TCP

| Porta     | Protocolo | Função                                                          |
| --------- | --------- | --------------------------------------------------------------- |
| 10250 | TCP       | Porta principal do kubelet (API interna)                        |
| 10255     | TCP       | Porta de leitura (desativada em versões recentes por segurança) |

### 📌 kube-proxy — TCP/UDP

O kube-proxy usa portas dinâmicas, dependendo do modo (iptables, ipvs ou eBPF).  
Porém, ele normalmente:

- Não expõe portas fixas,    
- Mas usa portas do range definido pelos Services do cluster.    
### Range padrão dos Services:

|Range|Protocolo|Função|
|---|---|---|
|30000–32767|TCP/UDP|NodePorts usados para acessar Services externamente|


## 🧩 3. Portas usadas pelo Cluster como um todo

### 📌 NodePort Range — TCP/UDP

Portas abertas em todos os Workers quando um Service usa NodePort:

| Range           | Protocolo | Função                      |
| --------------- | --------- | --------------------------- |
| 30000–32767 | TCP/UDP   | Acesso externo aos Services |

### 📌 CNI Plugins (rede)

Dependem do plugin (Calico, Flannel, Cilium etc.). Exemplos:

### Calico

|Porta|Protocolo|Função|
|---|---|---|
|179|TCP|BGP peering|
|4789|UDP|VXLAN|
|5473|TCP|Typha|

### Flannel

|Porta|Protocolo|
|---|---|
|8472|UDP (VXLAN)|

### Cilium

|Porta|Protocolo|
|---|---|
|4240|TCP|
|8472|UDP|

## 🧠 4. Comunicação entre componentes (importante em firewalls)

### Control Plane → Worker

|Porta|Protocolo|Função|
|---|---|---|
|10250|TCP|kubelet API|
|30000–32767|TCP/UDP|NodePorts|

### Worker → Control Plane

|Porta|Protocolo|Função|
|---|---|---|
|6443|TCP|API Server|

### Worker ↔ Worker

Depende do plugin de rede (CNI), geralmente UDP (VXLAN) ou TCP (BGP/eBPF).


- API Server (6443) é a portaria principal do condomínio.    
- etcd (2379-2380) é o arquivo central onde tudo é registrado.    
- kubelet (10250) é o telefone do zelador do bloco, onde ele recebe ordens.    
- NodePorts (30000–32767) são as garagens numeradas que permitem acesso externo ao bloco.    
- CNI (como Calico/Flannel) são as ruas internas, com suas próprias regras de tráfego.

 🔹 Control Plane

- 6443/TCP — API Server    
- 2379–2380/TCP — etcd    
- 10257/TCP — Controller Manager    
- 10259/TCP — Scheduler    

🔹 Workers

- 10250/TCP — kubelet    
- 30000–32767/TCP/UDP — NodePorts    
- (CNI pode usar 4789/UDP, 8472/UDP, 179/TCP, etc.)

# Introdução Pods, Replica Set, Deployments e Service

Um Pod é a menor unidade que o Kubernetes gerencia.  
Ele é um invólucro que contém um ou mais containers que devem rodar juntos e compartilhar:

- rede    
- IP    
- portas    
- filesystem temporário   

> Na prática, a maioria dos Pods contém apenas 1 container, mas podem ter mais (ex: sidecar containers).

Funções do Pod:

- Executar containers    
- Compartilhar recursos entre os containers internos    
- Ser a “unidade básica” de scheduling

## Deployment gerencia versões, atualizações e ReplicaSets

O Deployment é o nível mais alto de controle para aplicações “stateless”.  
Ele usa ReplicaSets internamente para gerenciar Pods.

### O que um Deployment oferece:

- Deploy de uma aplicação    
- Atualizações sem downtime (rolling updates)    
- Rollbacks automáticos se algo der errado    
- Histórico de versões    
- Controle declarativo do estado (YAML)    

Quando você altera a versão da imagem, o Deployment:

1. Cria um novo ReplicaSet    
2. Diminui o antigo ReplicaSet    
3. Faz troca gradual    
4. Mantém o sistema disponível   

> É o jeito padrão e recomendado de rodar aplicações no Kubernetes.

## ReplicaSet garante o número de Pods em execução

Um ReplicaSet é o componente responsável por garantir que sempre exista a quantidade desejada de Pods rodando.

Exemplo:

- Você diz que quer 3 réplicas    
- Se 1 Pod morrer, o ReplicaSet cria outro    
- Se houver 4 Pods, ele apaga 1   

> Porém: ReplicaSets raramente são usados diretamente, porque o Deployment faz esse trabalho para você.

## Service — fornece networking e acesso estável aos Pods

Pods nascem e morrem o tempo todo.  
Cada vez que um Pod é recriado, ele ganha um novo IP.

O Service resolve esse problema, oferecendo:

- Um IP fixo e estável    
- Load balancing entre Pods    
- Descoberta de serviço    
- Regras de rede (ClusterIP, NodePort, LoadBalancer)    

### Tipos mais usados:

- ClusterIP → acesso interno (padrão)    
- NodePort → expõe porta em todos os Workers    
- LoadBalancer → cria load balancer do provedor cloud    
- Headless Service → para acesso direto aos Pods (StatefulSets)    

> Sem um Service, sua aplicação não seria acessível de forma confiável.

- Pod → é um apartamento (contém um ou mais moradores = containers).    
- ReplicaSet → garante que sempre existam x apartamentos ocupados.    
- Deployment → é o administrador do bloco, que controla reformas, atualizações e histórico.    
- Service → é a portaria/telefone com ramal fixo que sempre sabe onde cada morador está, mesmo que se mudem de apartamento (Pods morram e sejam recriados).

|Componente|Função|
|---|---|
|Pod|Executa containers; menor unidade do Kubernetes|
|ReplicaSet|Mantém o número desejado de Pods|
|Deployment|Gerencia deploys, updates e ReplicaSets|
|Service|Expõe e dá acesso estável aos Pods|

# Entendimento e Instalado o kubectl


O kubectl é a ferramenta de linha de comando usada para interagir com o Kubernetes.  
É através dele que você cria, consulta, atualiza e exclui recursos dentro de um cluster.

Com o kubectl você pode, por exemplo:

- Criar Pods, Deployments e Services    
- Ver logs dos containers    
- Aplicar arquivos YAML    
- Executar comandos dentro de containers    
- Ver o estado do cluster    
- Escalar aplicações    

Ou seja: kubectl é o “controle remoto” do Kubernetes.


Pense no Kubernetes como um grande condomínio:

- O Control Plane é a administração do condomínio.    
- Os Workers são os apartamentos onde o trabalho realmente acontece.    
- O kubectl é o interfone:  
    É por meio dele que você se comunica com a administração (API Server), envia ordens e recebe informações.

## ✔ Como instalar o kubectl

A instalação varia de acordo com o sistema operacional. Aqui estão as formas mais comuns (modo resumido e moderno):

 🔵 Linux (qualquer distro)

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

Verificar versão:

```bash
kubectl version --client
```

---

🟣 Ubuntu/Debian (via apt)

```bash
sudo apt-get update
sudo apt-get install -y ca-certificates curl
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg \
  https://packages.kubernetes.io/core:/stable:/v1.29/deb/Release.key
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] \
  https://packages.kubernetes.io/core:/stable:/v1.29/deb/ /" \
  | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl
```

---

 🟦 Windows (via Chocolatey)

```powershell
choco install kubernetes-cli
```

---

 🟩 Windows (via Scoop)

```powershell
scoop install kubectl
```

---

 🟧 MacOS (via Homebrew)

```bash
brew install kubectl
```


## 📌 Como testar se está funcionando

Depois de conectado a um cluster (ex.: kind, minikube, k3d, EKS, GKE etc.):

```bash
kubectl get nodes
```

# Criando o nosso primeiro Cluster com o Kind

O Kind (Kubernetes IN Docker) é uma ferramenta que permite criar clusters Kubernetes locais usando containers Docker como nodes.  
Ele é leve, rápido e ideal para:

- estudos    
- testes locais    
- CI/CD    
- desenvolvimento   

Em vez de criar VMs ou usar cloud, o Kind faz tudo dentro de containers Docker.

Se o computador é uma casa:

- O Docker é como construir “quartos rápidos” usando paredes de drywall.    
- O Kind usa esses quartos para montar um mini-condomínio Kubernetes completo dentro da sua casa.   

Ou seja:  
→ Cada node do cluster Kind é um container Docker.

## ✔ 1. Pré-requisitos

- Docker instalado e funcionando    
- Linux, macOS ou Windows (via WSL2 ou Docker Desktop)    

Testar Docker:

```bash
docker ps
```
## ✔ 2. Instalando o Kind

### Linux / macOS

```bash
curl -Lo ./kind https://kind.sigs.k8s.io/dl/latest/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
```

### Windows (PowerShell)

```powershell
choco install kind
```

Verificar:

```bash
kind version
```
## ✔ 3. Criando o Primeiro Cluster

O comando padrão é:

```bash
kind create cluster
```

Isso cria:

- 1 node de controle    
- kubeconfig para o kubectl    
- comunicação com API Server    
- tudo isolado dentro do Docker    

Testar:

```bash
kubectl get nodes
```

Você deve ver algo como:

```
NAME                 STATUS   ROLES           VERSION
kind-control-plane   Ready    control-plane   v1.29.x
```

## ✔ 4. Criando Cluster com Configuração Personalizada

Você pode criar clusters com múltiplos nodes:

Crie o arquivo `cluster.yaml`:

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
  - role: worker
  - role: worker
```

Criar:

```bash
kind create cluster --config cluster.yaml --name giropops
```

Agora:

```bash
kubectl get nodes
```

```
NAME                       ROLES           STATUS
giropops-control-plane     control-plane    Ready
giropops-worker            worker           Ready
giropops-worker2           worker           Ready
```
## ✔ 5. Deletando o Cluster

```bash
kind delete cluster
```

## ✔ 6. Onde fica o kubeconfig?

O Kind escreve automaticamente no arquivo padrão:

```
~/.kube/config
```

Ou seja: kubectl já fica pronto para uso.

O Kind é a forma mais rápida e simples de criar um ambiente Kubernetes local.  
Ele usa containers como nodes e permite criar clusters completos para estudo e desenvolvimento em apenas alguns segundos.

Aqui está o texto ajustado, com explicação clara sobre namespaces, comandos úteis, boas práticas e mantendo o mesmo estilo dos seus outros tópicos:


# Primeiros passos no Kubernetes com o kubectl

Agora que você já tem um cluster (ex.: Kind, Minikube, k3d ou cloud) e o kubectl instalado, é hora de dar os primeiros comandos essenciais para entender como conversar com o Kubernetes.

O kubectl é o seu controle remoto — tudo o que você quer que o cluster faça, você pede através dele.


## 🧭 1. Verificando o estado do cluster

### ✔ Listar os nodes

```bash
kubectl get nodes
```

### ✔ Ver informações detalhadas

```bash
kubectl describe nodes
```

## 🗂️ 2. Entendendo Namespaces

Um Namespace é uma forma de organizar recursos dentro do cluster.  
Eles servem para separar ambientes, isolar times, ou simplesmente organizar melhor os objetos.

Namespaces mais comuns:

- `default` → onde seus recursos vão por padrão    
- `kube-system` → onde ficam componentes internos    
- `kube-public` → acesso público    
- `kube-node-lease` → health-check dos nodes    
- `dev`, `hml`, `prod` → comuns em clusters de empresas    

### ✔ Listar namespaces:

```bash
kubectl get ns
```

### ✔ Criar um namespace:

```bash
kubectl create ns meu-ambiente
```

### ✔ Usar comandos em um namespace específico:

```bash
kubectl get pods -n meu-ambiente
```

### ✔ Definir permanentemente o namespace em um contexto:

```bash
kubectl config set-context --current --namespace=meu-ambiente
```

Dica: se você não especificar um namespace, o kubectl usa o `default`.


## 📦 3. Criando o seu primeiro Pod

Vamos criar um Pod simples rodando Nginx:

```bash
kubectl run meu-pod --image=nginx
```

Verificar:

```bash
kubectl get pods
```

Ver detalhes:

```bash
kubectl describe pod meu-pod
```

Ver logs:

```bash
kubectl logs meu-pod
```

Entrar dentro do container:

```bash
kubectl exec -it meu-pod -- bash
```

## 🔄 4. Deletando um Pod

```bash
kubectl delete pod meu-pod
```

## 📁 5. Criando recursos via YAML

Crie um arquivo `pod.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: meu-pod
spec:
  containers:
    - name: nginx
      image: nginx
```

Aplicar:

```bash
kubectl apply -f pod.yaml
```

Ver mudanças:

```bash
kubectl get pods
```

Atualizar e reaplicar:

```bash
kubectl apply -f pod.yaml
```

## 🌐 6. Expondo o Pod com um Service

```bash
kubectl expose pod meu-pod --port=80 --type=NodePort
```

Ver service:

```bash
kubectl get svc
```

## 🧹 7. Limpando recursos

```bash
kubectl delete -f pod.yaml
kubectl delete svc meu-pod
```


## 🧠 Dicas importantes iniciantes

### ✔ Documentação interna do Kubernetes (SUPER útil)

```bash
kubectl explain pod
kubectl explain deployment.spec.replicas
```

### ✔ Ver tudo o que está rodando no cluster:

```bash
kubectl get all --all-namespaces
```

### ✔ Ver recursos separados por namespace:

```bash
kubectl get pods -n kube-system
```

### ✔ Auto-complete do kubectl (altamente recomendado)

```bash
source <(kubectl completion bash)
```

- O cluster é o condomínio    
- Cada namespace é como um _andar_ ou _bloco_ dentro do condomínio    
- Os Workers são os apartamentos    
- O kubelet é o zelador    
- O kubectl é o interfone que você usa para dar ordens e consultar o estado de tudo


Com esses comandos você já sabe:

- usar namespaces    
- listar recursos    
- criar Pods    
- visualizar logs    
- acessar containers    
- aplicar arquivos YAML    
- criar Services    
- gerenciar o cluster com kubectl    

# Conhecendo o YAML e o kubectl com dry-run

O Kubernetes utiliza arquivos YAML para definir tudo o que existe dentro do cluster: Pods, Deployments, Services, ConfigMaps, Secrets e muito mais.  
O YAML funciona como um “manual de instruções” que diz ao Kubernetes exatamente como você quer que um recurso seja criado.

Já o comando dry-run permite testar a criação de recursos sem realmente criá-los, ajudando você a validar YAMLs e gerar arquivos de configuração automaticamente.

## 📘 1. O que é YAML no Kubernetes?

O YAML é um formato baseado em indentação que descreve objetos.  
Ele segue sempre a mesma estrutura:

```
apiVersion
kind
metadata
spec
```

### ✔ Estrutura básica

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: meu-pod
spec:
  containers:
    - name: nginx
      image: nginx
```

### ✔ Explicação simples:

- apiVersion → qual versão da API o recurso usa    
- kind → tipo do objeto (Pod, Deployment, Service…)    
- metadata → nome, labels, anotações    
- spec → a “receita” de como o objeto funciona


No nosso condomínio Kubernetes:

- O YAML é o projeto arquitetônico que você entrega para a administração.    
- O Kubernetes lê esse projeto e constrói o que você pediu.    
- Mudar o YAML = pedir uma reforma.

## 📄 2. Criando YAMLs automaticamente com kubectl (dry-run)

O kubectl pode gerar automaticamente arquivos YAML para você usando o dry-run, evitando que você tenha que escrever tudo do zero.

### ✔ Exemplo: gerar YAML de um Pod sem criar nada

```bash
kubectl run meu-pod --image=nginx --dry-run=client -o yaml
```

Saída: um YAML completo do Pod.

### ✔ Exportar para um arquivo

```bash
kubectl run meu-pod --image=nginx --dry-run=client -o yaml > pod.yaml
```

Agora você pode editar o arquivo e depois aplicar:

```bash
kubectl apply -f pod.yaml
```
## 🧪 3. O que é kubectl dry-run?

O dry-run permite testar ações sem realmente executá-las.

Existem dois tipos:

### ✔ `--dry-run=client`

O kubectl valida o YAML localmente, sem falar com o cluster.  
Bom para gerar arquivos.

### ✔ `--dry-run=server`

O kubectl envia a configuração para o cluster, mas não cria o recurso.  
O servidor valida tudo (versões, campos, API, policies).

Exemplo:

```bash
kubectl apply -f pod.yaml --dry-run=server
```

Se houver erro no YAML, ele avisa antes de você aplicar de verdade.


## 🔧 4. Gerando YAMLs para outros recursos

### ✔ Deployment

```bash
kubectl create deployment meu-app \
  --image=nginx \
  --replicas=3 \
  --dry-run=client -o yaml
```

### ✔ Service

```bash
kubectl expose deployment meu-app \
  --port=80 \
  --type=NodePort \
  --dry-run=client -o yaml
```

### ✔ Namespace

```bash
kubectl create ns dev --dry-run=client -o yaml
```

## 🔍 5. Validando e Inspecionando YAML

### ✔ Mostrar documentação de qualquer campo:

```bash
kubectl explain pod.spec
```

Campo específico:

```bash
kubectl explain deployment.spec.strategy
```

### ✔ Validar YAML sem criar:

```bash
kubectl apply -f pod.yaml --dry-run=server
```

Com YAML e o `dry-run`, você aprende:

- Como o Kubernetes representa e cria recursos    
- Como gerar YAML automaticamente    
- Como validar configurações antes de aplicá-las    
- Como evitar erros antes de impactar o cluster    
- Como usar o kubectl de maneira prática e profissional    