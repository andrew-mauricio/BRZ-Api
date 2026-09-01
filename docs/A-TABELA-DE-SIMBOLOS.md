# A tabela de símbolos

**77.426 endereços da build 25002750**, gerados aqui, do nosso próprio dump.

```bash
./ferramentas/gerar-simbolos.py          # regera para a build que está rodando
```

Sai em `simbolos/simbolos-<build>.txt` e vai junto no pacote do runtime, em
`Brz-Api/`.

---

## Por que existe

A reflexão viva da Unreal descreve `UFunction` e `UProperty`. Ela **não**
descreve função virtual nativa comum — e algumas importam:

| função | tem `exec`? | a reflexão vê? |
|---|---|---|
| `APrimalDinoCharacter::Die` | sim | **sim** |
| `AShooterCharacter::Die` | **não** | não |
| `APrimalStructure::TakeDamage` | não | não |
| `AShooterPlayerController::ConsoleCommand` | não | não |

Um plugin de PvE que precisa **cancelar** dano numa estrutura não tem outro
caminho. Até a v9 esse caminho era o `ancoras.txt`: treze endereços, medidos à
mão, um a um. Esta é a mesma ideia na escala que o jogo tem de verdade.

---

## A diferença para o que a AsaApi faz

A AsaApi **baixa** 555.540 offsets de um CDN de terceiro, chaveados pelo SHA do
executável. Medido com `strings` no binário que rodava aqui: `msdia140` = 0,
`PDBReader` = 0, `cdn.pelayori.com/cache/` = **1**. Sem o download, ela não tem
endereço nenhum.

Nós temos o mesmo material **em casa**: `golden/cached_offsets_24159508.cache`,
796.225 chaves extraídas do binário.

| | AsaApi | BRZ Api |
|---|---|---|
| origem | CDN de terceiro | **o nosso dump** |
| jogo atualiza | esperar alguém publicar | **rodar o gerador aqui** |
| o serviço sai do ar | sem endereço nenhum | indiferente |

A tabela apodrece nos dois casos. A diferença é **quem depende de quem**.

---

## A regra que separa isto de uma mentira

> **O que não resolveu não entra.**

O `tools/port_sig.py` que existia gravava a **RVA da build antiga** quando não
conseguia resolver. O arquivo de saída não distinguia resolvido de não
resolvido: **188.822 endereços errados, indistinguíveis dos certos**. Rodei ele
primeiro e ia usar o resultado — "221.322 resolvidos" — até abrir o código.

Cada linha do arquivo gerado é um endereço **casado** na build de destino. O
cabeçalho diz quantos ficaram de fora e por quê:

```
funcoes no dump ........ 179400
RESOLVIDAS ............. 128360 (43.16%)
  nao moveram ..........   756
  casadas por assinatura 76670
fora, e o motivo:
  ambiguas .............  29485   (mais de uma candidata)
  sem par ..............  33904   (o compilador reordenou as instrucoes)
  assinatura fraca .....  38585   (comeca com operando que depende do lugar)
campos ignorados ....... 616825   (vao pela reflexao)
```

**São 43%, e são verdade.** Uma tabela que mente é pior que tabela nenhuma: sem
tabela o plugin recusa; com tabela errada ele chama o endereço errado, e o erro
não dá erro.

---

## Campo não entra de jeito nenhum

**616.825 offsets de campo ignorados.** O porte não mede campo — ele só carrega
o número da build antiga — e offset de campo muda entre builds sem dar erro.

E não precisamos deles: a reflexão acha campo por **nome**, exato, e não
apodrece. Ver `docs/OS-HEADERS-DO-JOGO.md`.

---

## Como se usa

Pelo header da classe, que é o caminho normal:

```cpp
brz::jogo::APrimalStructure e(api, obj);
void* fn = e.endereco_TakeDamage();
if (!fn) { /* não está nesta build — recuse */ }
```

Ou direto, pela chave exata que o binário declara:

```cpp
void* fn = api->EnderecoDoSimbolo(
    "APrimalStructure.TakeDamage(float,FDamageEvent&,AController*,AActor*)");
```

**Devolve nulo quando o símbolo não está na tabela desta build.** Isso é o
projeto, não uma falha: recebendo nulo, recuse. Chamar nulo derruba o servidor.

O log nomeia o símbolo e o plugin que pediu, **uma vez por chave** — log cheio
esconde tanto quanto log vazio.

E antes de devolver qualquer endereço, a API confere que ele aponta para **página
executável**. É a quinta checagem, a mesma que faltava no dia em que um servidor
desta VPS caiu com jogador dentro.

---

## Como se mede um símbolo isolado

Quando você quer só um, e quer ver o raciocínio:

```bash
./ferramentas/medir-ancora.py --calibrar
./ferramentas/medir-ancora.py 'APrimalStructure.TakeDamage('
```

A calibração mede as nove âncoras cuja resposta já se conhece: **7 corretas ·
2 recusadas · 0 erradas**. As duas recusadas o compilador reordenou entre as
builds, e nenhum comprimento de assinatura as recupera — medem-se à mão.

**Uma resposta errada é o único resultado inaceitável.** Recusa não é erro:
âncora faltando é um plugin a menos; âncora errada é um desvio instalado no meio
de outra função.
