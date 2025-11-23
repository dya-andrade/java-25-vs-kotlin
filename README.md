# 📘 **POC Completa Java 25 (Didático e Profissional)**


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
