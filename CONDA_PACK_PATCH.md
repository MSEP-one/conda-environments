### CONDA-PACK PATCH
When packing the environment for windows there was loading errors in the unpacking. Some investigation resulted on filepaths was being wrongly interpreted.
To solve the issue a patch for [conda-pack](https://anaconda.org/conda-forge/conda-pack) was made.

If you ever need to regenerate the conda windows environment, make sure to apply [the patch](0001-Normalize-long-filepaths-on-windows.patch)



### UPDATE CONDA AND PATCH LOCALLY
Navigate to this "msep/godot_project/python/conda" folder.

Make sure the git lfs is installed, run "git lfs install". And make sure that you are on the main
branch "git checkout main" in the current git repository.

Create a new directory here and call it for example "unpacked" Unpack the .tar.gz for the desired
platform (there can be an error message after unpacking, but it can be ignored as unpacking simply
could restore symlinks) if only single platform needs update (or all of them if all need an
improvement), for example "env_linux_x86_64.tar.gz" inside the newly created "unpacked" folder so
that that all the many directories appear right as children of "unpacked".

Figure out what files need to be patched, "unpacked/lib/python3.10/site-packages/openmm/app/simulation.py".
Remember that the contents of the platform specific archive are unpacked locally so you can test
your changes by modifying for example: "/home/<user>/.local/share/godot/app_userdata/MSEP/msep.one/lib/python3.10/site-packages/openmm/app/simulation.py"

Make a diff between the file in "unpacked" the original "simulation.py" and the file with your
modifications, for example "simulation.py" from your local user folder (see above): 
"git diff --no-index unpacked/lib/python3.10/site-packages/openmm/app/simulation.py /home/<user>/.local/share/godot/app_userdata/MSEP/msep.one/lib/python3.10/site-packages/openmm/app/simulation.py > 0002-catch-simulation-exception.patch"
And place the patch file here in "msep/godot_project/python/conda" folder.

If you are not the person who did the changes locally, then create at first a backup of the original
"simulation.py" that you unpacked and do the diff between the backup and the new changes after you
have applied the new changes (see below).

(The patch will be used later, you will have the fix implemented without the patch with the proceeding steps)

Then apply the changes from the modified local file (or the file that you got somewhere if you
didn't do the changes) to the original "simulations.py" so that you have the new changes without
patching right away.

Then go to the "unpacked" folder and run "tar -cvzf ../linux-fix.tar.gz ." command to create the new
archive, it will create the archive outside the "unpacked" with the contents of the "unpacked"
children.

You can then go up to "msep/godot_project/python/conda" and remove the "unpacked" folder.

You can compare the old and new platform archives with "du env_linux_x86_64.tar.gz" and "du
linux-fix.tar.gz" they should be quite similar in size.

Then if everything looks fine you can remove the "env_linux_x86_64.tar.gz" and rename
"linux-fix.tar.gz" to "env_linux_x86_64.tar.gz". Tip: You can move "env_linux_x86_64.tar.gz" at
first to Trash before completely deleting.

Then run Godot and you should see after confirming the welcome screen the gears with a message that
the new workspace is preparing, at this time the platform archive is being unpacked and local files
are being overwritten. If you encounter red messages in the Output log in Godot, then most likely
you forgot to remove the "unpacked" folder.

If you have done some unique changes, you can add info to this .md file.

Then commit and push the new changes (while in the godot_project/python/conda/ folder): "git add .",
you can do "git status" and see that the new patch file and archive are added (and this CONDA_PACK
PATCH.md file too if you did any changes to it). Then just: "git push origin main".

If you are on a detached head in conda sub repo, then switch to the "main" branch (make sure you
don't lose your changes).

Make a branch on msep (conda is a sub repo of it) repo. Switch to msep repo and add
the conda changes that appear in git status. Then push the new feature branch.

Create a PR of your feature branch into the main Describe the conda changes in that PR (just leave
commit list).
