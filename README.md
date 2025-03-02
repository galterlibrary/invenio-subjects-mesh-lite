# invenio-subjects-mesh-lite

*MeSH topical subject terms without qualifiers for InvenioRDM*

<a href="https://pypi.org/project/invenio-subjects-mesh-lite/">
  <img src="https://img.shields.io/pypi/v/invenio-subjects-mesh-lite.svg">
</a>

Install this extension to get Medical Subject Headings topics without qualifiers into your instance.

If you are looking for a larger MeSH vocabulary with qualifiers, use
[invenio-subjects-mesh](https://github.com/galterlibrary/invenio-subjects-mesh) instead.

> [!NOTE]
> Both extensions map the `MeSH` subject id, so you can only install one of them. However, they
> use the same pattern for subject ids, so you can switch from one to the other easily if your
> needs change in the future.

## Installation

From your instance directory:

```bash
pipenv install invenio-subjects-mesh-lite
```

This will add it to your Pipfile.

## Versions

This repository follows [calendar versioning](https://calver.org/) for year and month:

`2021.06.18` is both a valid semantic version and an indicator of the year-month corresponding to the loaded MeSH terms.
`18` here is a patch number (not a day).

So far the package is compatible with InvenioRDM 9.1+'s subjects "ABI". If there is a breaking change, a compatibility
table will be provided to indicate version cross-compatibility.

## Usage

There are 2 types of users for this package. Instance operators and package maintainers.

### Instance operators

For instance operators, after you have installed the extension as per the steps above, you will want to reload your instance's fixtures: `pipenv run invenio rdm-records fixtures`. This will install the new terms in your instance.

Alternatively, or if you want to update your already loaded subjects to a new listing (e.g. from one year's list to another), you can update your instance's MeSH subjects as per below. Updating subjects this way takes care of everything for you: the subjects themselves and the records/drafts using those subjects. **WARNING** This operation can _remove_ subjects.

```bash
# In your instance's project
# Download up-to-date listings
pipenv run invenio galter_subjects mesh download -d /path/to/downloads/storage/ -y YEAR
# Generate file containg deltas to transition your instance to the downloaded listing
pipenv run invenio galter_subjects mesh deltas -d /path/to/downloads/storage/ -y YEAR -f topic -o deltas_mesh.csv
# Update your instance - *this operation will modify your instance*
pipenv run invenio galter_subjects update deltas_mesh.csv
```

Look at the help text for these commands to see additional options that can be passed.
In particular, options for `galter_subjects update` allow you to store renamed, replaced or removed subjects on records according to a template of your choice.


### Maintainers

When a new list of MeSH term comes out, this package should be updated. Here we show how.

**Pre-requisite/Context**

[Install the distribution package for development](#development) before you do anything.

**Commands**

Once you have it installed, you can run the following commands in the isolated virtualenv:

```bash
# In this project
# Download up-to-date listings
(venv) invenio galter_subjects mesh download -d /path/to/downloads/storage/ -y YEAR
# Generate file containing initial listing
(venv) invenio galter_subjects mesh file -d /path/to/downloads/storage/ -y YEAR -f topic -o invenio_subjects_mesh_lite/vocabularies/subjects_mesh.csv
```

Sorting the resulting csv is a nice touch for humans to better parse the changes between versions.

When you are happy with the list, bump the version in `pyproject.toml` and release it.


## Development

Install the project in editable mode with `dev` dependencies in an isolated virtualenv (`(venv)` denotes that going forward):

```bash
(venv) pip install -e .[dev]
# or if using pipenv
pipenv run pip install -e .[dev]
```

Run tests:

```bash
(venv) invoke test
# or shorter
(venv) inv test
# or if using pipenv
pipenv run inv test
```

Check manifest:

```bash
(venv) inv check-manifest
# or if using pipenv
pipenv run inv check-manifest
```

Clean out artefacts:

```bash
(venv) inv clean
# or if using pipenv
pipenv run inv clean
```
