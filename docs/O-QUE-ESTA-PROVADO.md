# O que está provado, e como

Este documento existe porque a pergunta *"a API está pronta?"* não tem resposta
de uma palavra, e responder "sim" sem qualificar já custou noites de servidor
fora do ar nesta VPS.

Aqui está o que foi **medido**, contra o quê, e — a parte que mais importa — **o
que cada prova não pode afirmar**.

---

## Provado contra o binário do jogo, sem rodar nada

| o quê | quantos | contra o quê |
|---|---|---|
| constantes de layout da engine | **28 de 28** | offsets reais do dump |
| bytes de cada desvio | **11 de 11** | `objdump` no exe da build viva |
| assinaturas de função desviada | **13 de 13** | as 796.225 chaves |
| funções da tabela | **138 de 138** | o header, uma a uma |
| nomes que os plugins pedem ao jogo | **48** | o dump + os 7.006 bitfields |
| nomes nos headers gerados | **32.103** | o dump |
| retornos de três valores | **41 chamadas** | leitura do código |
| caminhos quentes | **6 raízes** | enumeração da cadeia de chamadas |
| o decodificador x o `objdump` | **os 11 desvios** | as duas medem, e concordam |
| relocação de `[rip+...]` | **4.360 conferidas** | relocadas de verdade, `objdump` dos dois lados |
| bytes de cada âncora | **16 de 16** | 32 bytes, contra o exe que roda |
| chamadas por nome | **16** | o `exec` do dump prova UFunction |
| máscaras de bitfield | **7 usos** | o cache de 6.537 bitfields |
| offset usado no mesmo objeto | **47 usos** | leitura do código |
| a tabela x a API da comunidade | **37 de 37** | a `IApiUtils.h` deles |
| `MinApiVersion` x o que se chama | **24 plugins** | o mapa versão→função |
| os números da documentação | **14 afirmações** | o artefato que produz cada um |

`./ferramentas/calibrar.sh` roda tudo: **536 verdes**.

**Cada guarda foi calibrada** — planta-se o defeito que ela deve pegar e exige-se
a acusação. Uma guarda que nunca acusou nada não é uma guarda; é um enfeite.

---

## Provado rodando, sem o jogo

- **Carrega num processo Windows real**, sob Wine: 13 checagens, incluindo a
  quarentena exercitada com um plugin que **cai de propósito**
  (`ferramentas/plugins-de-teste/QuedaProposital`).
- **Os 22 plugins compilam no Visual Studio de verdade**, no PC —
  `ferramentas/compilar-no-pc.sh`. "Compilou no MinGW" prova que compila no
  MinGW, e mais nada: MSVC e MinGW discordam do layout de `std::string`, de
  `std::function` e de vtable.

---

## Provado no ARK rodando

- a reflexão passa nas **quatro provas** com o jogo vivo: 1,8 milhão de objetos,
  `FName` decodificando com 299 de 300 nomes plausíveis;
- os **22 plugins carregam**, 0 falhas;
- os desvios de chat, console e RCON **armam**;
- o servidor **anuncia para join**.

---

## O que NÃO está provado

**Nenhum comando de plugin respondeu na tela de um jogador.** Isso é o que
falta, e é o que decide.

O histórico obriga a dizer isso com todas as letras: já houve arranque com log
inteiramente verde — 22 plugins, 0 falhas, todos os desvios armados — e na tela
não funcionava nada. A causa era a assinatura do chat declarada com 2 parâmetros
onde o jogo tem 4; os dois que faltavam chegam em `R8` e `R9`, e a nossa função
os sobrescrevia antes de chamar a original.

Aquele defeito hoje é impossível: `conferir-assinaturas.py` compara as 13
assinaturas contra o binário. **Mas "aquele defeito é impossível" não é "não há
defeito".**

E a frase acima se provou literal em 01/09/2026. Cruzando as chamadas por nome
contra o dump, apareceram **oito** funções que a reflexão nunca poderia achar —
e três delas explicam o silêncio na tela melhor que o defeito do chat:

| o que estava escrito | o que o jogo declara | o que acontecia |
|---|---|---|
| `ClientServerChatMessage`, 1 argumento | 3: texto, cor, negrito | a cor chegava `(0,0,0,0)` — preto com **alfa zero**, invisível |
| `ClientServerNotification` | não existe; a UFunction é `...Single`, com 8 | a chamada devolvia 0, sempre |
| `K2_SetActorLocation`, 3 argumentos | 4, e o `bTeleport` ficava em zero | teleporte virava "andar até lá", que a colisão barra |

O bloco de parâmetros é **zerado** antes de escrever. Argumento que falta não
vira lixo: vira **zero** — e zero é valor legítimo para cor, para booleano e
para ponteiro. Por isso a chamada "dá certo" e o efeito não acontece.

Mais três, do mesmo tipo, que nunca funcionaram desde o primeiro dia:

- `EOSIDdoJogador` lia um campo chamado `EOSId`, que **não existe** em nenhuma
  das 796.225 chaves do dump. O EOS ID é a chave de casa, loja e permissão.
- `MoverItem` chamava `TransferItemToInventory`, que não tem `exec` — e com o
  parâmetro errado: o primeiro é o inventário de **origem**, não o item.
- O cofre (`Vault`) chamava `GetItemBytes` e `AddItemObject`, as duas sem `exec`.

E, transversal a tudo: **todo** `LerBit`/`EscreverBit` da árvore passava a
máscara `1`. `APrimalCharacter.bIsDead` mora no bit **5**;
`bReplicatedIsSubmerged` no bit **6**; `bNeutered` no bit **3**. Três dos cinco
campos nunca foram lidos do bit certo.

Nada disso dava erro. Todos passavam nas 178 provas que existiam.

O que mudou não foi a atenção — foi haver, agora, guardas que cruzam **o que o
código diz** com **o que o binário declara**: `conferir-chamadas-por-nome.py`,
`conferir-nomes-do-motor.py`, `conferir-bits.py`, `conferir-mesmo-objeto.py`. E
o motor passou a avisar, em execução, quando uma chamada recebe menos argumentos
do que a função declara.

---

## O que ficou fechado em 01/09/2026

**Nenhuma entrada da tabela promete no header e recusa no código.** Era o caso de
`DarItem`, `MoverItem`, `EOSIDdoJogador`, `AcharJogadorPorEOSID` e
`RodarComando` — cinco funções que existiam, compilavam, e devolviam zero desde
o primeiro dia. `conferir-tabela.py` guarda isso agora.

As quatro recusas que sobraram são **condicionais**, e as quatro condições estão
satisfeitas nesta build: `CriarTextoDoJogo` e `CriarTextoRicoDoJogo` precisam
das âncoras do alocador e do `FText::FromString`; `EnderecoIP` precisa da
`EnderecoDeRede`; `DarItem` precisa que `AddNewItem` esteja na reflexão. Todas
presentes.

E **as três entradas escritas por gente têm prova**: `PluginInfo.json` (quem
escreve o plugin), `config.json` (o dono do servidor) e a linha de comando (o
jogador). Nenhuma das três tinha um caso rodado contra ela antes.

---

## O que cada prova não pode afirmar

Isto não é modéstia; é o limite real de cada instrumento, e ignorá-lo é como não
ter o instrumento.

**O dump é da build 24159508; o servidor roda a 25002750.** As constantes
conferidas são da *engine* (`UObject`, `UStruct`, `FField`, `FNamePool`) e só
mudam quando a Unreal muda de versão — não a cada patch. Concordância é
evidência forte, não prova de execução.

**Existir no dump não prova que a classe certa tem o campo.** A reflexão procura
na classe do objeto e nas ancestrais; a conferência responde "existe alguma
classe com esse nome". **Não** existir prova que o nome está errado — e é esse o
lado que importa: o falso negativo é impossível.

**Compilar prova que o C++ é válido, não que os nomes existem.**
`campo_AbsoluteBaseLevel()` devolve uma *string*, e string errada compila.

**`conferir-custo.py` é enumeração, não medição.** Diz *que* há custo caro num
caminho quente e *onde* — não quantos milissegundos. Medir é a linha `[tempo]`
que o `DespachanteTick` escreve a cada 30 s com o servidor rodando:

```
[tempo] 1800 quadros em 30s (60.0/s). Dentro do nosso codigo:
        12 ms (0.04% da janela), pior batimento 3 ms.
```

Se o servidor lagar e esse número for ~0%, **o problema não é nosso** — e isso
vale tanto quanto descobrir que é.

**A tabela de símbolos resolve 71%**, e os outros 29% ficaram de fora
**nomeados**, com o motivo escrito: ambíguos, sem par, assinatura fraca.
`EnderecoDoSimbolo` devolve nulo para eles, de propósito.

E dentro dos 71% há **duas qualidades diferentes de verdade**, marcadas linha a
linha no arquivo:

- **sem marca** — o endereço veio de *casamento de bytes*: a assinatura da
  função na build velha foi achada, única, na build nova. Não há inferência.
- **`# ordem`** — era ambígua, e a *posição* entre as vizinhas já resolvidas
  separou a resposta. Isso é inferência, com taxa de erro **medida por
  retenção** (`gerar-simbolos.py --aferir` esconde respostas conhecidas e conta
  os erros). Pequena, e **não zero**.

O motor lê essa marca e avisa no log a primeira vez que cada símbolo inferido é
usado — para que um crash tenha primeiro suspeito. A marca existe porque
endereço plausível indistinguível do certo já custou caro nesta árvore: o
`port_sig.py` gravava o endereço da build **velha** quando não resolvia, e foram
188.822 deles.

---

## Por que as guardas existem, uma a uma

Cada uma nasceu de um defeito real, e o cabeçalho de cada ferramenta conta qual:

| guarda | o defeito que a criou |
|---|---|
| `conferir-assinaturas` | chat desviado com 2 parâmetros onde o jogo tem 4 |
| `conferir-layout` | `ChildProperties` é `0x50` aqui e `0x70` no Conan |
| `conferir-desvios` | patch não atômico: o prólogo sumia e o epílogo ficava |
| `conferir-plugins` | `bIsInWater` não existe; o campo é `bReplicatedIsSubmerged` |
| `conferir-tabela` | o header prometia limpeza no descarregamento que não existia |
| `conferir-custo` | 1,75 milhão de syscalls por segundo |
| `conferir-tresvalores` | `LerBit` devolve −1 e o header dizia que devolvia dois valores |
| `conferir-headers` | sobrecarga do jogo virando nome C++ duplicado |
| `versao-exigida` | `MinApiVersion` escrito à mão, mentindo para menos |
| `conferir-porte` | `grep` casando dentro do nome decorado do C++ |
| `conferir-decodificador` | o rodapé AFIRMAVA que o decodificador concorda com o `objdump`, e ninguém tinha medido |
| `conferir-relocacao` | `[rip+...]` era recusado; recusar custava 13,4% das funções do jogo |
| `conferir-ancoras` | o jogo atualiza, o RVA cai noutra função, e o desvio arma em silêncio |
| `conferir-simbolos` | a chave existe no dump e falta na tabela: compila, roda, devolve **nulo** |
| `conferir-alcance` | o porte perde uma função nativa e ela some **sem erro**: `EnderecoDoSimbolo` devolve nulo e o plugin conclui que ela não está nesta build |
| `conferir-reentrancia` | `RemoverComandoRcon` de dentro do próprio comando invalida o laço |
| `conferir-promessas` | `UsesRawOffsets` "transforma silêncio em recusa" — e a recusa nunca existiu |
| `conferir-retornos` | recusar só protege quem olha a recusa; nulo entregue ao jogo cai dentro do ARK |
| `conferir-buffers` | três funções de caminho num só buffer: o log mentia sobre arquivo |
| `conferir-texto` | todo acento virava `?` na entrada e `aÃ§Ã£o` na saída |
| `conferir-cobertura-asaapi` | a comparação com a API da comunidade era de memória |
| `conferir-nomes-do-motor` | o campo `EOSId` **não existe** — e é a chave de casa, loja e permissão |
| `conferir-documentacao` | "107 de 107" e "178 verdes" estavam certos no dia em que foram escritos |
| `conferir-bits` | toda a árvore passava máscara `1`, e `bIsDead` mora no bit 5 |
| `conferir-ordem-tabela` | campo novo no MEIO da struct: todo plugin compilado chama o vizinho |
| `conferir-minapiversion` | seis plugins passaram a exigir v12 e nenhum declarava |
| `conferir-chamadas-por-nome` | sem `exec` não é UFunction: `MoverItem` nunca moveu um item |
| `conferir-mesmo-objeto` | offset tirado do componente e usado no dino: lê outra coisa, sem erro |

E **três dessas guardas acusaram código correto na primeira versão** — 38 usos
certos, 12 funções que existiam, 2 bitfields que não faltavam. Todas foram
corrigidas antes de "consertar" o código acusado.

Guarda que acusa o certo é pior que guarda nenhuma: ela treina quem lê a ignorar
o vermelho.
