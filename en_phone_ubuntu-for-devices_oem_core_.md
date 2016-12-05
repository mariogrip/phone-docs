





# Savvy Core tests

These tests verify the platform components required to implement the Savvy API
are working properly. As of this version, the main component is the ubuntu-
touch-customization-hooks package. Downstreams should not have to worry much
about the tests in the core other than verifying that they should be passing.
If the tests in core are not passing, all downstream customizations will
probably be broken.

## core.test_build

path = '/custom/build_id'

    Custom tarball build timestamp, generated by jenkins
class BuildCustomizationTests(*args, **kwargs)

    test_build_id()  
build_id is read during every boot by an upstart job:

custom-dconf-update.conf

If build_id is newer than the dconf database on the device, then the system
infers a new customization tarball has been installed and that potentially new
custom dconf keys have been shipped. If so, the upstart job regenerates the
dconf database.

Other properties of build_id:

  * generated automatically by jenkins at build time.
  * contents must be a valid date epoch string.
  * **read only** from the perspective of downstream.

## core.test_customization_enabled

customized_flag_path = '~/.customized'

    Flag to indicate pre-loaded sample content has been copied once
class GeneralCustomizationEnablement(*args, **kwargs)

test_customization_enablement()

    Downstreams may wish to ship pre-loaded sample content such as movies or music to their users. The implementation copies content from the read-only filesystem to the user's home directory, which is not known until first boot. The presence of the ~/.customized flag signals that the sample content has already been copied to the user area, and that it should not be copied again. This implementation allows the user to delete the sample content if desired, but will also properly re-copy the content to a new user after a "factory reset" event. For more details, read scripts/touch in the following package: lp:ubuntu/initramfs-tools-ubuntu-touch. Downstreams should _not_ modify this file.

## core.test_upstartjobs

upstart1 = '/usr/share/upstart/sessions/custom-env.conf'

    Custom Upstart job file
class UpstartCustomizationTests(*args, **kwargs)

    To implement the Ubuntu Savvy architecture, the contents of the custom tarball are unpacked into the /custom directory. The rest of the system is adapted to search in the /custom directory for data or directives to change look, feel, and behavior from default.  
To inform the system to search in /custom for these components, an Upstart job
is shipped in the ubuntu-touch-customization-hooks package. This Upstart job
sets the proper environment variables directing the system to search in
/custom for customization components.

This test ensures the ubuntu-touch-customization-hooks package has been
installed properly and that it sets the proper environment variables.

Downstream _should not_ modify this package or the Upstart job. However, if
you require additional environment variables, please [file a bug in thesavilerow project](https://bugs.launchpad.net/savilerow/+bugs).

test_presence_of_customized_upstart_files()

    Ensure files for Upstart job are installed in filesystem.
test_if_dconf_paths_are_exported()

    Ensure Upstart job has set customization environment variables properly.




