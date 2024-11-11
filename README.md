# Resultado do desafio
Este é um "challenge by coodesh", para testar os cohecimentos em SQL Server que será a linguagem SQL que irei usar.
Neste caso não há muito sentido em criar um .gitignore pois as quarys estaram nas linhas abaixo.

#consultas solicitadas

- Listar todos Clientes que não tenham realizado uma compra;

Explicação , basta verificar que clientes não tenham pedidos, ou que tenham pedidos mais não tenham pedidos com status de concluído.
Na primeira query trago os clientes que não tiveram nenhum pedido e uno o resultado com a seguinda query onde busco todos os clientes
que tenham pedidos mais nenhum deles com status "concluído", ou seja, fizeram os pedidos e mas não finalizaram a compra.
  
select c.customer_id, c.first_name, c.last_name, c.phone, c.email
from customers c
where not exists (select 1
                  from orders o
                  where o.customer_id = c.customer_id)
union
select c.customer_id, c.first_name, c.last_name, c.phone, c.email
from customers c
      inner join (select r.customer_id, s.order_status, count(1)
                  from customers r
                       inner join orders s
                       on r.customer_id = s.customer_id
                  where order_status = "Concluído"
                  group by r.customer_id, s.order_status
                  having count(1) = 0) o
       on o.customer_id = c.customer_id

 
- Listar os Produtos que não tenham sido comprados

Como no exemplo acima procuro na primeira lista os produtos que nunca foram relacionados no order_items
e uno com produtos que foram relacionados mais não tiram nenuma compra concluída

select p.product_id, p.product_name
from products p 
where not exists (select 1 from order_items i
                  where i.product_id = p.product_id)
union
select p.produt_id, p.product_name
from products p
     inner join order_items i
     on i.product_id = i.product_id
     inner join (select o.order_id, o.order_status, count(1)
                 from orders o
                      inner join order_item m
                      on m.order_id = o.order_id
                 where o.order_status = "Concluído"
                 group by o.order_id, o.order_status
                 having count(1) = 0) x
      on i.order_id = x.order_id
 
- Listar os Produtos sem Estoque;

Aqui vejo quais produtos tem a soma no stoque igual a 0 e listo.

select p.product_id, p.product_name
from product p
     inner join (select s.product_id, sum(isnull(t.quantity, 0)) as quantity
                 from products s
                      left join sotcks t
                      on t.product_id = s.product_id
                 group by s.product_id) x
      on x.product_id = p.product_id
 where x.quantity = 0

     
- Agrupar a quantidade de vendas que uma determinada Marca por Loja.

A partir dos pedidos conclídos, eu busco os itens desses pedidos, dentro dos itens os produtos e por fim as marcas.
Agurpo e somo as quantidades vendidas.

select o.store_id, s.store_name, p.brand_id, b.brand_name, sum(o.quantity) 
from orders o
     inner join order_items o
     on o.order_id = o.order_id
     inner join products p
     on o.product_id = p.product_id
     inner join brands b
     on b.brand_id = p.brand_id
     inner join stores s
     on o.store_id = s.store_id
where o.order_status = "Cocluída"
group by o.store_id, s.store_name, p.brand_id, b.brand_name

- Listar os Funcionarios que não estejam relacionados a um Pedido.

select s.staff_id, s.first_name, s.last_name, s.email, s.phone, s.active
from staffs s
     right join orders r
     on r.staff_id = s.staff_id
where r.order_id = @order_id
and s.staff_id is null

# DBA Challenge 20240802


## Introdução

Nesse desafio trabalharemos utilizando a base de dados da empresa Bike Stores Inc com o objetivo de obter métricas relevantes para equipe de Marketing e Comercial.

Com isso, teremos que trabalhar com várioas consultas utilizando conceitos como `INNER JOIN`, `LEFT JOIN`, `RIGHT JOIN`, `GROUP BY` e `COUNT`.

### Antes de começar
 
- O projeto deve utilizar a Linguagem específica na avaliação. Por exempo: SQL, T-SQL, PL/SQL e PSQL;
- Considere como deadline da avaliação a partir do início do teste. Caso tenha sido convidado a realizar o teste e não seja possível concluir dentro deste período, avise a pessoa que o convidou para receber instruções sobre o que fazer.
- Documentar todo o processo de investigação para o desenvolvimento da atividade (README.md no seu repositório); os resultados destas tarefas são tão importantes do que o seu processo de pensamento e decisões à medida que as completa, por isso tente documentar e apresentar os seus hipóteses e decisões na medida do possível.
 

## O projeto

- Criar as consultas utilizando a linguagem escolhida;
- Entregar o código gerado do Teste.

### Modelo de Dados:

Para entender o modelo, revisar o diagrama a seguir:

![<img src="samples/model.png" height="500" alt="Modelo" title="Modelo"/>](samples/model.png)


## Selects

Construir as seguintes consultas:

- Listar todos Clientes que não tenham realizado uma compra;
- Listar os Produtos que não tenham sido comprados
- Listar os Produtos sem Estoque;
- Agrupar a quantidade de vendas que uma determinada Marca por Loja. 
- Listar os Funcionarios que não estejam relacionados a um Pedido.


## Readme do Repositório

- Deve conter o título do projeto
- Uma descrição sobre o projeto em frase
- Deve conter uma lista com linguagem, framework e/ou tecnologias usadas
- Como instalar e usar o projeto (instruções)
- Não esqueça o [.gitignore](https://www.toptal.com/developers/gitignore)
- Se está usando github pessoal, referencie que é um challenge by coodesh:  

>  This is a challenge by [Coodesh](https://coodesh.com/)

## Finalização e Instruções para a Apresentação

1. Adicione o link do repositório com a sua solução no teste
2. Verifique se o Readme está bom e faça o commit final em seu repositório;
3. Envie e aguarde as instruções para seguir. Sucesso e boa sorte. =)
