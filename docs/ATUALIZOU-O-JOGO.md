# O jogo atualizou. O que fazer.

> Este é o documento que a BRZ Api existe para tornar curto.
>
> A API de terceiro que usávamos antes **baixava uma tabela de 555.540 offsets
> de um CDN que não controlamos**, chaveada pelo SHA do executável. Enquanto
> aquela tabela não fosse publicada para a build nova, nada funcionava — e a
> espera não dependia de nós.
>
> Aqui não há tabela para esperar. O que muda a cada build são **13 linhas de um
> arquivo de texto**, e nem a API nem os plugins recompilam.

---

## O caminho curto

```bash
cd ~/ark/asa-api
./checar-portao-asa.sh                       # o que quebrou, e onde
cd brz-api
./ferramentas/medir-ancora.py --calibrar     # a ferramenta ainda mede?
```

Se o portão passar, **não há nada a fazer**: a reflexão acha classe, campo e
função pelo NOME, e nome não muda quando o jogo é recompilado.

O que pode quebrar é só o `loader/ancoras.txt` — os endereços do que a reflexão
**não** descreve.

---

## Por que existem âncoras, se tudo é por nome

A reflexão viva da Unreal descreve `UFunction` e `UProperty`. Ela **não**
descreve função virtual nativa comum.

E a diferença aparece onde menos se espera:

| função | tem `exec`? | a reflexão vê? |
|---|---|---|
| `APrimalDinoCharacter::Die` | sim | **sim** |
| `AShooterCharacter::Die` | **não** | não |
| `APrimalCharacter::TakeDamage` | não | não |
| `APrimalStructure::TakeDamage` | não | não |

Um plugin de PvE que precisa **cancelar** dano numa estrutura não tem outro
caminho. Por isso as âncoras existem — e por isso são treze, não meio milhão.

**Faltar uma âncora é um plugin que não faz UMA coisa, e diz isso no log. Não é
um servidor que não sobe.**

---

## Medir de novo

### 1. Confira que a ferramenta ainda mede

```bash
./ferramentas/medir-ancora.py --calibrar
```

Ela mede as **nove âncoras cuja resposta já está no `ancoras.txt`** e compara.
O resultado esperado é:

```
7 corretas · 2 recusadas · 0 ERRADAS
PASSOU. Recusa nao e' erro — e' o projeto funcionando.
```

**Uma resposta ERRADA é o único resultado inaceitável.** Recusa não é erro: uma
âncora faltando é um plugin a menos; uma âncora errada instala um desvio no
meio de outra função, e o servidor cai num lugar que não aponta de volta.

### 2. Meça cada âncora

```bash
./ferramentas/medir-ancora.py 'UObject.ProcessEvent('
./ferramentas/medir-ancora.py 'FMemory.Malloc('
./ferramentas/medir-ancora.py 'APlayerController.GetPlayerNetworkAddress('
./ferramentas/medir-ancora.py 'RCONClientConnection.ProcessRCONPacket('
./ferramentas/medir-ancora.py 'FText.FromString(FString&)'
```

A chave é um prefixo de uma entrada do `golden/cached_offsets_24159508.cache`.
Para descobrir a chave de algo:

```bash
python3 ~/ark/asa-api/tools/oraculo.py -f TakeDamage
```

### 3. As duas que a ferramenta recusa

`APrimalStructure::TakeDamage` e `AShooterCharacter::Die` **não casam por
assinatura**, e não é ajuste que falta: entre as builds o compilador
**reordenou as instruções** delas. `Die` casa nos 18 primeiros bytes e diverge
no 19º; `TakeDamage` diverge no 7º. Foi testado de 16 a 128 bytes, das posições
0, 16, 32, 64, 128 e 256, com o filtro de tamanho apertado a 5%.

Essas duas se medem à mão, com desmontador, comparando o corpo da função na
build de referência com a candidata na build nova.

> **Não aceite o palpite de um agente sobre elas.** Já aconteceu de 168 agentes
> produzirem um layout "lido do binário" que não existia. O que vale é o
> exemplar vivo com resposta conhecida.

### 4. `GUObjectArray` e `NamePool` são DADOS, não função

Essas duas não têm assinatura de código — são variáveis globais. Elas se acham
pelo padrão de uso (a instrução que as referencia) ou por varredura validada.

E é por isso que **elas não precisam ser confiadas**: `BrzReflexao.cpp` faz
quatro provas antes de aceitá-las.

1. a contagem de objetos é plausível;
2. os ponteiros de classe são legíveis;
3. a forma do cabeçalho do `FName` é **medida**, testando 300 entradas — a UE5
   tem duas formas possíveis, e supor a errada lê texto de onde não há;
4. os nomes obrigatórios aparecem: `Object`, `Class`, `Function`, `Package`.

**Se as provas não passarem, `Pronta()` devolve 0 e NENHUM plugin sobe.** É de
propósito: melhor não carregar que ler memória errada calado.

### 5. Escreva no arquivo e confira

Atualize `loader/ancoras.txt` — inclusive o cabeçalho, que traz o número da
build, o SHA do exe e a data. Depois:

```bash
./ferramentas/calibrar.sh            # 74 testes, sem precisar do jogo
./ferramentas/provar-sob-wine.sh     # carrega de verdade, num processo real
cd .. && ./checar-portao-asa.sh
```

---

## O que NÃO precisa ser feito

- **Recompilar a API.** Nenhuma âncora está cravada no binário.
- **Recompilar os 22 plugins.** Eles falam com a tabela de funções, não com o
  jogo. Foi para isso que a fronteira C existe.
- **Esperar terceiro nenhum.** Não há tabela para baixar.

---

## Se o servidor não subir

`SOCORRO.md`, na raiz do repositório, é a porta de entrada por sintoma.

O primeiro lugar a olhar é o log do carregador: ele **diz** o que recusou e por
quê. Um motor que devolve zero em silêncio faz o plugin agir sobre dado
inventado — por isso nada aqui falha calado.
