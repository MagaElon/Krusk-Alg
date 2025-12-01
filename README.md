# Krusk-Alg
Krusk Alg
import java.util.*;

// Represents an edge in the graph
class Edge implements Comparable<Edge> {
    int u, v, weight;

    Edge(int u, int v, int weight) {
        this.u = u;
        this.v = v;
        this.weight = weight;
    }

    @Override
    public int compareTo(Edge other) {
        return Integer.compare(this.weight, other.weight);
    }
}

// Disjoint Set Union (Union-Find) with path compression & union by rank
class DSU {
    int[] parent, rank;

    DSU(int n) {
        parent = new int[n];
        rank = new int[n];

        for (int i = 0; i < n; i++) {
            parent[i] = i;   // each node is its own parent
            rank[i] = 0;
        }
    }

    // Find the root with path compression
    int find(int x) {
        if (parent[x] != x)
            parent[x] = find(parent[x]);  // path compression
        return parent[x];
    }

    // Union by rank
    boolean union(int x, int y) {
        int rootX = find(x);
        int rootY = find(y);

        if (rootX == rootY)
            return false;  // already in same set → cycle (skip edge)

        if (rank[rootX] < rank[rootY]) {
            parent[rootX] = rootY;
        } else if (rank[rootX] > rank[rootY]) {
            parent[rootY] = rootX;
        } else {
            parent[rootY] = rootX;
            rank[rootX]++;
        }

        return true;
    }
}

// Main Kruskal Algorithm
public class KruskalMST {

    public static int kruskal(int n, List<Edge> edges) {
        Collections.sort(edges); // sort by weight
        DSU dsu = new DSU(n);
        int mstWeight = 0;
        int edgesUsed = 0;

        for (Edge e : edges) {
            if (dsu.union(e.u, e.v)) {    // if no cycle
                mstWeight += e.weight;
                edgesUsed++;
                if (edgesUsed == n - 1) break; // MST complete
            }
        }

        if (edgesUsed != n - 1) {
            throw new RuntimeException("Graph is not connected; MST doesn't exist.");
        }

        return mstWeight;
    }

    public static void main(String[] args) {
        int n = 5; // number of vertices

        List<Edge> edges = new ArrayList<>();
        edges.add(new Edge(0, 1, 10));
        edges.add(new Edge(0, 2, 6));
        edges.add(new Edge(0, 3, 5));
        edges.add(new Edge(1, 3, 15));
        edges.add(new Edge(2, 3, 4));

        int mst = kruskal(n, edges);
        System.out.println("Total weight of MST: " + mst);
    }
}
