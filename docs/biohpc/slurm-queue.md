---
jupytext:
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

# SLURM Queue


```{code-cell} ipython3
:tags: ["remove-input","full-width"]
import os


def find_project_root(start_dir='.', file_to_find='favicon.ico'):
    """
    Find the project root directory by searching for a specific file.
    
    Args:
        start_dir (str): The directory to start the search from.
        file_to_find (str): The name of the file to search for.
        
    Returns:
        str: The absolute path of the project root directory.
    """
    current_dir = os.path.abspath(start_dir)
    
    while True:
        # Check if the file exists in the current directory
        if os.path.isfile(os.path.join(current_dir, file_to_find)):
            return current_dir
        
        # Check if we've reached the root directory
        parent_dir = os.path.dirname(current_dir)
        if parent_dir == current_dir:
            raise FileNotFoundError(f"Could not find '{file_to_find}' in the directory tree.")
        
        # Move up one directory
        current_dir = parent_dir

# usage
project_root = find_project_root()
```

```{code-cell} python3
:tags: ["remove-input","full-width"]


def print_file_as_markdown_table_block(file_path):
    """
    Read an external file and print its contents as a Markdown table.
    
    Args:
        file_path (str): Path to the file to be read
    """
    try:
        with open(file_path, 'r') as file:
            content = file.read()
            print(f'|JOBID| PARTITION|     NAME |    USER |STATUS |  ELAPSED     TIME |  NODES | NODELIST(REASON)|')
            print(f'|-----|----------|----------|---------|-------|-------------------|--------|-----------------|')
            print(content)
            #print('```')
    except FileNotFoundError:
        print(f"Error: File not found at {file_path}")
    except IOError as e:
        print(f"Error reading file: {e}")

print_file_as_markdown_table_block(os.path.join(project_root,"_data", 'eccoqueue.txt'))
```
