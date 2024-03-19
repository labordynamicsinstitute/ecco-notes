# RAIS data

RAIS is the Brazilian Linked Employer-Employee Data. We are approved to use a copy at Cornell. 
To get added to the project, contact [Lars](mailto:lars.vilhuber@cornell.edu).

## Accessing the Data

Data are hosted in a controlled environment, and cannot be removed. To obtain access,

- [ ] Sign the acknowledgement of confidentiality (will be sent by Lars)
- [ ] Request access to the BioHPC cluster (request to be added to the "lab" for `ecco_lv39` *and* `ecco_rais` (see [ECCO notes](ecco-notes)).
- [ ] Once approved, follow [instructions on how to use the ECCO BioHPC cluster](ecco-notes).

## On the server

Data are at `/home/ecco_rais`, with data in `/home/ecco_rais/data`. Please be conscious that data files can be very large. Do not do computations on the (shared) `/home` drive. Use the SLURM facility to submit long-running jobs, or reserve a compute node, and copy files to `/workdir` in the first step of your processing.

For questions about the cleaned data, contact V
