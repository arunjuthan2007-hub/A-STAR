<h1>ExpNo 4 : Implement A* search algorithm for a Graph</h1> 
<h3>Name: Arunjuthan.M.A      </h3>
<h3>Register Number:  212225230020         </h3>
<H3>Aim:</H3>
<p>To ImplementA * Search algorithm for a Graph using Python 3.</p>
<H3>Algorithm:</H3>

``````
// A* Search Algorithm
1.  Initialize the open list
2.  Initialize the closed list
    put the starting node on the open 
    list (you can leave its f at zero)

3.  while the open list is not empty
    a) find the node with the least f on 
       the open list, call it "q"

    b) pop q off the open list
  
    c) generate q's 8 successors and set their 
       parents to q
   
    d) for each successor
        i) if successor is the goal, stop search
        
        ii) else, compute both g and h for successor
          successor.g = q.g + distance between 
                              successor and q
          successor.h = distance from goal to 
          successor (This can be done using many 
          ways, we will discuss three heuristics- 
          Manhattan, Diagonal and Euclidean 
          Heuristics)
          
          successor.f = successor.g + successor.h

        iii) if a node with the same position as 
            successor is in the OPEN list which has a 
           lower f than successor, skip this successor

        iV) if a node with the same position as 
            successor  is in the CLOSED list which has
            a lower f than successor, skip this successor
            otherwise, add  the node to the open list
     end (for loop)
  
    e) push q on the closed list
    end (while loop)

``````

<hr>
<h2>Sample Graph I</h2>
<hr>

![image](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/b1377c3f-011a-4c0f-a843-516842ae056a)

<hr>
<h2>Sample Input</h2>
<hr>
10 14 <br>
A B 6 <br>
A F 3 <br>
B D 2 <br>
B C 3 <br>
C D 1 <br>
C E 5 <br>
D E 8 <br>
E I 5 <br>
E J 5 <br>
F G 1 <br>
G I 3 <br>
I J 3 <br>
F H 7 <br>
I H 2 <br>
A 10 <br>
B 8 <br>
C 5 <br>
D 7 <br>
E 3 <br>
F 6 <br>
G 5 <br>
H 3 <br>
I 1 <br>
J 0 <br>
<hr>
<h2>Sample Output</h2>
<hr>
Path found: ['A', 'F', 'G', 'I', 'J']


<hr>
<h2>Sample Graph II</h2>
<hr>

![image](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/acbb09cb-ed39-48e5-a59b-2f8d61b978a3)


<hr>
<h2>Sample Input</h2>
<hr>
6 6 <br>
A B 2 <br>
B C 1 <br>
A E 3 <br>
B G 9 <br>
E D 6 <br>
D G 1 <br>
A 11 <br>
B 6 <br>
C 99 <br>
E 7 <br>
D 1 <br>
G 0 <br>
<hr>
<h2>Sample Output</h2>
<hr>
Path found: ['A', 'E', 'D', 'G']



<h2>Program : </h2>

```

from collections import defaultdict
import networkx as nx
import matplotlib.pyplot as plt

H_dist = {}
Graph_nodes = {}

def aStarAlgo(start_node, stop_node):

    open_set = {start_node}
    closed_set = set()

    g = {start_node: 0}
    parents = {start_node: start_node}

    while open_set:

        n = min(open_set,
                key=lambda v: g[v] + H_dist[v])

        if n == stop_node:

            path = []

            while parents[n] != n:
                path.append(n)
                n = parents[n]

            path.append(start_node)
            path.reverse()

            print("\nPath found:", path)
            print("Total Cost:", g[stop_node])

            return path

        open_set.remove(n)
        closed_set.add(n)

        for (m, weight) in get_neighbors(n):

            new_cost = g[n] + weight

            if m not in g or new_cost < g[m]:

                g[m] = new_cost
                parents[m] = n

                if m in closed_set:
                    closed_set.remove(m)

                open_set.add(m)

    print("Path does not exist!")
    return None


# Function to return neighbors
def get_neighbors(v):

    return Graph_nodes.get(v, [])


# Heuristic function
def heuristic(n):

    return H_dist[n]


# Main program
graph = defaultdict(list)

G = nx.Graph()

n, e = map(int, input(
    "Enter number of nodes and edges: "
).split())

print("\nEnter edges (u v cost):")

for i in range(e):

    u, v, cost = input(
        f"Edge {i+1}: "
    ).split()

    cost = int(cost)

    graph[u].append((v, cost))
    graph[v].append((u, cost))

    G.add_edge(u, v, weight=cost)


Graph_nodes = graph

# Display adjacency list
print("\nAdjacency List:")

for node in graph:
    print(node, "->", graph[node])


# Read heuristic values
print("\nEnter Heuristic Values:")

for i in range(n):

    node, h = input(
        f"{i+1}. Node Heuristic: "
    ).split()

    H_dist[node] = int(h)


print("\nHeuristic Values:")
print(H_dist)


# Draw original graph
plt.figure(figsize=(8, 6))

pos = nx.spring_layout(G, seed=20)

nx.draw(
    G, pos,
    with_labels=True,
    node_color="skyblue",
    node_size=1800,
    font_weight="bold"
)

edge_labels = nx.get_edge_attributes(G, 'weight')

nx.draw_networkx_edge_labels(
    G, pos,
    edge_labels=edge_labels
)

plt.title("Original Graph")
plt.axis("off")
plt.show()


# Get start and goal nodes
start = input("\nEnter Start Node: ")
goal = input("Enter Goal Node: ")


if start not in graph:

    print("Invalid Start Node!")

elif goal not in graph:

    print("Invalid Goal Node!")

else:

    shortest_path = aStarAlgo(start, goal)

    if shortest_path:

        path_edges = [
            (shortest_path[i], shortest_path[i + 1])
            for i in range(len(shortest_path) - 1)
        ]

        # Highlight shortest path
        plt.figure(figsize=(8, 6))

        nx.draw_networkx_nodes(
            G, pos,
            node_color="skyblue",
            node_size=1800
        )

        nx.draw_networkx_labels(
            G, pos,
            font_weight="bold"
        )

        nx.draw_networkx_edges(
            G, pos,
            edge_color="gray",
            width=2
        )

        nx.draw_networkx_edges(
            G, pos,
            edgelist=path_edges,
            edge_color="red",
            width=4
        )

        nx.draw_networkx_edge_labels(
            G, pos,
            edge_labels=edge_labels
        )

        plt.title("Shortest Path Highlighted (Red)")
        plt.axis("off")
        plt.show()


```

<h2>Output : </h2>

<img width="1341" height="380" alt="image" src="https://github.com/user-attachments/assets/46e3f41c-4be6-475e-93d3-02fac66a36cd" />

<img width="1333" height="255" alt="image" src="https://github.com/user-attachments/assets/64f0d0c2-cfcb-483c-a3f5-d827b85827f5" />


<img width="1007" height="791" alt="image" src="https://github.com/user-attachments/assets/5f2ea855-23b8-4b20-83df-663eb3b21d92" />


<img width="1302" height="156" alt="image" src="https://github.com/user-attachments/assets/ef977b96-5722-4d87-aee1-ac60d2cdcb2e" />



<img width="797" height="628" alt="image" src="https://github.com/user-attachments/assets/f2c2a601-b0a3-4676-b604-3e69a4d164db" />




