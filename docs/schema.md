# Extending the Schema and Validator

Past BIDS Extension Proposals (BEPs) have required extending the specification itself as well as the BIDS Validator. 
After rendering the BEP into markdown it was incumbent on the BEP leads or maintainers to 
then update the Validator with rules and logic to handle the new BEP.
This double entry is a byproduct of the fact that the Validator was built in javascript to run both 
locally and from within the browser to validate BIDS data.

Efforts have been made to eliminate needing to implement a BEP in both a mark-up langugage and 
in javascript to satisfy the Validator. These two steps can be reduced into one by using the 
machine readable schema; one single input is then used for the specification, the Validator, 
or any other software or tool that interacts with BIDS. A change now made to a BEP, the spec in general,
is now a change made and updated within the validator

## History and Motivations for the Schema

If you're just returning to BIDS, creating a new BEP, or simply curious as to the timeline and history
of the transition from markdown to a yaml schema to render and use the BIDS Specification you are encouraged
to browse the links below:

### Motivations

- Machine readablity ensures interoperability with other tools and utilities
- Uniform implementation between tools that use BIDS. Previously, other tools relied on their own data structures implemented from an interpretation of the specification.
- Easier maintainance of the specification, tools, and other applications <sup>[1][start_of_schema],[2][bids_schema_validation_for_datatypes]</sup>

## What is the Schema?

The BIDS schema is a machine readable representation of the BIDS Standard written in [YAML](https://en.wikipedia.org/wiki/YAML). It is (by and large) the BIDS Specification
but in a declaritive form. For a full accounting of all methods, operators, and options provided to the user by the Schema we encourage you to look at the developer documentation
contained within the source code here on [GitHub][schema_readme.md].

Examples of a few of the elements contained within the schema can be seen below:

---

The structure of the schema files within the spec:

```
src/schema
├── BIDS_VERSION
├── meta
│   └── context.yaml
├── objects
│   ├── columns.yaml
│   ├── ...
│   └── suffixes.yaml
├── rules
│   ├── checks
│   │   ├── asl.yaml
│   │   ├── ...
│   │   └── mri.yaml
│   ├── ...
│   └── suffixes.yaml
└── SCHEMA_VERSION
```

---

The requirements for an anatomical MR:

```yaml

```


---

A glossary entry for the NIfTI file extension (.nii):

```yaml
nii:
  value: .nii
  display_name: NIfTI
  description: |
    A Neuroimaging Informatics Technology Initiative (NIfTI) data file.
```

Will produce the following entry in the [rendering](https://bids-specification.readthedocs.io/en/stable/glossary.html#nii-extensions):

![rendered_nifti_entity](./assets/img/rendered_nifti_entity.png)

---

[state_of_the_schema_presentation]: https://docs.google.com/presentation/d/1ldEbElaFm__jtkLoEcn2PQ-LGj1dfmdjWxDvE11eiNk/edit?usp=sharing
[start_of_schema]: https://github.com/bids-standard/bids-specification/issues/466
[bids_schema_validation_for_datatypes]: https://github.com/bids-standard/bids-validator/pull/1325

[BIDS Sprint 3 Discussion]: https://googledrive.uploadmeeeeeric.com
[BIDS Sprint 2 Discussion]: https://googledrive.addthislinkadamputitsomewhere.com
[BIDS Sprint 1 Discussion]: https://zoomneedstouploadthis.com

[jsonified_schema_v1.8.0]: https://bids-specification.readthedocs.io/en/v1.8.0/schema.json
[schema_readme.md]: https://github.com/bids-standard/bids-specification/blob/master/src/schema/README.md
[bidsschematools_pypi]: https://pypi.org/project/bidsschematools/
[bidsschematools_rtd]: https://bidsschematools.readthedocs.io/en/latest/?badge=latest
