# Routers

- [Introdução](#introduction)
- [Configuração](#config)
- [Tipos](#types)
 - [Literal](#literal)
 - [Segment](#segment)
 - [Regex](#regex)

<a name="introduction"></a>
## Introdução

Routers é um mecanismo para interpretar as requisições HTTP que são gerados. Com o Router você pode basicamente mapear e direcionar para o código a onde a quela requisição deve ser levada e oque é argumento ou não

<a name="config"></a>
## Configuração

A configuração de um roteamente é simples tendo em mente as seguintes propriedades:

<table>
	<tr>
		<th>Propriedade</th>
		<th>Tipo</th>
		<th>Regra</th>
		<th width="100%" align="left">Descrição</th>
	</tr>
	<tr>
		<td>map</td>
		<td>string</td>
		<td>requirido</td>
		<td>Mapeamento</td>
	</tr>
		<tr>
		<td>defaults</td>
		<td>array</td>
		<td>opcional</td>
		<td>Argumentos defaults</td>
	</tr>
		<tr>
		<td>options</td>
		<td>array</td>
		<td>opcional</td>
		<td>Opções para Informações extra para o mapeamento seu tipo</td>
	</tr>
		<tr>
		<td>type</td>
		<td>string</td>
		<td>opcional</td>
		<td>Default: Rabbit\Routing\Mapping\Literal </td>
	</tr>
</table>

Na propriedade `defaults` temos 5 argumentos que são tipos comportamentais pois as mesmas influenciam nos aspectos do framework:

<table>
	<tr>
		<th>Argumento</th>
		<th>Tipo</th>
		<th width="100%" align="left">Descrição</th>
	</tr>
	<tr>
		<td>module</td>
		<td>string</td>
		<td>Determina que módulo você está solicitando [Default: application]</td>
	</tr>
		<tr>
		<td>namespace</td>
		<td>string</td>
		<td>Determina que namespace você está solicitando [Default: main]</td>
	</tr>
		<tr>
		<td>controller</td>
		<td>string</td>
		<td>Determina que controller você está solicitando [Default: index]</td>
	</tr>
		<tr>
		<td>action</td>
		<td>string</td>
		<td>Determina que action você está solicitando [Default: index]</td>
	</tr>
	</tr>
		<tr>
		<td>_format</td>
		<td>string</td>
		<td>Informa que format [Default: html]</td>
	</tr>
</table>

**Yaml:**

Podemos definir um Router através do arquivo `router.yml` dentro da pasta do módulo ex:
	
	/root
	  application
	    Modules
	      Application
	        router.yml

Onde mapeamos da seguinte forma:

```yaml
nome_mapeamento:
 type: ''
 map: ''
 defaults:
  arg1: 'value1'
  namespace: 'main'
 options:
  option1:
   arg1: ''
```

Um exemplo de mapeamento:

```yaml
# Mapeamento utilizando como default o tipo Literal
MeuRouterArtigo:
 map: '/artigo'
 defaults:
  action: 'list'
  module: 'artigo'
  
# Mapeamento utilizando como tipo Segment
Modulox\Namespacey\Nomez:
 type: 'Rabbit\Routing\Mapping\Segment'
 map: '/artigo/:id'
 defaults:
  action: 'view'
  module: 'artigo'
 options:
  requirements:
   id: '[0-9]+' # só irá combinar com esse router se o id do tipo numeral
```


<a name="types"></a>
## Tipos

Rabbit temos 3 tipos de Routers Literal, Segment e Regex cada 1 tem um peso sobre o outro em termos de peso temos:

<table>
	<tr>
		<th>#</th>
		<th>Tipo</th>
		<th>Força</th>
	</tr>
	<tr>
		<td>1</td>
		<td>Literal</td>
		<td>Forte</td>
	</tr>
	<tr>
		<td>2</td>
		<td>Segment</td>
		<td>Medio</td>
	</tr>
	<tr>
		<td>3</td>
		<td>Regex</td>
		<td>Fraco</td>
	</tr>
</table>

Assim sendo: Mapeamento do tipo Literal sobrepoem as de Segment e a de Segment as de Regex.

<a name="literal"></a>
### Literal

 * type: Rabbit\Routing\Mapping\Literal

O Roteramento do tipo `Literal` é basicamente conforme sua tradução "ao pé da letra", ele só irá fazer a combinção com o roteamento se a requisião for exatamente o que você solicitou.


**Exemplo:**

-------
	REQUEST URL: site.com.br/usuarios

RouterMap:
	
```yaml
Modulox\Namespacey\Usuarios:
 map: '/usuarios'
 defaults:
  controller: 'usuario'
  action: list
```
	
Com isso ao fazer o request para a url informada o mesmo será combinado com o mapeamento e iria mandar o usuário para:
	
	module:          application
	namespace:       main
	controller:      usuario
	action:          list
	
-------

<a name="segment"></a>
### Segment

 * type: Rabbit\Routing\Mapping\Segment

O Roteamento do tipo `Segment` a idéia é segmentar a URL através da barra ( **/** ).

O interesante deste mapeamento é que podemos recuperar alguns segments atribuindo uma variável para o mesmo. As variáveis são definidos através de dois pontos ( **:** ) `:nome_var` assim no segmento que você informa uma variável o valor na quele segmento será setado para a variável.

Os mesmos argumentos de comportamentos são permitidos como nome das variável e tornando assim as mesmas comportamentais.

Os mapeamentos do tipo `Segment` possuem algumas opções como segue abaixo

**Options**
<table>
	<tr>
		<th>Opção</th>
		<th>Tipo</th>
		<th width="100%" align="left">Descrição</th>
	</tr>
	<tr>
		<td>requirements</td>
		<td>array</td>
		<td>
		Cria validações para as variáveis<br />
		URL: site.com.br/noticia/:id		
<pre>
requirements:
 id: '\d+'
</pre>
		</td>
	</tr>
</table>

Os `Segment` permitem também o mapeamento de segmentos opcionais entre colchetes ( [ ... ] ) como o mapeamento:

---------
```yaml
Application\Main\Segmentx:
 type: 'Rabbit\Routing\Mapping\Segment'
 map: '/artigos[/:categoria]'
 defaults:
  module: artigos
  controller: categoria
  action: list
 options:
  requirements:
   categoria: '[[:alpha:]]'
```
---------