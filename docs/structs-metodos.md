# Structs e Métodos em Go

## Introdução

Em Go, `structs` são estruturas utilizadas para agrupar diferentes tipos de dados em um único objeto.
Já os métodos permitem associar comportamentos a essas estruturas, deixando o código mais organizado e reutilizável.

---

# Structs em Go

## O que é uma Struct?

Uma `struct` é um tipo composto que agrupa variáveis sob um único nome.

## Exemplo básico

```go
package main

import "fmt"

type Pessoa struct {
    Nome  string
    Idade int
}

func main() {
    p := Pessoa{
        Nome:  "João",
        Idade: 22,
    }

    fmt.Println(p.Nome)
    fmt.Println(p.Idade)
}
```

---

## Acessando campos da Struct

Os campos podem ser acessados usando ponto (`.`).

```go
fmt.Println(p.Nome)
```

Também é possível alterar valores:

```go
p.Idade = 23
```

---

## Struct Anônima

Go permite criar structs sem definir um tipo separado.

```go
usuario := struct {
    Nome string
    XP   int
}{
    Nome: "Stark",
    XP:   9000,
}
```

---

# Métodos em Go

## O que é um Método?

Um método é uma função associada a uma struct.

## Sintaxe

```go
func (variavel Tipo) NomeMetodo() {
    // código
}
```

---

## Exemplo de Método

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

---

# Receiver em Métodos

O valor entre parênteses antes do nome do método é chamado de `receiver`.

```go
func (p Pessoa) Metodo() {
}
```

O receiver indica qual tipo possui aquele método.

---

# Métodos com Ponteiros

Quando queremos alterar os dados da struct original, usamos ponteiros.

## Exemplo

```go
package main

import "fmt"

type Conta struct {
    Saldo float64
}

func (c *Conta) Depositar(valor float64) {
    c.Saldo += valor
}

func main() {
    conta := Conta{Saldo: 100}

    conta.Depositar(50)

    fmt.Println(conta.Saldo)
}
```

### Saída

```txt
150
```

---

# Diferença entre Receiver Normal e Ponteiro

| Tipo | Comportamento |
|---|---|
| Receiver normal | Trabalha com cópia |
| Receiver ponteiro | Altera o objeto original |

---

# Boas Práticas

## Use Structs quando:

- Precisar agrupar informações relacionadas
- Representar objetos do mundo real
- Organizar dados complexos

## Use Métodos quando:

- O comportamento pertence ao objeto
- Desejar código mais limpo
- Precisar reutilizar funcionalidades

---

# Exemplo Completo

```go
package main

import "fmt"

type Personagem struct {
    Nome   string
    Vida   int
    Ataque int
}

func (p Personagem) ExibirStatus() {
    fmt.Println("Nome:", p.Nome)
    fmt.Println("Vida:", p.Vida)
    fmt.Println("Ataque:", p.Ataque)
}

func (p *Personagem) ReceberDano(dano int) {
    p.Vida -= dano
}

func main() {
    player := Personagem{
        Nome:   "Jinx",
        Vida:   100,
        Ataque: 25,
    }

    player.ExibirStatus()

    player.ReceberDano(30)

    fmt.Println("\nApós receber dano:")
    player.ExibirStatus()
}
```

---

# Conclusão

Structs e métodos são fundamentais em Go para organizar dados e comportamentos de forma eficiente.
Eles ajudam na criação de aplicações mais limpas, reutilizáveis e fáceis de manter.
