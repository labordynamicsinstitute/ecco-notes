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
    Read an external file and return its contents as a Markdown table.
    
    Args:
        file_path (str): Path to the file to be read.
        
    Returns:
        str: The Markdown table as a string.
    """
    try:
        with open(file_path, 'r') as file:
            content = file.readlines()
            
            # Initialize the Markdown table
            table = []
            table.append('| JOBID | PARTITION | NAME      | STATUS | ELAPSED TIME | NODES | NODELIST(REASON) |')
            table.append('|-------|-----------|-----------|--------|--------------|-------|------------------|')
            
            # Process each line in the file
            for line in content:
                columns = line.split()  # Split by whitespace
                if len(columns) < 8:
                    continue  # Skip lines with insufficient columns
                
                # Extract relevant columns, skipping the "USER" column (index 3)
                jobid = columns[0]
                partition = columns[1]
                name = columns[2]
                status = columns[4]
                elapsed_time = columns[5]
                nodes = columns[6]
                nodelist = columns[7]
                
                # Add the row to the table
                table.append(f'| {jobid} | {partition} | {name} | {status} | {elapsed_time} | {nodes} | {nodelist} |')
            
            # Return the table as a single string
            return '\n'.join(table)
    except FileNotFoundError:
        return f"Error: File not found at {file_path}"
    except IOError as e:
        return f"Error reading file: {e}"

# Usage
markdown_table = print_file_as_markdown_table_block(os.path.join(project_root, "_data", "eccoqueue.txt"))
print(markdown_table)
```
