import heapq
import matplotlib.pyplot as plt
import networkx as nx
routes = {
    "Hyderabad": {
        "Mumbai": 700,
        "Bengaluru": 500,
        "Delhi": 1500
    },
    "Mumbai": {
        "Hyderabad": 700,
        "Delhi": 900
    },
    "Bengaluru": {
        "Hyderabad": 500,
        "Delhi": 1300
    },
    "Delhi": {
        "Hyderabad": 1500,
        "Mumbai": 900,
        "Bengaluru": 1300
    }
}
def optimize_route(source, destination):
    distances = {airport: float("inf") for airport in routes}
    previous = {airport: None for airport in routes}
   distances[source] = 0
    queue = [(0, source)]
    while queue:
        current_distance, current = heapq.heappop(queue)
        if current_distance > distances[current]:
            continue
        if current == destination:
            break

        for neighbor, distance in routes[current].items():
            new_distance = current_distance + distance

            if new_distance < distances[neighbor]:
                distances[neighbor] = new_distance
                previous[neighbor] = current

                heapq.heappush(
                    queue, (new_distance, neighbor)
                )

    if distances[destination] == float("inf"):
        return [], float("inf")

    path = []
    current = destination

    while current is not None:
        path.append(current)
        current = previous[current]

    path.reverse()

    return path, distances[destination]
source = "Hyderabad"
destination = "Delhi"

path, distance = optimize_route(source, destination)
print("AIR TRAFFIC ROUTE OPTIMIZATION")
print("=" * 35)
print("Source Airport:", source, "(HYD)")
print("Destination Airport:", destination, "(DEL)")
print("Optimized Route:", " -> ".join(path))
print("Total Distance:", distance, "km")
G = nx.Graph()

for airport, connections in routes.items():
    for neighbor, distance_value in connections.items():
        G.add_edge(
            airport, neighbor,
            weight=distance_value
        )
positions = {
    "Hyderabad": (0, 0),
    "Mumbai": (1, 1.5),
    "Bengaluru": (1, -1.5),
    "Delhi": (3, 0)
}

plt.figure(figsize=(10, 
nx.draw_networkx_nodes(
    G, positions,
    node_color="lightblue",
    node_size=2500
)
nx.draw_networkx_labels(
    G, positions,
    font_size=10,
    font_weight="bold"
)


nx.draw_networkx_edges(
    G, positions,
    edge_color="gray",
    width=2
)
edge_labels = nx.get_edge_attributes(G, "weight")

nx.draw_networkx_edge_labels(
    G, positions,
    edge_labels=edge_labels
)
if len(path) > 1:
    selected_edges = list(zip(path, path[1:]))

    nx.draw_networkx_edges(
        G, positions,
        edgelist=selected_edges,
        edge_color="green",
        width=4
    )

plt.title(
    "Air Traffic Route Optimization\n"
    "Optimized Distance: {} km".format(distance)
)

plt.axis("off")
plt.tight_layout()
plt.show()
