## How to add release channels to your OctoPrint plugin

Release channels are very helpful to get people to test new functionality, commonly through the well-known 'release candidate'

Adding release channels to OctoPrint has existed for a while now, but until OctoPrint 1.5.0 they were not configurable from the UI, so nobody used them.

Here's a guide detailing 'how to add a release channel', and then how to publish releases utilising them afterwards!

### Configuring your plugin's Software Update hook

1. Find the current SW update hook, looks something like this:
   ```python
   def get_update_information(self):
        return {
           "eeprom_marlin": {
               "displayName": "Marlin EEPROM Editor",
               "displayVersion": self._plugin_version,
               # version check: github repository
               "type": "github_release",
               "user": "cp2004",
               "repo": "OctoPrint-EEPROM-Marlin",
               "current": self._plugin_version,
               # update method: pip
               "pip": "https://github.com/cp2004/OctoPrint-EEPROM-Marlin/archive/{target_version}.zip",
            }
        }
    ```
2. Add the following block of code, to add a 'Stable' channel:
    ```python
    [...]
    "stable_branch": {
        "name": "Stable",
        "branch": "master",
        "comittish": ["master"],
    }
    [...]
    ```
    Note that this assumes your stable branch *is* master, newer Github repositories are created with `main` as the branch. Check if you are unsure.
3. Add the following block of code to add a 'Release Candidate' channel:
   ```
   [...]
   "prerelease_branches": [
        {
             "name": "Release Candidate",
             "branch": "rc",
             "comittish": ["rc", "master"],
        }
   ]
   [...]
   ```
   For this to work, you need to have a branch on your repository named `rc`. We'll come on to why this is important later, when configuring your release. 
   
   Also, as we have configured `comittish` to look at `master` as well, users subscribed to this channel will also get your stable releases.
4. Run your plugin, and check that they show up in the UI, under 'Software Update'

### Development & release process, to use this channel.
* **Stable releases:**
   1. Make sure your code is merged to the 'Stable' branch you defined above
   2. Make sure you have incremented the version in `setup.py`, or you use something like versioneer to do this for you.
   3. Tagging the release:
   
      These releases need to be tagged from your `master` branch (or whatever you configured it above). 
      In the github UI, this can be selected to the right of the tag. See also [Creating a release](https://docs.github.com/en/free-pro-team@latest/github/administering-a-repository/managing-releases-in-a-repository#creating-a-release) from the github docs.
   
* **Release candidates:**
   1. The code that you want to pre-release needs to be merged to your 'Release Candidate' branch, before you can create the release.
   2. Make sure you have incrememented the version number in `setup.py` to something considered *below* the stable version that this release candidate will become. [See PEP 440 for more details (long read)](https://www.python.org/dev/peps/pep-0440/).
      Examples: `1.1.0rc1`, `2.3.0rc3`
   3. Tagging the release:
       
      These releases need to be tagged from your `rc` branch (or whatever you configured it above), and marked as 'pre-release', as we told OctoPrint this was a prerelease channel.
      
      The tag that you use is important, since it lets OctoPrint know whether the user needs to update or not, and that it will be considered below the next 'stable' version.
   
      It is recommended to tag release candidates with the letters `rc`, such as `1.5.0rc1`. See also [Semantic Versioning](https://semver.org/).
      Make sure you select the tag to be created from the `rc` branch, so that the Software Update will pick it up on the right channel!
   
 Finished! Now you can test out this new functionality with your next release. 
 If you don't feel confident that you will get this all right first time, create a dummy plugin to test it with. I did this to start with, before rolling them out to my various plugins.
 
 ***Guide by Charlie Powell, @cp2004***
 
 
