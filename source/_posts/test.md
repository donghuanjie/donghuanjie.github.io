---
title: Hexo Plugin Test
date: 2023-05-02 23:03:22
mathjax: true
tags:
---

### Button

If we want to quote another post, we can use a button.

{% btn /2023/05/01/Hello-World/, Previous Chapter, arrow-left fa-fw fa-lg, Previous Chapter (Full Image) %} {% btn https://www.google.com, Next Chapter, arrow-right fa-fw fa-lg, Next Chapter (Label) %}


### Mermaid

Visit https://github.com/mermaid-js/mermaid to check the usage.

```mermaid
graph TD
A[Hard] -->|Text| B(Round)
B --> C{Decision}
C -->|One| D[Result 1]
C -->|Two| E[Result 2]
```


{% tabs First unique name %}
<!-- tab Solution 1 -->
**This is Tab 1.**
```java
public class Tab1 {

}
```
<!-- endtab -->

<!-- tab Test 2 -->
**This is Tab 2.**
```java
public class Tab2 {
    
}
```
<!-- endtab -->

<!-- tab -->
**This is Tab 3.**
```java
public class Tab3 {
    
}
```
<!-- endtab -->

<!-- tab -->
**This is Tab 4.**
```java
public class Tab4 {
    
}
```
<!-- endtab -->
{% endtabs %}


### Math Equations

Check for math equations: https://theme-next.js.org/docs/third-party-services/math-equations

Test the math equations:
$$\begin{equation} \label{eq1}
e=mc^2
\end{equation}$$

