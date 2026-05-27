# Gerenciamento de Pacotes

O gerenciamento de pacotes em Go é feito pelo sistema oficial **Go Modules**, padrão desde a versão 1.16. Um *módulo* é um conjunto de pacotes versionados em conjunto, descrito por um arquivo `go.mod` na raiz do projeto. Com ele, o Go controla quais dependências o projeto usa e em quais versões.

---

## Inicializando um módulo

O comando `go mod init` cria o arquivo `go.mod` e define o **caminho do módulo** (module path), que é o identificador de importação do projeto:

```bash
go mod init github.com/usuario/api-go
```

Para projetos de estudo, um nome simples também funciona:

```bash
go mod init api-go
```

## O arquivo go.mod

O `go.mod` declara o caminho do módulo, a versão do Go e as dependências do projeto:

```go
module github.com/usuario/api-go

go 1.22

require github.com/gin-gonic/gin v1.10.0
```

| Diretiva | O que significa |
|---|---|
| `module` | Caminho de importação do módulo |
| `go` | Versão mínima do Go esperada pelo projeto |
| `require` | Dependências e suas versões |

!!! note
    Você raramente edita o `go.mod` à mão. Os comandos `go get` e `go mod tidy` mantêm o arquivo atualizado automaticamente.

## Adicionando dependências

Use `go get` para baixar uma dependência e registrá-la no `go.mod`:

```bash
go get github.com/gin-gonic/gin
```

Depois, basta importá-la no código:

```go
import "github.com/gin-gonic/gin"
```

## Atualizando dependências

Para atualizar para as versões mais recentes compatíveis:

```bash
# Atualiza uma dependência específica
go get -u github.com/gin-gonic/gin

# Atualiza todas as dependências do módulo
go get -u ./...
```

## Organizando com go mod tidy

`go mod tidy` sincroniza o `go.mod` com o que o código realmente usa: adiciona o que está faltando e remove o que não é mais importado.

```bash
go mod tidy
```

## O arquivo go.sum

O `go.sum` é gerado automaticamente e guarda os checksums (hashes) de cada dependência. Ele garante a **integridade** das versões baixadas — se um pacote for adulterado, o build falha. Os dois arquivos, `go.mod` e `go.sum`, devem ser versionados no Git.

## Comandos úteis

| Comando | O que faz |
|---|---|
| `go mod init <caminho>` | Cria o módulo e o arquivo `go.mod` |
| `go get <pacote>` | Adiciona ou atualiza uma dependência |
| `go mod tidy` | Adiciona dependências faltantes e remove as não usadas |
| `go mod download` | Baixa as dependências para o cache local |
| `go list -m all` | Lista o módulo e todas as suas dependências |

## Exemplo completo

Um servidor HTTP mínimo usando o framework `gin`, instalado via Go Modules:

```go
package main

import "github.com/gin-gonic/gin"

func main() {
    r := gin.Default()

    r.GET("/", func(c *gin.Context) {
        c.JSON(200, gin.H{
            "mensagem": "Olá, Go!",
        })
    })

    r.Run() // escuta em :8080 por padrão
}
```

Para rodar o projeto a partir do zero:

```bash
go mod init github.com/usuario/api-go
go get github.com/gin-gonic/gin
go run main.go
```
