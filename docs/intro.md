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

# Computing notes

Cornell has a decentralized computing support infrastructure. Members of the Economics Department can choose from several options, as needed for a particular project. These pages provide some info, and links for further reading.

## Using these pages

These pages are a continous work-in-progress. They provide information based on contributors' experiences, which probably is incomplete, and occasionally may be out-of-date. The information is currently edited by [Lars](https://github.com/larsvilhuber/).

## Quick Start

- [Request access to the Econ Computer Cluster ECCO](access) to get started
- [Launch a job on the Econ Computer Cluster ECCO](slurm-quick-start) to develop code with 1 core, or run jobs with 400 cores
- [Reserve a machine on the Econ Computer Cluster ECCO](reserving) if you need exclusive access to a machine (prevent others from running even non-competing jobs)
- [Connect to a CCSS Cloud Computing Solution](https://socialsciences.cornell.edu/computing-and-data/cloud-computing-solutions/account-instructions)

## Contributing

If you did not find something on these pages, want to add something to these pages, or think something should be here, [open a Github issue](https://github.com/labordynamicsinstitute/ecco-notes/issues/new/choose)! If you don't know how to create a Github issue, have a look [here](https://docs.github.com/en/issues/tracking-your-work-with-issues/quickstart). 

:::{admonition} If you don't know what Github is...
:class: dropdown

... you have [homework](https://swcarpentry.github.io/git-novice/) to do! In the meantime, contact [Lars](https://www.ilr.cornell.edu/people/lars-vilhuber).

:::

Past contributors include
```{code-cell} ipython3
:tags: ["remove-input","full-width"]
import os
import requests

# Only run if in GitHub Actions (GITHUB_REPOSITORY env var is set)
github_repo = os.environ.get("GITHUB_REPOSITORY")
if github_repo:
    url = f"https://api.github.com/repos/{github_repo}/contributors"
    response = requests.get(url)
    if response.status_code == 200:
        contributors = response.json()
        filtered = [c for c in contributors if c['login'] != 'larsvilhuber']
        links = [f"[{c['login']}]({c['html_url']})" for c in filtered]
        print(", ".join(links))
    else:
        print("Could not fetch contributors from GitHub API.")
else:
    print("Not running in GitHub Actions. Skipping contributor listing.")
```

