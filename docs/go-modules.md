# Estudos de Go (Golang)

==================================================
ARRAYS, SLICES E MAPS
==================================================

# Arrays

Arrays são estruturas de tamanho fixo que armazenam elementos do mesmo tipo.

Exemplo:

```go
package main

import "fmt"

func main() {

    numeros := [5]int{1, 2, 3, 4, 5}

    fmt.Println(numeros)
}
```

Características:

- Tamanho fixo
- Mesmo tipo de dado
- Índices começam em 0

Acessando posições:

```go
fmt.Println(numeros[0])
```

Alterando valores:

```go
numeros[1] = 10
```

--------------------------------------------------

# Slices

Slices são estruturas dinâmicas baseadas em arrays.

Exemplo:

```go
package main

import "fmt"

func main() {

    nomes := []string{"Ana", "Carlos", "Maria"}

    fmt.Println(nomes)
}
```

Adicionando elementos:

```go
nomes = append(nomes, "João")
```

Tamanho do slice:

```go
fmt.Println(len(nomes))
```

Capacidade do slice:

```go
fmt.Println(cap(nomes))
```

Criando slices com make:

```go
numeros := make([]int, 5)
```

--------------------------------------------------

# Maps

Maps armazenam dados em formato chave-valor.

Exemplo:

```go
package main

import "fmt"

func main() {

    aluno := map[string]string{
        "nome": "Carlos",
        "curso": "ADS",
    }

    fmt.Println(aluno)
}
```

Acessando valores:

```go
fmt.Println(aluno["nome"])
```

Adicionando elementos:

```go
aluno["idade"] = "22"
```

Removendo elementos:

```go
delete(aluno, "idade")
```

==================================================
STRUCTS E MÉTODOS
==================================================

# Structs

Structs são estruturas usadas para agrupar diferentes tipos de dados.

Exemplo:

```go
package main

import "fmt"

type Pessoa struct {
    Nome  string
    Idade int
}

func main() {

    p := Pessoa{
        Nome:  "Carlos",
        Idade: 22,
    }

    fmt.Println(p)
}
```

Acessando atributos:

```go
fmt.Println(p.Nome)
```

Alterando atributos:

```go
p.Idade = 23
```

--------------------------------------------------

# Métodos

Métodos são funções associadas a structs.

Exemplo:

```go
package main

import "fmt"

type Pessoa struct {
    Nome string
}

func (p Pessoa) Apresentar() {
    fmt.Println("Olá, meu nome é", p.Nome)
}

func main() {

    pessoa := Pessoa{Nome: "Carlos"}

    pessoa.Apresentar()
}
```

Método com retorno:

```go
func (p Pessoa) Saudacao() string {
    return "Olá " + p.Nome
}
```

Método com ponteiro:

```go
func (p *Pessoa) FazerAniversario() {
    p.Idade++
}
```

==================================================
GERENCIAMENTO DE PACOTES
==================================================

# Introdução

O gerenciamento de pacotes em Go é responsável por organizar, instalar e controlar dependências utilizadas em projetos.

A linguagem Go possui um sistema chamado Go Modules.

--------------------------------------------------

# Inicializando um módulo

```bash
go mod init nome-do-projeto
```

Exemplo:

```bash
go mod init api-go
```

--------------------------------------------------

# Arquivo go.mod

Exemplo:

```go
module api-go

go 1.25
```

--------------------------------------------------

# Instalando dependências

```bash
go get github.com/gin-gonic/gin
```

Importando no código:

```go
import "github.com/gin-gonic/gin"
```

--------------------------------------------------

# Atualizando dependências

```bash
go get -u
```

--------------------------------------------------

# Limpando dependências

```bash
go mod tidy
```

--------------------------------------------------

# Arquivo go.sum

Responsável pela integridade das dependências.

--------------------------------------------------

# Baixando dependências

```bash
go mod download
```

--------------------------------------------------

# Listando módulos

```bash
go list -m all
```

--------------------------------------------------

# Exemplo completo

```go
package main

import "github.com/gin-gonic/gin"

func main() {

    r := gin.Default()

    r.GET("/", func(c *gin.Context) {
        c.JSON(200, gin.H{
            "mensagem": "Olá Go",
        })
    })

    r.Run()
}
```

==================================================
CONCLUSÃO
==================================================

Go possui uma sintaxe simples e eficiente para trabalhar com estruturas de dados, organização de código e gerenciamento de dependências.

Arrays, slices, maps, structs, métodos e Go Modules são conceitos fundamentais para qualquer desenvolvedor Go.

