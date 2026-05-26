# Testes Automatizados em Go

Testar código é parte fundamental do desenvolvimento em Go. A linguagem já vem com um framework de testes embutido — sem precisar instalar nada. Basta importar o pacote `testing` e usar o comando `go test`.

---

## O Pacote `testing`

O pacote `testing` fornece os tipos e funções necessários para escrever testes. Toda função de teste deve:

1. Estar em um arquivo com sufixo `_test.go`
2. Ter o nome começando com `Test`
3. Receber um parâmetro do tipo `*testing.T`

```go
// arquivo: matematica_test.go
package matematica

import "testing"

func TestSoma(t *testing.T) {
    resultado := Soma(2, 3)
    esperado := 5

    if resultado != esperado {
        t.Errorf("Soma(2, 3) = %d; esperado %d", resultado, esperado)
    }
}
```

```go
// arquivo: matematica.go
package matematica

func Soma(a, b int) int {
    return a + b
}
```

> [!NOTE]
> Arquivos `_test.go` são ignorados na compilação normal do projeto (`go build`). Eles só são incluídos quando você roda `go test`.

---

## Rodando os Testes

| Comando                        | O que faz                                              |
|-------------------------------|--------------------------------------------------------|
| `go test ./...`               | Roda todos os testes do projeto recursivamente         |
| `go test .`                   | Roda testes do pacote atual                            |
| `go test -v ./...`            | Modo verboso: mostra nome de cada teste                |
| `go test -run TestSoma`       | Roda apenas o teste `TestSoma`                         |
| `go test -cover ./...`        | Mostra porcentagem de cobertura de código              |
| `go test -coverprofile=c.out` | Gera arquivo de cobertura para análise detalhada       |

### Exemplo de saída com `-v`

```
--- PASS: TestSoma (0.00s)
--- FAIL: TestSubtrai (0.00s)
    matematica_test.go:18: Subtrai(5, 3) = 1; esperado 2
FAIL
```

---

## Métodos de `*testing.T`

| Método          | Comportamento                                            |
|-----------------|----------------------------------------------------------|
| `t.Error()`     | Registra falha, mas continua o teste                    |
| `t.Errorf()`    | Igual ao anterior, com formatação                       |
| `t.Fatal()`     | Registra falha e **para** o teste imediatamente         |
| `t.Fatalf()`    | Igual ao anterior, com formatação                       |
| `t.Log()`       | Loga mensagem (visível só com `-v` ou em falhas)        |
| `t.Skip()`      | Pula o teste com uma mensagem                           |

```go
func TestDivisao(t *testing.T) {
    _, err := Dividir(10, 0)
    if err == nil {
        t.Fatal("Esperava erro ao dividir por zero, mas não veio nenhum")
    }
    // t.Fatal para aqui — não executa o que vier abaixo se falhar
    t.Log("Erro retornado corretamente:", err)
}
```

> [!WARNING]
> Prefira `t.Errorf` quando quiser continuar verificando outras condições no mesmo teste. Use `t.Fatalf` apenas quando uma falha impossibilita continuar (ex: retorno `nil` que seria desreferenciado em seguida).

---

## Table-Driven Tests (Testes em Tabela)

Este é o padrão mais idiomático em Go para testar múltiplos casos de forma organizada. Em vez de duplicar funções, você define uma tabela de casos e itera sobre ela.

```go
package matematica

import "testing"

func TestSomaTabela(t *testing.T) {
    casos := []struct {
        nome     string
        a, b     int
        esperado int
    }{
        {"positivos", 2, 3, 5},
        {"com zero", 0, 7, 7},
        {"negativos", -4, -6, -10},
        {"misto", -3, 8, 5},
    }

    for _, tc := range casos {
        t.Run(tc.nome, func(t *testing.T) {
            resultado := Soma(tc.a, tc.b)
            if resultado != tc.esperado {
                t.Errorf("Soma(%d, %d) = %d; esperado %d",
                    tc.a, tc.b, resultado, tc.esperado)
            }
        })
    }
}
```

> [!NOTE]
> `t.Run()` cria **subtestes** nomeados. Você pode rodar um caso específico com `go test -run TestSomaTabela/com_zero`. Os subtestes também aparecem individualmente no modo verboso (`-v`), facilitando identificar qual caso falhou.

### Vantagens do Table-Driven

| Vantagem              | Descrição                                                        |
|-----------------------|------------------------------------------------------------------|
| DRY                   | Lógica de teste escrita uma vez; dados separados da lógica       |
| Fácil de expandir     | Adicionar um caso = adicionar uma linha na tabela                |
| Diagnóstico claro     | O campo `nome` aparece no output, identificando o caso falho     |
| Subtestes independentes | Cada `t.Run` roda isolado; uma falha não cancela os outros     |

---

## Cobertura de Código

A cobertura mostra quais linhas do seu código foram exercidas pelos testes.

```bash
# Ver porcentagem geral
go test -cover ./...

# Gerar relatório detalhado
go test -coverprofile=coverage.out ./...
go tool cover -html=coverage.out
```

O comando `go tool cover -html=coverage.out` abre um navegador com o código colorido: verde para linhas cobertas, vermelho para não cobertas.

> [!NOTE]
> 100% de cobertura não significa 100% de qualidade. Cobertura alta é boa, mas o mais importante é que os casos relevantes (incluindo casos de erro e edge cases) sejam testados.

---

## Benchmarks

Além de testes funcionais, Go suporta testes de performance com `*testing.B`:

```go
func BenchmarkSoma(b *testing.B) {
    for i := 0; i < b.N; i++ {
        Soma(10, 20)
    }
}
```

```bash
go test -bench=. ./...
```

Saída:
```
BenchmarkSoma-8    1000000000    0.3 ns/op
```

> [!NOTE]
> O `b.N` é ajustado automaticamente pelo runtime para que o benchmark rode por tempo suficiente. Nunca coloque um número fixo — deixe o Go determinar.

---

## Exemplo Completo

A seguir, um exemplo real com função, teste em tabela e tratamento de erro:

```go
// calculadora.go
package calculadora

import (
    "errors"
    "fmt"
)

func Dividir(a, b float64) (float64, error) {
    if b == 0 {
        return 0, errors.New("divisão por zero não é permitida")
    }
    return a / b, nil
}
```

```go
// calculadora_test.go
package calculadora

import (
    "testing"
)

func TestDividir(t *testing.T) {
    casos := []struct {
        nome     string
        a, b     float64
        esperado float64
        erroEsperado bool
    }{
        {"divisão normal", 10, 2, 5, false},
        {"divisão com float", 7, 2, 3.5, false},
        {"divisão por zero", 9, 0, 0, true},
    }

    for _, tc := range casos {
        t.Run(tc.nome, func(t *testing.T) {
            resultado, err := Dividir(tc.a, tc.b)

            if tc.erroEsperado {
                if err == nil {
                    t.Errorf("esperava erro, mas não veio nenhum")
                }
                return
            }

            if err != nil {
                t.Fatalf("erro inesperado: %v", err)
            }

            if resultado != tc.esperado {
                t.Errorf("Dividir(%.1f, %.1f) = %.1f; esperado %.1f",
                    tc.a, tc.b, resultado, tc.esperado)
            }
        })
    }
}
```

Rodando:

```bash
go test -v -cover .
```

```
--- PASS: TestDividir (0.00s)
    --- PASS: TestDividir/divisão_normal (0.00s)
    --- PASS: TestDividir/divisão_com_float (0.00s)
    --- PASS: TestDividir/divisão_por_zero (0.00s)
PASS
coverage: 100.0% of statements
```
