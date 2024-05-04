---
title: Eulerian Path
date: 2024-03-15 10:59:47
categories:
  - [Algorithm]
tags:
  - Graph theory
---

### Seven Bridges of Königsberg

[Seven Bridges of Königsberg](https://en.wikipedia.org/wiki/Seven_Bridges_of_K%C3%B6nigsberg) is a graph problem solved by Euler. There are 7 bridges connecting 2 islands with mainland. The question is, is it possible to cross all the bridges exactly one time?

{% img [class names] /images/graph/Konigsberg_bridges.png 400 300 '"Seven Bridges of Königsberg" "alt"' %}

It turns out that the problem can be generalized into a graph problem if we consider each land as a vertex and each bridge as an edge, what we want to do is to finish a "one line draw" to traverse all edges exactly once and pass all vertices (can pass vertices more than one time):

{% img [aa] /images/graph/Konigsberg_graph.png 250 200 '"Seven Bridges graph" "alt"' %}

### Concepts

[Degree](https://en.wikipedia.org/wiki/Degree_(graph_theory)):
- In graph theory, the degree (or valency) of a vertex of a graph is the number of edges that are incident to the vertex; in a multigraph, a loop contributes 2 to a vertex's degree, for the two ends of the edge.

### Eulerian Path

In graph theory, an [Eulerian path](https://en.wikipedia.org/wiki/Eulerian_path) is a path in a finite graph that visits every edge exactly once (allowing for revisiting vertices). Similarly, an Eulerian circuit or Eulerian cycle is an Eulerian trail that starts and ends on the same vertex. They were first discussed by Leonhard Euler while solving the famous Seven Bridges of Königsberg problem in 1736. The problem can be stated mathematically like this:

- Given the graph in the image, is it possible to construct a path (or a cycle; i.e., a path starting and ending on the same vertex) that visits each edge exactly once?



Euler's Theorem:

- A connected graph has an Euler cycle if and only if every vertex has even degree.


