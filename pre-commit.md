## Adding pre-commit hooks to a plugin

Pre-commit formatting is awesome. It makes sure that your files are properly formatted before commiting. 
Adding them to a file watcher and workflow means all the hassle is handled for you as well... beautiful.

1. Put this file in the project root, as .pre-commit-config.yaml (This assumes Py2/3 compatibility for now):
```yaml
exclude: ^(translations/|.*\.css|.*\.svg|octoprint_autologin_config/_version.py|versioneer.py)
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v2.3.0
    hooks:
      - id: end-of-file-fixer
      - id: trailing-whitespace
      - id: check-case-conflict
      - id: check-json
      - id: check-yaml
      - id: check-toml
      - id: check-merge-conflict
      - id: fix-encoding-pragma
  - repo: https://github.com/OctoPrint/codemods
    rev: main
    hooks:
      - id: codemod_dict_to_literal
        stages: ["manual"]
      - id: codemod_set_to_literal
        stages: ["manual"]
      - id: codemod_not_in
        stages: ["manual"]
  - repo: https://github.com/pre-commit/mirrors-isort
    rev: v5.5.4
    hooks:
      - id: isort
  - repo: https://github.com/psf/black
    rev: stable
    hooks:
      - id: black
  - repo: https://gitlab.com/pycqa/flake8
    rev: 3.8.1
    hooks:
      - id: flake8
        additional_dependencies:
          - flake8-bugbear
  - repo: https://github.com/prettier/prettier
    rev: master
    hooks:
      - id: prettier
   ```

2. Adding flake8 config to setup.cfg, to configure the warnings:
```ini
[flake8]
max-line-length = 90
extend-ignore = E203, E231, E265, E266, E402, E501, E731
select = B,C,E,F,W,T4,B9
exclude =
    setup.py
    versioneer.py
    <plugin_package>/_version.py
```

3. Running `pre-commit install` will make sure that you cannot commit without the tests passing.

4. PyCharm setup
   See the section in [OctoPrint's documentation](https://docs.octoprint.org/en/master/development/environment.html#pycharm)
