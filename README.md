<div align="center">

<img src="docs/icone.png" alt="Ícone do Kietto" width="96">

# Kietto

**Seu celular em silêncio, do jeito que você escolher.**
Filtra ligações indesejadas, protege suas horas de sono e de estudo e traz um despertador completo. Tudo roda no aparelho, sem conta e sem enviar nada para a internet.

![Android](https://img.shields.io/badge/Android-10_ou_superior-3DDC84?logo=android&logoColor=white)
![Java](https://img.shields.io/badge/Java-nativo-ED8B00?logo=openjdk&logoColor=white)
![Sem servidor](https://img.shields.io/badge/Dados-só_no_aparelho-5B6B38)
![Status](https://img.shields.io/badge/Versão-0.5.0_em_testes-6B7280)

> 🔒 **Código-fonte privado.** Este repositório é uma vitrine do projeto: o que ele faz, como foi construído
> e as decisões técnicas. Demonstração do código sob pedido.

</div>

![Telas do Kietto](docs/prancha.webp)

## O que o Kietto faz

| | |
|---|---|
| **Filtro de chamadas** | Bloqueia por número completo ou por prefixo (DDD e primeiros dígitos). A lista de permitidos sempre vence os bloqueios. |
| **Restringir quem pode ligar** | Só passam contatos da agenda e números permitidos. |
| **Modo Sono** | Horário diário, inclusive atravessando a meia-noite. Na hora de dormir, só toca quem você liberou. |
| **Foco / Estudo** | De 1 a 720 minutos: pausa a internet dos apps, silencia as notificações e rejeita ligações. Ao terminar, tudo volta sozinho. |
| **Bloqueio total** | Só a lista de permitidos passa, até você encerrar. |
| **Despertador** | Alarmes únicos ou por dia da semana, soneca de 5, 10 ou 15 minutos e botão de parar na tela bloqueada. |
| **Histórico e estatísticas** | Até 100 ligações analisadas, com data, hora e motivo, e o resumo dos últimos 7 dias. |
| **Backup e aparência** | Cópia manual das configurações em um arquivo e tema claro ou escuro. |

## Telas

| Início | Chamadas | Modo Sono |
|---|---|---|
| <img src="docs/telas/inicio.webp" width="260" alt="Tela inicial com o Modo Sono ativo"> | <img src="docs/telas/chamadas.webp" width="260" alt="Configuração do bloqueador de chamadas"> | <img src="docs/telas/sono.webp" width="260" alt="Modo Sono com horário de início e término"> |

| Despertador | Histórico | Estatísticas |
|---|---|---|
| <img src="docs/telas/alarmes.webp" width="260" alt="Lista de alarmes"> | <img src="docs/telas/historico.webp" width="260" alt="Histórico de ligações com o motivo"> | <img src="docs/telas/estatisticas.webp" width="260" alt="Bloqueios dos últimos 7 dias"> |

| Foco / Estudo | Notificações por app | Tema escuro |
|---|---|---|
| <img src="docs/telas/foco.webp" width="260" alt="Sessão de foco com duração"> | <img src="docs/telas/notificacoes.webp" width="260" alt="Controle de notificações de cada app"> | <img src="docs/telas/escuro.webp" width="260" alt="Tela inicial no tema escuro"> |

> Os números de telefone que aparecem são fictícios, usados nos testes.

## Como uma ligação é decidida

O Android entrega cada ligação recebida ao Kietto, que responde antes de o telefone tocar.
A ordem das regras é fixa e testada:

```mermaid
flowchart TD
    A[Ligação recebida] --> B{Foco ativo?}
    B -- sim --> X[Rejeita e registra: Foco / Estudo]
    B -- não --> C{Proteção ligada e sem pausa?}
    C -- não --> Y[Deixa tocar e registra o motivo]
    C -- sim --> D{Número na lista de permitidos?}
    D -- sim --> Y
    D -- não --> E{Número bloqueado ou prefixo bloqueado?}
    E -- sim --> X2[Rejeita e registra o motivo]
    E -- não --> F{Modo restrito, Sono ou Bloqueio total?}
    F -- não --> Y
    F -- sim --> G{É contato salvo?}
    G -- sim --> Y
    G -- não --> X2
```

Detalhes que dão trabalho e que o app trata:
- **Normalização de números:** `+55`, `0`, operadora, parênteses e espaços viram um formato único (DDD + número), e números 0800 e similares são reconhecidos como serviço.
- **Consulta à agenda com limite de tempo:** se a agenda demorar mais de 1,5 segundo, o app decide sem ela, para nunca segurar uma ligação.
- **Sempre registra o motivo:** cada ligação analisada guarda por que foi permitida ou bloqueada (ex.: "Prefixo bloqueado", "Modo Sono").

## Decisões técnicas

- **Privacidade por padrão:** regras, contatos e histórico ficam no aparelho. Não há conta, servidor, anúncio nem rastreamento.
- **Android nativo, sem frameworks:** Java direto sobre as APIs do sistema (`CallScreeningService`, `AlarmManager`, `VpnService`, `NotificationManager` e provedor de condições do Não Perturbe). O APK tem cerca de 200 KB.
- **Foco sem servidor:** a "pausa na internet" usa uma VPN local, que só interrompe o tráfego dos apps no próprio aparelho. Nada passa por servidor externo.
- **Interface feita em código:** componentes, ícones vetoriais e paleta próprios, com tema claro e escuro.
- **Permissões só quando precisa:** contatos, alarmes exatos, tela de alarme e notificações são pedidos no momento de uso, com explicação.
- **Alarme confiável:** alarmes exatos, tela de parada na tela bloqueada e religação depois de reiniciar o celular.
- **Compilação sem Gradle:** script em PowerShell que compila, alinha e assina o APK (JDK 21 e Android SDK 36).

## Em números

| | |
|---|---|
| Código | ~5 mil linhas em 24 arquivos Java |
| APK | ~210 KB |
| Versão mínima | Android 10 |
| Dados enviados à internet | nenhum |

## Tecnologias

**Java** · **Android SDK 36** · CallScreeningService · AlarmManager · VpnService · Notificações e Não Perturbe · PowerShell (build)

## Status

Versão 0.5.0, em testes no aparelho. Em preparação para a Google Play.

---

<div align="center">
Feito por <a href="https://github.com/lucianofonsecadev">Luciano Fonseca</a> · Brasília
</div>
