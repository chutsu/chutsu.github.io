# Find the max depth of a binary tree
``` 
class TreeNode(object):
    def __init__(self):
        self.data = None
        self.left = None
        self.right = None


def max_depth(self, node):
    if node is None:
        return 0

    return 1 + max(max_depth(node.left), max_depth(node.right))
```


# Find the min depth of a binary tree
``` 
class TreeNode(object):
    def __init__(self):
        self.data = None
        self.left = None
        self.right = None


def min_depth(self, node):
    if node is None:
        return 0

    return 1 + min(min_depth(node.left), min_depth(node.right))
```


# Determine whether a tree is balanced
``` 
class TreeNode(object):
    def __init__(self):
        self.data = None
        self.left = None
        self.right = None


def min_depth(self, node):
    if node is None:
        return 0

    return 1 + min(min_depth(node.left), min_depth(node.right))
```


# Traverse a tree in Depth-first order
Recursive traverse
```
class TreeNode(object):
    def __init__(self):
        self.data = None
        self.left = None
        self.right = None


def dfs_inorder_traverse_recursive(node, path=[]):
    if node is None:
        return path
    if node.left is None and node.right is none:
        return path

    path.extend(dfs_traverse_recursive(node.left, path))
    path.extend(self.data)
    path.extend(dfs_traverse_recursive(node.right, path))


def dfs_preorder_traverse_recursive(node, path=[]):
    if node is None:
        return path
    elif node.left is None and node.right is none:
        return path

    path.extend(self.data)
    path.extend(dfs_traverse_recursive(node.left, path))
    path.extend(dfs_traverse_recursive(node.right, path))


def dfs_postorder_traverse_recursive(node, path=[]):
    if node is None:
        return path
    elif node.left is None and node.right is none:
        return path

    path.extend(dfs_traverse_recursive(node.left, path))
    path.extend(dfs_traverse_recursive(node.right, path))
    path.extend(self.data)
```

Iterative traverse
```
class TreeNode(object):
    def __init__(self):
        self.data = None
        self.left = None


def dfs_postorder_traverse_iterative(node, path=[]):
    stack = []

    if node is None:
        return path

    stack.append(node)
    while len(stack) > 0:
       curr_node = stack.pop()
       if curr_node not in path:
            path.append(curr_node)
            stack.push(curr_node.left)
            stack.push(curr_node.right)

```