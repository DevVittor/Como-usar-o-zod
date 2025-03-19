![Imagem do Zod](https://i.imgur.com/Y6MLN0P.jpeg)

# Manual do Zod

Vou listar os principais recursos do pacote **Zod**, uma biblioteca de validação e declaração de esquemas `TypeScript-first`, e explicar o que cada coisa faz e quando você deve usá-la. **Zod **é amplamente utilizado para validar dados em tempo de execução, garantindo que eles correspondam a um esquema predefinido, com inferência de tipos estática para **TypeScript**.

## Tipos Primitivos

**Zod** oferece métodos para validar tipos básicos de dados. Esses são os blocos de construção para criar esquemas mais complexos.

 `z.string()`

- **O que faz:** Valida que o dado é uma string.
- **Quando usar:** Use quando você espera uma entrada de texto, como nomes, emails ou mensagens.

**Exemplo:**

````JavaScript
const nameSchema = z.string();
nameSchema.parse("João"); // ✅ "João"
nameSchema.parse(123); // ❌ Erro: Expected string, received number
````

`z.number()`

* **O que faz**: Valida que o dado é um número.
* **Quando usar**: Use para validar entradas numéricas, como idades ou preços.

**Exemplo:**

````JavaScript
const ageSchema = z.number();
ageSchema.parse(25); // ✅ 25
ageSchema.parse("25"); // ❌ Erro: Expected number, received string
````

`z.boolean()`

* **O que faz**: Valida que o dado é um booleano (`true `ou `false`).
* **Quando usar**: Use para campos de escolha binária, como "aceita termos?".

**Exemplo:**

````JavaScript
const acceptSchema = z.boolean();
acceptSchema.parse(true); // ✅ true
acceptSchema.parse("true"); // ❌ Erro: Expected boolean, received string
````

`z.date()`

* **O que faz**: Valida que o dado é um objeto `Date`.
* **Quando usar**: Use para validar datas, como datas de nascimento ou eventos.

**Exemplo:**

````JavaScript
const dateSchema = z.date();
dateSchema.parse(new Date()); // ✅ Date
dateSchema.parse("2023-01-01"); // ❌ Erro: Expected date, received string
````

`z.literal(valor)`

* **O que faz**: Valida que o dado é exatamente igual a um valor específico.
* **Quando usar**: Use para restringir a entrada a um valor fixo, como um status específico.

**Exemplo:**

````JavaScript
const statusSchema = z.literal("active");
statusSchema.parse("active"); // ✅ "active"
statusSchema.parse("inactive"); // ❌ Erro: Invalid literal value
````

## Tipos Compostos

Zod permite criar esquemas para estruturas mais complexas, como objetos, arrays e uniões.

`z.object({})`

* **O que faz**: Define um esquema para um objeto com propriedades específicas.
* **Quando usar**: Use para validar dados estruturados, como formulários ou respostas de API.

**Exemplo:**

````JavaScript
const userSchema = z.object({
  name: z.string(),
  age: z.number(),
});
userSchema.parse({ name: "João", age: 25 }); // ✅
userSchema.parse({ name: "João" }); // ❌ Erro: Missing age
````

`z.array(schema)`

* **O que faz**: Valida que o dado é um array contendo itens que seguem um esquema específico.
* **Quando usar**: Use para listas, como uma coleção de IDs ou itens de um carrinho.

**Exemplo:**

````JavaScript
const numbersSchema = z.array(z.number());
numbersSchema.parse([1, 2, 3]); // ✅
numbersSchema.parse([1, "2"]); // ❌ Erro: Expected number, received string
````

`z.union([schema1, schema2])`

* **O que faz**: Valida que o dado corresponde a um de vários esquemas possíveis.
* **Quando usar**: Use quando o dado pode ter múltiplos tipos válidos, como string ou número.

**Exemplo:**

````JavaScript
const idSchema = z.union([z.string(), z.number()]);
idSchema.parse("123"); // ✅
idSchema.parse(123); // ✅
idSchema.parse(true); // ❌ Erro: Invalid type
````

`z.intersection(schema1, schema2)`

* **O que faz**: Combina dois esquemas, exigindo que o dado satisfaça ambos.
* **Quando usar**: Use para criar esquemas que precisam combinar propriedades de múltiplos objetos.

**Exemplo:**

````JavaScript
const nameSchema = z.object({ name: z.string() });
const ageSchema = z.object({ age: z.number() });
const combinedSchema = z.intersection(nameSchema, ageSchema);
combinedSchema.parse({ name: "João", age: 25 }); // ✅
````

## Modificadores de Validação

Zod oferece métodos para adicionar regras adicionais aos esquemas.

`.optional()`

* **O que faz**: Torna uma propriedade opcional (pode ser undefined).
* **Quando usar**: Use quando um campo pode ser omitido.

**Exemplo:**

````JavaScript
const userSchema = z.object({
  name: z.string(),
  age: z.number().optional(),
});
userSchema.parse({ name: "João" }); // ✅
````

`.nullable()`

* **O que faz**: Permite que o valor seja `null`.
* **Quando usar**: Use quando o campo pode explicitamente ser `null`.

**Exemplo:**

````JavaScript
const nameSchema = z.string().nullable();
nameSchema.parse(null); // ✅
nameSchema.parse("João"); // ✅
````

`.min(valor) / .max(valor)`

* **O que faz**: Define limites mínimo e máximo para números, strings ou arrays.
* **Quando usar**: Use para restringir tamanhos ou valores, como comprimento de senha.

**Exemplo:**

````JavaScript
const passwordSchema = z.string().min(8);
passwordSchema.parse("12345678"); // ✅
passwordSchema.parse("123"); // ❌ Erro: String must be at least 8 characters
````

`.email()`

* **O que faz**: Valida que a string é um email válido.
* **Quando usar**: Use em campos de email.

**Exemplo:**

````JavaScript
const emailSchema = z.string().email();
emailSchema.parse("joao@example.com"); // ✅
emailSchema.parse("joao"); // ❌ Erro: Invalid email
````

`.nonempty()`

* **O que faz**: Garante que um array ou string não esteja vazio.
* **Quando usar**: Use quando uma lista ou texto não pode ser vazia.

**Exemplo:**

````JavaScript
const itemsSchema = z.array(z.string()).nonempty();
itemsSchema.parse(["item"]); // ✅
itemsSchema.parse([]); // ❌ Erro: Array cannot be empty
````

## Métodos de Transformação e Refinamento

Esses métodos permitem transformar ou adicionar validações personalizadas.

`.transform(fn)`

* **O que faz**: Transforma o dado após a validação.
* **Quando usar**: Use para normalizar dados, como converter strings para minúsculas.

**Exemplo:**

````JavaScript
const nameSchema = z.string().transform((val) => val.toLowerCase());
nameSchema.parse("João"); // ✅ Retorna "joão"
````

`.refine(fn, { message })`

* **O que faz**: Adiciona uma validação personalizada.
* **Quando usar**: Use para regras específicas que não são cobertas pelos métodos padrão.

**Exemplo:**

````JavaScript
const passwordSchema = z.string().refine(
  (val) => val.includes("@"),
  { message: "Password must include @" }
);
passwordSchema.parse("abc@123"); // ✅
passwordSchema.parse("abc123"); // ❌ Erro: Password must include @
````

## Métodos de Parsing

Zod oferece formas de validar dados e lidar com os resultados.

`.parse(data)`

* **O que faz**: Valida o dado e retorna o valor se válido, ou lança um erro se inválido.
* **Quando usar**: Use quando você quer garantir que os dados são válidos e está ok com erros sendo lançados.

**Exemplo:**

````JavaScript
const schema = z.string();
schema.parse("test"); // ✅ "test"
schema.parse(123); // ❌ Lança erro
````

`.safeParse(data)`

* **O que faz**: Valida o dado e retorna um objeto com `success`(true/false) e `data`ou `error`.
* **Quando usar**: Use quando você quer lidar com erros de forma controlada sem lançar exceções.

**Exemplo:**

````
//JavaScript
const schema = z.string();
schema.safeParse("test"); // ✅ { success: true, data: "test" }
schema.safeParse(123); // ❌ { success: false, error: ZodError }
````

## Utilitários para Objetos

Métodos específicos para manipular esquemas de objetos.

`.pick({ campos })`

* **O que faz**: Cria um novo esquema com apenas as propriedades especificadas.
* **Quando usar**: Use para extrair um subconjunto de campos de um objeto maior.

**Exemplo:**

````JavaScript
const userSchema = z.object({ name: z.string(), age: z.number() });
const nameSchema = userSchema.pick({ name: true });
nameSchema.parse({ name: "João" }); // ✅
````

`.omit({ campos })`

* **O que faz**: Cria um novo esquema excluindo as propriedades especificadas.
* **Quando usar**: Use para remover campos indesejados.

**Exemplo:**

````JavaScript
const userSchema = z.object({ name: z.string(), age: z.number() });
const noAgeSchema = userSchema.omit({ age: true });
noAgeSchema.parse({ name: "João" }); // ✅
````

`.merge(schema)`

* **O que faz**: Combina dois esquemas de objeto em um só.
* **Quando usar**: Use para unir esquemas relacionados.

**Exemplo:**

````JavaScript
const baseSchema = z.object({ name: z.string() });
const extraSchema = z.object({ age: z.number() });
const mergedSchema = baseSchema.merge(extraSchema);
mergedSchema.parse({ name: "João", age: 25 }); // ✅
````

## Outros Recursos Úteis

`z.infer<typeof schema>`

* **O que faz**: Infere o tipo TypeScript a partir de um esquema Zod.
* **Quando usar**: Use para tipar variáveis com base no esquema.

**Exemplo:**

````JavaScript
const userSchema = z.object({ name: z.string() });
type User = z.infer<typeof userSchema>; // { name: string }
````

`z.enum([valores])`

* **O que faz**: Cria um esquema para uma lista fixa de valores.
* **Quando usar**: Use para campos com opções limitadas, como status ou categorias.

**Exemplo:**

````JavaScript
const statusSchema = z.enum(["active", "inactive"]);
statusSchema.parse("active"); // ✅
statusSchema.parse("pending"); // ❌ Erro: Invalid enum value
````

### Quando Usar Zod no Geral?

* **Validação de entrada de usuário**: Garanta que dados de formulários ou APIs sejam válidos antes de processá-los.
* **Integração com APIs**: Valide respostas de APIs externas para evitar "dados impossíveis".
* **Type Safety em Runtime**: Combine a segurança de tipos do TypeScript em tempo de compilação com validação em tempo de execução.
* **Substituição de interfaces duplicadas**: Evite repetir definições de tipo manualmente; Zod infere tipos automaticamente.

Essa lista cobre os principais recursos do Zod. A biblioteca é extremamente flexível e pode ser estendida com plugins (como `zod-to-json-schema` ou `drizzle-zod`) para casos mais específicos. 
