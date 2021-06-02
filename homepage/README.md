# FDO Homepage

This project will create the FDO homepage with mkdocs-material layout to support multiple versions for FDO documentation.

## Project layout

    mkdocs.yml    # The configuration file.
    docs/
        index.md  # The documentation homepage.
        images/   # images files.

## Steps to follow during new version release

* After the release tag creation on the docs repo(eg: 0.5.0), add the line for newly created tag in `docs/index.md` file and raise the PR.
* Once PR gets merged, navigate into the homepage directory and run `mkdocs build` command.
* site directory will be generated. Push the files under site directory to the `gh-pages-multi` branch.
