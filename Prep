

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

```
