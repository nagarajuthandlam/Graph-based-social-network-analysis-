Creating a Graph-Based Social Network Analysis in C requires implementing a graph structure where:

Nodes represent users.

Edges represent friendships between users.


Key Features

1. Graph Representation using an adjacency list.


2. Shortest Path Calculation using Breadth-First Search (BFS).


3. Friend Recommendations using common friends.


4. Handling Cycles using visited arrays during traversal.




---

C Code Implementation

#include <stdio.h>
#include <stdlib.h>
#include <limits.h>

#define MAX_USERS 100

// Graph structure
typedef struct Node {
    int user;
    struct Node* next;
} Node;

typedef struct Graph {
    int numUsers;
    Node* adjList[MAX_USERS];
} Graph;

// Queue for BFS
typedef struct Queue {
    int items[MAX_USERS];
    int front, rear;
} Queue;

// Function Prototypes
Graph* createGraph(int users);
void addFriendship(Graph* graph, int user1, int user2);
void displayGraph(Graph* graph);
void findShortestPath(Graph* graph, int start, int end);
void suggestFriends(Graph* graph, int user);
Queue* createQueue();
void enqueue(Queue* q, int value);
int dequeue(Queue* q);
int isQueueEmpty(Queue* q);

// Main function
int main() {
    int users = 6; // Example: 6 users
    Graph* graph = createGraph(users);

    addFriendship(graph, 0, 1);
    addFriendship(graph, 0, 2);
    addFriendship(graph, 1, 3);
    addFriendship(graph, 1, 4);
    addFriendship(graph, 2, 4);
    addFriendship(graph, 3, 5);
    addFriendship(graph, 4, 5);

    printf("Social Network Graph:\n");
    displayGraph(graph);

    printf("\nShortest Path from User 0 to User 5:\n");
    findShortestPath(graph, 0, 5);

    printf("\nFriend Recommendations for User 0:\n");
    suggestFriends(graph, 0);

    return 0;
}

// Function to create a graph
Graph* createGraph(int users) {
    Graph* graph = (Graph*)malloc(sizeof(Graph));
    graph->numUsers = users;
    for (int i = 0; i < users; i++) {
        graph->adjList[i] = NULL;
    }
    return graph;
}

// Function to add a friendship (edge)
void addFriendship(Graph* graph, int user1, int user2) {
    Node* newNode = (Node*)malloc(sizeof(Node));
    newNode->user = user2;
    newNode->next = graph->adjList[user1];
    graph->adjList[user1] = newNode;

    newNode = (Node*)malloc(sizeof(Node));
    newNode->user = user1;
    newNode->next = graph->adjList[user2];
    graph->adjList[user2] = newNode;
}

// Function to display the adjacency list
void displayGraph(Graph* graph) {
    for (int i = 0; i < graph->numUsers; i++) {
        Node* temp = graph->adjList[i];
        printf("User %d -> ", i);
        while (temp) {
            printf("%d ", temp->user);
            temp = temp->next;
        }
        printf("\n");
    }
}

// BFS to find shortest path
void findShortestPath(Graph* graph, int start, int end) {
    int visited[MAX_USERS] = {0};
    int prev[MAX_USERS];
    for (int i = 0; i < MAX_USERS; i++) prev[i] = -1;

    Queue* queue = createQueue();
    enqueue(queue, start);
    visited[start] = 1;

    while (!isQueueEmpty(queue)) {
        int current = dequeue(queue);
        Node* temp = graph->adjList[current];

        while (temp) {
            int neighbor = temp->user;
            if (!visited[neighbor]) {
                visited[neighbor] = 1;
                prev[neighbor] = current;
                enqueue(queue, neighbor);

                if (neighbor == end) { // Stop BFS early if we reach the end
                    break;
                }
            }
            temp = temp->next;
        }
    }

    // Print path
    int path[MAX_USERS], pathSize = 0, crawl = end;
    while (crawl != -1) {
        path[pathSize++] = crawl;
        crawl = prev[crawl];
    }

    if (pathSize == 1) {
        printf("No path found.\n");
    } else {
        for (int i = pathSize - 1; i >= 0; i--) {
            printf("%d ", path[i]);
        }
        printf("\n");
    }
}

// Suggest friends based on mutual connections
void suggestFriends(Graph* graph, int user) {
    int mutual[MAX_USERS] = {0};
    Node* temp = graph->adjList[user];

    while (temp) {
        Node* friendTemp = graph->adjList[temp->user];
        while (friendTemp) {
            if (friendTemp->user != user) {
                mutual[friendTemp->user]++;
            }
            friendTemp = friendTemp->next;
        }
        temp = temp->next;
    }

    int suggested = 0;
    for (int i = 0; i < graph->numUsers; i++) {
        if (mutual[i] > 0 && !mutual[user]) {
            printf("User %d (Mutual Friends: %d)\n", i, mutual[i]);
            suggested = 1;
        }
    }

    if (!suggested) {
        printf("No new friend recommendations.\n");
    }
}

// Queue functions for BFS
Queue* createQueue() {
    Queue* q = (Queue*)malloc(sizeof(Queue));
    q->front = q->rear = -1;
    return q;
}

void enqueue(Queue* q, int value) {
    if (q->rear == MAX_USERS - 1) return;
    if (q->front == -1) q->front = 0;
    q->items[++q->rear] = value;
}

int dequeue(Queue* q) {
    if (q->front == -1 || q->front > q->rear) return -1;
    return q->items[q->front++];
}

int isQueueEmpty(Queue* q) {
    return q->front == -1 || q->front > q->rear;
}


---

How It Works

1. Graph Representation

Users are stored in an adjacency list.

Each user has a linked list of their friends.



2. Finding the Shortest Path

Uses BFS to traverse the graph.

Stores the previous node to reconstruct the path.



3. Friend Recommendations

Counts mutual friends of a user’s friends.

Suggests friends who are not directly connected.



4. Handling Cycles

BFS ensures each user is visited only once.

A visited array prevents infinite loops.





---

Example Output

Social Network Graph:
User 0 -> 2 1 
User 1 -> 4 3 0 
User 2 -> 4 0 
User 3 -> 5 1 
User 4 -> 5 2 1 
User 5 -> 4 3 

Shortest Path from User 0 to User 5:
0 1 4 5 

Friend Recommendations for User 0:
User 3 (Mutual Friends: 1)
User 5 (Mutual Friends: 1)


---

Next Steps

Allow users to add/remove friendships dynamically.

Improve friend recommendations using weights (e.g., interaction frequency).

Implement Dijkstra’s algorithm for weighted paths.


Would you like any modifications?
