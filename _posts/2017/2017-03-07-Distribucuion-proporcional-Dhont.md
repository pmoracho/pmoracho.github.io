---
layout: post
---
layout: post
title: Distribución proporcional - el sistema d'Hondt en SQL 
date: 2017-03-08
tag:
  - desarrollo
  - sql
  - algoritmos
show_meta: true
comments: true
published: true
mathjax: false
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
---

La distribución proporcional conlleva un problema no menor en cuanto a la
manera de manejar el resto. Esto es un tema altamente estudiado en los sistemas
electorales: **¿Como distribuimos un número entero de bancas entre cada partido
según sus votos?**. Obtener un coeficiente de los votos de cada partido y en
función a este coeficiente calcular los votos es el primer paso, el verdadero
problema está en que hacer esto indefectiblemente genera un "resto". Los
distintos sistemas electorales buscan encontrar una solución a como repartir
este "resto" de la manera más equitativa posible.

Según la Wikipedia en política se usa:

1. el [método del Resto
   Mayor](https://es.wikipedia.org/wiki/M%C3%A9todo_del_resto_mayor) 

	Tras escrutar todos los votos, se divide el número de votos de cada lista entre
	un cociente que representa el número de votos requeridos para obtener un
	escaño. El resultado para cada partido se compondrá normalmente de una parte
	entera y un resto fraccional. En primer lugar se asigna a cada lista un número
	de escaños igual a su parte entera. Esto dejará normalmente algunos escaños sin
	asignar. Entonces se ordenan los partidos en función de sus restos, y los
	partidos con mayores restos obtienen un escaño extra cada uno, hasta repartir
	todos los escaños.

2. o bien el de [Promedios
   mayores](https://es.wikipedia.org/wiki/M%C3%A9todo_de_promedios_mayores)

	Los métodos de promedio mayor se basan en la división sucesiva del total de
	votos de cada lista por una serie de divisores. Esto produce una tabla de
	cocientes, o promedios, con una fila para cada divisor y una columna para
	cada lista. El escaño n se asigna a la lista cuya columna contiene el
	n-ésimo mayor cociente, hasta repartir todos los escaños


Todo esto se me me dió por investigar al reconocer éste como un problema
bastante frecuente en los sistemas. Puntualmente me encuentro definiendo un
algoritmo para distribuir clientes entre gestores, la idea es hacer hacer que
cada gestor mantenga una cartera "medianamente" proporcional de clientes. Para
no reinventar la rueda me puse a analizar que sistema usamos puntualmente en la
**Argentina** para la asignación de bancas, me encontré que se trata del
conocido como [Sistema
D´Hondt](https://es.wikipedia.org/wiki/Sistema_d%27Hondt). No es muy complejo
que digamos, recomiendo la lectura del artículo de la wikipedia. 


# El D´Hondt en SQL

Si.. es factible de hacer y bastante compacto. El dialecto usado es el T-SQL,
algunas funciones son propias del SQL-Server, pero no debiera ser difícil de
migrar. El ejemplo es el mismo del artículo de la qiki, 7 bancas que deben
repartirse entre 5 partidos.


```sql
DECLARE @Bancas TABLE (
	BancaId 	INT
)

DECLARE @Partidos TABLE (
	PartidoId 	INT,
	Votos		INT
)

DECLARE @PartidosBancas TABLE (
	BancaId		INT,
	PartidoId 	INT,
	Votos		INT,
	Orden		INT
)

-- Crear un registro por Banca (7 bancas)
INSERT INTO @Bancas (BancaId)
VALUES	(1),(2),(3),(4),(5),(6),(7)

-- Crear un registro por Partido con sus votos (5 Partidos)
INSERT INTO @Partidos (PartidoId, Votos)
SELECT	1, 340000 UNION
SELECT	2, 280000 UNION
SELECT	3, 160000 UNION
SELECT	4, 60000 UNION
SELECT	5, 15000

INSERT INTO @PartidosBancas (BancaId, PartidoId, Votos, Orden)
SELECT 	B.BancaId, 
	P.PartidoId, 
	P.Votos / B.BancaId, 
	ROW_NUMBER() OVER (ORDER BY P.Votos / B.BancaId DESC)
	FROM @Partidos P
	INNER JOIN @Bancas B
		ON 1 = 1
	ORDER BY P.Votos / B.BancaId DESC
		
-- Cuantas bancas a cada partido
SELECT 	PartidoId, COUNT(1) AS 'Bancas' 
	FROM @PartidosBancas
	WHERE Orden <= (SELECT COUNT(1) FROM @Bancas)
	GROUP BY PartidoId
```
Comentarios?
