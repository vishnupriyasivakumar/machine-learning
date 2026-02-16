import graphviz

def add_nodes_edges(tree, dot, parent_name=None, edge_label=''):
    """
    Recursively adds nodes and edges to a graphviz Digraph object.
    """
    if isinstance(tree, dict):
        # This is an internal node (attribute split)
        attribute_name = list(tree.keys())[0]
        current_node_name = f'{attribute_name}{id(tree)}'
        dot.node(current_node_name, attribute_name, shape='box')
        if parent_name:
            dot.edge(parent_name, current_node_name, label=edge_label)

        for value, subtree in tree[attribute_name].items():
            add_nodes_edges(subtree, dot, current_node_name, str(value))
    else:
        # This is a leaf node (class label)
        leaf_node_name = f'leaf_{tree}{id(tree)}'
        dot.node(leaf_node_name, tree, shape='ellipse', style='filled', fillcolor='lightgreen')
        if parent_name:
            dot.edge(parent_name, leaf_node_name, label=edge_label)

def visualize_tree(tree, title='ID3 Decision Tree'):
    """
    Generates and displays a graphviz visualization of the decision tree.
    """
    dot = graphviz.Digraph(comment=title, format='png')
    dot.attr(rankdir='TB', size='10,10') # Top to Bottom layout

    add_nodes_edges(tree, dot)

    # Render the graph and display it
    print(f"Displaying {title}:")
    display(dot)

# Visualize the ID3 decision tree
visualize_tree(id3_decision_tree, 'ID3 Decision Tree for Medical Diagnosis'
