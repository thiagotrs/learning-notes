## Linguagem

### Alocação de Memória

- Para tipos primitivos a memória (stack) é alocada na declaração.
- Para tipos de referência a memória (heap) é alocada na inicialização (new).

### Valores Padrão

| Tipo | Valor padrão |
| --- | --- |
| byte | 0 |
| short | 0 |
| int | 0 |
| long | 0L |
| float | 0.0f |
| double | 0.0d |
| char | '\u0000' |
| String (ou qualquer object) | null |
| boolean | false |

### Inicialização

- Array é um tipo de referência
- Elementos do Array são inicializados com o valor padrão do tipo quando ele é instanciado.
- Atributos são inicializados com o valor padrão do tipo quando o objeto é instanciado.
- Variáveis locais devem ser inicializadas.

### Função

- Passagem de parâmetros em funções é sempre por cópia: cópia do valor para primitivos e cópia de referência para objetos.
- Classes *“Wrappers”* fazem *“autoboxing**”*** (criam nova instância) na passagem de parâmetros.
- String criam novas instancias na passagem de parâmetros.
- Métodos polimórficos tem o mesmo nome mas parâmetros diferentes.
- Assinatura de um método: (nome + parâmetros)

### Ordem inicialização de Classes

- Blocos de inicialização são executados antes dos construtores.
- Blocos de inicialização estático é executado apenas umas vez quando a classe é carregada pela JVM.

```java
class OrderInit {
  static int staticOrder = 1;
	int order = 3;
	static { System.out.println(2); }
	{ System.out.println(4); }
	OrdemInit() { System.out.println(5); }
}
```

1. Bloco de inicialização estático é executado quando a JVM carregar.
2. Alocado espaço em memória para o objeto.
3. Cada atributo é criado e inicializado com valores default ou o que for passado.
4. Bloco de inicialização é executado na ordem em que aparece.
5. Construtor é executado.

### Modificadores Protected vs Default

- Modificador `protected` dá acesso a quem estiver no pacote e as subclasses independente do pacote.
- Modificador default dá acesso apenas a quem estiver no pacote.

### Enumeradores

```java
public enum PersonType {
	PESSOA_JURIDICA(1),
	PESSOA_FISICA(2)

	private final type;

	public PersonType(int type) {
		this.type = type;
	}

	public int getType() {
		return this.type;
	}
}
```

```java
public enum PaymentType {
	DEBIT {
		@Override
		public double calcDiscount(double value) {
			return value * 0.1;
		}
	},
	CREDIT{
		@Override
		public double calcDiscount(double value) {
			return value * 0.5;
		}
	}

	public abstract double calcDiscount(double value);
}
```

### Classes Abstratas

- Classes abstratas filhas podem implementar os métodos abstratos do pai ou deixar para as subclasses implementarem.

### Interfaces

- Métodos de interface são sempre `public abstract`.
- Atributos de interface são sempre `public static final` (constantes).
- Métodos estáticos de interfaces não fazem parte da API (a classe não herda).
- Métodos default de mesma assinatura de interfaces diferentes, deverão ser sobrescritos pela classe que as implementam. Ex: `Interface.super.method();`
- Interfaces filhas de outra interface com um método default podem:
    1. Manter a implementação do pai.
    2. Fornecer a própria implementação.
    3. Transformar em método abstrato para ser implementado pelas subclasses. 
- Interfaces funcionais só devem ter apenas 1 método abstrato, mas podem ter métodos default e estáticos.

### Tratamento de Exceção

- *“Unchecked Exceptions”* (filhas de `RuntimeException`) não precisam ser tratadas.
- *“try-with-resources”* apenas com classes que implementam `Closeble` ou `AutoCloseble`
- *“try-with-resources”* fecha em ordem reversa a aberta

```java
try (BufferedReader br = new BufferedReader(new FileReader("ex.txt"))) {
	br.readLine();
}
```

---

- Valores das classes *Wrapper* não fazem cast automático

---

### Generics

- `List<? extends Animal>` como argumento de um método não poderá adicionar elementos a lista passada.
- `List<? super Animal>` como argumento de um método poderá adicionar subclasses a lista passada mas itera só com `Object`.
- `List<Animal>` como argumento de um método deve passar somente `List<Animal>` e não uma subclasse `List<Dog>`.

```java
void add(List<? extends Animal> animals) {
	animals.add(new Animal()); // ERROR!!!
}

void showList(List<? super Animal> animals) {
	for(Object animal : animals) {
		System.out.println(animal);
  }
}

void clearList(List<Animal> animals) {
	animals.clear();
}
// exeutando o método
clearList(new ArrayList<Dog>()); // ERROR
```

### Classes Internas

- `OuterClass.this` para acessar a referência a classe externa de dentro da classe interna.
- Classes estáticas internas acessam a classe externa como outras classe externas `new OuterClass`.

```java
class OuterClass {
	class InnerClass() { }
	static class StaticInnerClass() { }
	interface InnerInterface { }
}
interface OuterInterface {
	interface InnerInterface { }
}

OuterClass oc = new OuterClass();
OuterClass.InnerClass ic = oc.new InnerClass();

OuterClass.StaticInnerClass sic = new OuterClass.StaticInnerClass();
OuterClass.StaticInnerClass.staticMethod();

OuterInterface.InnerInterface ii = new ImplementedInnerInterface();
OuterClass.InnerInterface ii = new ImplementedInnerInterface();
```

### Classes Locais (dentro de métodos)

- Só podem acessar variáveis locais final ou efetivamente final.
- Podem acessar atributos de classes externas.
- Não podem ter nada `static` e nem interfaces, mas pode ter `static final`.

### Classes Anônimas

- Métodos não sobrescritos não podem ser usados fora da classe anônima.
- As regras de Classes Locais também são validas para classes Anônimas.

```java
Animal anonAnimal = new Animal() {
	@Override
	public void walk() {
		System.out.println("Animal is walking!");
	}
}

InterfaceRun anomRun = new InterfaceRun() {
	@Override
	public void run() {
		System.out.println("Its's run!");
	}
}
```

### Expressões Lambda

- Uma Lambda só pode acessar variáveis locais final ou efetivamente final.

```java
String preffix = "Lambda: "; // preffix é efetivamente final
Function<String, String> func = s -> preffix.concat(s);
```

### Method Reference

- 4 formas de uso.

```java
// 1. Método estático
Function<String, Integer> func = Integer::parseInt;
Function<String, Integer> func = s -> Integer.parseInt(s); // equivalent
// 2. Método de instância de objeto (1 instância)
Consumer<String> consum = System.out::println;
Consumer<String> consum = s -> System.out.println(s); // equivalent
// 3. Método de instância de objeto (N instâncias)
UnaryOperator<String> unOp = String::concat;
UnaryOperator<String> unOp = s -> s.concat(s); // equivalent
// 4. Método construtor
Supplier<Set<String>> supp = HashSet:new
Supplier<Set<String>> supp = () -> new HashSet<>(); // equivalent
```

---

### JAVA 9

- *“Factory methods”* para List, Set  e Map `of()` que são imutáveis.
- Variáveis efetivamente final para *“try-with-resources”*.
- Métodos privados em interfaces.
- Interfaces para *“Reactive Streams”*.
- Construtores de Classes *“Wrappers”* ficam obsoletos em favor de `valueOf()`.
- Método `finalize()` fica obsoleto.

### JAVA 10

- Declaração de variável com `var`.
- Métodos para copiar coleções `copyOf()` e são imutáveis.
- Coletores imutáveis `toUnmodifiableSet()`, `toUnmodifiableList()`, `toUnmodifiableMap()`.
- Novo método `Optional.orElseThrow()`.

### JAVA 11

- Declaração de variável com `var` em argumentos lambdas.
- Novo cliente HTTP.
- Novo método em Collection `toArray(Integer[]::new)`.
- Novos métodos em String: `isBlank()`, `lines()`, `strip()`, `repeat()` ...
