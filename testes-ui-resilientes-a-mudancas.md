<img src="https://media2.dev.to/dynamic/image/width=1000,height=420,fit=cover,gravity=auto,format=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2Fc3uycui47h92h09egg1y.png" />

# Testes de UI resilientes a mudanças

> Ref: [Kent C. Dodds: Making your UI tests resilient to change](https://kentcdodds.com/blog/making-your-ui-tests-resilient-to-change)

> Ref: [Autify Blog: Why you shouldn’t use ids in E2E testing](https://nocode.autify.com/blog/why-id-should-not-be-used)

> Ref: [Cypress: Selecting Elements](https://docs.cypress.io/app/core-concepts/best-practices#Selecting-Elements)

Evitar buscar elementos pela tag genérica <button> ou por classes, que estão sujeitas a alterações.

O ideal é fazer o teste o mais próximo possível de como o usuário faria.

O usuário não vai abrir o Dev Tools e procurar o elemento com o ID ou nome username-field, ele vai procurar o campo de usuário.

O cenário de teste mais próximo do que o usuário faria é:

1. Digitar o nome de usuário no campo com a label "Nome de usuário"
2. Digitar a senha no campo com a label "Senha"
3. Clicar no botão que tem o texto "Entrar"

Mas nem sempre é possível seguir os passos do usuário dessa forma, sem falar que se basear no texto também está sujeito a alterações (o campo "nome de usuário" pode mudar para "Usuário").

Buscar pelo ID ou class é interessante, mas se um sistema que utiliza Bootstrap (`.btn-primary`) passa a utilizar Styled Components, ou Material UI, ou Tailwind... é preciso refazer todos os testes para adaptar às novas classes.

Se reutilizarmos nomes de classes por conta de estilo, ou alterarmos nomes de casses ou até mesmo a estrutura de componentes (uma `<div>` ou `<span>` a mais ou a menos), a cada alteração, o teste precisa ser reescrito, indicando um teste fraco e falho.

O problema principal é que a relação entre o código e o teste está muito implícita. É necessário superar essa dificuldade tornando a relação mais explícita.

Então a forma ideal é **criar um data-attribute como data-test para identificar os elementos que devem ser testados**, garantindo que o data-attribute não será utilizado para nenhum outro propósito.

Adicionando "metadata" aos elementos que serão explicitamente testados. Tornando a relação entre o código e o teste mais explícita e o desenvolvedor pode alterar ids, classes e estruturas sem dificuldade, desde que mantendo a indicação do teste explícita com o data-attribute .

```
<button
    type="submit"
    name="submit"
    id="form1234-submt"
    class="btn btn-primary"
    data-test="login-btn-submit"> ✅
    Enviar
</button>
```

## Mais código para escrever?

Embora seja a prática ideal, os desenvolvedores têm que se preocupar agora, além dos Ids , também com os data-test para manter a compatibilidade entre o código e o teste, evitando repeti-los ou removê-los ou alterá-los em refatorações de código ou criação de novos.

Uma possibilidade para diminuir riscos e ainda manter a referência é identificar componentes maiores com o data-attribute , em vez de identificar todos os componentes individualmente.

Talvez escrever:

```
<div class="modal" tabindex="-1" role="dialog" 👉🏻data-test="modal"👈🏻>
  <div class="modal-dialog" role="document">
    <div class="modal-content">
      <div class="modal-header">        
        <h5 class="modal-title">Confirm</h5>
          <button
            type="button"
            class="close"
            data-dismiss="modal"
            aria-label="Close">
          <span aria-hidden="true">&times;</span>        
        </button>      
      </div>      
      <div class="modal-body">
        <p>Really submit?</p>     
      </div>
      <div class="modal-footer">        
        <button
          type="button"
          class="btn btn-primary">
          Submit
        </button>
        <button
          type="button"
          class="btn btn-secondary"
          data-dismiss="modal">
            Cancel
        </button>
      </div>   
    </div> 
  </div>
</div>
```

Em vez de :

```
<div class="modal" tabindex="-1" role="dialog" 👉🏻data-test="modal"👈🏻>
    <div class="modal-dialog" role="document">
        <div class="modal-content">
            <div class="modal-header">        
                <h5 class="modal-title" 👉🏻data-test="modal-title"👈🏻>Confirm</h5>
                <button
                    type="button"
                    class="close"
                    data-dismiss="modal"
                    aria-label="Close"
                   👉🏻data-test="modal-close"👈🏻>
                    <span aria-hidden="true">&times;</span>        
                </button>      
            </div>      
            <div class="modal-body" 👉🏻data-test="modal-body"👈🏻>
                <p>Really submit?</p>     
            </div>
            <div class="modal-footer">        
                 <button
                     type="button"
                     class="btn btn-primary"
                    👉🏻data-test="modal-btn-submit"👈🏻>
                     Submit</button>        
                 <button
                     type="button"
                     class="btn btn-secondary"
                     data-dismiss="modal"
                    👉🏻data-test="modal-btn-cancel"👈🏻>Cancel</button>
            </div>   
        </div> 
    </div>
</div>
```
Assim é possível se basear no componente maior para localizar os itens internos:

```
this.page.locator('data-test=modal >> button:has-text("Submit")')
```

O código acima busca somente o botão com o texto "Submit" que está dentro do elemento com o data-test=modal , ignorando outros botões, possivelmente com o mesmo texto.

Dependendo do formato e tamanho do projeto, isso pode ser útil.

## Suja o código?

Se houver alguma preocupação com o fato de colocar em produção data-attributes que indiquem os testes (não é um grande problema, mas...), é possível remover esses atributos de produção com plugins como o babel-plugin-react-remove-properties.

## Boas práticas do Cypress

Utilização de atributos data-* provém melhor contexto aos seletores, isolando das alterações de CSS e JavaScript.

- Evitar localizar elementos por tags , classes e Ids , altamente sujeitos a mudanças.
- É interessante localizar objetos pelo texto, se o conteúdo do texto fizer parte do cenário de teste, evitar se for um objeto que o texto pode ser alterado e não faça sentido para o teste.
- Utilizar data-attributes facilita a identificação dos elementos exclusiva para testes, isolando de alterações de HTML, JS e CSS.

## Quando utilizar a localização por texto?

```
await page.locator('text=Log in').click();
```

Se o conteúdo (texto) do elemento alterar, você quer que o teste falhe?

- Se SIM: localizar o elemento por texto
- Se NÃO: localizar o elemento por data-attribute

## Possível conclusão

- O que deve ser imutável em uma aplicação não são suas casses ou Ids, mas o comportamento.
- Quando o comportamento é alterado, o teste E2E deve detectar isso.
- Testes E2E não devem travar o desenvolvimento além do comportamento.

Considerando isso, semelhante ao indicado nas boas práticas do Cypress, é interessante utilizar a localização por texto (quando necessário) e um pouco mais de `data-attribute`.

- Localizar elementos por texto, quando necessário
- Usar `data-attributes`, preferencialmente para identificar componentes UI e não todos os elementos individualmente.

Vantagens:

- Não emprega Ids e classes internas, garantindo a manutenibilidade do código.
- Comparado ao uso massivo de `data-attributes`, diminui o custo de desenvolvimento e manutenção de código.
