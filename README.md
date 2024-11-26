# Guide
 - [Start](#start)
 - [Modules](#modules)
    - [Nodes](#nodes)
    - [Tree](#tree)
 - [Usage](#usage)

---

## Start

This is a library to create decision trees and calculate the expected value of the best strategy.

To install the library, run the following command:
```bash
pip install precision-tree
```

---

## Modules

The library has two main modules: `nodes` and `tree`.

The `nodes` module contains the classes to create the nodes of the decision tree. The `tree` module contains the class to wrap the root node and calculate the expected value of the best strategy and visualize the tree.

### Nodes

The `nodes` module contains the following classes:

- `DecisionNode`: Represents a decision node.

- `ChanceNode`: Represents a chance node.

- `PayoffNode`: Represents a payoff node.

All of the classes are subclasses of the `Node` class.

#### DecisionNode

The `DecisionNode` class represents a decision node (in graph it looks like a squere).

The following table shows the attributes of the `DecisionNode` class:

| Attribute | Description |
| --- | --- |
| `name` | The name of the node. |
| `branches` | A list of branches. |


The following table shows the methods of the `DecisionNode` class:

| Method | Description |
| --- | --- |
| `add_branch(self, label, child)` | Adds a branch to the node. `label` is the name of the branch and `child` is the child node. |
| `calculate_value` | Calculates the expected value of the best strategy. |


#### ChanceNode

The `ChanceNode` class represents a chance node (in graph it looks like a circle).

The following table shows the attributes of the `ChanceNode` class:

| Attribute | Description |
| --- | --- |
| `name` | The name of the node. |
| `cost` | The cost of the node. |
| `years` | The number of years. |
| `branches` | A list of branches. |

The following table shows the methods of the `ChanceNode` class:

| Method | Description |
| --- | --- |
| `add_branch(self, label, child, probability)` | Adds a branch to the node. `label` is the name of the branch, `child` is the child node, and `probability` is the probability of the branch. |
| `calculate_value` | Calculates the expected value of the best strategy. |

#### PayoffNode

The `PayoffNode` class represents a payoff node (in graph it looks like a triangle).

The following table shows the attributes of the `PayoffNode` class:

| Attribute | Description |
| --- | --- |
| `name` | The name of the node. |
| `value` | The value of the node. |
| `branches` | A list of branches. |

The following table shows the methods of the `PayoffNode` class:

| Method | Description |
| --- | --- |
| `calculate_value` | Calculates the expected value of the best strategy. |

### Tree

The `tree` module contains the `TreeWrapper` class.

The following table shows the attributes of the `TreeWrapper` class:

| Attribute | Description |
| --- | --- |
| `root` | The root node of the tree. |

The following table shows the methods of the `TreeWrapper` class:

| Method | Description |
| --- | --- |
| `show(self, title: str, format: str):` | Shows the tree. `title` is the title of the tree and `format` is the format of the file `png`, `pdf` etc. |
| `get_optimal_path` | Returns the optimal path as a tuple, where the first element is the optimal nodes and second element is the optimal edges. |
| `calculate_value` | Calculates the expected value of the best strategy. |


---

## Usage


**Example**
```python
from precision_tree.nodes import DecisionNode, ChanceNode, PayoffNode
from precision_tree.tree import TreeWrapper

root = DecisionNode("Start Decision")

# Big Factory
big_factory = ChanceNode("Big Factory", cost=700, years=5)
high_demand = PayoffNode("High Demand", 280)
low_demand = PayoffNode("Low Demand", -80)
big_factory.add_branch("High Demand", high_demand, 0.8)
big_factory.add_branch("Low Demand", low_demand, 0.2)

# Small Factory
small_factory = ChanceNode("Small Factory", cost=300, years=5)
high_demand_small = PayoffNode("High Demand Small", 180)
low_demand_small = PayoffNode("Low Demand Small", -55)
small_factory.add_branch("High Demand", high_demand_small, 0.8)
small_factory.add_branch("Low Demand", low_demand_small, 0.2)

# Stop by Year
stop_by_year = DecisionNode("Stop by Year")
negative_info = PayoffNode("Negative Info", 0)

positive_info = DecisionNode("Positive Info")
big_factory_1 = ChanceNode("Big Factory 1", cost=700, years=4)
high_demand_1 = PayoffNode("High Demand 1", 280)
low_demand_1 = PayoffNode("Low Demand 1", -80)
big_factory_1.add_branch("High Demand 1", high_demand_1, 0.9)
big_factory_1.add_branch("Low Demand 1", low_demand_1, 0.1)

small_factory_1 = ChanceNode("Small Factory 1", cost=300, years=4)
high_demand_small_1 = PayoffNode("High Demand Small 1", 1800)
low_demand_small_1 = PayoffNode("Low Demand Small 1", -55)
small_factory_1.add_branch("High Demand 1", high_demand_small_1, 0.9)
small_factory_1.add_branch("Low Demand 1", low_demand_small_1, 0.1)

positive_info.add_branch("Big Factory", big_factory_1)
positive_info.add_branch("Small Factory", small_factory_1)

stop_by_year.add_branch("Positive Info", positive_info, 0.7)
stop_by_year.add_branch("Negative Info", negative_info, 0.3)

root.add_branch("Big Factory", big_factory)
root.add_branch("Small Factory", small_factory)
root.add_branch("Stop by Year", stop_by_year)

tree = TreeWrapper(root)
print(f"Expected Value of the best strategy: {tree.calculate_value():.2f}")
print(f"Optimal branch: {tree.get_optimal_path()[0]}")
```

**Output**

```bash
Expected Value of the best strategy: 86.00
Optimal branch: {'Unfavorable 1', 'Favorable 1', 'Big Factory 1', 'Without Analytics', 'Decision'}
```
