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


root = DecisionNode("Decision")

# Decision Node Without Analytics
without_analytics = DecisionNode("Without Analytics")

big_factory = ChanceNode("Big Factory 1", cost=0, years=1)
big_factory.add_branch(label="Favorable P(0.7)", child=PayoffNode("Favorable 1", 200), probability=0.7)
big_factory.add_branch(label="Unfavorable P(0.3)", child=PayoffNode("Unfavorable 1", -180), probability=0.3)

small_factory = ChanceNode("Small Factory 1", cost=0, years=1)
small_factory.add_branch(label="Favorable P(0.7)", child=PayoffNode("Favorable 2", 100), probability=0.7)
small_factory.add_branch(label="Unfavorable P(0.3)", child=PayoffNode("Unfavorable 2", -20), probability=0.3)

without_analytics.add_branch(label="Big Factory", child=big_factory)
without_analytics.add_branch(label="Small Factory", child=small_factory)
without_analytics.add_branch(label="Sell Patent", child=PayoffNode("Sell Patent 1", 10))

# Make Decision Node With Analytics
with_analytics = ChanceNode("With Analytics", cost=8, years=1)

# Positive Result
positive_result = DecisionNode("Positive Result")

big_factory_positive = ChanceNode("Big Factory 2", cost=0, years=1)
big_factory_positive.add_branch(label="Favorable", child=PayoffNode("Favorable 3", 200), probability=0.81)
big_factory_positive.add_branch(label="Unfavorable", child=PayoffNode("Unfavorable 3", -180), probability=0.19)

small_factory_positive = ChanceNode("Small Factory 2", cost=0, years=1)
small_factory_positive.add_branch(label="Favorable", child=PayoffNode("Favorable 4", 100), probability=0.81)
small_factory_positive.add_branch(label="Unfavorable", child=PayoffNode("Unfavorable 4", -20), probability=0.19)

positive_result.add_branch(label="Big Factory", child=big_factory_positive)
positive_result.add_branch(label="Small Factory", child=small_factory_positive)
positive_result.add_branch(label="Sell Patent", child=PayoffNode("Sell Patent 2", 10))

# Negative Result
negative_result = DecisionNode("Negative Result")

big_factory_negative = ChanceNode("Big Factory 3", cost=0, years=1)
big_factory_negative.add_branch(label="Favorable", child=PayoffNode("Favorable 5", 200), probability=0.28)
big_factory_negative.add_branch(label="Unfavorable", child=PayoffNode("Unfavorable 5", -180), probability=0.72)

small_factory_negative = ChanceNode("Small Factory 3", cost=0, years=1)
small_factory_negative.add_branch(label="Favorable", child=PayoffNode("Favorable 6", 100), probability=0.28)
small_factory_negative.add_branch(label="Unfavorable", child=PayoffNode("Unfavorable 6", -20), probability=0.72)

negative_result.add_branch(label="Big Factory", child=big_factory_negative)
negative_result.add_branch(label="Small Factory", child=small_factory_negative)
negative_result.add_branch(label="Sell Patent", child=PayoffNode("Sell Patent 3", 10))

with_analytics.add_branch(label="Positive Result", child=positive_result, probability=0.48)
with_analytics.add_branch(label="Negative Result", child=negative_result, probability=0.52)

# Add Branches to Root
root.add_branch(label="With Analytics", child=with_analytics)
root.add_branch(label="Without Analytics", child=without_analytics)

tree = TreeWrapper(root)
print(f"Expected Value of the best strategy: {tree.calculate_value():.2f}")
print(f"Optimal branch: {tree.get_optimal_path()[0]}")
```

**Output**

```bash
Expected Value of the best strategy: 86.00
Optimal branch: {'Unfavorable 1', 'Favorable 1', 'Big Factory 1', 'Without Analytics', 'Decision'}
```
