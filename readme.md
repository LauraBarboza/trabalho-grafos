# Trabalho Prático Final — Etapa 1  
**Disciplina:** GCC262 - Grafos e suas Aplicações  
**Instituição:** Universidade Federal de Lavras (UFLA)  
**Professor:** Mayron César O. Moreira  
**Aluno:** Laura Costa Sarto Barboza

## ✅ Objetivo

Este projeto tem como objetivo realizar o pré-processamento de dados de instâncias de grafos para um problema de roteamento com restrições. A etapa 1 consiste em:

- Representar o problema com estruturas de dados adequadas;
- Implementar a leitura dos arquivos `.dat` no formato proposto pelo professor;
- Calcular e apresentar métricas relevantes sobre os grafos;
- Gerar visualizações com apoio da biblioteca `pandas`.

---

## 🧠 Como o problema foi modelado

O grafo é representado como um **multigrafo direcionado e não direcionado**, onde:

- **Vértices (`self.conjunto_vertices`)**: conjunto de nós presentes no grafo.
- **Lista de adjacência (`self.lista_adjacencia`)**: armazena os vizinhos e seus custos de cada vértice.
- **Arestas requeridas (`self.arestas_requeridas`)**: subconjunto de arestas não direcionadas que precisam ser atendidas.
- **Arcos requeridos (`self.arcos_requeridos`)**: subconjunto de arcos direcionados que precisam ser atendidos.
- **Vértices requeridos (`self.vertices_requeridos`)**: nós com demanda direta.
- **Capacidade (`self.capacidade_veiculo`)** e **depósito (`self.vertice_deposito`)**: parâmetros logísticos da instância.

Essas estruturas foram implementadas com `set()` e `defaultdict(list)`, todos da Standard Library do Python.

---

## 🗃️ Leitura dos dados

A função `carregar_arquivo(caminho)` interpreta arquivos `.dat` estruturados conforme o modelo fornecido.

As seções do arquivo reconhecidas são:

- `Depot Node:` → define o vértice do depósito
- `Capacity:` → define a capacidade dos veículos
- `ReN.` → vértices requeridos
- `ReE.` → arestas requeridas (não direcionadas)
- `ReA.` → arcos requeridos (direcionados)

A função trata as linhas com cuidado, ignora comentários e cabeçalhos, e converte nomes como `N4`, `E1` e `A1` para inteiros relevantes, preenchendo as estruturas do grafo.

---

## 📊 Métricas calculadas

A função `gerar_tabela()` gera duas tabelas usando `pandas`:

### 🔹 Estatísticas gerais do grafo

| Métrica                   | Descrição |
|---------------------------|-----------|
| Qtd Vértices              | Total de nós distintos |
| Qtd Arestas               | Total de arestas requeridas |
| Qtd Arcos                 | Total de arcos requeridos |
| Vértices Requeridos       | Nós com demanda direta |
| Arestas/Arcos Requeridas  | Elementos que devem ser atendidos |
| Densidade                 | Relação entre número de conexões e o total possível |
| Componentes Conectados    | Subgrafos conexos disjuntos |
| Grau Mínimo / Máximo      | Número mínimo/máximo de conexões por vértice |
| Caminho Médio             | Média das distâncias entre todos os pares |
| Diâmetro                  | Maior distância entre dois nós |

### 🔹 Intermediação (betweenness)

Calcula quantas vezes um vértice aparece nos caminhos mais curtos entre outros pares de vértices.

---

## 📈 Justificativa da escolha do algoritmo de caminhos mínimos

Para o cálculo de métricas como **caminho médio**, **diâmetro** e principalmente **intermediação (betweenness)**, é necessário conhecer os **menores caminhos entre todos os pares de vértices do grafo** — o que é conhecido como o problema de **All-Pairs Shortest Paths (APSP)**.

Neste trabalho, foi utilizado o **algoritmo de Floyd-Warshall**, devido às seguintes razões:

- É um algoritmo **simples de implementar** e bem adaptado para estruturas matriciais;
- Calcula o menor caminho entre **todos os pares de vértices** em tempo \( O(V^3) \), sendo adequado para os tamanhos das instâncias fornecidas;
- É eficaz mesmo em grafos **não direcionados ou com pesos negativos**, desde que não existam ciclos negativos;
- Permite derivar facilmente a **matriz de predecessores**, necessária para a reconstrução dos caminhos mínimos utilizados na métrica de intermediação.

Embora existam alternativas como o **algoritmo de Dijkstra**, que apresenta melhor desempenho assintótico quando otimizado com estruturas como heaps, ele exige múltiplas execuções (uma por vértice), o que tornaria a implementação mais complexa sem ganho significativo para os tamanhos de instância utilizados aqui.

---

## 💻 Bibliotecas utilizadas

Somente bibliotecas **da Standard Library** :

- `collections.defaultdict` – lista de adjacência eficiente
- `collections.deque` – fila para BFS
- `math.inf` – usado no algoritmo de Floyd-Warshall
- `pandas` – permitido no `.ipynb` para visualização das tabelas

---

## ▶️ Como usar

1. No seu notebook `.ipynb`, importe o arquivo `.py` ou copie a classe `Grafo`.
2. Utilize o seguinte exemplo para executar:

```python
g = Grafo()
g.carregar_instancia("BHW1.dat")  # ou qualquer outro .dat
geral, intermediacao = g.gerar_tabelas_resultado()
display(geral)
display(intermediacao.sort_values(by="Intermediação", ascending=False))
```

---

## 📁 Visualização (opcional)

Você pode plotar um gráfico da intermediação com:

```python
import matplotlib.pyplot as plt

intermediacao.sort_values(by="Intermediação", ascending=False).plot(kind="bar")
plt.title("Intermediação por vértice")
plt.xlabel("Vértice")
plt.ylabel("Intermediação")
plt.grid(True)
plt.show()
```

