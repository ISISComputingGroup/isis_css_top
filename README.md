# isis_css_top

Top level repository for build of cs-studio with isis customisations

# Release process

```
git tag 2025.01.01  # Replace with today's date.
git push origin tag 2025.01.01
```

Release will be built automatically by github actions. After it builds, manually deploy the p2 site to `icp_p2` area and binaries to `icp_binaries` area.

# Dependencies

Some dependencies of cs-studio are no longer available via maven central (due to being very old `SNAPSHOT` builds).

They are instead made available on `https://isiscomputinggroup.github.io/isis_css_top/`, which is added to
[the list of maven repositories](https://github.com/ISISComputingGroup/cs-studio/blob/27c3b0f61e6c5444f65a1b591a037c55083513c9/pom.xml#L76)
which cs-studio uses at build time. That repo is deployed from the `gh-pages` branch of this repository.

Pushing new content to the `gh-pages` branch is a manual process. To keep the repository size as small as possible, prefer to `git commit --amend`
the single commit on the `gh-pages` branch rather than creating new commits.

# Developer build (maven)

```
git clone --recursive https://github.com/ISISComputingGroup/isis_css_top.git
build.bat
```

To build without an initial clean use
```
build.bat noclean
```

To save time if you have already done a full build and 
subsequently only modify the `cs-studio\product\repository` area you can use
```
build.bat noclean products
```

Uncomment line to add `-e -X` flags to maven in `build.bat` for debugging information
  
# Developer build (eclipse)

First you will need to build via maven as above.

Next:
- Create a new target platform definition
- Add the following directories to your new target platform definition:
  * `cs-studio-thirdparty/repository/target/repository`
  * `diirt/p2/target/repository`
  * `maven-osgi-bundles/repository/target/repository`
  * `org.csstudio.display.builder/repository/target/repository`
  * `org.csstudio.sns/repository/target/repository`
- Set it as the "active" target platform definition
- Next, import all of the features from `org.csstudio.sns` into your IDE
- Under `org.csstudio.sns/repository` find `basic-epics.product`
- Open this product and synchronize it with the current target platform state
- Click on the "contents" tab, and for each feature that can't be resolved:
  * Import the relevant **feature** projects into eclipse. Note, you do not need to import the plugins themselves, just the features that define them are sufficient.
- Ensure all imported projects are set to use JDK8 with a compiler compliance of 1.8. Also ensure that [your java 8 installation is patched with JAVAFX](https://github.com/ISISComputingGroup/ibex_developers_manual/wiki/Upgrade-Java#additional-optional-steps-for-developer-installations-not-required-on-instruments).
- You should now have a project with no build errors in eclipse - however it will not launch at this time due to dependency version conflicts
- Go into the run configurations menu and select "plugins"
- Add required plugins
- Press validate plugins. This may report some errors, you need to fix these. Sometimes eclipse selects a newer dependency version when it actually required an older one.
- Ensure the following plugins are added explicitly:
  * `org.eclipse.equinox.ds`
  * `org.eclipse.equinox.event`
- You should now have a validation which reports no errors.
- In the run configurations menu select the box to "clear workspace before launching"
- Press apply and save
- Launch!
