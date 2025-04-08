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

```{code-cell} ipython3
:tags: ["remove-input","full-width"]
from IPython.display import HTML, display

def print_file_as_markdown_table_block(file_path):
    """
    Read an external file and display its contents as an HTML table.
    
    Args:
        file_path (str): Path to the file to be read.
    """
    try:
        with open(file_path, 'r') as file:
            content = file.readlines()
            
            # Initialize the HTML table
            table = []
            table.append('<table>')
            table.append('<tr><th>JOBID</th><th>PARTITION</th><th>NAME</th><th>STATUS</th><th>ELAPSED TIME</th><th>NODES</th><th>NODELIST(REASON)</th></tr>')
            
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
                table.append(f'<tr><td>{jobid}</td><td>{partition}</td><td>{name}</td><td>{status}</td><td>{elapsed_time}</td><td>{nodes}</td><td>{nodelist}</td></tr>')
            
            table.append('</table>')
            
            # Display the table as HTML
            display(HTML(''.join(table)))
    except FileNotFoundError:
        display(HTML(f"<p style='color:red;'>Error: File not found at {file_path}</p>"))
    except IOError as e:
        display(HTML(f"<p style='color:red;'>Error reading file: {e}</p>"))

# Usage
print_file_as_markdown_table_block(os.path.join(project_root, "_data", "eccoqueue.txt"))
