# Pod `waltenne` - Multi-Container com Volumes EmptyDir

O arquivo `pod.yml` define um **Pod multi-container** chamado `waltenne` que roda dois containers simultaneamente, cada um com seu próprio volume `EmptyDir` e limites de recursos configurados.

## Estrutura do Pod

### Containers

1. **`ubuntu`**
   - Imagem: `ubuntu`
   - Comando: dorme por 9999 segundos (pra manter vivo)
   - Monta o volume: `primeiro-volume` em `/waltenne`
   - Recursos:
     - Requests: 0.3 CPU, 64Mi RAM
     - Limits: 1.5 CPU, 128Mi RAM

2. **`webserver`**
   - Imagem: `nginx`
   - Monta o volume: `segundo-volume` em `/waltenne2`
   - Recursos:
     - Requests: 0.5 CPU, 64Mi RAM
     - Limits: 1 CPU, 128Mi RAM

## Volumes EmptyDir

- **`primeiro-volume`**: 50Mi de limite, montado no container `ubuntu`
- **`segundo-volume`**: 100Mi de limite, montado no container `webserver`

> **Lembrando**: `EmptyDir` é volátil. Os dados serão perdidos quando o Pod é deletado.

## Como usar

### 1. Criar o Pod
```bash
kubectl apply -f pod.yml
```

### 2. Verificar status
```bash
kubectl get pods
kubectl describe pod waltenne
```

### 3. Acessar os containers

**No container ubuntu:**
```bash
kubectl exec -it waltenne -c ubuntu -- bash
# Dentro do container:
cd /waltenne
ls -la
```

**No container nginx:**
```bash
kubectl exec -it waltenne -c webserver -- sh
# Dentro do container:
cd /waltenne2
ls -la
```

### 4. Testar os volumes

Dentro de cada container, você pode criar arquivos nos diretórios montados (`/waltenne` e `/waltenne2`) para verificar o funcionamento dos volumes.

### 5. Verificar consumo de recursos
```bash
kubectl top pod waltenne
```

### 6. Deletar o Pod
```bash
kubectl delete -f pod.yml
# ou
kubectl delete pod waltenne
```

## Configurações importantes

- **`dnsPolicy: ClusterFirst`**: Usa o DNS do cluster Kubernetes
- **`restartPolicy: Always`**: Se o container cair, Kubernetes tenta reiniciar
- **Labels**: `run: waltenne` - útil para selecionar esse Pod em queries

## Casos de uso deste Pod

- Testar comunicação entre containers no mesmo Pod
- Experimentar com volumes `EmptyDir`
- Praticar configuração de limites de recursos (CPU/memória)
- Simular um ambiente com múltiplos serviços compartilhando um Pod

## Observações

1. O container `ubuntu` não faz nada além de dormir - é só pra manter o container vivo
2. O container `nginx` serve conteúdo web na porta 80 por padrão
3. Os volumes são independentes - dados não são compartilhados entre containers
4. Ajuste os limites de CPU/memória conforme a capacidade do seu cluster

## Exemplo de teste rápido

```bash
# Criar arquivo no volume do ubuntu
kubectl exec waltenne -c ubuntu -- touch /waltenne/teste.txt

# Listar arquivos no volume do ubuntu
kubectl exec waltenne -c ubuntu -- ls -la /waltenne/

# Verificar se nginx está rodando
kubectl exec waltenne -c webserver -- curl -I localhost:80
```
