<img src="https://media2.dev.to/dynamic/image/width=1000,height=420,fit=cover,gravity=auto,format=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2Ft19zssbvpwv66xi7pdkt.png" />

# Melhores práticas para criação e automação de testes E2E

> Ref: [Best practices for creating end-to-end tests](https://www.datadoghq.com/blog/test-creation-best-practices/)

Os testes E2E são críticos para monitorar fluxos de trabalho chaves de uma aplicação — como criar uma conta ou adicionar itens num carrinho de compras — e garantir que os usuários não encontrem funcionalidades quebradas.

É importante criar um plano de testes que provêm uma cobertura apropriada e fácil de adotar e manter a longo prazo.

As melhores práticas para criação de testes E2E incluem:
- Definir a cobertura de testes
- Criar testes eficientes e significativos
- Desenhar suítes de teste coerentes e fáceis de navegar
- Criar notificações que permitam que o time responda aos problemas rapidamente

## Definir a cobertura de testes
Antes de começar a criar os testes, é importante considerar os fluxos da aplicação que devem ser testados. Nem todos os fluxos da aplicação cabem nos testes E2E.

Não deixar os testes muito grandes, complexos e de difícil manutenção.

A cobertura de testes deve ser representativa para os usuários e como eles usam a aplicação.

**Identificar e priorizar os fluxos de trabalho mais utilizados da aplicação**. Assim, colocando no escopo das suítes de teste o que traz mais valor para o negócio.

Essa abordagem ajudará a alcançar melhor balanço entre cobertura de testes e manutenibilidade.

Fluxos-chave de uma aplicação podem incluir:
- Criar ou entrar numa conta.
- Agendar um voo.
- Adicionar itens num carrinho de compras.
- Ver a lista de empregados de uma companhia.
- Fazer um depósito na conta.

Interessante descobrir quais os principais fluxos utilizados na aplicação. Ferramentas como RUM (Real User Monitoring) da Datadog, ou mesmo acompanhar o Google Analytics. Ver quais as URLs mais visitadas, quais os fluxos mais utilizados.

Além de testar os fluxos mais populares, é importante **criar testes para os fluxos não tão utilizados**, mas ainda assim críticos (como recuperar a senha ou editar o perfil). Criar testes para esses fluxos permite identificar problemas de funcionalidade antes que os clientes reclamem.

## Criar testes eficientes e significativos para fluxos chave
Sem um bom plano de testes de como abordar um fluxo de trabalho específico, os testes podem se tornar complexos, com um número grande de passos, dependências e asserções desnecessárias, aumentando as falhas e tempo de execução, tornando mais difícil de resolver e responder aos problemas rapidamente.

Boas práticas para suítes de testes mais eficientes:

- Quebrar fluxos de trabalho em testes menores
- Criar asserções significativas para verificar o comportamento esperado
- Criar testes que se adaptem a fatores fora do nosso controle
- Manter o estado da aplicação para que testes possam ser executados mais de uma vez sem alterar o resultado

### Quebrar fluxos de trabalho em testes menores

É importante criar testes que não dupliquem passos.

**Separar fluxos de teste em testes menores e mais focados** ajuda a manter o escopo, reduz pontos de manutenção e facilita encontrar erros e falhas.

Por exemplo: um teste que verifica a funcionalidade de carrinho de compras, não precisa incluir os passos de criar uma conta. Se o teste falhar, você não vai querer perder tempo tentando descobrir onde ocorreu a falha: no carrinho ou na hora de criar a conta.

**Separar testes menores que podem ser reutilizados.**

Utilizar a prática _DRY (don’t repeat yourself)_ e _Page Object Models (POM)_, onde organizamos os itens e ações de uma página ou componente em um arquivo separado, que serão chamados nos testes definitivos.

Criar componentes de teste, da mesma forma que separamos componentes em front-end. Em caso de falhas, basta localizar e reparar apenas aquele componente e não uma suíte inteira de testes.

### Criar asserções significativas para verificar o comportamento esperado

Construir suítes de testes eficientes é garantir que os testes verifiquem comportamentos importantes da aplicação.

As asserções do teste são passos que descrevem o fluxo lógico de uma ação na aplicação. Elas adicionam valor aos testes imitando o que os usuários esperam ver quando eles interagem com a aplicação.

Exemplo: uma asserção pode confirmar que o usuário é redirecionado para a página inicial e vê uma mensagem de boas-vindas após fazer o login.

Asserções comumente usadas em testes:

- Um elemento tem (ou não tem) determinado conteúdo.
- Um elemento ou texto está presente (ou não) em uma página.
- Uma URL contém um determinado texto, número ou expressão regular.
- Um arquivo foi baixado.
- Um e-mail foi enviado e inclui determinado texto.

É importante incluir apenas asserções que imitam o que o usuário espera que aconteça num fluxo de trabalho.

Um teste da funcionalidade de compra, por exemplo, apenas precisa incluir asserções que são relevantes à compra, como verificar a confirmação.

### Criar testes que se adaptem a fatores fora do nosso controle

Teste geralmente falham se a aplicação demorar para carregar. O tempo de carregamento pode ser afetado por fatores como uma inesperada falha na rede ou uma onda incomum de tráfego que pode sobrecarregar o servidor.

Inconsistências de demora no carregamento podem levar a falsos positivos, e adicionar tempos de espera manualmente no teste (hard coding) pode deixar os testes menos confiáveis.

Uma melhor prática de garantir que os testes se adaptem a flutuações de tempo é desenhá-los para **esperar automaticamente** até que uma página esteja pronta para interagir antes de executar (ou refazer) um passo de teste.

Isso não apenas elimina a necessidade de adicionar esperas no código, como também permite focar em falhas legítimas no ambiente de produção.

```
//exemplo no Playwright: aguardando o estado "network idle" antes de testar

async navigate(): Promise<void> {
    const url = await this.page.url();
    if (url != this.baseUrl) {
        await this.page.goto(this.baseUrl);
    }
    await this.page.waitForLoadState('networkidle');
}

await frame.click('button');  // o clique aciona a execução
await frame.waitForLoadState();  // espera pelo estado de "carregado"
```

> Ref: [Playwright: waitForLoadState](https://playwright.dev/docs/api/class-frame#frame-wait-for-load-state)

O Playwright realiza uma série de verificações de ação nos elementos antes de executar ações para garantir que essas ações se comportem conforme o esperado. Ele espera automaticamente que todas as verificações relevantes sejam aprovadas e só então executa a ação solicitada. Se as verificações necessárias não passarem dentro do `timeout` fornecido, a ação falhará com o `TimeoutError`.

Por exemplo, para `page.click()`, o Playwright garantirá que:

- o elemento está presente no DOM
- o elemento está visível
- o elemento está estável, não está sendo animado ou terminou a animação
- o elemento pode receber um evento/ação, não está ofuscado por outros elementos
- o elemento está habilitado

> Ref: [Playwright: Auto-waiting](https://playwright.dev/docs/actionability)

### Manter o estado da aplicação para que testes possam ser executados mais de uma vez sem alterar o resultado

Testes dependem de dados limpos para recriar os passos para cada execução. É importante ter configurações dedicadas aos testes (credenciais de login, ambientes, cookies) que são usadas somente nos testes.

É importante criar testes que possam ser executados sem serem afetados pelos seus próprios resultados, os testes devem deixar o estado da aplicação e ambiente de teste sem alteração antes e depois da execução.

Um teste que foca em adicionar múltiplos itens em um carrinho de compras, por exemplo, deve possuir um mecanismo que exclua os itens do carrinho depois.

É interessante ter um recurso que consiga apagar do banco de dados as transações executadas pelos testes.

Também é possível ter subtestes que limpem a execução dos testes após cada execução (`afterAll`).

```
test.beforeAll(async ({ browser }) => {
    page = await browser.newPage();
    homePage = new HomePage(page, BASE_URL);
    await homePage.navigate();
});

test('do tests', async () => {
    // faz os testes
});

test.afterAll(async () => {
    await homePage.clean(); // volta ao estado original
    await page.close();
});
```

## Desenhar suítes de teste coerentes e fáceis de navegar

### Configurar testes por ambiente

Cada ambiente que é utilizado para testar as funcionalidades durante o desenvolvimento podem requerer configurações diferentes para rodar os testes.

Por exemplo: testes em ambiente de desenvolvimento talvez só precisem executar em um dispositivo, enquanto testes em pré-produção requerem vários diferentes tipos de dispositivos.

O ideal é separar os testes por ambiente para garantir que serão executados somente o necessário para cada ambiente.

Outra dica é identificar nos títulos dos testes o ambiente/dispositivo testado, facilitando a identificação e/ou correção quando um teste falhar.

Importante configurar variáveis de ambiente quando colocar os testes em integração contínua (CI).

### Organizar testes por localidade, dispositivos e mais

Encontrar maneiras de organizar e categorizar os testes é um outro aspecto chave para desenvolver suítes de testes coerentes.

**Desenhando uma boa estrutura organizacional** permite gerenciar facilmente as suítes de teste enquanto eles aumentam.

Uma forma de adicionar estrutura aos testes é incorporar metadados ou etiquetas como tipo de teste, ambiente e dispositivo. Etiquetas (tags) provém melhor contexto sobre o que está sendo testado, facilitando encontrar quais fluxos de trabalho estão quebrando.

Apenas adicionando o ambiente no título do teste ajuda identificar onde o teste está executando. Tags também permite categorizar os testes com atributos chave no momento de criar os testes.

Adicionar metadados e tags aos testes é especialmente útil se você gerenciar uma suíte muito grande de testes e precisa revisar apenas uma parte deles. Por exemplo, pode usar uma tag “team” para buscar e executar testes de um time que desenvolveu uma funcionalidade ou a tag “site” para encontrar os testes associados a um site específico.

Interessante utilizar tags nos testes (o Playwright permite) para identificar e executar facilmente apenas os testes prioritários (smoke tests), ou talvez testes relacionados a uma sprint ou funcionalidade.

Escrevendo tags no Playwright:

```
import { test, expect } from '@playwright/test';

test('Test login page @fast', async ({ page }) => {
    // ...
});

test('Test full report @slow', async ({ page }) => {
    // ...
});
```

Você poderá então rodar apenas os testes com uma determinada a tag:

```
npx playwright test --grep @fast
```

Ou se quiser o contrário, pular os testes com uma determinada tag:

```
npx playwright test --grep-invert @slow
```

> Ref: [Playwright: Tag tests](https://playwright.dev/docs/test-annotations#tag-tests)

## Criar notificações que permitam que o time responda aos problemas rapidamente

Independentemente de como você estrutura os testes ou onde os executa, é importante que o time esteja apto para acessar rapidamente as falhas dos testes, assim como saberem quando a suíte de testes finalizou a execução com sucesso.

Se você está executando testes de um serviço crítico em produção, os desenvolvedores deverão saber o estado e resultado dos testes assim que possível. Se todos os testes passaram, eles poderão seguir adiante com o desenvolvimento. Se há falhas, eles precisam ser notificados imediatamente para resolver os problemas rapidamente.

Relatórios de teste geralmente são difíceis de analisar, especialmente se você adiciona mais testes à suíte. Se executar os testes em diferentes dispositivos, você precisará ver vários diferentes relatórios para encontrar falhas em cada dispositivo. Perdendo mais tempos navegando no resultado dos testes do que em encontrar e consertar o problema.

O ideal seria **construir notificações que automaticamente consolidem** e encaminhem os resultados para os times apropriados, provendo o contexto necessário sobre o que foi testado.

**Interessante configurar que o teste (CI) notifique por e-mail ou envie para grupos no Teams**, por exemplo.
