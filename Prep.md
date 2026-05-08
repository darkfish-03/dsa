
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
