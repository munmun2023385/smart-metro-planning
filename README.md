# smart-metro-planning
smart metro planning using dijkstra and prims algorithm
# Smart Metro Network Planning with Adjustable Fare Rate
import networkx as nx
import matplotlib.pyplot as plt

# Step 1: Create the metro network graph
metro = nx.Graph()

# Step 2: Define stations
stations = ["A", "B", "C", "D", "E", "F"]
metro.add_nodes_from(stations)

# Step 3: Add connections (edges with distances in km)
metro.add_weighted_edges_from([
    ("A", "B", 4),
    ("A", "C", 2),
    ("B", "C", 5),
    ("B", "D", 10),
    ("C", "E", 3),
    ("E", "D", 4),
    ("D", "F", 11)
])

# Draw the metro network
pos = nx.spring_layout(metro)
plt.figure(figsize=(8, 6))
nx.draw(metro, pos, with_labels=True, node_color='lightblue', node_size=2000, font_size=14)
edge_labels = nx.get_edge_attributes(metro, 'weight')
nx.draw_networkx_edge_labels(metro, pos, edge_labels=edge_labels)
plt.title("Metro Network Graph")
plt.show()

# Step 4: Input from user
source = input("Enter source station: ").upper()
target = input("Enter destination station: ").upper()
fare_rate = float(input("Enter fare rate per km (e.g., 1.5 or 2): "))
avg_speed = 30  # km/h

try:
    # Shortest Path using Dijkstra
    shortest_path = nx.dijkstra_path(metro, source=source, target=target)
    path_length = nx.dijkstra_path_length(metro, source=source, target=target)
    fare = path_length * fare_rate
    time = path_length / avg_speed

    print("\n--- Option 1: Shortest Path ---")
    print("Route:", shortest_path)
    print("Distance:", path_length, "km")
    print(f"Estimated Fare: ₹{fare:.2f}")
    print(f"Estimated Time: {time:.2f} hours")

    # Alternate path
    all_paths = list(nx.all_simple_paths(metro, source=source, target=target))
    alternate_path = None

    for path in all_paths:
        if path != shortest_path:
            alternate_path = path
            break

    if alternate_path:
        alt_distance = sum(metro[u][v]['weight'] for u, v in zip(alternate_path, alternate_path[1:]))
        alt_fare = alt_distance * fare_rate
        alt_time = alt_distance / avg_speed

        print("\n--- Option 2: Alternate Path ---")
        print("Route:", alternate_path)
        print("Distance:", alt_distance, "km")
        print(f"Estimated Fare: ₹{alt_fare:.2f}")
        print(f"Estimated Time: {alt_time:.2f} hours")

        print("\n--- Path Comparison Summary ---")
        if path_length < alt_distance:
            print("Shortest path is quicker and cheaper.")
        elif path_length > alt_distance:
            print("Alternate path is shorter in distance.")
        else:
            print("Both paths have equal distance.")

        if time < alt_time:
            print("Shortest path also saves more time.")
        elif time > alt_time:
            print("Alternate path saves more time.")
        else:
            print("Both take the same time.")
    else:
        print("\nNo alternate path available.")

    # Highlight shortest path in graph
    highlighted_edges = list(zip(shortest_path, shortest_path[1:]))
    edge_colors = ['red' if (u, v) in highlighted_edges or (v, u) in highlighted_edges else 'black' for u, v in metro.edges()]

    plt.figure(figsize=(8, 6))
    nx.draw(metro, pos, with_labels=True, node_color='lightgreen', node_size=2000, font_size=14, edge_color=edge_colors, width=2)
    nx.draw_networkx_edge_labels(metro, pos, edge_labels=edge_labels)
    plt.title("Shortest Path Highlighted")
    plt.show()

except Exception as e:
    print("Error:", e)

# Prim's Algorithm: Infrastructure planning
mst = nx.minimum_spanning_tree(metro)
plt.figure(figsize=(8, 6))
nx.draw(mst, pos, with_labels=True, node_color='lightcoral', node_size=2000, font_size=14)
mst_labels = nx.get_edge_attributes(mst, 'weight')
nx.draw_networkx_edge_labels(mst, pos, edge_labels=mst_labels)
plt.title("Minimum Spanning Tree (Prim's Algorithm)")
plt.show()

# Station Importance (centrality)
centrality = nx.degree_centrality(metro)
print("\n--- Station Centrality (Importance Score) ---")
for station, value in centrality.items():
    print(f"{station}: {round(value, 2)}")