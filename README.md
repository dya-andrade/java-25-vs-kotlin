# 🚀 POC — Novidades do Java 25  
Este documento apresenta uma POC real utilizando os principais recursos modernos do Java 25, trazendo explicações claras, glossário para iniciantes e comparação direta entre Java e Kotlin.

Ele inclui:
- ✨ Novidades do Java 25 explicadas de forma simples  
- 🔧 Código real da POC  
- 🧠 Glossário técnico para iniciantes  
- 🤝 Comparação Java vs Kotlin  
---

# 🧱 1. Estrutura da POC

````

poc-java25/
├─ src/
│   ├─ Main.java
│   ├─ PaymentService.java
│   ├─ FfmExample.java
│   ├─ ScopedContextDemo.java
│   └─ models/
│        └─ Payment.java
└─ pom.xml

````

Esta POC demonstra, na prática, como usar os recursos mais relevantes e atualizados do Java 25.

---

# 🧪 2. Código da POC (Exemplos reais)

## 2.1 `models/Payment.java` — Records e Record Patterns
```java
package models;

public record Payment(String id, double amount, String status) {}
````

---

## 2.2 `PaymentService.java` — Pattern Matching + Sequenced Collections

```java
import models.Payment;
import java.util.*;

public class PaymentService {

    private final List<Payment> payments = new ArrayList<>();

    public void addPayment(Payment p) {
        payments.add(p);
    }

    public void processPayments() {
        for (var p : payments) {
            switch (p) {
                case Payment(String id, double amount, _) -> {
                    System.out.println("Processando pagamento: " + id + " -> " + amount);
                }
                default -> System.out.println("Pagamento desconhecido");
            }
        }
    }

    public void printLatest() {
        System.out.println("Primeiro pagamento: " + payments.getFirst());
        System.out.println("Último pagamento: " + payments.getLast());
    }
}
```

---

## 2.3 `ScopedContextDemo.java` — Scoped Values

```java
import jdk.incubator.concurrent.ScopedValue;

public class ScopedContextDemo {

    static final ScopedValue<String> REQUEST_ID = ScopedValue.newInstance();

    public static void runWithScopedValue() {
        ScopedValue.where(REQUEST_ID, "REQ-12345").run(() -> {
            System.out.println("Scoped value: " + REQUEST_ID.get());
        });
    }
}
```

---

## 2.4 `FfmExample.java` — Foreign Function & Memory API

```java
import java.lang.foreign.*;
import java.lang.invoke.*;

public class FfmExample {

    public static void runFFM() throws Throwable {
        Linker linker = Linker.nativeLinker();

        SymbolLookup std = linker.defaultLookup();
        var printf = std.find("printf").orElseThrow();

        MethodHandle handle = linker.downcallHandle(
                printf,
                FunctionDescriptor.ofVoid(ValueLayout.ADDRESS)
        );

        try (Arena arena = Arena.ofConfined()) {
            var msg = arena.allocateUtf8String("Hello from FFM API!\n");
            handle.invoke(msg);
        }
    }
}
```

---

## 2.5 `Main.java` — Unnamed Class, Virtual Threads e integração da POC

```java
void main() throws Exception {

    var service = new PaymentService();
    service.addPayment(new models.Payment("P1", 100.0, "NEW"));
    service.addPayment(new models.Payment("P2", 90.5, "NEW"));

    service.processPayments();
    service.printLatest();

    ScopedContextDemo.runWithScopedValue();

    Thread.ofVirtual().start(() ->
            System.out.println("Rodando em virtual thread")
    ).join();

    FfmExample.runFFM();
}
```

---

# ✨ 3. Novidades do Java 25 (Explicação Simplificada)

Cada recurso é explicado para que iniciantes entendam **sem precisar conhecimento avançado**.

---

## 3.1 Unnamed Classes & Instance Main

Permite criar um arquivo apenas com um `main()`:

```java
void main() {
    System.out.println("Olá Java 25!");
}
```

✔ Menos código
✔ Ideal para POCs, testes rápidos e scripts

---

## 3.2 Record Patterns + Pattern Matching

O Java consegue “abrir” um objeto automaticamente:

```java
case Payment(String id, double amount, _) -> ...
```

✔ Mais seguro
✔ Menos linhas de código

---

## 3.3 Unnamed Variables & Patterns (`_`)

Usado quando você não precisa de determinada informação.

✔ Evita criar variáveis desnecessárias
✔ Deixa o código mais limpo

---

## 3.4 Sequenced Collections

Agora listas e coleções ordenadas têm métodos próprios:

* `getFirst()`
* `getLast()`
* `addFirst()`
* `addLast()`
* `reversed()`

Antes isso era manual e chato.

---

## 3.5 String Templates

Interpolação de string nativa:

```java
String msg = STR."Pagamento {p.id()} no valor de {p.amount()}";
```

✔ Mais legível
✔ Menos concatenação

---

## 3.6 Virtual Threads (Project Loom)

Threads extremamente leves.

**Simplificando:**
Uma thread normal = garçom caro
Uma virtual thread = garçom barato

✔ Suporta milhares/milhões
✔ Ótimo para I/O (banco, API, arquivos)
✔ Evita complexidade de programação reativa

---

## 3.7 Scoped Values

Substituem `ThreadLocal` com segurança e imutabilidade.

✔ Garantem passagem de contexto
✔ Funcionam perfeitamente com virtual threads
✔ Não permitem mutação acidental

---

## 3.8 Foreign Function & Memory API (FFM)

Permite chamar código C sem JNI.

✔ Mais rápido
✔ Mais seguro
✔ Mais fácil

Ideal para:

* análise de dados
* processamento de imagens
* integração com bibliotecas nativas

---

# 🤝 4. Java 25 vs Kotlin — Comparação Direta

## 💛 Kotlin — Pontos Fortes

* Sintaxe curta
* Null-safety nativa
* Ideal para Android
* Coroutines muito poderosas
* Produtividade altíssima

### ❌ Desvantagens

* Compilação mais lenta
* Ecossistema menor
* Performance menos previsível para alta carga
* Menos devs experientes no backend

---

## 🔵 Java (21–25) — Pontos Fortes

* Virtual Threads (muito mais escalável)
* FFM API para integração nativa
* Ecossistema gigantesco
* Performance excelente
* Padrão em sistemas bancários/enterprise

### ❌ Desvantagens

* Verbosidade maior
* Null-safety não nativo
* Menos flexível que Kotlin em extensões

---

## 🥊 Qual linguagem escolher?

| Situação                           | Melhor escolha |
| ---------------------------------- | -------------- |
| Alta carga / alto tráfego          | **Java 21–25** |
| Workers / filas / I/O pesado       | **Java**       |
| Android                            | **Kotlin**     |
| Equipes pequenas                   | **Kotlin**     |
| Integração C / performance extrema | **Java + FFM** |
| Projetos enterprise                | **Java**       |

---

* 📌 Motivos técnicos
* 📌 Motivos de negócio
* 📌 Motivos arquiteturais
* 📌 Motivos regulatórios
* 📌 O que Java tem que Kotlin NÃO tem
* 📌 Onde Kotlin é bom, mas não o suficiente
* 📌 Cenários onde Kotlin funciona

Tudo de forma **didática**, direta e com profundidade profissional.

---

## 🟦 1. **Java domina todo o ecossistema financeiro há 25 anos**

Setores como:

* bancos
* emissores
* adquirentes
* subadquirentes
* gateways de pagamento
* antifraude
* câmbio
* open finance

usam Java como **padrão de mercado**.

Isso significa:

* todas as regras
* frameworks
* libs de criptografia
* libs de certificação
* drivers
* conectores
* middlewares
* HSMs (Hardware Security Module)
* integrações bancárias

já têm **suporte nativo e maduro para Java**.

💬 *Na prática:*
Quando você precisa integrar com um banco ou sistema legado, **Java sempre funciona; Kotlin nem sempre**.

---

## 🟦 2. **Conformidade legal, risco operacional e auditoria exigem robustez**

Pagamentos e fintechs trabalham com:

* PCI-DSS
* ISO 8583
* ISO 20022
* LGPD
* Basileia
* Resoluções do BACEN
* Mensageria SWIFT
* Regras anti-fraude
* Sarbanes-Oxley (SOX) para empresas US

Esses padrões foram criados quando **Kotlin nem existia**.

A maiorias das libs de:

* assinaturas digitais
* HSM
* criptografia avançada
* certificados
* tokens JWT compatíveis com padrões antigos
* simuladores bancários

são **Java puro**.

E bancos não arriscam com tecnologias menos maduras.

---

## 🟦 3. **Java tem melhor integração com infraestrutura crítica**

Sistemas financeiros lidam com:

* transações por segundo absurdas
* filas gigantes
* liquidações
* conciliações
* replicações
* engines de risco

Java foi construído para:

* latência baixa
* throughput alto
* controle fino de memória
* previsibilidade do GC
* integração com C/C++ via FFM (antes JNI)
* multi-threading real
* performance consistente

**Pagamentos NÃO toleram:**

❌ pausas inesperadas
❌ imprevisibilidade de performance
❌ comportamento desconhecido do runtime

Java é **altamente previsível**.

---

## 🟦 4. **Virtual Threads (Java 21–25) deixou Java MUITO mais escalável que Kotlin**

Kotlin usa **coroutines**, que são boas, mas:

* exigem um runtime próprio
* podem gerar contextos inconsistentes
* exigem disciplina na troca de dispatcher
* nem toda lib é “coroutine-safe”
* exceções silenciosas podem acontecer em coroutines mal estruturadas

Java agora tem:

* Virtual Threads
* Structured Concurrency
* Scoped Values

Tudo **nativo**, padronizado, seguro e extremamente rápido.

💬 *Resultado real:*
Sistemas de pagamentos com alto volume (ex: conciliadores, workers de liquidação) rodam **muito melhor em virtual threads** do que em coroutines.

---

## 🟦 5. **FFM API** (Java 22–25)

Java consegue agora:

* chamar C
* acessar memória nativa
* integrar com HSM e libs de criptografia
* processar buffers binários enormes

Kotlin NÃO tem FFM.

Ele depende totalmente da JVM — mas **sem acesso direto ao runtime moderno**.

Em fintech, onde você:

* lê binários ISO 8583
* processa mensagens FIX
* manipula byte arrays grandes
* integra com hardware de segurança

Java > Kotlin.

---

## 🟦 6. **Confiabilidade e maturidade**

O setor financeiro é conservador por motivos:

* dinheiro real
* risco operacional
* compliance
* auditoria
* risco sistêmico
* multas altíssimas

Java já provou por décadas que aguenta:

* tráfego absurdo
* picos de uso inesperados
* falhas distribuídas
* alta disponibilidade
* clustering crítico

Kotlin, mesmo excelente, **não foi testado nesse nível global** ainda.

---

## 🟦 7. **Maior disponibilidade de devs com experiência real**

Pagamentos e bancos precisam de engenheiros que entendem:

* concorrência
* mensageria bancária
* transações distribuídas
* ACID
* idempotência
* consistência eventual

90% dos profissionais que dominam isso estão…
➡️ em Java.

Equipes de risco, crédito e pagamentos **são muito mais fortes em Java**.

---

## 🟦 8. **Ecossistema Java é gigantesco e totalmente otimizado para finanças**

Exemplos reais usados em fintechs:

* Spring Boot
* Quarkus
* Micronaut
* Kafka
* Flink
* Akka (JVM)
* Hazelcast
* Ignite
* Netty
* Vert.x
* Drools (engine de regras)
* Muitos SDKs bancários

Todos têm suporte primeiro em **Java**.

Kotlin funciona em cima disso, mas nem sempre com **integração perfeita**, principalmente com:

* annotations complexas
* bytecode gerado por data classes
* reflection diferenciado

---

## 🟦 9. **Compliance e auditoria inesperadas**

Alguns bancos e adquirentes simplesmente dizem:

> *“A integração é suportada oficialmente apenas em Java.”*

E isso acontece MUITO.

---

## 🟦 10. **Kotlin é excelente — mas para outro tipo de uso**

Kotlin funciona **muito bem** para:

* Android
* microsserviços pequenos
* APIs REST simples
* equipes pequenas
* linguagem de alta produtividade

Mas para sistemas centrais como:

* motor de liquidação
* contabilidade
* processamento de pagamentos
* conciliação
* antifraude
* ledger
* câmbio e remessa
* PIX em alto volume
* controles de risco

A regra do mercado ainda é:

> **Java é o padrão ouro.**

---

# 🟩 **Resumo mais direto possível**

## Java é melhor para fintech, bancos e pagamentos porque:

* Tem mais maturidade
* Tem mais ferramentas bancárias
* Tem mais bibliotecas específicas
* É mais previsível em alta carga
* Tem virtual threads nativas
* Tem FFM API para integração nativa
* Tem mais devs experientes
* É o padrão esperado pelo mercado
* É mais seguro para auditorias e compliance
* Tem melhor performance estável

## Kotlin perde porque:

* É menos maduro nesse domínio
* Coroutines não são tão previsíveis quanto Virtual Threads
* Ecossistema é menor
* Poucos devs dominam em nível bancário
* Não tem FFM
* Não tem runtime próprio (depende da JVM, mas sem acesso direto a tudo)

---

# 📚 5. Glossário — Termos Técnicos Explicados para Iniciantes

## I/O

Toda operação de entrada/saída: banco, arquivos, rede.
É lento → Virtual Threads ajudam.

## Thread

Um fluxo de execução.
Pense como um “garçom” que faz uma tarefa.

## Virtual Thread

Garçom barato → você cria milhões.

## Heap

Onde vivem os objetos Java.

## Off-Heap

Memória fora da JVM: rápida e manual.

## JNI

Jeito antigo de chamar C → difícil e perigoso.

## FFM API

Novo jeito de chamar C → simples e seguro.

## Record

Classe imutável automática.

## Pattern Matching

Java reconhece tipos automaticamente.

## Scoped Values

Variáveis seguras compartilhadas por contexto.

## String Template

Interpolação de string moderna.

## POC

Prova de conceito → protótipo rápido.

---
---

# 🧠 **FFM API — Explicação Completa e Didática**

A **FFM API** é uma grande novidade do Java moderno (21+ até 25).
FFM significa:

> **Foreign Function & Memory API**
> (API de Funções e Memória Estrangeira)

Ou seja:

### 👉 Ela permite que o Java:

1. **Chame funções escritas em outras linguagens**, como **C, C++, Rust, Go**
2. **Acesse memória fora do Java** (memória nativa / off-heap)
3. **Faça isso sem usar JNI**, que era o jeito antigo e complicado.

---

# 🎯 **Por que a FFM API existe?**

Antes dela, quem queria chamar código C precisava usar **JNI**, que era:

* difícil
* verboso
* inseguro
* cheio de ponteiros
* fácil de causar crash na JVM
* chato de configurar

A FFM API resolve tudo isso trazendo:

* segurança
* velocidade
* ergonomia
* modernidade
* suporte oficial da JVM
* tipo-checking (verificação de tipos)

Ela é considerada um dos maiores avanços do Java nos últimos anos.

---

# 🟢 **Para que serve? (casos reais)**

A FFM API é importante para:

### ✔ Integração com bibliotecas de alta performance

Math, ML, gráficos, criptografia, análise de imagens…

### ✔ Acesso a hardware

Placas gráficas, sensores, dispositivos nativos, CUDA, etc.

### ✔ Desempenho extremo em dados

Acesso direto a memória:

* bancos de dados embutidos
* caches paralelos
* manipulação de milhões de registros
* processamentos científicos

### ✔ Substituir JavaCPP, JNI, JNA, Panama bindings

Menos código, mais velocidade.

---

# 🔵 **O que a FFM API faz exatamente?**

Ela tem **3 capacidades principais**:

---

## 1️⃣ **Chamar funções C nativamente**

Sem DLL/JNI customizado, sem header, sem arquivo `.h`.

Exemplo simples de chamar `printf` do C:

```java
SymbolLookup lookup = linker.defaultLookup();
var printf = lookup.find("printf").orElseThrow();

MethodHandle handle = linker.downcallHandle(
    printf,
    FunctionDescriptor.ofVoid(ValueLayout.ADDRESS)
);
```

Isso chama **diretamente** a função `printf()` do sistema operacional.

É Java chamando C **sem JNI**.

---

## 2️⃣ **Acessar memória nativa (off-heap)**

O Java normalmente usa **heap**, controlado pelo garbage collector (GC).

A FFM permite alocar memória fora desse heap:

```java
try (Arena arena = Arena.ofConfined()) {
    MemorySegment msg = arena.allocateUtf8String("Hello!");
}
```

Isso cria um bloco de memória igual ao `malloc()` do C, mas:

* seguro
* verificado pela JVM
* sem ponteiros perigosos

---

## 3️⃣ **Criar ponteiros e tipos nativos**

O FFM API modela:

* ponteiros
* structs (estruturas do C)
* arrays nativos
* tipos primitivos C (int, float, char, long…)

Exemplo de layout de struct:

```java
GroupLayout structLayout = MemoryLayout.structLayout(
    ValueLayout.JAVA_INT.withName("id"),
    ValueLayout.JAVA_DOUBLE.withName("price")
);
```

Isso representa a struct C:

```c
struct Item {
   int id;
   double price;
};
```

---

# 🟧 **Mas é seguro? Não causa crash?**

O grande diferencial:

> **A FFM API é 100% segura e checada pela JVM.**

Com JNI, era fácil:

* acessar memória errada
* causar segmentation fault
* travar a JVM inteira

Com FFM:

* layouts são verificados
* tamanhos são validados
* você não acessa memória inválida
* não mexe em ponteiros brutos

É como “C com cinto de segurança”.

---

# 🧩 **Analogia simples (para iniciantes)**

Imagine que:

* o Java vive numa casa (Heap)
* a memória nativa é uma garagem grande (Off-Heap)
* JNI era você entrando na garagem vendado
* A FFM API é você entrando com lanterna, mapa e segurança

Você tem:

* mais espaço
* mais velocidade
* mais segurança

---

# 🔥 **Exemplo real, explicado passo a passo**

### Objetivo: chamar o `printf` do C.

#### Passo 1: pegar o "linker"

```java
Linker linker = Linker.nativeLinker();
```

👉 O *linker* é quem faz o Java conversar com bibliotecas nativas.

#### Passo 2: procurar a função `printf` no sistema

```java
SymbolLookup std = linker.defaultLookup();
var printf = std.find("printf").orElseThrow();
```

👉 Ele procura dentro da libc, a biblioteca padrão do C.

#### Passo 3: criar um "método Java" apontando para `printf`

```java
MethodHandle handle = linker.downcallHandle(
    printf,
    FunctionDescriptor.ofVoid(ValueLayout.ADDRESS)
);
```

👉 Agora você pode chamar `printf` como se fosse um método Java.

#### Passo 4: alocar a string nativa (off-heap)

```java
var msg = arena.allocateUtf8String("Hello!");
```

👉 A string é alocada direto na memória nativa.

#### Passo 5: chamar a função C

```java
handle.invoke(msg);
```

🎉 E acabou.
Você chamou C dentro do Java.

---

# 🧩 **Resumo fácil**

| O que é?            | API para acessar memória nativa e chamar funções C                       |
| ------------------- | ------------------------------------------------------------------------ |
| Por que existe?     | Substituir JNI e permitir alto desempenho                                |
| É seguro?           | Sim, totalmente checado pela JVM                                         |
| Precisa escrever C? | Não                                                                      |
| Para quem é útil?   | Projetos de performance, integrações nativas, ML, gráficos, bancos, etc. |

---

# ⚙ Quando você deve usar FFM?

Use quando você precisa de:

* performance extrema
* acesso a hardware
* usar uma lib C/C++/Rust famosa
* evitar JNI
* processar grandes volumes de dados
* interoperar com sistemas antigos em C

---

