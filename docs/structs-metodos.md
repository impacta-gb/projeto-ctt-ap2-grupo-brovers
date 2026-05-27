# Structs e Métodos

Em Go, `structs` agrupam diferentes tipos de dados em um único valor, e os **métodos** associam comportamentos a esses tipos. Juntos, eles são a base para organizar dados e lógica de forma limpa e reutilizável — sem precisar de classes ou herança.

---

## Structs

Uma `struct` é um tipo composto que agrupa campos (variáveis) sob um único nome.

### Exemplo básico

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

### Acessando e alterando campos

Os campos são acessados com ponto (`.`) e podem ser lidos ou alterados:

```go
fmt.Println(p.Nome) // leitura
p.Idade = 23        // alteração
```

### Struct anônima

Go permite criar uma struct sem definir um tipo nomeado separado, útil para valores pontuais:

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

## Métodos

Um método é uma função associada a uma struct. O valor entre parênteses antes do nome do método é o **receiver**, que indica a qual tipo o método pertence.

### Sintaxe

```go
func (variavel Tipo) NomeMetodo() {
    // código
}
```

### Exemplo de método

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

## Métodos com ponteiros

Por padrão, um receiver recebe uma **cópia** da struct, então alterações não afetam o valor original. Quando o método precisa modificar a struct, use um receiver do tipo ponteiro (`*Tipo`):

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

```text
150
```

| Tipo de receiver | Comportamento |
|---|---|
| Receiver por valor (`Tipo`) | Trabalha com uma cópia; não altera o original |
| Receiver por ponteiro (`*Tipo`) | Altera o objeto original |

!!! note
    Por convenção, se **algum** método de um tipo usa receiver por ponteiro, normalmente todos usam, para manter a consistência.

## Quando usar

- Use **structs** para agrupar informações relacionadas, representar entidades do mundo real e organizar dados complexos.
- Use **métodos** quando o comportamento pertence ao próprio dado, para deixar o código mais limpo e reutilizável.

---

## Exemplo completo

O exemplo a seguir combina struct, método por valor (apenas lê) e método por ponteiro (altera o estado):

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
