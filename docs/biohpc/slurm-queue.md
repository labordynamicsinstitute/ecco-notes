# SLURM Queue

```python
import os

def print_file_as_markdown_code_block(file_path, language=''):
    """
    Read an external file and print its contents as a Markdown fenced code block.
    
    Args:
        file_path (str): Path to the file to be read
        language (str, optional): Language identifier for syntax highlighting
    """
    try:
        with open(file_path, 'r') as file:
            content = file.read()
            print(f'```{language}')
            print(content)
            print('```')
    except FileNotFoundError:
        print(f"Error: File not found at {file_path}")
    except IOError as e:
        print(f"Error reading file: {e}")

# Example usage
project_root = os.path.abspath(os.path.dirname(__file__))
file_path = os.path.join(project_root, '../../_data/eccoqueue.txt')
print_file_as_markdown_code_block(file_path)
```
