<img src="https://media2.dev.to/dynamic/image/width=1000,height=420,fit=cover,gravity=auto,format=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2F0m3bbmdv37dvxl2k7qol.png">

# Teste E2E (end-to-end)

O teste end-to-end é uma metodologia utilizada para testar se o fluxo de um aplicativo está sendo executado conforme o projeto do início ao fim. O objetivo da realização de testes end-to-end é identificar dependências do sistema e garantir que a informação certa seja passada entre vários componentes e sistemas.

Resumindo, o teste end-to-end é um forma de realizar testes nas quais visam provar o sistema de uma forma mais completa simulando o ambiente real.

Os testes E2E são críticos para monitorar fluxos de trabalho chaves de uma aplicação — como criar uma conta ou adicionar itens num carrinho de compras — e garantir que os usuários não encontrem funcionalidades quebradas.

É importante criar um plano de testes que provêm uma cobertura apropriada e fácil de adotar e manter a longo prazo. Veja nosso documento de Melhores práticas para criar testes E2E.

## Automação de teste E2E com Playwright
> https://playwright.dev/

### Vantagens dos Testes Automatizados no Front-End
- Testes de ponta a ponta, simulando todas as possíveis interações do usuário, sem dependência de interações manuais.
- Garantir qualidade após mudanças.
- Diminuição do tempo de testes antes de lançar uma nova versão.
- A cada nova funcionalidade lançada e testada manualmente, os testes automatizados garantem que nada que já funciona foi alterado.
- Revalidar todo o sistema a cada atualização do código.
- Ajuda a manter uma documentação viva.

### Vantagens do Playwright
- Executa nos principais navegadores: Chrome, Firefox, Webkit (Safari) e Edge.
- Simula dispositivos (Android, iOS) e resoluções.
- Auto-wait: com ele é possível tornar a escrita de testes mais simples, permitindo que o desenvolvedor se preocupe somente com as interações com o navegador, deixando o trabalho de aguardar os elementos totalmente com o framework.
- Recorder/CodeGen: o Playwright permite que o desenvolvedor execute as ações de um teste manualmente e essas ações são gravadas em forma de código, facilitando a criação dos testes. A ferramenta também possui um buscador de seletores (semelhante ao “inspecionar elemento” do DevTools) que permite validar os seletores escritos pelo desenvolvedor ou buscar novos seletores para escrever o teste.
- Possui mais ferramentas e tem melhor desempenho que outros frameworks de teste E2E.
