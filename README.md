# MinIO Setup

## Configuração

1. **Copie o arquivo de exemplo**:
   ```bash
   cp .env.example .env
   ```

2. **Edite o arquivo `.env`** conforme necessário:
   ```env
   # Credenciais (obrigatórias)
   MINIO_ROOT_USER=minioadmin
   MINIO_ROOT_PASSWORD=minioadmin123
   
   # Configuração opcional
   MINIO_BROWSER_REDIRECT_URL=http://localhost:9001
   MINIO_PORT=9000
   MINIO_CONSOLE_PORT=9001
   MINIO_BUCKET=minio-bucket
   UID=1000
   GID=1000
   ```

## Para executar

```bash
docker-compose up -d
```

**📁 Criação automática da pasta `data`:** O docker-compose criará automaticamente a pasta `./data` com as permissões corretas (755, owner 1000:1000) usando um container de inicialização.

## Acessos

- **API MinIO**: http://localhost:9000 (ou porta definida em MINIO_PORT)
- **Console Web**: http://localhost:9001 (ou porta definida em MINIO_CONSOLE_PORT)

## Credenciais

- **Usuário**: Definido em `MINIO_ROOT_USER` (padrão: minioadmin)
- **Senha**: Definida em `MINIO_ROOT_PASSWORD` (padrão: minioadmin123)

## Estrutura de dados

Os dados serão salvos na pasta `./data` do diretório atual.

## 🔧 Solução de Problemas

### Erro: "file access denied" ou container reiniciando

Se o MinIO não conseguir iniciar devido a problemas de permissão, você pode corrigir manualmente:

**Parar o serviço:**
```bash
docker-compose down
```

**Criar pasta data com permissões corretas:**
```bash
mkdir -p data
sudo chown -R 1000:1000 data
chmod -R 755 data
```

**Reiniciar o serviço:**
```bash
docker-compose up -d
```

### Verificar se está funcionando

```bash
# Status dos containers
docker ps | grep minio

# Logs do MinIO
docker logs minio

# Testar API
curl http://localhost:9000/minio/health/live
```

## Variáveis de Ambiente Disponíveis

| Variável | Descrição | Valor Padrão |
|----------|-----------|--------------|
| `MINIO_ROOT_USER` | Usuário administrador | `minioadmin` |
| `MINIO_ROOT_PASSWORD` | Senha do administrador | `minioadmin123` |
| `MINIO_BROWSER_REDIRECT_URL` | URL de redirecionamento | `http://localhost:9001` |
| `MINIO_PORT` | Porta da API | `9000` |
| `MINIO_CONSOLE_PORT` | Porta do console web | `9001` |
| `MINIO_BUCKET` | Bucket padrão (opcional) | `minio-bucket` |
| `UID` | ID do usuário Linux/Mac | `1000` |
| `GID` | ID do grupo Linux/Mac | `1000` |

## Segurança

⚠️ **Para produção**, altere as credenciais padrão por valores mais seguros!

## Como funciona

1. **Container `minio-init`**: Executa primeiro e cria a pasta `./data` com permissões corretas
2. **Container `minio`**: Inicia após o init container e usa a pasta já configurada
3. **Dependência**: O MinIO só inicia depois que a pasta está pronta 