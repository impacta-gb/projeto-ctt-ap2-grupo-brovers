# Fundamentos de Go

Go (também chamado de Golang) é uma linguagem de programação de código aberto criada pelo Google em 2009. Ela foi projetada para ser simples, eficiente e segura, ideal para sistemas modernos que precisam de alto desempenho e concorrência.

---

### O que é Go e por que usar?

Go combina a velocidade de linguagens compiladas (como C) com a legibilidade de linguagens de alto nível (como Python). Alguns motivos para escolher Go:

|Característica|Descrição|
|---|---|
|Compilação rápida|Gera binários nativos em segundos|
|Tipagem estática|Erros de tipo são detectados em tempo de compilação|
|Concorrência nativa|Goroutines e channels são parte da linguagem|
|Garbage collector|Gerenciamento de memória automático, sem ponteiros manuais|
|Biblioteca padrão|Rica e bem documentada, cobre HTTP, I/O, criptografia e muito mais|
|Simplicidade|Poucas palavras-chave, fácil de aprender e manter|

### Como instalar

#### Linux

```bash
# Baixe o pacote oficial (substitua pela versão mais recente em https://go.dev/dl/)
wget https://go.dev/dl/go1.22.0.linux-amd64.tar.gz

# Remova instalação anterior (se houver) e extraia
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf go1.22.0.linux-amd64.tar.gz

# Adicione ao PATH no seu ~/.bashrc ou ~/.zshrc
echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.bashrc
source ~/.bashrc

# Verifique a instalação
go version
```

#### macOS

```bash
# Com Homebrew
brew install go

# Verifique a instalação
go version
```

#### Windows

Baixe o instalador `.msi` em [https://go.dev/dl/](https://go.dev/dl/) e siga o assistente de instalação. O `PATH` é configurado automaticamente.

> [!NOTE] Após a instalação, execute `go version` no terminal para confirmar que tudo está correto. A saída esperada é algo como `go version go1.22.0 linux/amd64`.

### Primeiro programa: Hello, World!

Crie um arquivo chamado `main.go` com o seguinte conteúdo:

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

Para executar:

```bash
go run main.go
```

|Comando|O que faz|
|---|---|
|`go run`|Compila e executa o programa em uma única etapa|
|`go build`|Compila e gera um binário executável|
|`go fmt`|Formata o código seguindo o estilo oficial do Go|
|`go vet`|Analisa o código em busca de erros comuns|

---

