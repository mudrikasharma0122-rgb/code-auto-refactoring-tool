import ast
import astor   # works for all Python 3 versions

class RefactorTool(ast.NodeTransformer):
    def __init__(self):
        self.var_count = 1
        self.var_map = {}

    # Rename variables
    def visit_Name(self, node):
        if isinstance(node.ctx, ast.Store):
            if node.id not in self.var_map:
                new_name = f"var{self.var_count}"
                self.var_map[node.id] = new_name
                self.var_count += 1
            node.id = self.var_map[node.id]

        elif isinstance(node.ctx, ast.Load):
            if node.id in self.var_map:
                node.id = self.var_map[node.id]

        return node

    # Simplify conditions
    def visit_Compare(self, node):
        self.generic_visit(node)

        if (len(node.ops) == 1 and
            isinstance(node.ops[0], ast.Eq) and
            isinstance(node.comparators[0], ast.Constant)):

            if node.comparators[0].value is True:
                return node.left

            if node.comparators[0].value is False:
                return ast.UnaryOp(op=ast.Not(), operand=node.left)

        return node


def refactor_code(code):
    try:
        tree = ast.parse(code)
        transformer = RefactorTool()
        new_tree = transformer.visit(tree)
        ast.fix_missing_locations(new_tree)

        # Use astor instead of ast.unparse
        return astor.to_source(new_tree)

    except Exception as e:
        return f"Error: {e}"


if __name__ == "__main__":
    print("Enter your Python code (type END to finish):\n")

    lines = []
    while True:
        line = input()
        if line.strip() == "END":
            break
        lines.append(line)

    input_code = "\n".join(lines)

    print("\n🔹 Original Code:\n")
    print(input_code)

    output_code = refactor_code(input_code)

    print("\n🔹 Refactored Code:\n")
    print(output_code)
