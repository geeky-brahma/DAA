Here are C programs for each of the problems you've listed. Each one adheres to the instructions provided and demonstrates the desired algorithms with output statements to clarify the steps and results.

---

### 1. Binary Search for Leftmost Appearance with Comparisons

```c
#include <stdio.h>

int binarySearchLeftmost(int arr[], int size, int key, int *comparisons) {
    int left = 0, right = size - 1, result = -1;
    *comparisons = 0;
    
    while (left <= right) {
        (*comparisons)++;
        int mid = left + (right - left) / 2;

        if (arr[mid] == key) {
            result = mid;
            right = mid - 1; // Move left to find leftmost occurrence
        } else if (arr[mid] < key) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    return result;
}

int main() {
    int size, key, comparisons;
    printf("Enter size of array: ");
    scanf("%d", &size);

    int arr[size];
    printf("Enter elements of the array: ");
    for (int i = 0; i < size; i++) {
        scanf("%d", &arr[i]);
    }

    printf("Enter the key to be searched: ");
    scanf("%d", &key);

    int index = binarySearchLeftmost(arr, size, key, &comparisons);
    if (index != -1) {
        printf("%d found at index position %d\n", key, index);
    } else {
        printf("%d not found in the array\n", key);
    }
    printf("Number of comparisons: %d\n", comparisons);

    return 0;
}
```

---

### 2. Insertion Sort with File Input and Output for Different Cases

This program assumes that input files `inAsce.dat`, `inDesc.dat`, and `inRand.dat` contain the data, and sorted output will be stored in respective files. It calculates the number of comparisons and displays whether it's a best or worst case.

```c
#include <stdio.h>
#include <stdlib.h>

void insertionSort(int arr[], int n, int *comparisons) {
    *comparisons = 0;
    for (int i = 1; i < n; i++) {
        int key = arr[i];
        int j = i - 1;
        while (j >= 0 && arr[j] > key) {
            arr[j + 1] = arr[j];
            j--;
            (*comparisons)++;
        }
        arr[j + 1] = key;
    }
}

void processFile(const char *inputFile, const char *outputFile, int isBestCase) {
    FILE *inFile = fopen(inputFile, "r");
    FILE *outFile = fopen(outputFile, "w");

    int arr[500], size = 0, comparisons;

    while (fscanf(inFile, "%d", &arr[size]) != EOF && size < 500) {
        size++;
    }
    fclose(inFile);

    printf("Before Sorting: ");
    for (int i = 0; i < size; i++) {
        printf("%d ", arr[i]);
    }
    printf("\n");

    insertionSort(arr, size, &comparisons);

    for (int i = 0; i < size; i++) {
        fprintf(outFile, "%d ", arr[i]);
    }
    fclose(outFile);

    printf("After Sorting: ");
    for (int i = 0; i < size; i++) {
        printf("%d ", arr[i]);
    }
    printf("\n");
    printf("Number of Comparisons: %d\n", comparisons);
    printf("Scenario: %s\n", isBestCase ? "Best-case" : "Worst-case");
}

int main() {
    int choice;
    printf("MAIN MENU (INSERTION SORT)\n");
    printf("1. Ascending Data\n2. Descending Data\n3. Random Data\n4. ERROR (EXIT)\n");
    printf("Enter option: ");
    scanf("%d", &choice);

    switch (choice) {
        case 1:
            processFile("inAsce.dat", "outInsAsce.dat", 1);
            break;
        case 2:
            processFile("inDesc.dat", "outInsDesc.dat", 0);
            break;
        case 3:
            processFile("inRand.dat", "outInsRand.dat", 0);
            break;
        default:
            printf("Exiting program.\n");
    }

    return 0;
}
```

---

### 3. Breadth-First Search and Distance Calculation in an Undirected Graph

```c
#include <stdio.h>
#include <stdlib.h>

#define MAX_NODES 100

typedef struct {
    int adj[MAX_NODES][MAX_NODES];
    int n;
} Graph;

void addEdge(Graph *graph, int u, int v) {
    graph->adj[u][v] = 1;
    graph->adj[v][u] = 1;
}

void bfs(Graph *graph, int start, int *distances) {
    int visited[MAX_NODES] = {0}, queue[MAX_NODES], front = 0, rear = 0;
    
    queue[rear++] = start;
    visited[start] = 1;
    distances[start] = 0;

    printf("BFS Traversal: ");
    while (front < rear) {
        int u = queue[front++];
        printf("%d ", u);
        
        for (int v = 1; v <= graph->n; v++) {
            if (graph->adj[u][v] && !visited[v]) {
                queue[rear++] = v;
                visited[v] = 1;
                distances[v] = distances[u] + 2;
            }
        }
    }
    printf("\n");
}

int main() {
    int n, m, u, v, s;
    printf("Enter number of nodes and edges: ");
    scanf("%d %d", &n, &m);

    Graph graph = { .n = n };

    printf("Enter the edges (u v):\n");
    for (int i = 0; i < m; i++) {
        scanf("%d %d", &u, &v);
        addEdge(&graph, u, v);
    }

    printf("Enter the starting node: ");
    scanf("%d", &s);

    int distances[MAX_NODES];
    for (int i = 1; i <= n; i++) distances[i] = -1;

    bfs(&graph, s, distances);

    printf("Distance from node %d: [ ", s);
    for (int i = 1; i <= n; i++) {
        printf("%d ", distances[i]);
    }
    printf("]\n");

    return 0;
}
```

---

Here are C implementations for problems 4 and 5.

---

### 4. Checking if a Directed Graph is One-Way-Connected

This program checks if a given directed graph is "one-way-connected," meaning that for any two nodes, there is at least a one-way path between them. We'll use Depth-First Search (DFS) to explore reachability and check both directions for every node.

```c
#include <stdio.h>
#include <stdlib.h>

#define MAX_NODES 100

// Graph structure with adjacency matrix representation
typedef struct {
    int adj[MAX_NODES][MAX_NODES];
    int n; // Number of nodes
} Graph;

void addEdge(Graph *graph, int u, int v) {
    graph->adj[u][v] = 1;
}

// Depth-First Search (DFS) for reachability
void dfs(Graph *graph, int start, int *visited) {
    visited[start] = 1;
    for (int v = 0; v < graph->n; v++) {
        if (graph->adj[start][v] && !visited[v]) {
            dfs(graph, v, visited);
        }
    }
}

// Check if the graph is one-way connected
int isOneWayConnected(Graph *graph) {
    int visited[MAX_NODES];
    
    // Check reachability from every node
    for (int i = 0; i < graph->n; i++) {
        for (int j = 0; j < graph->n; j++) visited[j] = 0;
        dfs(graph, i, visited); // Traverse i -> j

        // Check if each node is either reachable from or can reach all other nodes
        for (int j = 0; j < graph->n; j++) {
            if (i != j && !visited[j]) {
                for (int k = 0; k < graph->n; k++) visited[k] = 0;
                dfs(graph, j, visited); // Traverse j -> i
                if (!visited[i]) {
                    return 0; // Not one-way connected
                }
            }
        }
    }
    return 1; // One-way connected
}

int main() {
    Graph graph = { .n = 0 };
    int nodes, edges, u, v;
    
    printf("Enter the number of nodes and edges: ");
    scanf("%d %d", &nodes, &edges);
    graph.n = nodes;

    printf("Enter the edges (u v):\n");
    for (int i = 0; i < edges; i++) {
        scanf("%d %d", &u, &v);
        addEdge(&graph, u, v);
    }

    if (isOneWayConnected(&graph)) {
        printf("The given graph is one-way connected.\n");
    } else {
        printf("The given graph is NOT one-way connected.\n");
    }

    return 0;
}
```

### 5. Greedy Algorithms to Reduce `n` to `1`

This program includes two functions: one for reducing `n` to `1` using only decrement and division by 2 (`greedy1`), and another using decrement, division by 2, and increment for optimization (`greedy2`).

```c
#include <stdio.h>

int greedy1(int n) {
    int steps = 0;
    while (n > 1) {
        if (n % 2 == 0) {
            n /= 2;
        } else {
            n -= 1;
        }
        steps++;
    }
    return steps;
}

int countFactorsOf2(int x) {
    int count = 0;
    while (x % 2 == 0) {
        x /= 2;
        count++;
    }
    return count;
}

int greedy2(int n) {
    int steps = 0;
    while (n > 1) {
        if (n % 2 == 0) {
            n /= 2;
        } else {
            // Evaluate n-1 and n+1 to see which has more factors of 2
            if (countFactorsOf2(n - 1) > countFactorsOf2(n + 1)) {
                n -= 1;
            } else {
                n += 1;
            }
        }
        steps++;
    }
    return steps;
}

int main() {
    int n;
    printf("Enter the value of n: ");
    scanf("%d", &n);

    int steps1 = greedy1(n);
    int steps2 = greedy2(n);

    printf("Using greedy1: %d steps to reduce %d to 1.\n", steps1, n);
    printf("Using greedy2: %d steps to reduce %d to 1.\n", steps2, n);

    return 0;
}
```

---

In both programs, each function implements a different reduction strategy. The `greedy1` function uses only decrement and division by 2, while `greedy2` introduces increment by 1 as well for cases when it might optimize the step count.

Let me know if you need further explanation on any part!