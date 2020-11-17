## Adding `python-versioneer` to an OctoPrint plugin

This guide uses Gina's custom versioneer that allows for different branches to be versioned automatically, using a lookup file.
If you want to use mainline versioneer, most of this applies with the exception of the lookupfile sections.

1. Download Gina's custom `versioneer.py` file from the [OctoPrint repository](https://github.com/OctoPrint/OctoPrint/blob/master/versioneer.py)
2. Add it to the project root of your plugin, eg `OctoPrint-WS281x_LED_Status/versioneer.py`
3. Add the configuration:
   * `setup.cfg`:
   
     ```
     [versioneer]
     VCS = git
     style = pep440-tag
     # be sure to change to your plugin!
     versionfile_source = octoprint_ws281x_led_status/_version.py
     versionfile_build = octoprint_ws281x_led_status/_version.py
     # (optional) add your version prefix here, eg you tag as `version-0.1.0` the prefix would be `version-`
     tag_prefix =
     parentdir_prefix =
     lookupfile = .versioneer-lookup
     ```
   
   * `setup.py`:
   
     Add `import versioneer` to the top of the file
     
     Modify `plugin_version` to become `plugin_version = versioneer.get_version()`
     
     Add `plugin_cmdclass = versioneer.get_cmdclass()` somewhere in all those variables
     
     Lower down the file, find the `setup_parameters` and add `cmdclass=plugin_cmdclass` as another kwarg to the function call
    
   * Your plugin's `__init__.py`:
   
     Add the following to somewhere near the top:
     
     ```python
     from ._version import get_versions
     __version__ = get_versions()["version"]
     del get_versions
     ```
     
     and near the `__plugin_name__` and `__plugin_pythoncompat__` fields, add:
     
     `__plugin_version = __version__`
     
   * Run the versioneer installation (assumes project root):
   
     `python versioneer.py setup`
     
     It should create a `_version.py` file within your plugin's package
     
   * Versioneer lookup file:
   
     An optional part, if you want different branches to have different versions. If you leave this out, remember to make sure it is not in `setup.cfg`. If not included, versions will only increase with tags.
     
     Add the file `.versioneer-lookup` (plain text, no extension):
     ```
     # Configuration file for the versioneer lookup, manually mapping tags based on branches
     #
     # Format is
     #
     #   <branch-regex> <tag> <reference commit>
     #
     # The file is processed from top to bottom, the first matching line wins. If <tag> or <reference commit> are left out,
     # the lookup table does not apply to the matched branches

     # These branches should only use tags, use for your release branches
     master
     main
     pre-release
     rc/.*
     rc
     
     # neither should disconnected checkouts, e.g. 'git checkout <tag>'
     HEAD
     \(detached.*
     
     # here, maintenance is currently the branch for 0.5.2 - hash is of previous tag on this branch
     maintenance 0.5.2 2f7cec9d462e7073c8f65530b0951b8c4cf2dd50 pep440-dev
  
     # devel is currently the branch for work towards 0.6.0
     devel 0.6.0 2f7cec9d462e7073c8f65530b0951b8c4cf2dd50 pep440-dev

     # Every other branch is also development, so is resolved to 0.6.0 as well for now
     .* 0.6.0 2f7cec9d462e7073c8f65530b0951b8c4cf2dd50 pep440-dev
     ```
     
That *should* be everything! Give it a go, install your plugin and see the version number change as you push commits and tag releases!
