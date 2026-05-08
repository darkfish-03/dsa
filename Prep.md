
You are given N Airflow tasks (labeled 0 to N−1) and a list of dependencies where dependencies[i]=[a,b] indicates that task a must be completed before task b can start. Find a valid order of execution.

```
#include <iostream>
#include <vector>
#include <queue>
#include <stdexcept>

using namespace std;

class TaskExecutionOrderEvaluator {
public:

    vector<int> evaluate(
        int numberOfTasks,
        const vector<vector<int>>& dependencies
    ) {

        vector<vector<int>> graph(numberOfTasks);
        vector<int> indegree(numberOfTasks, 0);

        // Build graph
        for (const auto& dependency : dependencies) {

            int prerequisite = dependency[0];
            int dependent = dependency[1];

            graph[prerequisite].push_back(dependent);
            indegree[dependent]++;
        }

        queue<int> readyTasks;

        // Add all independent tasks
        for (int task = 0; task < numberOfTasks; task++) {
            if (indegree[task] == 0) {
                readyTasks.push(task);
            }
        }

        vector<int> executionOrder;

        // Kahn's Algorithm
        while (!readyTasks.empty()) {

            int currentTask = readyTasks.front();
            readyTasks.pop();

            executionOrder.push_back(currentTask);

            for (int nextTask : graph[currentTask]) {

                indegree[nextTask]--;

                if (indegree[nextTask] == 0) {
                    readyTasks.push(nextTask);
                }
            }
        }

        // Cycle detection
        if (executionOrder.size() != numberOfTasks) {
            throw runtime_error(
                "Cycle detected. No valid execution order exists."
            );
        }

        return executionOrder;
    }
};

void printOrder(const vector<int>& order) {

    for (int task : order) {
        cout << task << " ";
    }

    cout << endl;
}

void testSimpleDag() {

    cout << "Running Simple DAG Test" << endl;

    TaskExecutionOrderEvaluator evaluator;

    vector<vector<int>> dependencies = {
        {0, 1},
        {0, 2},
        {1, 3},
        {2, 3}
    };

    vector<int> result =
        evaluator.evaluate(4, dependencies);

    cout << "Execution Order: ";
    printOrder(result);

    cout << endl;
}

void testNoDependencies() {

    cout << "Running No Dependency Test" << endl;

    TaskExecutionOrderEvaluator evaluator;

    vector<vector<int>> dependencies = {};

    vector<int> result =
        evaluator.evaluate(3, dependencies);

    cout << "Execution Order: ";
    printOrder(result);

    cout << endl;
}

void testCycleDetection() {

    cout << "Running Cycle Detection Test" << endl;

    TaskExecutionOrderEvaluator evaluator;

    vector<vector<int>> dependencies = {
        {0, 1},
        {1, 2},
        {2, 0}
    };

    try {

        evaluator.evaluate(3, dependencies);

        cout << "Expected cycle but none detected" << endl;
    }
    catch (const exception& e) {

        cout << "Exception Caught: "
             << e.what()
             << endl;
    }

    cout << endl;
}

void runAllTests() {

    testSimpleDag();

    testNoDependencies();

    testCycleDetection();
}

int main() {

    runAllTests();

    return 0;
}

TC : O(V+E)
SC : O(V+E)

```


At Airbnb, we want each person to have only one account to ensure trust and prevent misuse of our platform. Sometimes, people can end up with multiple accounts by using the same email, phone number, governement Id, same device or other identity information. We need a way to find accounts that are duplicates of one another.

Given a list of Airbnb accounts (each named "A", "B", etc.) with their identity attributes (such as email address, phone number, etc) build a function that will return duplicate accounts for any given account.

Input:

Accounts = [
    {name: A, email: alice@example.com, phone: 12345},
    {name: B, email: bob@example.com, phone: 23456},
    {name: C, email: ALICE@example.com, phone: 67890},
    {name: D, email: doug@example.com, phone: 12345},
]
account = A
Output:

Duplicate Accounts = [C, D]
Follow-up: what if we add another identifier GovtId?

```
#include <iostream>
#include <vector>
#include <unordered_map>
#include <algorithm>

using namespace std;

class Account {
public:
    string accountId;

    // {attributeType, attributeValue}
    vector<pair<string, string>> attributes;

    Account(string id,
            vector<pair<string, string>> attrs) {

        accountId = id;
        attributes = attrs;
    }
};

class DSU {
private:
    vector<int> parent;
    vector<int> rank;

public:

    DSU(int n) {

        parent.resize(n);
        rank.resize(n, 0);

        for (int i = 0; i < n; i++) {
            parent[i] = i;
        }
    }

    int find(int x) {

        if (parent[x] != x) {
            parent[x] = find(parent[x]); // Path compression
        }

        return parent[x];
    }

    void unite(int x, int y) {

        int rootX = find(x);
        int rootY = find(y);

        if (rootX == rootY) {
            return;
        }

        // Union by rank
        if (rank[rootX] < rank[rootY]) {
            parent[rootX] = rootY;
        }
        else if (rank[rootX] > rank[rootY]) {
            parent[rootY] = rootX;
        }
        else {
            parent[rootY] = rootX;
            rank[rootX]++;
        }
    }
};

class DuplicateAccountFinder {

private:

    string normalize(string type, string value) {

        // Example normalization

        if (type == "EMAIL") {

            transform(value.begin(),
                      value.end(),
                      value.begin(),
                      ::tolower);
        }

        return value;
    }

public:

    vector<string> findDuplicates(vector<Account>& accounts,
                                  string targetAccountId) {

        int n = accounts.size();

        DSU dsu(n);

        // key => TYPE:VALUE
        // value => first account index
        unordered_map<string, int> attributeMap;

        // Build connected components
        for (int i = 0; i < n; i++) {

            for (auto& attribute : accounts[i].attributes) {

                string type = attribute.first;
                string value = normalize(type,
                                         attribute.second);

                string key = type + ":" + value;

                if (attributeMap.count(key)) {

                    dsu.unite(i, attributeMap[key]);
                }
                else {
                    attributeMap[key] = i;
                }
            }
        }
        
        // Find target account
        int targetIndex = -1;

        for (int i = 0; i < n; i++) {

            if (accounts[i].accountId ==
                targetAccountId) {

                targetIndex = i;
                break;
            }
        }

        if (targetIndex == -1) {
            return {};
        }

        int targetRoot = dsu.find(targetIndex);

        vector<string> duplicates;

        // Collect all accounts
        // in same connected component
        for (int i = 0; i < n; i++) {

            if (i == targetIndex) {
                continue;
            }

            if (dsu.find(i) == targetRoot) {
                duplicates.push_back(accounts[i].accountId);
            }
        }

        return duplicates;
      
    }
};

int main() {

    vector<Account> accounts = {

        Account(
            "A",
            {
                {"EMAIL", "alice@example.com"},
                {"PHONE", "12345"},
                {"GOVT_ID", "P123"}
            }
        ),

        Account(
            "B",
            {
                {"EMAIL", "bob@example.com"},
                {"PHONE", "23456"}
            }
        ),

        Account(
            "C",
            {
                {"EMAIL", "ALICE@example.com"},
                {"PHONE", "67890"}
            }
        ),

        Account(
            "D",
            {
                {"EMAIL", "doug@example.com"},
                {"PHONE", "12345"}
            }
        ),

        Account(
            "E",
            {
                {"GOVT_ID", "P123"}
            }
        )
    };

    DuplicateAccountFinder finder;

    vector<string> result =
        finder.findDuplicates(accounts, "A");

    cout << "Duplicate accounts for A: ";

    for (string accountId : result) {
        cout << accountId << " ";
    }

    cout << endl;

    return 0;
}

```
In a popular travel destination, several Airbnb hosts offer unique accommodations. Each host has a listing located at a specific point in the city and can influence other listings nearby. Each property is represented by a 0-indexed 2D integer array listings, where listings[i] = [xi, yi, ri]. Here, (xi, yi) represents the X-coordinate and Y-coordinate of the i-th host's property, while ri denotes the radius of their influence.

When a guest books a property, that booking will result in nearby hosts to receive a booking as well, provided their listings fall within the influence radius of the original booking. The influence of one property can create a ripple effect, activating multiple bookings.

Your task is to determine the maximum number of listings that can receive bookings if a guest initially books only one property.

```
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

class MaxBookingEvaluator {

private:

    int dfs(int listing,
            vector<vector<int>>& graph,
            vector<bool>& visited) {

        visited[listing] = true;

        int bookings = 1;

        for (int neighbor : graph[listing]) {

            if (!visited[neighbor]) {

                bookings += dfs(neighbor,
                                graph,
                                visited);
            }
        }

        return bookings;
    }

    bool canInfluence(vector<int>& source,
                      vector<int>& target) {

        long long x1 = source[0];
        long long y1 = source[1];
        long long radius = source[2];

        long long x2 = target[0];
        long long y2 = target[1];

        long long dx = x1 - x2;
        long long dy = y1 - y2;

        long long distanceSquared =
            dx * dx + dy * dy;

        long long radiusSquared =
            radius * radius;

        return distanceSquared <= radiusSquared;
    }

public:

    int evaluate(vector<vector<int>>& listings) {

        int numberOfListings = listings.size();

        vector<vector<int>> graph(numberOfListings);

        // Build directed graph
        for (int i = 0; i < numberOfListings; i++) {

            for (int j = 0; j < numberOfListings; j++) {

                if (i == j) {
                    continue;
                }

                if (canInfluence(listings[i],
                                 listings[j])) {

                    graph[i].push_back(j);
                }
            }
        }

        int maximumBookings = 0;

        // Try starting from every listing
        for (int i = 0; i < numberOfListings; i++) {

            vector<bool> visited(numberOfListings,
                                 false);

            int currentBookings =
                dfs(i,
                    graph,
                    visited);

            maximumBookings =
                max(maximumBookings,
                    currentBookings);
        }

        return maximumBookings;
    }
};

int main() {

    vector<vector<int>> listings = {
        {2, 1, 3},
        {6, 1, 4}
    };

    MaxBookingEvaluator evaluator;

    int result =
        evaluator.evaluate(listings);

    cout << "Maximum bookings: "
         << result << endl;

    return 0;
}

```

You are playing a board game represented by a grid. Each cell in the grid is described by a two-character string:

The first character represents the area type (e.g., G, W, S).
The second character represents the number of crowns in that cell.
Example board:

[
  ["G1", "G2", "W0", "W1", "S1"],
  ["G2", "G3", "W0", "W1", "S1"],
  ["S2", "S3", "S1", "G1", "S1"],
  ["G1", "G2", "W0", "W1", "S1"],
  ["G1", "G2", "W0", "W1", "S1"]
]
Scoring Rules
An area is defined as a group of orthogonally connected cells (up, down, left, right) with the same area type.
For each distinct area:
Count the number of cells in the area.
Sum the number of crowns across all cells in the area.
The area’s score is calculated as: (area size) × (total crowns in that area)
The total score is the sum of the scores of all areas on the board.
Example
For the G area starting at position (0, 0):
Area size = 4 cells
Total crowns = 8
Area score = 4 × 8 = 32
For the W area starting at position (0, 2):
Area size = 4 cells
Total crowns = 2
Area score = 4 × 2 = 8
Calculate and return the total score for the entire board.
```
#include <iostream>
#include <vector>
#include <queue>

using namespace std;

class KingdomScoreCalculator {

private:

    vector<pair<int, int>> directions = {
        {1, 0},
        {-1, 0},
        {0, 1},
        {0, -1}
    };

public:

    int calculateScore(vector<vector<string>>& board) {

        int rows = board.size();
        int cols = board[0].size();

        vector<vector<bool>> visited(
            rows,
            vector<bool>(cols, false)
        );

        int totalScore = 0;

        for (int row = 0; row < rows; row++) {

            for (int col = 0; col < cols; col++) {

                if (visited[row][col]) {
                    continue;
                }

                char terrainType =
                    board[row][col][0];

                int areaSize = 0;
                int crownCount = 0;

                queue<pair<int, int>> bfsQueue;

                bfsQueue.push({row, col});
                visited[row][col] = true;

                while (!bfsQueue.empty()) {

                    auto current =
                        bfsQueue.front();

                    bfsQueue.pop();

                    int currentRow = current.first;
                    int currentCol = current.second;

                    areaSize++;

                    crownCount +=
                        board[currentRow][currentCol][1] - '0';

                    for (auto& direction : directions) {

                        int newRow =
                            currentRow + direction.first;

                        int newCol =
                            currentCol + direction.second;

                        if (newRow >= 0 &&
                            newRow < rows &&
                            newCol >= 0 &&
                            newCol < cols &&
                            !visited[newRow][newCol] &&
                            board[newRow][newCol][0] == terrainType) {

                            visited[newRow][newCol] = true;

                            bfsQueue.push({newRow, newCol});
                        }
                    }
                }

                totalScore +=
                    areaSize * crownCount;
            }
        }

        return totalScore;
    }
};

int main() {

    vector<vector<string>> board = {

        {"G1", "G2", "W0", "W1", "S1"},
        {"G2", "G3", "W0", "W1", "S1"},
        {"S2", "S3", "S1", "G1", "S1"},
        {"G1", "G2", "W0", "W1", "S1"},
        {"G1", "G2", "W0", "W1", "S1"}
    };

    KingdomScoreCalculator calculator;

    cout << "Total Score: "
         << calculator.calculateScore(board)
         << endl;

    return 0;
}
```

Given a list of strings, create a mapping where each value is the shortest unique substring that can uniquely identify its corresponding key string from all other strings in the list. This simulates an auto-complete system where typing the minimum unique substring should uniquely identify the full string.

Input
A list of strings

Output
A dictionary/map where:

Keys are the original full strings
Values are the shortest unique substrings that can uniquely identify each key
Example
Input
[
    'The Phantom Menace',
    'Attack of the Clones',
    'Revenge of the Sith',
    'A New Hope',
    'The Empire Strikes Back',
    'The Return of the Jedi',
    'The Force Awakens',
    'The Last Jedi'
]
Output
{
    'The Phantom Menace': 'to',
    'Attack of the Clones': 'tt',
    'Revenge of the Sith': 'v',
    'A New Hope': 'ho',
    'The Empire Strikes Back': 'b',
    'The Return of the Jedi': 'u',
    'The Force Awakens': 'aw',
    'The Last Jedi': 'tj'
}
```

#include <iostream>
#include <vector>
#include <string>
#include <unordered_map>

using namespace std;

// Node structure for the Trie
struct TrieNode {
    unordered_map<char, TrieNode*> children;
    int count = 0;

    // Optional: Destructor to prevent memory leaks in a real interview
    ~TrieNode() {
        for (auto& pair : children) {
            delete pair.second;
        }
    }
};

class SuffixTrie {
public:
    SuffixTrie() {
        root = new TrieNode();
    }

    ~SuffixTrie() {
        delete root;
    }

    // Insert all possible suffixes of a word
    void insertsuffixes(const string& word) {
        for (int i = 0; i < word.length(); ++i) {
            TrieNode* curr = root;
            for (int j = i; j < word.length(); ++j) {
                char c = word[j];
                if (curr->children.find(c) == curr->children.end()) {
                    curr->children[c] = new TrieNode();
                }
                curr = curr->children[c];
                curr->count++; // Tracks how many strings share this substring
            }
        }
    }

    // Find the shortest substring that has a count of exactly 1
    string findShortestUnique(const string& word) {
        string shortest = word;
        
        for (int i = 0; i < word.length(); ++i) {
            TrieNode* curr = root;
            string currentSub = "";
            for (int j = i; j < word.length(); ++j) {
                char c = word[j];
                currentSub += c;
                curr = curr->children[c];

                if (curr->count == 1) {
                    if (currentSub.length() < shortest.length()) {
                        shortest = currentSub;
                    }
                    break; // Move to next suffix; this is the shortest for this start point
                }
            }
        }
        return shortest;
    }

private:
    TrieNode* root;
};

unordered_map<string, string> getShortestUniqueMapping(const vector<string>& inputs) {
    SuffixTrie trie;
    
    // Pass 1: Build the Trie with all suffixes
    for (const string& s : inputs) {
        trie.insertsuffixes(s);
    }

    // Pass 2: Query each string for its shortest unique substring
    unordered_map<string, string> result;
    for (const string& s : inputs) {
        result[s] = trie.findShortestUnique(s);
    }
    return result;
}

int main() {
    vector<string> inputs = {
        "The Phantom Menace", "Attack of the Clones", "Revenge of the Sith",
        "A New Hope", "The Empire Strikes Back", "The Return of the Jedi",
        "The Force Awakens", "The Last Jedi"
    };

    auto mapping = getShortestUniqueMapping(inputs);

    for (const auto& pair : mapping) {
        cout << "'" << pair.first << "': '" << pair.second << "'" << endl;
    }

    return 0;
}
l, l-1, l-2, 1 = l(l+1)/2
TC : O(N*L^2)
```
String str = "Leetcode is a good community for software developers"
String text = "is for developers"

O/P: [1,7] since the given text is found between index 1 to 7 in given string

String str = "Leetcode is a good community for software developers"
String text = "is for lawyers"

O/P: [-1,-1] since all the words are not found in the given string

String str = "Leetcode is a good community for software developers"
String text = "developers for"

O/P: [-1,-1] since all the words in the text are not in the same order as in the string

```
#include <iostream>
#include <vector>
#include <string>
#include <sstream>

using namespace std;

// Helper function to split string into words
vector<string> split(const string& s) {
    vector<string> words;
    stringstream ss(s);
    string word;
    while (ss >> word) {
        words.push_back(word);
    }
    return words;
}

vector<int> findWordRange(string str, string text) {
    vector<string> strWords = split(str);
    vector<string> textWords = split(text);

    if (textWords.empty()) return {-1, -1};

    int textPtr = 0;
    int startIdx = -1;
    int endIdx = -1;

    for (int i = 0; i < strWords.size(); ++i) {
        if (strWords[i] == textWords[textPtr]) {
            if (textPtr == 0) {
                startIdx = i; // Mark the beginning of the match
            }
            
            textPtr++;

            if (textPtr == textWords.size()) {
                endIdx = i; // Mark the end of the match
                return {startIdx, endIdx};
            }
        }
    }

    return {-1, -1};
}

int main() {
    string str = "Leetcode is a good community for software developers";
    
    // Case 1: Success
    vector<int> res1 = findWordRange(str, "is for developers");
    cout << "[" << res1[0] << ", " << res1[1] << "]" << endl; // Output: [1, 7]

    // Case 2: Missing words
    vector<int> res2 = findWordRange(str, "is for lawyers");
    cout << "[" << res2[0] << ", " << res2[1] << "]" << endl; // Output: [-1, -1]

    // Case 3: Wrong order
    vector<int> res3 = findWordRange(str, "developers for");
    cout << "[" << res3[0] << ", " << res3[1] << "]" << endl; // Output: [-1, -1]

    return 0;
}

```
