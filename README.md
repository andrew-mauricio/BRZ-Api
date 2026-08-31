# BRZ Api

**A plugin API for ARK: Survival Ascended dedicated servers.**
No PDB. No offset table. No third-party CDN. It asks the running game.

[![runtime](https://img.shields.io/badge/runtime-proprietary-informational)](LICENSE)
[![sdk](https://img.shields.io/badge/SDK-MIT-green)](https://github.com/andrew-mauricio/BRZ-Api-SDK)
[![table](https://img.shields.io/badge/table-v2%20·%2089%20functions-blue)](docs/COBERTURA.md)
[![game](https://img.shields.io/badge/game%20build-25002750-lightgrey)](#)

*Português abaixo · [Portuguese below](#brz-api-em-português)*

---

## Install — three steps

1. Put **`winmm.dll`** next to `ArkAscendedServer.exe`, in
   `ShooterGame\Binaries\Win64\`.
2. Copy the **`Brz-Api\`** folder from this package next to it too. It carries
   `ancoras.txt` — thirteen addresses, and the only file that is specific to a
   game build. **Without it the API loads, refuses to initialise, and no plugin
   runs** — on purpose: reading the wrong memory quietly is worse than not
   loading.
3. Rename the **original** `winmm.dll` of that folder to **`winmm_orig.dll`**.
   (On Windows there is none there — copy it from `C:\Windows\System32\`.)

Start the server. `Brz-Api\Logs\BrzApi.log` appears, and an empty `Plugins\`.

**The game updated?** Only `ancoras.txt` changes — the API does not recompile,
and neither do your plugins. The file names the build it was measured for, and
the API refuses to start when its own proofs fail, so a stale file cannot pass
silently.

**Running under Wine?** Wine ships its own `winmm`, so it has to be told to
prefer ours:

```
WINEDLLOVERRIDES=winmm=n,b
```

Drop a plugin into `Brz-Api\Plugins\MyPlugin\MyPlugin.dll` and restart. The log
tells you what loaded, and what did not, and why.

---

## What it does differently

| | the usual way | BRZ Api |
|---|---|---|
| **where addresses come from** | a table of ~555,000 offsets, downloaded from a third-party CDN and keyed by the exe's hash | **live reflection** — the running game is asked |
| **game updates** | rebuild every plugin | replace a **two-line text file** |
| **what a plugin includes** | hundreds of C++ headers, an import library, vcpkg | **one header**, no library |
| **which compiler** | must match the API's, exactly | **any** — the boundary is plain C |
| **a plugin misbehaves** | it can take the server with it | contained, quarantined, and named in the log |

The first row is why this exists. If the CDN is down, changes format, or simply
does not publish a cache for the next build, an offset-table API has no
addresses at all. This one asks the game.

## What it refuses to do

These are design decisions, not gaps:

- **It does not unload or reload a running plugin.** A plugin in use has hooks
  armed and tasks scheduled pointing into its own DLL. Freeing that code makes
  the game jump into unmapped memory *later*, far from the cause, and the log
  will not point back. Installing a **new** plugin without a restart is
  supported.
- **It does not guess instruction lengths.** The decoder covers what appears in
  an MSVC prologue and **refuses** everything else. A refused hook is an
  inconvenience; a guessed one is a crashed server with no log.
- **It does not invent results.** A function that cannot do its job returns
  failure and writes to the log *which* function it was and *which* plugin
  asked.
- **It does not allocate from the wrong heap.** An `FString` handed to the game
  uses the game's own allocator.

## Where things go

```
ShooterGame\Binaries\Win64\
    winmm.dll                 ← this
    winmm_orig.dll            ← the real one, renamed
    Brz-Api\
        ancoras.txt           the anchor addresses for this game build
        Logs\BrzApi.log
        Plugins\
            MyPlugin\
                MyPlugin.dll        (the .dll must match the folder name)
                PluginInfo.json
                config.json
```

## Writing plugins

Everything you need is in **[BRZ-Api-SDK](https://github.com/andrew-mauricio/BRZ-Api-SDK)**
— one header, MIT, with a Visual Studio walkthrough and working examples.

## Licence

The runtime and loader in this repository are under a **proprietary licence**
([`LICENSE`](LICENSE)). In short: **write and sell plugins freely**, run the API
on as many servers as you like — but do not sell the API, do not re-host the
files (share the link, not the file), and do not use this material to build
another plugin API for ARK.

The SDK — the header and the examples, everything you actually touch — is
**MIT**.

---

# BRZ Api (em português)

**Uma API de plugins para servidores dedicados de ARK: Survival Ascended.**
Sem PDB, sem tabela de offsets, sem CDN de terceiro. Ela pergunta ao jogo
rodando.

## Instalar — três passos

1. Ponha o **`winmm.dll`** ao lado do `ArkAscendedServer.exe`, em
   `ShooterGame\Binaries\Win64\`.
2. Copie a pasta **`Brz-Api\`** deste pacote para lá também. Ela traz o
   `ancoras.txt` — treze endereços, e o único arquivo que é específico de uma
   build do jogo. **Sem ele a API carrega, recusa iniciar e nenhum plugin
   roda** — de propósito: ler memória errada calado é pior que não carregar.
3. Renomeie a `winmm.dll` **original** daquela pasta para **`winmm_orig.dll`**.

Suba o servidor. Aparece `Brz-Api\Logs\BrzApi.log` e um `Plugins\` vazio.

**Sob Wine**, é preciso mandar preferir a nossa: `WINEDLLOVERRIDES=winmm=n,b`.

**O jogo atualizou?** Só o `ancoras.txt` muda — a API não recompila, e os seus
plugins também não. O arquivo diz para qual build foi medido, e a API se recusa
a iniciar quando as próprias provas não passam: um arquivo velho não passa
calado.

## A diferença que mais importa

Uma API que depende de tabela de offsets baixada de um serviço de terceiro fica
**sem endereço nenhum** no dia em que aquele serviço sai do ar, muda de formato
ou não publica o cache da build nova. Esta pergunta ao jogo.

Na prática, para quem administra: **atualizou o ARK, os seus plugins continuam
os mesmos.** Troca-se um arquivo de duas linhas.

## O que ela se recusa a fazer

Isto é desenho, não falta:

- **Não recarrega plugin em uso.** Liberar o código de um plugin com hook armado
  faz o jogo saltar para memória desmapeada *depois*, longe da causa. Instalar
  plugin **novo** sem reiniciar funciona.
- **Não chuta comprimento de instrução.** Recusar um hook é aborrecimento;
  chutar é servidor caído sem log.
- **Não devolve resultado inventado.** Quem não consegue fazer o trabalho
  devolve falha e diz no log qual função era e qual plugin pediu.
- **Não aloca do heap errado.** Uma `FString` entregue ao jogo usa o alocador
  **dele**.

## Licença

Runtime e carregador: **proprietária** ([`LICENSE`](LICENSE)). Você pode
escrever e **vender** os seus plugins livremente, e rodar a API em quantos
servidores quiser — inclusive pagos. Não pode vender a API, re-hospedar os
arquivos (compartilhe o link, não o arquivo), nem usar este material para fazer
outra API de plugins para ARK.

O SDK — o header e os exemplos, tudo o que você toca — é **MIT**.

---

> ARK: Survival Ascended is a trademark and property of Studio Wildcard. This
> project is independent and has no affiliation with, sponsorship from, or
> approval by Studio Wildcard or Snail Games.
