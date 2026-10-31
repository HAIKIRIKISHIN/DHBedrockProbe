# DH Bedrock Probe 0.1

Primeiro mod nativo de diagnóstico do projeto Distant Horizons para Bedrock Android.

## Objetivo desta versão

- Confirmar que o LeviLauncher/Preloader carrega uma biblioteca nativa ARM64.
- Registrar as fases `load`, `enable`, `disable` e `unload`.
- Criar uma pasta `data` própria do mod.
- Gerar um artefato `.levipack` por GitHub Actions.

**Esta versão NÃO instala hooks gráficos, não lê memória do Minecraft e não desenha geometria.**
É deliberadamente mínima para validar o carregamento antes de adicionar qualquer integração de renderer.

## Base técnica

- LeviLauncher Android + Preloader Android.
- API pública `pl/Mod.hpp`.
- Preloader fixado em `0.2.2`.
- ABI `arm64-v8a`.
- Android API 28 como mínimo de compilação.
- NDK `28.2.13676358`.

## Como compilar na nuvem

1. Crie um repositório GitHub vazio.
2. Envie todos os arquivos desta pasta para a branch `main`.
3. Abra a aba **Actions** do repositório.
4. Execute **Build DH Bedrock Probe** com `Run workflow` (ou faça um push).
5. Quando terminar, abra a execução e baixe o artefato `DHBedrockProbe-arm64`.
6. Importe o `.levipack` no LeviLauncher.

O workflow usa GitHub-hosted runner e não precisa de Android Studio no celular.

## Teste inicial

Faça backup dos mundos antes de testar qualquer launcher/mod nativo. Use uma instalação/instância separada se disponível.

Resultado esperado no primeiro teste:
- o mod aparece na lista de mods nativos;
- o carregamento não fecha o jogo;
- os logs mostram `load` e `enable`;
- ao desativar/fechar, aparecem `disable` e `unload` quando o launcher executa essas fases.

## Próximas etapas

- P0: validar carregamento e lifecycle.
- P1: diagnóstico de contexto EGL e callback de frame, ainda sem desenho.
- P2: triângulo de teste GLES com restauração de estado.
- P3: testar profundidade/oclusão.
- P4: obter câmera e coordenadas.
- P5: primeira montanha sintética.

## Licenças e terceiros

O projeto usa o SDK público Preloader Android, fixado pela tag `0.2.2`, como dependência externa via CMake FetchContent. Consulte a licença e os termos de cada dependência antes de redistribuir builds.
