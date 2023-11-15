
## Titulo

Implemente uma versão do checkout da hotmart suportando a forma de pagamento via cartão de crédito, pix ou boleto.

## Descrição detalhada

Queremos implementar tudo que for necessário do lado da API para suportar o fluxo de checkout da Hotmart. A tela com as informações pode ser acessada através deste link => https://pay.hotmart.com/V23451860F. 

Agora vamos ver o detalhe dos fluxos em função da forma de pagamento escolhida. 

* **Cartão de crédito**: Quando a forma escolhida for o cartão de crédito, o processamento deve ser feito na hora já gerando uma compra para da pessoa para àquele produto ou informando que não foi possível realizar a compra. 
* **Boleto bançario**: Quando a forma escolhida for um boleto bancário, a compra deve ser gerada num estado de "espera". Afinal de contas o boleto bancário ainda precisa ser processado. 
* **Pix**: Quando a forma escolhida for o pix, deve ser gerado um qrcode com um número supostamente associado a conta da Hotmart. Esse qrcode deve ser retornado. A compra deve ser gerada num estado de espera, até o pagamento ser confirmado. A confirmação desse pagamento deve ser feita em até X minutos, onde X é configurável via aplicação. 

Caso um código de cupom seja passado, o mesmo deve ser aplicado sobre o valor do infoproduto antes do processamento da compra. Caso o cupom não esteja válido, o fluxo é interrompido e nenhuma compra é gerada. 

## Mais contexto sobre como funciona a Hotmart para pessoas na hora de registrar seus infoprodutos

* Na hotmart uma mesma conta pode comprar infoprodutos ou registrar seus próprios.
* Para cada infoproduto você pode criar uma ou mais ofertas. 
* Para cada infoproduto você define o tempo de acesso
* Caso tenha sido escolhida modelo de inscriçao recorrente, você define o tempo de renovação. 
* Na oferta você configura:
    * Preço
    * Número máximo de parcelas
    * Moeda que você quer oferecer (aqui não leve em consideração neste desafio)
    * Quem vai pagar juros? pessoa compradora ou infoprodutora. Quando a escolha é a pessoa compradora, o valor da parcela leva em consideração a taxa. Quando a escolha é a própria pessoa compradora, isso influencia no seu calculo de comisso. 
    * A ativação do parcelamento inteligente. Ele é ativado quando o valor total da compra não cabe no cartão. Nesse caso é feita a cobrança no cartão no valor condicionado ao número máximo de parcelas. Se o máximo for 10 e compra for de 100, então vai rolar a tentativa de debitar 10 do cartão. E aí durante dez meses vai ser realizado tal débito. 
* A hotmart define a taxa X de comissão por venda, onde X é configurável por infoprodutos. Existe um valor default, mas ele pode variar em função de negociações. 
* A hotmart define o valor de juros ao mês a ser cobrado para parcelamento. Esse valor precisa ser configurável também por conta. Essa explicação pode ser conferida aqui também
[https://help.hotmart.com/pt-br/article/quais-tipos-e-formas-de-pagamento-eu-posso-escolher-para-meu-produto-/216440337](https://help.hotmart.com/pt-br/article/quais-tipos-e-formas-de-pagamento-eu-posso-escolher-para-meu-produto-/216440337)
* Quando uma compra é feita, a comissão da hotmart é calculada em cima do valor total que vai ser repassado para a pessoa infoprodutora. Se for uma compra parcelada com o infoprodutor assumindo o juros, esse valor também é descontado na comissão. Ambos são calculados em cima do valor real. Ex: Uma compra de **100** é feita divida em 10x e realizada sem parcelamento inteligente, ou seja o valor é dividido no cartão mas o infoprodutor recebe de uma vez só. Suponha uma taxa de comissão de 10% e um taxa de juros de 0,5% ao mês(6% ao ano). Nesse caso, 5% seria descontado dos **100** + 10% em cima do mesmo **100**.  No final sobraria 85 para a pessoa infoprodutora. 
* Se for uma compra parcelada com o juros sendo repassado para a pessoa cliente, aí basta descontar a comissão padrão da Hotmart. 

## Critérios de aceitação

* Critérios específicos para compras com cartão de crédito
  * O número de parcelas deve ser menor ou igual ao limite configurado na oferta.
  * Caso a oferta tenha sido configurada para aceitar parcelamento inteligente, o valor precisa ser dividido considerando o maior número de parcelas configurado na oferta e este valor deve ser cobrado de maneira recorrente no cartão da pessoa.  
* Um email para a pessoa compradora deve ser enviado independente do resultado da finalização de compra. Agora vamos detalhar os emails. 
  * Em caso de compra processada com sucesso, deve ser enviado um email informando que a compra foi realizada com sucesso e um link(pode ser ficticio de acesso ao infoproduto)
  * Em caso de falha, deve ser enviado um email informando que a compra não pôde ser processada. 
  * Em caso de compra com boleto bancário, precisa ser enviado um email informando que a compra vai ser processada com sucesso em até X horas(onde X é configurável) pós pagamento. 
  * Em caso de compra com pix, basta seguir o fluxo de caso de compra de sucesso. Se passar do limite de tempo configurado e a compra não tiver sido concluída com sucesso, o email de falha deve ser enviado. 
* Caso o email da pessoa da pessoa compradora não exista, uma nova pessoa cliente deve ser registrada no sistema com o mesmo email. E a compra deve ser associada com a pessoa. 
* Caso o email já exista, a compra deve ser associada com a conta existente. 
* Uma nova compra com todos detalhes deve ser registrada. Qual foi a forma de pagamento usada, dados dessa forma de pagamento, momento que a compra foi aprovada, cupom(caso tenha sido aplicado)
* A compra deve ficar relacionada com a oferta daquele produto. Dessa forma conseguimos saber as compras que vieram em função de uma determinada oferta. 
* Caso a compra tenha sido realizada com sucesso, o acesso ao infoproduto deve ser liberado considerando o tempo de acesso configurado no infoproduto. 

## Informações adicionais

* Crie um backlog com as tarefas antes de qualquer coisa. Aqui é um exercício de imaginação e ele é muito importante para a escrita de código de qualidade. A ideia é que você derive tudo que precisa ser feito para habilitar o checkout. 
* Fique a vontade para simular os serviços necessários para integrações como o de pix, gateway de cartão etc
* Está com dúvida? Manda uma mensagem no privado ou no canal público do Discord. Existe uma boa chance de alguma informação aqui precisar ser refinada. 
