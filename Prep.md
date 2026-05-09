
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

Given input of digit array, e.g. [4,1,8] return the smallest number can be created using all digits. For example [7,3,0,1] --> "1037". Note the result might overflow so you should return a string. You also don't need to use 0. For example, [0,1,2] should return "12".

Follow up 1: What's the time complexity, can you do in O(N) time?

Follow up 2: Similar to the original question with an additional lower bound as input. You need to make sure the number is greater than or equal to the lower bound. For example, [7,1,8] and lower bound = 719 should return "781"

```
#include <iostream>
#include <vector>
#include <string>

using namespace std;

string smallestNumber(const vector<int>& digits) {
    // 1. Count frequencies of each digit (0-9)
    // Space: O(1) as the size is fixed at 10
    int counts[10] = {0};
    for (int d : digits) {
        if (d >= 0 && d <= 9) {
            counts[d]++;
        }
    }

    string result = "";

    // 2. Build string using digits 1 through 9
    // Per instructions: "You also don't need to use 0"
    for (int i = 1; i <= 9; ++i) {
        // Append digit 'i', counts[i] times
        result.append(counts[i], (char)(i + '0'));
    }

    return result.empty() ? "0" : result;
}

int main() {
    // Example 1: [4, 1, 8] -> "148"
    cout << "Input [4,1,8]: " << smallestNumber({4, 1, 8}) << endl;

    // Example 2: [7, 3, 0, 1] -> "137" (skipping 0)
    cout << "Input [7,3,0,1]: " << smallestNumber({7, 3, 0, 1}) << endl;

    // Example 3: [0, 1, 2] -> "12"
    cout << "Input [0,1,2]: " << smallestNumber({0, 1, 2}) << endl;

    return 0;
}

```
URL parser 
```
#include <iostream>
#include <vector>
#include <string>
#include <unordered_map>
#include <sstream>

using namespace std;

// Step 1: Helper to handle %XX decoding
string decode(string s) {
    string res = "";
    for (int i = 0; i < s.length(); i++) {
        if (s[i] == '%' && i + 2 < s.length()) {
            // Convert hex to char (e.g., %26 -> '&')
            res += (char)stoi(s.substr(i + 1, 2), nullptr, 16);
            i += 2;
        } else {
            res += s[i];
        }
    }
    return res;
}

void parseURL(string url) {
    // 1. Boundary Check: Find '?' and ensure there's content after it
    size_t qPos = url.find('?');
    if (qPos == string::npos || qPos == url.length() - 1) {
        cout << "{}" << endl;
        return;
    }

    // 2. Extract the query part and split by '&'
    string query = url.substr(qPos + 1);
    stringstream ss(query);
    string segment;
    
    // We'll use a map of vectors to simplify the "single vs multiple" logic
    unordered_map<string, vector<string>> storage;

    while (getline(ss, segment, '&')) {
        if (segment.empty()) continue; // Handles extra '&&'

        size_t eqPos = segment.find('=');
        if (eqPos == string::npos) {
            // Case: ?book (No '=' means value is "true")
            storage[decode(segment)].push_back("true");
        } else {
            // Case: ?key=value
            string key = decode(segment.substr(0, eqPos));
            string val = decode(segment.substr(eqPos + 1));
            storage[key].push_back(val);
        }
    }

    // 3. Final Formatting (Match the specific requirements)
    cout << "{ ";
    for (auto const& [key, values] : storage) {
        cout << key << ": ";
        if (values.size() > 1) {
            // Print as Array
            cout << "[";
            for(int i=0; i<values.size(); i++) 
                cout << "\"" << values[i] << "\"" << (i == values.size()-1 ? "" : ", ");
            cout << "] ";
        } else {
            // Print as Single String or Boolean
            if (values[0] == "true") cout << "true ";
            else cout << "\"" << values[0] << "\" ";
        }
    }
    cout << "}" << endl;
}

int main() {
    parseURL("http://api/movie?year=100&type=action");
    parseURL("http://api/movie?type%26view=scifi&type=action");
    parseURL("http://api/movie?book");
    parseURL("http://api/movie"); // No '?'
    return 0;
}

```

#include <iostream>
#include <string>
#include <unordered_map>
#include <sstream>

using namespace std;

// Simple Decode: Handles hex and '+' for spaces
string decode(string s) {
    string res = "";
    for (int i = 0; i < s.length(); i++) {
        if (s[i] == '%' && i + 2 < s.length()) {
            res += (char)stoi(s.substr(i + 1, 2), nullptr, 16);
            i += 2;
        } else if (s[i] == '+') {
            res += ' ';
        } else {
            res += s[i];
        }
    }
    return res;
}

Given a URL containing query parameters, extract all key-value pairs after the ? symbol.

If a key has a single value, store it as a string.
If a key appears multiple times, store its values as an array.
If a key appears without an explicit value, store it as true.
Decode special URL-encoded characters (e.g., %26 should be replaced with &, and %3D should be replaced with =).

```
void parseURL(string url) {
    size_t qPos = url.find('?');
    if (qPos == string::npos || qPos == url.length() - 1) {
        cout << "Empty Map" << endl;
        return;
    }

    unordered_map<string, string> result;
    stringstream ss(url.substr(qPos + 1));
    string segment;

    while (getline(ss, segment, '&')) {
        size_t eqPos = segment.find('=');
        string key = decode(segment.substr(0, eqPos));
        string val = (eqPos == string::npos) ? "" : decode(segment.substr(eqPos + 1));

        // If key exists, append with a comma; otherwise, create it
        if (result.count(key)) {
            result[key] += ", " + val;
        } else {
            result[key] = val;
        }
    }

    // Print the output
    for (auto const& [k, v] : result) {
        cout << k << " : " << (v == "" ? "true" : v) << endl;
    }
}

int main() {
    parseURL("http://api.com");
    return 0;
}
```

Pagination

```
#include <iostream>
#include <vector>
#include <string>
#include <unordered_set>

using namespace std;

class DisplayPage {
public:

    vector<string> displayPages(vector<string>& input, int pageSize) {

        vector<string> result;

        if (input.empty() || pageSize <= 0) {
            return result;
        }

        int n = input.size();

        // Instead of deleting from list
        // mark entries as used
        vector<bool> used(n, false);

        int remaining = n;

        unordered_set<string> seenHosts;

        bool allowDuplicates = false;

        int count = 0;

        int i = 0;

        while (remaining > 0) {

            // Reached end -> restart traversal
            if (i == n) {

                i = 0;

                // allow duplicates after one full pass
                allowDuplicates = true;
            }

            // Skip already used entries
            if (used[i]) {
                i++;
                continue;
            }

            string curr = input[i];

            int commaIndex = curr.find(',');

            string hostId = curr.substr(0, commaIndex);

            // Add if unique OR duplicates allowed
            if (seenHosts.find(hostId) == seenHosts.end() || allowDuplicates) {

                result.push_back(curr);

                seenHosts.insert(hostId);

                used[i] = true;

                remaining--;

                count++;
            }

            i++;

            // Page completed
            if (count == pageSize) {

                if (remaining > 0) {
                    result.push_back(" ");
                }

                seenHosts.clear();

                allowDuplicates = false;

                count = 0;

                i = 0;
            }
        }

        return result;
    }
};

void printResult(vector<string>& result) {

    for (string& s : result) {

        if (s == " ")
            cout << "---- PAGE BREAK ----" << endl;
        else
            cout << s << endl;
    }
}

int main() {

    vector<string> input = {
        "1,28,300.1,SanFrancisco",
        "4,5,209.1,SanFrancisco",
        "20,7,208.1,SanFrancisco",
        "23,8,207.1,SanFrancisco",
        "16,10,206.1,Oakland",
        "1,16,205.1,SanFrancisco",
        "6,29,204.1,SanFrancisco",
        "7,20,203.1,SanFrancisco",
        "8,21,202.1,SanFrancisco",
        "2,18,201.1,SanFrancisco",
        "2,30,200.1,SanFrancisco",
        "15,27,109.1,Oakland",
        "10,13,108.1,Oakland",
        "11,26,107.1,Oakland"
    };

    DisplayPage solution;

    vector<string> result = solution.displayPages(input, 5);

    printResult(result);

    return 0;
}
```

Arithmetic RLE 

```
vector<vector<int>> encoder(vector<int> data) {
    if (data.empty()) return {};
    if (data.size() == 1) return {{data[0], 0, 1}};

    vector<vector<int>> ans;
    int prev = data[0];
    int cnt = 1;
    // Fix 1: Calculate diff using the first two elements
    int diff = data[1] - data[0]; 
    
    for(int i = 1; i < data.size(); i++) {
        // Check if the current element continues the existing arithmetic pattern
        if(data[i] - data[i-1] == diff) {
            cnt++;
        } else {
            // Pattern broke: save the current run
            ans.push_back({prev, diff, cnt});
            
            // Fix 2: Reset for the new sequence
            prev = data[i];
            cnt = 1;
            // Fix 3: Check if there's another element to establish a new diff
            if (i + 1 < data.size()) {
                diff = data[i+1] - data[i];
            } else {
                diff = 0; // Default for a single trailing element
            }
        }
    }
    // Fix 4: Push the final sequence remaining after the loop ends
    ans.push_back({prev, diff, cnt});
    
    return ans;
}

#include <vector>
#include <iostream>

#include <vector>
#include <iostream>

class ArithmeticDecoder {
private:
    // Store a reference to the data to avoid copying
    const std::vector<std::vector<int>>& encodedData;
    size_t currentTupleIdx = 0;
    int currentElementInRun = 0;

public:
    // Constructor takes a const reference
    ArithmeticDecoder(const std::vector<std::vector<int>>& data) 
        : encodedData(data) {}

    bool hasNext() const {
        return currentTupleIdx < encodedData.size();
    }

    int next() {
        // Accessing the current tuple (run)
        const auto& run = encodedData[currentTupleIdx];
        
        // Calculate the nex
t value in the arithmetic sequence
        int value = run[0] + (currentElementInRun * run[1]);

        currentElementInRun++;

        // If we've exhausted this run, move to the next tuple
        if (currentElementInRun >= run[2]) {
            currentTupleIdx++;
            currentElementInRun = 0;
        }

        return value;
    }
};

int main() {
    std::vector<std::vector<int>> encoded = {{3, 1, 3}, {10, 5, 4}};
    ArithmeticDecoder it(encoded);

    while (it.hasNext()) {
        std::cout << it.next() << " ";
    }
    // Output: 3 4 5 10 15 20 25
    return 0;
}


```

List of List Iterator

```
#include <iostream>
#include <vector>
#include <stdexcept>

using namespace std;

class Vector2DIterator {
private:

    int row;
    int col;

    int lastRow;
    int lastCol;

    bool canRemove;

    vector<vector<int>>& vec2d;

public:

    Vector2DIterator(vector<vector<int>>& vec)
        : row(0),
          col(0),
          lastRow(-1),
          lastCol(-1),
          canRemove(false),
          vec2d(vec) {
    }

    int next() {

        if (!hasNext()) {
            throw runtime_error("No more elements");
        }

        int val = vec2d[row][col];

        lastRow = row;
        lastCol = col;

        col++;

        canRemove = true;

        return val;
    }

    bool hasNext() {

        while (row < vec2d.size()) {

            if (col < vec2d[row].size()) {
                return true;
            }

            row++;
            col = 0;
        }

        return false;
    }

    void remove() {

        if (!canRemove) {
            throw runtime_error(
                "remove() can only be called once after next()"
            );
        }

        vec2d[lastRow].erase(
            vec2d[lastRow].begin() + lastCol
        );

        // If current row affected,
        // shift col back by one
        if (lastRow == row) {
            col--;
        }

        // Remove empty row
        if (vec2d[lastRow].empty()) {

            vec2d.erase(vec2d.begin() + lastRow);

            if (lastRow < row) {
                row--;
            }
        }

        canRemove = false;
    }
};

int main() {

    vector<vector<int>> input = {
        {1, 2},
        {},
        {3, 4},
        {5}
    };

    Vector2DIterator it(input);

    while (it.hasNext()) {

        int val = it.next();

        cout << val << " ";

        if (val == 3) {
            it.remove();
        }
    }

    cout << endl;

    cout << "\nAfter remove():\n";

    for (auto& row : input) {

        for (int num : row) {
            cout << num << " ";
        }

        cout << endl;
    }

    return 0;
}
```


Terrain Renderer

```
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

using namespace std;

class TerrainRenderer {
public:

    void render(const vector<int>& heights) {

        if (heights.empty()) {
            return;
        }

        int maxHeight = *max_element(
            heights.begin(),
            heights.end()
        );

        // Print terrain top -> bottom
        for (int level = maxHeight; level >= 1; --level) {

            string row;

            for (int height : heights) {

                row += (height >= level) ? '+' : ' ';
            }

            cout << row << endl;
        }

        // Base layer
        cout << string(heights.size(), '+')
             << " <--- base layer"
             << endl;
    }
};

int main() {

    vector<int> terrain = {
        5, 4, 3, 2, 1,
        3, 4, 0, 3, 4
    };

    TerrainRenderer renderer;

    renderer.render(terrain);

    return 0;
}

```
Each water drop changes the effective landscape,
so flow decisions must be recomputed dynamically.
```
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

class WaterLand {
public:

    void pourWater(vector<int>& heights,
                   int k,
                   int water) {

        int n = heights.size();

        // Separate water layer
        vector<int> waters(n, 0);

        while (water--) {

            int best = k;

            // -------------------------
            // Try LEFT
            // -------------------------
            for (int i = k - 1; i >= 0; --i) {

                int curr =
                    heights[i] + waters[i];

                int next =
                    heights[best] + waters[best];

                // uphill -> stop
                if (curr > next) {
                    break;
                }

                // better position found
                if (curr < next) {
                    best = i;
                }
            }

            // -------------------------
            // Try RIGHT
            // -------------------------
            if (best == k) {

                for (int i = k + 1; i < n; ++i) {

                    int curr =
                        heights[i] + waters[i];

                    int next =
                        heights[best] + waters[best];

                    if (curr > next) {
                        break;
                    }

                    if (curr < next) {
                        best = i;
                    }
                }
            }

            // settle water
            waters[best]++;
        }

        render(heights, waters);
    }

private:

    void render(vector<int>& heights,
                vector<int>& waters) {

        int n = heights.size();

        int maxHeight = 0;

        for (int i = 0; i < n; ++i) {

            maxHeight = max(
                maxHeight,
                heights[i] + waters[i]
            );
        }

        // top -> bottom
        for (int level = maxHeight;
             level >= 1;
             --level) {

            for (int i = 0; i < n; ++i) {

                // terrain
                if (level <= heights[i]) {

                    cout << "+";
                }
                // water
                else if (
                    level <= heights[i] + waters[i]
                ) {

                    cout << "W";
                }
                else {

                    cout << " ";
                }
            }

            cout << endl;
        }

        cout << string(n, '+')
             << " <--- base layer"
             << endl;
    }
};

int main() {

    vector<int> terrain = {
        5,4,2,1,2,3,2,1,0,1,2,4
    };

    WaterLand solution;

    solution.pourWater(
        terrain,
        5,   // drop index
        10   // water units
    );

    return 0;
}
```
```


https://algo.monster/liteproblems/1257
```
class CommonRegionFinder {
public:

    string find(string region1,
                string region2,
                vector<vector<string>>& regionList) {

        unordered_map<string, string> parent;

        // child -> parent mapping
        for (const auto& regions : regionList) {

            string parentRegion = regions[0];

            for (int i = 1; i < regions.size(); i++) {
                parent[regions[i]] = parentRegion;
            }
        }

        // store all ancestors of region1
        unordered_set<string> ancestors;

        string current = region1;

        while (!current.empty()) {
            ancestors.insert(current);

            if (!parent.count(current)) {
                break;
            }

            current = parent[current];
        }

        // move region2 upward
        current = region2;

        while (!current.empty()) {

            if (ancestors.count(current)) {
                return current;
            }

            if (!parent.count(current)) {
                break;
            }

            current = parent[current];
        }

        return "";
    }
};
class Solution {
public:

    TreeNode* lowestCommonAncestor(TreeNode* root,
                                   TreeNode* p,
                                   TreeNode* q) {

        // base case
        if (root == nullptr ||
            root == p ||
            root == q) {
            return root;
        }

        TreeNode* leftLCA =
            lowestCommonAncestor(root->left, p, q);

        TreeNode* rightLCA =
            lowestCommonAncestor(root->right, p, q);

        // both sides returned non-null
        // current node is LCA
        if (leftLCA && rightLCA) {
            return root;
        }

        // otherwise return non-null side
        return leftLCA ? leftLCA : rightLCA;
    }
};
```
Key Value Store

```
#include <iostream>
#include <unordered_map>
#include <unordered_set>
#include <vector>

using namespace std;

class KeyValueStore {

private:

    // stores computed values
    unordered_map<string, int> values;

    // formula dependencies
    // c -> [a, a, b]
    unordered_map<string, vector<string>> formulas;

    // reverse dependency graph
    // a -> {c}
    unordered_map<string, unordered_set<string>> dependents;

public:

    // O(number of affected dependent nodes)
    void set_value(const string& key, int value) {

        // remove previous formula dependencies
        clear_dependencies(key);

        // key becomes direct value node
        formulas.erase(key);

        values[key] = value;

        // propagate updates
        recompute_dependents(key);
    }

    // O(number of dependencies + affected graph)
    void set_sum(const string& key,
                 const vector<string>& deps) {

        // remove old dependencies
        clear_dependencies(key);

        // store formula
        formulas[key] = deps;

        // build reverse dependency graph
        for (const string& dep : deps) {

            dependents[dep].insert(key);
        }

        // compute current value
        recompute(key);
    }

    // O(1)
    int get_value(const string& key) {

        if (!values.count(key)) {
            return 0;
        }

        return values[key];
    }

private:

    void clear_dependencies(const string& key) {

        if (!formulas.count(key)) {
            return;
        }

        for (const string& dep : formulas[key]) {

            dependents[dep].erase(key);
        }
    }

    void recompute(const string& key) {

        // recompute formula value
        if (formulas.count(key)) {

            int sum = 0;

            for (const string& dep : formulas[key]) {

                sum += get_value(dep);
            }

            values[key] = sum;
        }

        // propagate further
        recompute_dependents(key);
    }

    void recompute_dependents(const string& key) {

        if (!dependents.count(key)) {
            return;
        }

        for (const string& dependent : dependents[key]) {

            recompute(dependent);
        }
    }
};

int main() {

    KeyValueStore store;

    store.set_value("a", 5);
    store.set_value("b", 10);

    store.set_sum("c", {"a", "a", "b"});

    cout << store.get_value("c") << endl;
    // 20

    store.set_value("a", 7);

    cout << store.get_value("c") << endl;
    // 24

    store.set_value("c", 11);

    cout << store.get_value("c") << endl;
    // 11

    return 0;
}

```
Crates

```
class Solution {
public:
    int maxCandies(vector<int>& status, vector<int>& candies,
                   vector<vector<int>>& keys,
                   vector<vector<int>>& containedBoxes,
                   vector<int>& initialBoxes) {
        int n = status.size();
        vector<bool> can_open(n), has_box(n), used(n);
        for (int i = 0; i < n; ++i) {
            can_open[i] = (status[i] == 1);
        }

        queue<int> q;
        int ans = 0;
        for (int box : initialBoxes) {
            has_box[box] = true;
            if (can_open[box]) {
                q.push(box);
                used[box] = true;
                ans += candies[box];
            }
        }

        while (!q.empty()) {
            int big_box = q.front();
            q.pop();
            for (int key : keys[big_box]) {
                can_open[key] = true;
                if (!used[key] && has_box[key]) {
                    q.push(key);
                    used[key] = true;
                    ans += candies[key];
                }
            }
            for (int box : containedBoxes[big_box]) {
                has_box[box] = true;
                if (!used[box] && can_open[box]) {
                    q.push(box);
                    used[box] = true;
                    ans += candies[box];
                }
            }
        }

        return ans;
    }
};
O(n^2)
```

Sliding Puzzle

```
class Solution {
public:
    int slidingPuzzle(vector<vector<int>>& board) {
        string curr_state;
        for(int i=0;i<2;++i)
            for(int j=0;j<3;++j)
                curr_state.push_back(char('0'+board[i][j]));
        
        string target_state = "123450";
        //Apply BFS to count steps from curr_state to target_state
        queue<string> q;   //Curr_state, No of hops to reach
        q.push(curr_state);
        unordered_set<string> visited;
        visited.insert(curr_state);
        vector<vector<int>> dirs = {{1,3},{0,2,4},{1,5},
                                    {0,4},{1,3,5},{2,4}};//4-directional swap

        int levels=0;
        while(!q.empty()){
            int size=q.size();
            for(int i=0;i<size;++i){
                string curr_state = q.front();
                q.pop();

                if(curr_state == target_state)  return levels;
                
                //Find zero_pos to slide
                int zero_pos;
                for(int i=0;i<6;++i)
                    if(curr_state[i]=='0'){
                        zero_pos=i;
                        break;
                    }
                
                //Try 4 directional calls to unvisited states only
                for(auto& dir: dirs[zero_pos]){
                    string new_state = curr_state;
                    swap(new_state[zero_pos],new_state[dir]);

                    if(!visited.count(new_state)){
                        visited.insert(new_state);
                        q.push(new_state);
                    }
                }
            }
            levels+=1;
        }
        return -1;
    }
};
```
https://medium.com/@RamkrishnaKulka/traversal-of-a-self-looping-tree-a2e985eb9b34

BFS on Binary Grid

```
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int shortestPathBinaryMatrix(vector<vector<int>>& grid) {
        int n = grid.size();

        // blocked start or end
        if (grid[0][0] == 1 || grid[n-1][n-1] == 1)
            return -1;

        vector<vector<bool>> visited(n, vector<bool>(n, false));

        int dirs[8][2] = {
            {-1,-1}, {-1,0}, {-1,1},
            {0,-1},          {0,1},
            {1,-1},  {1,0},  {1,1}
        };

        queue<pair<int,int>> q;
        q.push({0,0});
        visited[0][0] = true;

        int steps = 1; // path length includes start cell

        while (!q.empty()) {
            int sz = q.size();

            while (sz--) {
                auto [r, c] = q.front();
                q.pop();

                if (r == n - 1 && c == n - 1)
                    return steps;

                for (auto &d : dirs) {
                    int nr = r + d[0];
                    int nc = c + d[1];

                    if (nr >= 0 && nc >= 0 && nr < n && nc < n &&
                        !visited[nr][nc] && grid[nr][nc] == 0) {

                        visited[nr][nc] = true;
                        q.push({nr, nc});
                    }
                }
            }

            steps++; // increase after each BFS layer
        }

        return -1;
    }
};

#include <bits/stdc++.h>
using namespace std;

struct Node {
    int r, c;
};

int solveHackerPlay(vector<vector<int>>& grid, int k) {
    int n = grid.size(), m = grid[0].size();

    if (grid[0][0] == 1 || grid[n-1][m-1] == 1)
        return -1;

    vector<vector<int>> dist(n, vector<int>(m, INT_MAX));
    queue<Node> q;

    q.push({0, 0});
    dist[0][0] = 0;

    int dr[4] = {-1, 1, 0, 0};
    int dc[4] = {0, 0, -1, 1};

    while (!q.empty()) {
        auto [r, c] = q.front();
        q.pop();

        for (int i = 0; i < 4; i++) {
            for (int step = 1; step <= k; step++) {
                int nr = r + dr[i] * step;
                int nc = c + dc[i] * step;

                if (nr < 0 || nc < 0 || nr >= n || nc >= m)
                    break;

                if (grid[nr][nc] == 1)
                    break;

                if (dist[nr][nc] < dist[r][c] + 1)
                    break;

                if (dist[nr][nc] > dist[r][c] + 1) {
                    dist[nr][nc] = dist[r][c] + 1;
                    q.push({nr, nc});
                }
            }
        }
    }

    return dist[n-1][m-1] == INT_MAX ? -1 : dist[n-1][m-1];
}

int main() {
    int n, m, k;
    cin >> n >> m >> k;

    vector<vector<int>> grid(n, vector<int>(m));

    for (int i = 0; i < n; i++)
        for (int j = 0; j < m; j++)
            cin >> grid[i][j];

    cout << solveHackerPlay(grid, k) << endl;
}

```
Resolve Battle

```
#include <iostream>
#include <vector>
#include <string>
#include <sstream>
#include <map>
#include <algorithm>

using namespace std;

// Represents the static input provided by the user
struct UnitCommand {
    string name;
    string currentArea;
    string targetArea;
    string action;
    string supportTarget;
};

// Represents the dynamic state during simulation
struct ResolveState {
    int strength = 1;
    bool isDead = false;
    string finalLocation;
};

vector<string> resolveDiplomacy(const vector<string>& commands) {
    map<string, UnitCommand> unitRegistry;
    map<string, ResolveState> resolution;
    map<string, vector<string>> areaContestants; // Who is ending up where?

    // 1. Parse and Initialize
    for (const string& line : commands) {
        stringstream ss(line);
        string name, current, action, target;
        ss >> name >> current >> action;

        UnitCommand cmd;
        cmd.name = name;
        cmd.currentArea = current;
        cmd.action = action;

        if (action == "Move") {
            ss >> target;
            cmd.targetArea = target;
        } else {
            cmd.targetArea = current;
            if (action == "Support") ss >> cmd.supportTarget;
        }

        unitRegistry[name] = cmd;
        resolution[name] = {1, false, cmd.targetArea};
        areaContestants[cmd.targetArea].push_back(name);
    }

    // 2. Validate Supports (O(N) optimization: check targetArea map instead of nested loop)
    for (auto const& [name, cmd] : unitRegistry) {
        if (cmd.action == "Support") {
            bool isCut = false;
            // Support is cut if anyone (other than the supporter) is moving into the supporter's area
            for (const string& occupant : areaContestants[cmd.currentArea]) {
                if (occupant != name) {
                    isCut = true;
                    break;
                }
            }

            if (!isCut && resolution.count(cmd.supportTarget)) {
                resolution[cmd.supportTarget].strength++;
            }
        }
    }

    // 3. Resolve Battles
    for (auto const& [area, names] : areaContestants) {
        if (names.size() > 1) {
            int maxS = 0;
            for (const string& n : names) maxS = max(maxS, resolution[n].strength);
            
            int winners = 0;
            for (const string& n : names) if (resolution[n].strength == maxS) winners++;

            for (const string& n : names) {
                if (winners > 1 || resolution[n].strength < maxS) {
                    resolution[n].isDead = true;
                }
            }
        }
    }

    // 4. Format Results
    vector<string> results;
    for (auto const& [name, res] : resolution) {
        results.push_back(name + " " + (res.isDead ? "[dead]" : res.finalLocation));
    }
    return results;
}

int main() {
    vector<string> input = {"A Paris Support B", "B Spain Move Marseille", "C Italy Move Marseille", "D London Move Paris"};
    for (const string& r : resolveDiplomacy(input)) cout << r << endl;
    return 0;
}

```
Minimum Window Substring


```
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    // Function to find the minimum window in s that contains all characters of t
    string minWindow(string s, string t) {
        // Frequency map to store required characters from target string
        unordered_map<char, int> targetFreq;
        for (char c : t) {
            targetFreq[c]++;
        }

        // Total unique characters required to match
        int required = targetFreq.size();

        // Sliding window pointers
        int left = 0, right = 0;

        // Counter to track how many unique characters in window match target
        int formed = 0;

        // Frequency map for characters in the current window
        unordered_map<char, int> windowFreq;

        // Track the minimum window length and its starting index
        int minLen = INT_MAX;
        int minLeft = 0;

        // Expand the window by moving right pointer
        while (right < s.size()) {
            // Add the current character into window
            char c = s[right];
            windowFreq[c]++;

            // If current character is in target and frequency matches, increase formed
            if (targetFreq.count(c) && windowFreq[c] == targetFreq[c]) {
                formed++;
            }

            // Try shrinking the window from the left
            while (left <= right && formed == required) {
                // Update the minimum window if this is smaller
                if ((right - left + 1) < minLen) {
                    minLen = right - left + 1;
                    minLeft = left;
                }

                // Remove the left character from window
                char leftChar = s[left];
                windowFreq[leftChar]--;

                // If leftChar is part of target and falls below required count, decrease formed
                if (targetFreq.count(leftChar) && windowFreq[leftChar] < targetFreq[leftChar]) {
                    formed--;
                }

                // Move the left pointer forward
                left++;
            }

            // Expand the window to the right
            right++;
        }

        // Return the minimum window substring, or empty if not found
        return minLen == INT_MAX ? "" : s.substr(minLeft, minLen);
    }
};

// Driver Code
int main() {
    string s = "ADOBECODEBANC";
    string t = "ABC";
    Solution sol;
    cout << sol.minWindow(s, t) << endl; // Output: "BANC"
    return 0;
}


#include <iostream>
#include <vector>
#include <string>
#include <climits>

using namespace std;

class MinimumOrderedWindowFinder {
public:

    pair<int, int> findMinimumWindow(
        const vector<string>& paragraph,
        const vector<string>& keywords) {

        if (paragraph.empty() || keywords.empty()) {
            return {-1, -1};
        }

        int bestWindowStart = -1;
        int bestWindowEnd = -1;
        int minimumWindowLength = INT_MAX;

        int searchStartIndex = 0;

        while (searchStartIndex < paragraph.size()) {

            // ---------------------------------------------------------
            // STEP 1:
            // Find a valid ordered sequence using forward traversal.
            // ---------------------------------------------------------

            int keywordIndex = 0;
            int paragraphIndex = searchStartIndex;

            while (paragraphIndex < paragraph.size() &&
                   keywordIndex < keywords.size()) {

                if (paragraph[paragraphIndex] ==
                    keywords[keywordIndex]) {

                    keywordIndex++;
                }

                paragraphIndex++;
            }

            // Could not find complete ordered sequence anymore.
            if (keywordIndex < keywords.size()) {
                break;
            }

            int windowEnd = paragraphIndex - 1;

            // ---------------------------------------------------------
            // STEP 2:
            // Shrink window greedily from right to left.
            //
            // We walk backwards to find the tightest possible start
            // index for this valid ending position.
            // ---------------------------------------------------------

            keywordIndex = keywords.size() - 1;
            paragraphIndex = windowEnd;

            while (keywordIndex >= 0) {

                if (paragraph[paragraphIndex] ==
                    keywords[keywordIndex]) {

                    keywordIndex--;
                }

                paragraphIndex--;
            }

            int windowStart = paragraphIndex + 1;

            // ---------------------------------------------------------
            // STEP 3:
            // Update best answer.
            // ---------------------------------------------------------

            int currentWindowLength =
                windowEnd - windowStart + 1;

            if (currentWindowLength < minimumWindowLength) {

                minimumWindowLength = currentWindowLength;

                bestWindowStart = windowStart;
                bestWindowEnd = windowEnd;
            }

            // ---------------------------------------------------------
            // STEP 4:
            // Continue searching from next position.
            //
            // Any better window must start after current start.
            // ---------------------------------------------------------

            searchStartIndex = windowStart + 1;
        }

        return {bestWindowStart, bestWindowEnd};
    }
};

int main() {

    vector<string> paragraph = {
        "apple",
        "banana",
        "cat",
        "apple",
        "dog",
        "banana",
        "apple",
        "dog",
        "cat"
    };

    vector<string> keywords = {
        "banana",
        "apple",
        "cat"
    };

    MinimumOrderedWindowFinder finder;

    pair<int, int> result =
        finder.findMinimumWindow(paragraph, keywords);

    cout << "Minimum Window:" << endl;
    cout << "Start Index = " << result.first << endl;
    cout << "End Index   = " << result.second << endl;

    if (result.first != -1) {

        cout << "Window Words: ";

        for (int index = result.first;
             index <= result.second;
             index++) {

            cout << paragraph[index] << " ";
        }

        cout << endl;
    }

    return 0;
}

```
Collatz Conjecture

```
#include <iostream>
#include <unordered_map>
#include <algorithm>

using namespace std;

class CollatzConjecture {
private:

    unordered_map<long long, int> memo;

public:

    //--------------------------------------------------
    // Find steps needed to reach 1
    //--------------------------------------------------

    int findSteps(long long number) {

        //--------------------------------------------------
        // Base case
        //--------------------------------------------------

        if (number <= 1) {
            return 1;
        }

        //--------------------------------------------------
        // Already computed
        //--------------------------------------------------

        if (memo.count(number)) {
            return memo[number];
        }

        long long nextNumber;

        //--------------------------------------------------
        // Collatz transformation
        //--------------------------------------------------

        if (number % 2 == 0) {
            nextNumber = number / 2;
        }
        else {
            nextNumber = 3 * number + 1;
        }

        //--------------------------------------------------
        // Recursive computation
        //--------------------------------------------------

        int steps = 1 + findSteps(nextNumber);

        //--------------------------------------------------
        // Store memo for original number
        //--------------------------------------------------

        memo[number] = steps;

        return steps;
    }

    //--------------------------------------------------
    // Find maximum steps from 1 to limit
    //--------------------------------------------------

    int findLongestSteps(int limit) {

        if (limit < 1) {
            return 0;
        }

        int maximumSteps = 0;

        for (int number = 1;
             number <= limit;
             number++) {

            int currentSteps =
                findSteps(number);

            maximumSteps =
                max(maximumSteps,
                    currentSteps);
        }

        return maximumSteps;
    }
};

int main() {

    CollatzConjecture solver;

    cout << "Test 1: "
         << solver.findLongestSteps(1)
         << endl;

    cout << "Test 2: "
         << solver.findLongestSteps(10)
         << endl;

    cout << "Test 3: "
         << solver.findLongestSteps(37)
         << endl;

    cout << "Test 4: "
         << solver.findLongestSteps(101)
         << endl;

    return 0;
}
```
Path to get all keys

```
class Solution {
public:
    
    vector<vector<int>> directions{{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
    
    int shortestPathAllKeys(vector<string>& grid) {
        int m = grid.size();
        int n = grid[0].size();
        
        queue<vector<int>> que; //{x, y, steps, current_key_status_decimal}
        int count = 0;
        for(int i = 0; i<m; i++) {
            
            for(int j = 0; j<n; j++) {
                
                if(grid[i][j] == '@') {
                    que.push({i, j, 0, 0});
                } else if(grid[i][j] >= 'a' && grid[i][j] <= 'f') {
                    count++;
                }
                
            }
            
        }
        
        /*
            If we have 3 keys - '111'  - Decimal value = 8 = 2^3 - 1
            If we have 4 keys - '1111' - Decimal value = 15 = 2^4 - 1
        */
        
        int final_key_status_decimal = pow(2, count) - 1;
        
        
        int visited[m][n][final_key_status_decimal+1];
        memset(visited, 0, sizeof(visited));
        
        while(!que.empty()) {
            
            auto temp = que.front();
            que.pop();
            
            int i                            = temp[0];
            int j                            = temp[1];
            int steps                        = temp[2];
            int current_key_status_decimal   = temp[3];
            
            if(current_key_status_decimal == final_key_status_decimal) {
                return steps;
            }
            
            for(vector<int> & dir : directions) {
                
                int new_i = i + dir[0];
                int new_j = j + dir[1];
                
                if(new_i >= 0 && new_i < m && new_j >= 0 && new_j < n && grid[new_i][new_j] != '#') {
                    char ch = grid[new_i][new_j];
                        
                    if(grid[new_i][new_j] >= 'A' && grid[new_i][new_j] <= 'F') { //Lock
                        
                        if(visited[new_i][new_j][current_key_status_decimal] == 0 && 
                          ((current_key_status_decimal >> (ch-'A')) & 1) == 1) { //Already have that key then we unlock
                            visited[new_i][new_j][current_key_status_decimal] = 1;
                            que.push({new_i, new_j, steps+1, current_key_status_decimal});
                        }
                        
                    } else if(grid[new_i][new_j] >= 'a' && grid[new_i][new_j] <= 'f') { //Key
                        
                        int newKey_status_decimal = current_key_status_decimal | (1 << (ch - 'a'));
                        
                        if(visited[new_i][new_j][newKey_status_decimal] == 0) {
                            visited[new_i][new_j][newKey_status_decimal] = 1;
                            que.push({new_i, new_j, steps+1, newKey_status_decimal});
                        }
                        
                    } else {
                        if(visited[new_i][new_j][current_key_status_decimal] == 0) {
                            visited[new_i][new_j][current_key_status_decimal] = 1;
                            que.push({new_i, new_j, steps+1, current_key_status_decimal});
                        }
                    }
                    
                }
                
            }
            
        }
        
        return -1;
    }
};
```

LIS
```
/* Dynamic Programming C++ implementation of LIS problem */
#include<bits/stdc++.h> 
using namespace std; 
	
/* lis() returns the length of the longest increasing 
subsequence in arr[] of size n */
int lis( int arr[], int n ) 
{ 
	int lis[n]; 

	lis[0] = 1; 

	/* Compute optimized LIS values in bottom up manner */
	for (int i = 1; i < n; i++ ) 
	{ 
		lis[i] = 1; 
		for (int j = 0; j < i; j++ ) 
			if ( arr[i] > arr[j] && lis[i] < lis[j] + 1) 
				lis[i] = lis[j] + 1; 
	} 

	// Return maximum value in lis[] 
	return *max_element(lis, lis+n); 
} 
	
/* Driver program to test above function */
int main() 
{ 
	int arr[] = { 10, 22, 9, 33, 21, 50, 41, 60 }; 
	int n = sizeof(arr)/sizeof(arr[0]); 
	printf("Length of lis is %d\n", lis( arr, n ) ); 
	return 0; 
}

class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n=nums.size();
        vector<int> lis;
        for(int i=0;i<n;++i){
            int lb = lower_bound(lis.begin(),lis.end(),nums[i])-lis.begin();
            if(lb==lis.size())
                lis.push_back(nums[i]);
            else
                lis[lb] = nums[i];
        }
        return lis.size();
    }
};
```
