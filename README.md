# Modular Enrichment Solution Pack

**Compatibility:** 7.2+

This solution pack implements a novel approach to enrichment playbooks. A primary enrichment playbook is responsible for all indicator types enrichment.
For each type several referenced playbooks are used to perform the enrichment from a single cyber threat intelligence source.
The primary playbook collects and compiles enrichment data from all referenced playbooks and computes the resulting indicator's attributes.

Documentation available [here](docs/README.md)